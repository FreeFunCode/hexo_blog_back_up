---
title: Android 源码分析（十一） 事件传递机制
date: 2018-07-15 13:18:25
tags: 安卓源码
categories: 
- Android
- 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/touchEvent.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.介绍</h2>
<p>　　Android三种事件类型：ACTION_DOWN,ACTOIN_MOVE,ACTION_UP。<br>　　事件传递的三个阶段：<br>　　　　分发(Dispatch)<br>　　　　　　方法：public boolean <strong>dispatchTouchEvent</strong>(MotionEvent ev)<br>　　　　拦截(Intercept)<br>　　　　　　方法：public boolean <strong>onInterceptTouchEvent</strong>(MotionEvent ev)<br>　　　　消费(Consume)<br>　　　　　　方法：public boolean <strong>onTouchEvent</strong>(MotionEvent event)</p>
<h2>二.源码分析</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">Activity.java   </span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Called to process touch screen events.  You can override this to
     * intercept all touch screen events before they are dispatched to the
     * window.  Be sure to call this implementation for touch screen events
     * that should be handled normally.
     *
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> ev The touch screen event.
     *
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> boolean Return true if this event was consumed.
     </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> dispatchTouchEvent(MotionEvent ev) {
        </span><span style="color: #0000ff;">if</span> (ev.getAction() ==<span style="color: #000000;"> MotionEvent.ACTION_DOWN) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">当此activity在栈顶时，用户对手机：触屏点击，按home，back，menu键都会触发此方法。
            </span><span style="color: #008000;">//</span><span style="color: #008000;">注：下拉statubar,旋转屏幕,锁屏，不会触发此方法.</span>
<span style="color: #000000;">            onUserInteraction();
        }
        </span><span style="color: #008000;">//</span><span style="color: #008000;">查看getWindow().对event做了什么分发？</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (getWindow().superDispatchTouchEvent(ev)) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> onTouchEvent(ev);
}

