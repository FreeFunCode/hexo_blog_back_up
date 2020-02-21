---
title: Android O service保活方案（startForeground&notification）
date: 2020-02-21 16:00:30
tags: service保活
categories: 
- Android
---

# Android O service保活方案（startForeground & notification）

### 一.问题

#### 问题：从android O版本开始，google为了控制资源使用，对service后台服务做了限制。

<!-- more -->	

##### 1. 第一个问题：后台service只能存活几分钟。

 现象就是，service启动后，应用退到后台运行，大概过几分钟service就会停止。堆栈信息：
 `ActivityManager: Stopping service due to app idle: xxxxService.`

 **分析源码:**
  
```
	// UID is now in the background (and not on the temp whitelist).  Was it
	// previously in the foreground (or on the temp whitelist)?
	if (!ActivityManager.isProcStateBackground(uidRec.setProcState)
			|| uidRec.setWhitelist) {
		uidRec.lastBackgroundTime = nowElapsed;
		if (!mHandler.hasMessages(IDLE_UIDS_MSG)) {
			// Note: the background settle time is in elapsed realtime, while
			// the handler time base is uptime.  All this means is that we may
			// stop background uids later than we had intended, but that only
			// happens because the device was sleeping so we are okay anyway.
			mHandler.sendEmptyMessageDelayed(IDLE_UIDS_MSG,
					mConstants.BACKGROUND_SETTLE_TIME);
		}
	}
```

> 如何是后台service并且不再白名单内，会发送一个延迟消息，延迟时间是BACKGROUND_SETTLE_TIME。

```
	public long BACKGROUND_SETTLE_TIME = DEFAULT_BACKGROUND_SETTLE_TIME;
	private static final long DEFAULT_BACKGROUND_SETTLE_TIME = 60*1000;
```

延迟时间大概是**1分钟**。

也就是：**8.0及以上版本手机中有一个机制，app退到后台后1分钟后会清理不再白名单中的后台service.**

----

##### 2.第二个问题：后台应用不能通过startService启动服务。

抛出的异常信息：`Not allowed to start service Intent XXX : app is in background uid UidRecord`

 **分析源码： **
```
		<!--检测当前app是否允许后台启动-->
        final int allowed = mAm.getAppStartModeLocked(r.appInfo.uid, r.packageName,
                r.appInfo.targetSdkVersion, callingPid, false, false, forcedStandby);
                <!--如果不允许  Background start not allowed-->
        if (allowed != ActivityManager.APP_START_MODE_NORMAL) {
            ...
            <!--返回 ? 告诉客户端现在处于后台启动状态，禁止你-->
            return new ComponentName("?", "app is in background uid " + uidRec);
        }

		 <!--返回值是？的情况下就是后台启动service的异常-->
		 if (cn.getPackageName().equals("?")) {
			throw new IllegalStateException(
					"Not allowed to start service " + service + ": " + cn.getClassName());
		}
```

-----

### 二.解决方案(startForeground & notification)：

回收优先级：前台进程<可视进程<服务进程<后台进程<内容供应根节点<空进程

开启前台Service，会在通知栏显示
通过**notification**方式 如音乐播放

**kotlin代码片段：**

> 启动/停止服务

```
if (!CallingStateListener.isCallingStateListener()) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                    startForegroundService(Intent(this, CallingStateListener::class.java))
                } else {
                    startService(Intent(this, CallingStateListener::class.java))
                }
            }

//关闭监听电话状态服务
        if (CallingStateListener.isCallingStateListener()) {
            stopService(Intent(this, CallingStateListener::class.java))
        }
		
```

> service 类

```		
class CallingStateListener : Service() {
    private val TAG = "CallingStateListener"
    private var phoneStateListener: PhoneStateListener? = null
    private var telephonyManager: TelephonyManager? = null
    private val notificationId = "callingChannelId"
    private val notificationName = "callingChannelName"

    override fun onCreate() {
        super.onCreate()

        initCallingStateListener()

        val notificationManager =
            getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        //创建NotificationChannel
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                notificationId,
                notificationName,
                NotificationManager.IMPORTANCE_HIGH
            )
            notificationManager.createNotificationChannel(channel)
        }
        startForeground(1, getNotification())

    }

    override fun onBind(intent: Intent?): IBinder? {
        return null
    }

    private fun initCallingStateListener() {

        phoneStateListener = object : PhoneStateListener() {
            @SuppressLint("MissingPermission")
            override fun onCallStateChanged(state: Int, incomingNumber: String) {
                super.onCallStateChanged(state, incomingNumber)

                when (state) {
                    TelephonyManager.CALL_STATE_IDLE // 待机，即无电话时，挂断时触发
                    -> {
                        Log.i(TAG, "callingState：挂断，对方手机号$incomingNumber")
                       
                    }

                    TelephonyManager.CALL_STATE_RINGING // 响铃，来电时触发
                    -> {
                        Log.i(TAG, "callingState：来电，对方手机号$incomingNumber")
                    }

                    TelephonyManager.CALL_STATE_OFFHOOK // 摘机，接听或打电话时触发
                    -> {
                        Log.i(TAG, "callingState：拨打电话，对方手机号$incomingNumber")

                    }

                    else -> {
                        Log.i(TAG, "callingState：其他")
                    }
                }
            }
        }

        // 设置来电监听器
        telephonyManager = getSystemService(TELEPHONY_SERVICE) as TelephonyManager?
        telephonyManager?.listen(phoneStateListener, PhoneStateListener.LISTEN_CALL_STATE)
    }


    private fun getNotification(): Notification {
        val builder = Notification.Builder(this)
            .setSmallIcon(R.mipmap.ic_launcher)
            .setContentTitle("通话服务")
            .setContentText("服务正在运行")
        //设置Notification的ChannelID,否则不能正常显示
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            builder.setChannelId(notificationId)
        }
        return builder.build()
    }

    override fun onDestroy() {
        stopForeground(true)
        super.onDestroy()
    }


    companion object {
        fun isCallingStateListener() =
            ServiceUtils.isServiceRunning(CallingStateListener::class.java)
    }

}
	
```