Window.java 是一个抽象类。在Activity里attach()查看Window创建过程
</span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> attach(...){

    attachBaseContext(context);

        mFragments.attachHost(</span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">parent</span><span style="color: #008000;">*/</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;">PhoneWindow 是 getWindow()的具体实现类。进去查看它的superDispatchTouchEvent()</span>
        mWindow = <span style="color: #0000ff;">new</span> PhoneWindow(<span style="color: #0000ff;">this</span><span style="color: #000000;">, window, activityConfigCallback);
        mWindow.setWindowControllerCallback(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
        mWindow.setCallback(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
        mWindow.setOnWindowDismissedCallback(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
        mWindow.getLayoutInflater().setPrivateFactory(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">if</span> (info.softInputMode !=<span style="color: #000000;"> WindowManager.LayoutParams.SOFT_INPUT_STATE_UNSPECIFIED) {
            mWindow.setSoftInputMode(info.softInputMode);
        }
        </span><span style="color: #0000ff;">if</span> (info.uiOptions != 0<span style="color: #000000;">) {
            mWindow.setUiOptions(info.uiOptions);
        }
        mUiThread </span>=<span style="color: #000000;"> Thread.currentThread();

        mMainThread </span>=<span style="color: #000000;"> aThread;
    ...
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">PhoneWindow.java extends Window</span>
<span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> superDispatchTouchEvent(MotionEvent event) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;">PhoneWindow Event 分发操作有在DecorView实现了，继续进去看看DecorView类。</span>
        <span style="color: #0000ff;">return</span><span style="color: #000000;"> mDecor.superDispatchTouchEvent(event);
    }

</span><span style="color: #008000;">//</span><span style="color: #008000;"> This is the top-level view of the window, containing the window decor.</span>
    <span style="color: #0000ff;">private</span> DecorView mDecor;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">DecorView.java </span>
     <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> superDispatchTouchEvent(MotionEvent event) {
         </span><span style="color: #008000;">//</span><span style="color: #008000;">DecorView就是activity的顶级view，
         </span><span style="color: #008000;">//</span><span style="color: #008000;">点击事件已经从activity传到View当中了，接下来要分析的就是顶级View把事件如何分发到各个子view中
         </span><span style="color: #008000;">//</span><span style="color: #008000;">DecorView extends FrameLayout</span>
        <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">super</span><span style="color: #000000;">.dispatchTouchEvent(event);
    }
    
     @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> dispatchTouchEvent(MotionEvent ev) {
        </span><span style="color: #0000ff;">final</span> Window.Callback cb =<span style="color: #000000;"> mWindow.getCallback();
        </span><span style="color: #0000ff;">return</span> cb != <span style="color: #0000ff;">null</span> &amp;&amp; !mWindow.isDestroyed() &amp;&amp; mFeatureId &lt; 0
                ? cb.dispatchTouchEvent(ev) : <span style="color: #0000ff;">super</span><span style="color: #000000;">.dispatchTouchEvent(ev);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">FrameLayout.java
    </span><span style="color: #008000;">//</span><span style="color: #008000;">FrameLayout extends ViewGroup 到 ViewGroup里看看</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> FrameLayout <span style="color: #0000ff;">extends</span> ViewGroup {}</pre>
</div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ViewGroup.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> dispatchTouchEvent(MotionEvent ev) {
        </span><span style="color: #0000ff;">if</span> (mInputEventConsistencyVerifier != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            mInputEventConsistencyVerifier.onTouchEvent(ev, </span>1<span style="color: #000000;">);
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the event targets the accessibility focused view and this is it, start
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> normal event dispatch. Maybe a descendant is what will handle the click.</span>
        <span style="color: #0000ff;">if</span> (ev.isTargetAccessibilityFocus() &amp;&amp;<span style="color: #000000;"> isAccessibilityFocusedViewOrHost()) {
            ev.setTargetAccessibilityFocus(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
        }

        </span><span style="color: #0000ff;">boolean</span> handled = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (onFilterTouchEventForSecurity(ev)) {
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> action =<span style="color: #000000;"> ev.getAction();
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> actionMasked = action &amp;<span style="color: #000000;"> MotionEvent.ACTION_MASK;

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Handle an initial down.
            </span><span style="color: #008000;">//</span><span style="color: #008000;">当点击事件是MotionEvent.ACTION_DOWN时，及手指刚刚触碰屏幕第一个触发的事件</span>
            <span style="color: #0000ff;">if</span> (actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_DOWN) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Throw away all previous state when starting a new touch gesture.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> The framework may have dropped the up or cancel event for the previous gesture
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> due to an app switch, ANR, or some other state change.</span>
<span style="color: #000000;">                cancelAndClearTouchTargets(ev);
                resetTouchState();
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Check for interception.
            </span><span style="color: #008000;">//</span><span style="color: #008000;">检查是否拦截</span>
            <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> intercepted;
            
            </span><span style="color: #008000;">//</span><span style="color: #008000;">判断是否父类是否拦截点击事件</span>
            <span style="color: #0000ff;">if</span> (actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_DOWN
                    </span>|| mFirstTouchTarget != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> disallowIntercept = (mGroupFlags &amp; FLAG_DISALLOW_INTERCEPT) != 0<span style="color: #000000;">;
                </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">disallowIntercept) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">调用拦截方法</span>
                    intercepted =<span style="color: #000000;"> onInterceptTouchEvent(ev);
                    ev.setAction(action); </span><span style="color: #008000;">//</span><span style="color: #008000;"> restore action in case it was changed</span>
                } <span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    intercepted </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
                }
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> There are no touch targets and this action is not an initial down
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> so this view group continues to intercept touches.</span>
                intercepted = <span style="color: #0000ff;">true</span><span style="color: #000000;">;
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If intercepted, start normal event dispatch. Also if there is already
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> a view that is handling the gesture, do normal event dispatch.</span>
            <span style="color: #0000ff;">if</span> (intercepted || mFirstTouchTarget != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                ev.setTargetAccessibilityFocus(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Check for cancelation.</span>
            <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> canceled = resetCancelNextUpFlag(<span style="color: #0000ff;">this</span><span style="color: #000000;">)
                    </span>|| actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_CANCEL;

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Update list of touch targets for pointer down, if needed.</span>
            <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> split = (mGroupFlags &amp; FLAG_SPLIT_MOTION_EVENTS) != 0<span style="color: #000000;">;
            TouchTarget newTouchTarget </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">boolean</span> alreadyDispatchedToNewTouchTarget = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            </span><span style="color: #008000;">//</span><span style="color: #008000;">没有被取消并且没有被拦截,事件传递到子View </span>
            <span style="color: #0000ff;">if</span> (!canceled &amp;&amp; !<span style="color: #000000;">intercepted) {

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the event is targeting accessiiblity focus we give it to the
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> view that has accessibility focus and if it does not handle it
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> we clear the flag and dispatch the event to all children as usual.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> We are looking up the accessibility focused host to avoid keeping
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> state since these events are very rare.</span>
                View childWithAccessibilityFocus =<span style="color: #000000;"> ev.isTargetAccessibilityFocus()
                        </span>? findChildWithAccessibilityFocus() : <span style="color: #0000ff;">null</span><span style="color: #000000;">;

                </span><span style="color: #0000ff;">if</span> (actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_DOWN
                        </span>|| (split &amp;&amp; actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_POINTER_DOWN)
                        </span>|| actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_HOVER_MOVE) {
                    </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> actionIndex = ev.getActionIndex(); <span style="color: #008000;">//</span><span style="color: #008000;"> always 0 for down</span>
                    <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> idBitsToAssign = split ? 1 &lt;&lt;<span style="color: #000000;"> ev.getPointerId(actionIndex)
                            : TouchTarget.ALL_POINTER_IDS;

                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Clean up earlier touch targets for this pointer id in case they
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> have become out of sync.</span>
<span style="color: #000000;">                    removePointersFromTouchTargets(idBitsToAssign);

                    </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> childrenCount =<span style="color: #000000;"> mChildrenCount;
                    </span><span style="color: #0000ff;">if</span> (newTouchTarget == <span style="color: #0000ff;">null</span> &amp;&amp; childrenCount != 0<span style="color: #000000;">) {
                        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">float</span> x =<span style="color: #000000;"> ev.getX(actionIndex);
                        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">float</span> y =<span style="color: #000000;"> ev.getY(actionIndex);
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Find a child that can receive the event.
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Scan children from front to back.</span>
                        <span style="color: #0000ff;">final</span> ArrayList&lt;View&gt; preorderedList =<span style="color: #000000;"> buildTouchDispatchChildList();
                        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> customOrder = preorderedList == <span style="color: #0000ff;">null</span>
                                &amp;&amp;<span style="color: #000000;"> isChildrenDrawingOrderEnabled();
                        </span><span style="color: #0000ff;">final</span> View[] children =<span style="color: #000000;"> mChildren;
                        </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = childrenCount - 1; i &gt;= 0; i--<span style="color: #000000;">) {
                            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> childIndex =<span style="color: #000000;"> getAndVerifyPreorderedIndex(
                                    childrenCount, i, customOrder);
                            </span><span style="color: #0000ff;">final</span> View child =<span style="color: #000000;"> getAndVerifyPreorderedView(
                                    preorderedList, children, childIndex);

                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If there is a view that has accessibility focus we want it
                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> to get the event first and if not handled we will perform a
                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> normal dispatch. We may do a double iteration but this is
                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> safer given the timeframe.</span>
                            <span style="color: #0000ff;">if</span> (childWithAccessibilityFocus != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                                </span><span style="color: #0000ff;">if</span> (childWithAccessibilityFocus !=<span style="color: #000000;"> child) {
                                    </span><span style="color: #0000ff;">continue</span><span style="color: #000000;">;
                                }
                                childWithAccessibilityFocus </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                                i </span>= childrenCount - 1<span style="color: #000000;">;
                            }

                            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">canViewReceivePointerEvents(child)
                                    </span>|| !isTransformedTouchPointInView(x, y, child, <span style="color: #0000ff;">null</span><span style="color: #000000;">)) {
                                ev.setTargetAccessibilityFocus(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
                                </span><span style="color: #0000ff;">continue</span><span style="color: #000000;">;
                            }

                            newTouchTarget </span>=<span style="color: #000000;"> getTouchTarget(child);
                            </span><span style="color: #0000ff;">if</span> (newTouchTarget != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Child is already receiving touch within its bounds.
                                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Give it the new pointer in addition to the ones it is handling.</span>
                                newTouchTarget.pointerIdBits |=<span style="color: #000000;"> idBitsToAssign;
                                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                            }

                            resetCancelNextUpFlag(child);
                            </span><span style="color: #0000ff;">if</span> (dispatchTransformedTouchEvent(ev, <span style="color: #0000ff;">false</span><span style="color: #000000;">, child, idBitsToAssign)) {
                                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Child wants to receive touch within its bounds.</span>
                                mLastTouchDownTime =<span style="color: #000000;"> ev.getDownTime();
                                </span><span style="color: #0000ff;">if</span> (preorderedList != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> childIndex points into presorted list, find original index</span>
                                    <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> j = 0; j &lt; childrenCount; j++<span style="color: #000000;">) {
                                        </span><span style="color: #0000ff;">if</span> (children[childIndex] ==<span style="color: #000000;"> mChildren[j]) {
                                            mLastTouchDownIndex </span>=<span style="color: #000000;"> j;
                                            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                                        }
                                    }
                                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                                    mLastTouchDownIndex </span>=<span style="color: #000000;"> childIndex;
                                }
                                mLastTouchDownX </span>=<span style="color: #000000;"> ev.getX();
                                mLastTouchDownY </span>=<span style="color: #000000;"> ev.getY();
                                newTouchTarget </span>=<span style="color: #000000;"> addTouchTarget(child, idBitsToAssign);
                                alreadyDispatchedToNewTouchTarget </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                            }

                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> The accessibility focus didn't handle the event, so clear
                            </span><span style="color: #008000;">//</span><span style="color: #008000;"> the flag and do a normal dispatch to all children.</span>
                            ev.setTargetAccessibilityFocus(<span style="color: #0000ff;">false</span><span style="color: #000000;">);
                        }
                        </span><span style="color: #0000ff;">if</span> (preorderedList != <span style="color: #0000ff;">null</span><span style="color: #000000;">) preorderedList.clear();
                    }

                    </span><span style="color: #0000ff;">if</span> (newTouchTarget == <span style="color: #0000ff;">null</span> &amp;&amp; mFirstTouchTarget != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Did not find a child to receive the event.
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Assign the pointer to the least recently added target.</span>
                        newTouchTarget =<span style="color: #000000;"> mFirstTouchTarget;
                        </span><span style="color: #0000ff;">while</span> (newTouchTarget.next != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                            newTouchTarget </span>=<span style="color: #000000;"> newTouchTarget.next;
                        }
                        newTouchTarget.pointerIdBits </span>|=<span style="color: #000000;"> idBitsToAssign;
                    }
                }
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Dispatch to touch targets.</span>
            <span style="color: #0000ff;">if</span> (mFirstTouchTarget == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> No touch targets so treat this as an ordinary view.</span>
                handled = dispatchTransformedTouchEvent(ev, canceled, <span style="color: #0000ff;">null</span><span style="color: #000000;">,
                        TouchTarget.ALL_POINTER_IDS);
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Dispatch to touch targets, excluding the new touch target if we already
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> dispatched to it.  Cancel touch targets if necessary.</span>
                TouchTarget predecessor = <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                TouchTarget target </span>=<span style="color: #000000;"> mFirstTouchTarget;
                </span><span style="color: #0000ff;">while</span> (target != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    </span><span style="color: #0000ff;">final</span> TouchTarget next =<span style="color: #000000;"> target.next;
                    </span><span style="color: #0000ff;">if</span> (alreadyDispatchedToNewTouchTarget &amp;&amp; target ==<span style="color: #000000;"> newTouchTarget) {
                        handled </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> cancelChild =<span style="color: #000000;"> resetCancelNextUpFlag(target.child)
                                </span>||<span style="color: #000000;"> intercepted;
                        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (dispatchTransformedTouchEvent(ev, cancelChild,
                                target.child, target.pointerIdBits)) {
                            handled </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                        }
                        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (cancelChild) {
                            </span><span style="color: #0000ff;">if</span> (predecessor == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                                mFirstTouchTarget </span>=<span style="color: #000000;"> next;
                            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                                predecessor.next </span>=<span style="color: #000000;"> next;
                            }
                            target.recycle();
                            target </span>=<span style="color: #000000;"> next;
                            </span><span style="color: #0000ff;">continue</span><span style="color: #000000;">;
                        }
                    }
                    predecessor </span>=<span style="color: #000000;"> target;
                    target </span>=<span style="color: #000000;"> next;
                }
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Update list of touch targets for pointer up or cancel, if needed.</span>
            <span style="color: #0000ff;">if</span><span style="color: #000000;"> (canceled
                    </span>|| actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_UP
                    </span>|| actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_HOVER_MOVE) {
                resetTouchState();
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (split &amp;&amp; actionMasked ==<span style="color: #000000;"> MotionEvent.ACTION_POINTER_UP) {
                </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> actionIndex =<span style="color: #000000;"> ev.getActionIndex();
                </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> idBitsToRemove = 1 &lt;&lt;<span style="color: #000000;"> ev.getPointerId(actionIndex);
                removePointersFromTouchTargets(idBitsToRemove);
            }
        }

        </span><span style="color: #0000ff;">if</span> (!handled &amp;&amp; mInputEventConsistencyVerifier != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            mInputEventConsistencyVerifier.onUnhandledEvent(ev, </span>1<span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> handled;
    }<br><br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>ViewGroup 分发事件总结：&nbsp;</p>
<p>&nbsp;<strong>1.</strong> android的事件传递是先传递到父类，再到子类的。 <br><strong>&nbsp;2.</strong> ViewGroup中可以通过onInterceptTouchEvent方法对事件传递进行拦截，但是子View可以通过requestDisallowInterceptTouchEvent(boolean disallowIntercept)控制父类的拦截事件是否调用。 <br>&nbsp;<strong>3</strong>. 子View消耗掉点击事件后，父类onTouchEvent方法不会调用，子View不消耗点击事件，会传到父类onTouchEvent方法，父类onTouchEvent方法返回false，则最终传递到activity的onTouchEvent方法。 <br>&nbsp;<strong>4.</strong> ViewGroup一旦调用onInterceptTouchEvent方法拦截点击事件后，本次点击序列事件则都交于该ViewGroup处理，并且onInterceptTouchEvent将不再执行。<br>&nbsp;<strong>5.</strong> 当dispatchTouchEvent在进行事件分发的时候，只有前一个action返回true，才会触发下一个action.也就是说，子view 未消耗点击事件，dispatchTouchEvent返回false，这样mFirstTouchTarget =null，则后续action直接由ViewGroup执行，不传递给子View。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> onInterceptTouchEvent(MotionEvent ev) {
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (ev.isFromSource(InputDevice.SOURCE_MOUSE)
                </span>&amp;&amp; ev.getAction() ==<span style="color: #000000;"> MotionEvent.ACTION_DOWN
                </span>&amp;&amp;<span style="color: #000000;"> ev.isButtonPressed(MotionEvent.BUTTON_PRIMARY)
                </span>&amp;&amp;<span style="color: #000000;"> isOnScrollbarThumb(ev.getX(), ev.getY())) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;"> @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> requestDisallowInterceptTouchEvent(<span style="color: #0000ff;">boolean</span><span style="color: #000000;"> disallowIntercept) {

        </span><span style="color: #0000ff;">if</span> (disallowIntercept == ((mGroupFlags &amp; FLAG_DISALLOW_INTERCEPT) != 0<span style="color: #000000;">)) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> We're already in this state, assume our ancestors are too</span>
            <span style="color: #0000ff;">return</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (disallowIntercept) {
            mGroupFlags </span>|=<span style="color: #000000;"> FLAG_DISALLOW_INTERCEPT;
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            mGroupFlags </span>&amp;= ~<span style="color: #000000;">FLAG_DISALLOW_INTERCEPT;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Pass it up to our parent</span>
        <span style="color: #0000ff;">if</span> (mParent != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            mParent.requestDisallowInterceptTouchEvent(disallowIntercept);
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>三.总结</h2>
<p>一张图介绍PhoneWindow,DecorView，Activity,ViewGroup,View 关系</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180715131417207-666045289.png" alt=""></p>
<p>在来一张图介绍下ViewGroup里的事件分发过程：（该图片来自https://www.jianshu.com/p/cf22ea3b09a5）</p>
<p>注：此图横着分析看，由上到下，由左到右。几个概念注意下 ：UserActivity(=Activiyt),ViewParent(=ViewGoup),ChildView(=View)。</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180715131456221-957174283.png" alt=""></p>
<p>&nbsp;</p>
</div>