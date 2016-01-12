---
layout: page
title: Android UI
category: 
description: Learning and Thinking in Android UI
---

[TOC]

# 1 

## 1.1 屏幕尺寸

http://developer.android.com/guide/topics/resources/more-resources.html#Dimension

http://www.zcool.com.cn/article/ZNjI3NDQ=.html

* dp: Density-independent Pixels(设备独立像素). 一种基于屏幕密度的抽象单位. 在160dpi的显示器上, 1dp = 1px. 推荐使用于布局.
* px: Pixels(像素). 屏幕的实际像素. 不同设备显示效果相同. 一般我们HVGA代表320x480像素，不推荐使用.
* sp: Scale-independent Pixels(与刻度无关的像素). 与dp类似，但是可以根据用户的字体大小首选项进行缩放. 推荐使用于字体显示.
* in: Inches(英寸). 屏幕的物理尺寸. 每英寸等于2.54厘米.
* pt: Points(点). 1/72英寸. 屏幕的物理尺寸.
* mm: Millimeters(毫米). 屏幕的物理尺寸.

## 1.2 屏幕密度

屏幕密度即dpi(dots per inch)或PPI(pixels per inch)

* ldpi: low density (120dpi) 0.75
* mdpi: medium density (160dpi) 1.0
* hdpi: high density (240dpi) 1.5
* xdpi: extra-high density (320dpi) 2.0
* xxdpi: extra-extra-high density (480dpi)

## 1.3 屏幕分辨率

分辨率就是手机屏幕的像素点数，一般描述成屏幕的“宽×高”, 常见的分辨率有480×800、720×1280、1080×1920等。720×1280表示此屏幕在宽度方向有720个像素，在高度方向有1280个像素.

## 1.4 DisplayMetrics

`android.util.DisplayMetrics`类

预定义的屏幕密度变量

DENSITY_LOW         120
DENSITY_MEDIUM      160
DENSITY_TV          213
DENSITY_HIGH        240
DENSITY_XHIGH       320
DENSITY_400         400
DENSITY_XXHIGH      480
DENSITY_560         560
DENSITY_XXXHIGH     640
DENSITY_DEFAULT     DENSITY_MEDIUM

公开成员变量:

widthPixels
heightPixels
density
densityDpi
scaledDensity
xdpi
ydpi

代码使用方式:

```
DisplayMetrics metrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(metrics);
```

# 2 View & ViewGroup

## 2.1 View

### 2.1.1 ID

#### ID

* setId(int)
* getId()
* findViewById(int)
* findViewTraversal(int)
* generateViewId()

XML属性

```
android:id
```

#### Position

* setLeft(int), setRight(int), onSizeChanged(int, int, int, int)
* getLeft(), getTop()
* setRight(int), setBottom(int), onSizeChanged(int, int, int, int)
* getRight(), getBottom()
* setFrame(int, int, int, int)
* onSizeChanged(int, int, int, int)

#### Size

* onMeasure(int, int), setMeasuredDimension(int, int), setMeasuredDimensionRaw(int, int), getMeasuredWidth(), getMeasuredHeight()
* getMeasuredWidthAndState(), getMeasuredHeightAndState(), getMeasuredState()
* getWidth(), getHeight()
* setMinimumWidth(int), getMinimumWidth()
* setMinimumHeight(int), getMinimumHeight()

#### XML属性

* android:minHeight
* android:minWidth

#### Padding

* setHorizontalFadingEdgeEnabled(boolean), isHorizontalFadingEdgeEnabled()
* setVerticalFadingEdgeEnabled(boolean), isVerticalFadingEdgeEnabled()

* getTopFadingEdgeStrength(), getBottomFadingEdgeStrength(), getLeftFadingEdgeStrength(), getRightFadingEdgeStrength()

* setPadding(int, int, int, int), setPaddingRelative(int, int, int, int)
* getPaddingLeft(), getPaddingTop(), getPaddingRight(), getPaddingBottom()
* getPaddingStart(), getPaddingEnd()
* isPaddingResolved(), resolvePadding(), resetResolvedPadding()
* recomputePadding()

#### XML属性

* android:requiresFadingEdge
* android:paddingEnd
* android:padding
* android:paddingLeft
* android:paddingRight
* android:paddingBottom
* android:paddingTop
* android:paddingStart 
* android:paddingEnd

#### 方法

* isPaddingOffsetRequired()
* getLeftPaddingOffset()
* getRightPaddingOffset()
* getTopPaddingOffset()
* getBottomPaddingOffset()
* getFadeTop(boolean)
* getFadeHeight(boolean)

#### Margin

View不直接支持, 但是其子类ViewGroup的支持, 见ViewGroup.MarginLayoutParams.

#### Alpha

* setAlpha(float), getAlpha()
* setAlphaNoInvalidation(float)
* setTransitionAlpha(float alpha), getTransitionAlpha()
* onSetAlpha(int)

#### XML属性

* android:alpha

#### Background

* getBackground()
* setBackground(Drawable)
* setBackgroundResource(int)
* setBackgroundColor(int)
* setBackgroundTintMode(PorterDuff.Mode)
* getBackgroundTintMode()
* applyBackgroundTint()
* setBackgroundTintList(ColorStateList)
* getBackgroundTintList()

#### XML属性

* android:background
* android:backgroundTint
* android:backgroundTintMode

#### Scale

* setScaleX(float), getScaleX()
* setScaleY(float), getScaleY()
* setPivotX(float), getPivotX()
* setPivotY(float), getPivotY()

#### XML属性

* android:scaleX
* android:scaleY
* android:transformPivotX
* android:transformPivotY

#### Rotate

* setRotation(float), getRotation()
* setRotationX(float), getRotationX()
* setRotationY(float), getRotationY()
* setPivotX(float), getPivotX()
* setPivotY(float), getPivotY()

#### XML属性

* android:rotation
* android:rotationX
* android:rotationY
* android:transformPivotX
* android:transformPivotY

#### Translation

* setTranslationX(float)
* getTranslationX()
* setTranslationY(float)
* getTranslationY()
* setTranslationZ(float)
* getTranslationZ()

#### XML属性

* android:translationX
* android:translationY
* android:translationZ

#### Elevation

* setElevation(float)
* getElevation()

#### XML属性

* android:elevation

#### Matrix

* getMatrix()
* hasIdentityMatrix()
* getInverseMatrix()

#### Scroll

* scrollBy(int, int), scrollTo(int, int), onScrollChanged(int, int, int, int)
* setScrollX(int), getScrollX()
* setScrollY(int), getScrollY()
* isHorizontalScrollBarEnabled()
* isVerticalScrollBarEnabled()
* setHorizontalScrollBarEnabled(boolean)
* setVerticalScrollBarEnabled(boolean)

#### XML属性

* android:scrollX 
* android:scrollY
* android:scrollbarAlwaysDrawHorizontalTrack 
* android:scrollbarAlwaysDrawVerticalTrack
* android:scrollbarDefaultDelayBeforeFade
* android:scrollbarFadeDuration 
* android:scrollbarSize
* android:scrollbarStyle
* android:scrollbarThumbHorizontal
* android:scrollbarThumbVertical
* android:scrollbarTrackHorizontal
* android:scrollbarTrackVertical
* android:scrollbars

#### Visibility

* setVisibility(int)
* getVisibility()
* isShown()

#### XML属性

android:visibility

#### Saveable

* setSaveEnabled(boolean)
* isSaveEnabled()
* setSaveFromParentEnabled(boolean)
* isSaveFromParentEnabled()

#### XML属性

* android:saveEnabled

#### Focusable

* setFocusable(boolean)
* setFocusableInTouchMode(boolean)
* isFocusable()
* isFocusableInTouchMode()

#### XML属性

* android:focusable
* android:focusableInTouchMode 

#### Clickable

* setClickable(boolean)
* setLongClickable(boolean)
* isClickable()
* isLongClickable()

#### XML属性

* android:clickable
* android:longClickable

#### HapticFeedbackEnabled

* setHapticFeedbackEnabled(boolean)
* isHapticFeedbackEnabled()
* performHapticFeedback(int)

#### XML属性

* android:hapticFeedbackEnabled

#### LayoutDirection

* LAYOUT_DIRECTION_LTR
* LAYOUT_DIRECTION_RTL
* LAYOUT_DIRECTION_INHERIT
* LAYOUT_DIRECTION_LOCALE

* setLayoutDirection(int)
* getLayoutDirection()
* getRawLayoutDirection()
* canResolveLayoutDirection()
* isLayoutRtl()
* resolveRtlPropertiesIfNeeded()
* isLayoutDirectionResolved()
* resolveLayoutDirection()
* resolveLayoutParams()
* isTextDirectionResolved()
* resolveTextDirection()
* isTextAlignmentResolved()
* resolveTextAlignment()
* isDrawablesResolved()
* resolveDrawables()
* isPaddingResolved()
* resolvePadding()
* onRtlPropertiesChanged()
* resetRtlProperties()
* resetResolvedLayoutDirection()
* resetResolvedTextDirection()
* resetResolvedTextAlignment()
* resetResolvedPadding()
* resetResolvedDrawables()

#### XML属性

* android:layoutDirection

#### WindowFocus

* hasWindowFocus()
* onWindowFocusChanged(boolean)

#### Seclected

* setSelected(boolean)
* isSelected()
* dispatchSetSelected(boolean)

#### Focus

* requestFocus(int, Rect)
* requestFocusFromTouch()
* isFocused()
* findFocus()
* clearFocus()
* setOnFocusChangeListener(OnFocusChangeListener)
* getOnFocusChangeListener()
* onFocusChanged(boolean, int, Rect)
* onFocusLost()

isFocusable()和isFocused()的关系:

#### Enabled

* setEnabled(boolean)
* isEnabled()

#### Pressed

* setPressed(boolean)
* isPressed()
* dispatchSetPressed(boolean)

#### Activated

* setActivated(boolean)
* isActivated()
* dispatchSetActivated(boolean)

#### Accelerated


#### Hovered

* setHovered(boolean)
* onHoverChanged(boolean)
* isHovered() 
* isHoverable()

#### Drag

* canAcceptDrag()

### 2.1.2 Layout

http://blog.csdn.net/luoshengyang/article/details/8372924

View的位置和尺寸到底是如何确定的?

1. 通常View只是需要简单地设置`android:layout_height`和`android:layout_width`XML属性即可. 然而, View源代码中并没有直接访问这两个属性. 实际上这两个XML属性是通过构造方法`LayoutParams(Context, AttributeSet)`创建一个新的ViewGroup.LayoutParams类的实例. 然后ViewGroup类的`addView(View, int, LayoutParams)`方法会添加新创建的View类的实例(该实例通常是由`android.view.LayoutInflater`类基于XML布局文件动态创建的)从而最终构建一个完整的视图树. 此时, 所有的View和ViewGroup实例中并没有位置和尺寸的概念, 而仅仅保存了mLayoutParams变量.

2. 接下来, 在遍历视图树的过程中, 视图树中View实例, 采用一定的机制(如ViewGroup的`getChildMeasureSpec`方法), 把所保存的mLayoutParams变量转换成MeasureSpec的int型变量.

3. 接下来, 将MeasureSpec的int型变量作为View的`measure`方法的参数. 此时, 所有的View实例仍然没有位置和尺寸的概念(或者说有了一定的尺寸概念), 而只是保存了mMeasuredWidth变量和mMeasuredHeight变量.

4. 最后, 通常情况下View的`layout(int, int, int, int)`方法的前两个参数为零, 后两个参数分别为View的`getMeasuredWidth`和`getMeasuredHeight`方法得到的值. 此时, 所有的View才有位置和尺寸概念, 保存了mLeft, mTop,  mRight, mBottom变量.

#### LayoutParams

* setLayoutParams(ViewGroup.LayoutParams)
* getLayoutParams()
* resolveLayoutParams()

而`android.view.ViewGroup.LayoutParams`类则仅仅描述View对象所期望尺寸的宽度和高度. 尺寸可以采用以下值之一:

* 一个实际数字
* MATCH_PARENT: View对象希望和父对象一样大(减去padding).
* WRAP_CONTENT: View对象希望足够大从而能够包裹它的内容(加上padding).

#### Measure

* makeMeasureSpec(int, int)
* getMode(int)
* getSize(int)
* adjust(int, int)

View对象使用`View.MeasureSpec`类来告知父对象所希望测量和定位的方式. MeasureSpecs可以是以下三个模式之一:

* UNSPECIFIED: 父对象不指定, 子对象可以自己确定.
* EXACTLY: 父对象指定, 子对象必须采用该尺寸.
* AT_MOST: 父对象指定最大尺寸, 子对象不超过即可.

* measure(int, int)
* onMeasure(int, int)
* setMeasuredDimension(int, int)
* setMeasuredDimensionRaw(int, int)
* resolveSize(int, int)
* resolveSizeAndState(int, int, int)
* getDefaultSize(int, int)

#### Layout

* layout(int, int, int, int)
* onLayout(boolean, int, int, int, int)
* requestLayout()
* forceLayout()
* isInLayout()
* isLayoutRequested() 
* isLayoutModeOptical(Object)
* setOpticalFrame(int, int, int, int)
* setFrame(int, int, int, int)

`layout`方法内部调用`setFrame`或`setOpticalFrame`, 而`serFrame`实质是:

1. 设置当前View的`mLeft`, `mTop`, `mRight`和`mBottom`
2. 触发相应的接口或监听器, 如`onLayout`
3. 调用`invalidate`方法刷新View的UI

而实际上`setTop`, `setLeft`, `setRight`和`setBottom`方法都会产生与`setFrame`相似的效果.

View类的`requestLayout`方法使得requestLayout请求在视图树中一直向上传递, 直到`ViewRootImpl`类处理. 而`ViewRootImpl`类的`requestLayout`方法会调用`performLayout`方法, 最终从视图树向下调用View类的`layout`方法.


### 2.1.3 Drawing

绘制是通过遍历视图树并且记录任何所需跟新视图的绘制命令来处理的. 之后, 整个树的绘制命令会被发到屏幕, 同时裁剪到新损坏的区域.

视图树记录量很大, 同时需要按照顺序绘制, 即父对象先于子对象, 而兄弟对象间按视图树所在顺序. 如果View设置了背景对象, 则View会在调用`onDraw()`方法返回之前绘制背景. 子对象绘制顺序可以通过ViewGroup的`setChildrenDrawnWithCacheEnabled(boolean)`定制顺序来覆写, 也可以通过View的`setZ(float)`定制Z值来覆写.

#### Draw

* Draw(Canvas)
* drawBackground(Canvas)
* draw(Canvas)
* onDraw(Canvas)
* dispatchDraw(Canvas)
* onDrawScrollBars(Canvas)
* isDirty()
* getDrawingTime()

`Draw`方法的绘制顺序:

1. 绘制背景
2. 如果需要, 保存画布预备fading
3. 绘制内容
4. 绘制子对象
5. 如果需要, 绘制fading, 恢复画布
6. 绘制装饰, 如滚动条

#### Invalidate

* postInvalidate()
* postInvalidate(int, int, int, int)
* postInvalidateDelayed(long)
* postInvalidateDelayed(int, int, int, int)
* postInvalidateOnAnimation()
* postInvalidateOnAnimation(int, int, int, int)

* invalidate()
* invalidate(boolean)
* invalidate(Rect)
* invalidate(int, int, int, int)
* invalidateDrawable(Drawable)
* invalidateOutline()

* invalidateParentIfNeeded()
* invalidateParentCaches()
* invalidateViewProperty(boolean, boolean) 

### 2.3.3 DrawingCache

#### 方法

* getDrawingCache()
* getDrawingCache(boolean)
* destroyDrawingCache()
* isDrawingCacheEnabled()
* setDrawingCacheEnabled(boolean)
* buildDrawingCache()
* buildDrawingCache(boolean)
* setDrawingCacheQuality(int)
* getDrawingCacheQuality()
* willNotCacheDrawing()
* setWillNotCacheDrawing(boolean)

#### XML属性

* android:drawingCacheQuality

#### DrawableState

* scheduleDrawable(Drawable, Runnable, long)
* unscheduleDrawable(Drawable, Runnable)
* mergeDrawableStates(int[], int[])
* onCreateDrawableState(int)
* onResolveDrawables(int)
* unscheduleDrawable(Drawable)
* verifyDrawable(Drawable)
* resetResolvedDrawables()
* isDrawablesResolved()
* drawableStateChanged()
* jumpDrawablesToCurrentState()
* resolveDrawables()
* getDrawableState()
* refreshDrawableState()

#### drawAnimation

* draw(Canvas, ViewGroup, long)
* drawAnimation(ViewGroup, long, Animation, boolean)
* startAnimation(Animation)
* clearAnimation()
* getAnimation()
* setAnimation(Animation)
* onAnimationStart()
* onAnimationEnd()

## 2.1.4 Event

View(不包括子类ViewGroup)的事件处理框架如下所示:

1. 系统产生一个事件, 或硬件触发, 或者软件产生, 或View外部产生, 或View内部产生.
2. 调度View的相应方法, 如dispatchXXX. 一般是设置内部状态, 处理通用性工作.
3. 在调度方法中, 可能会一步细分, 分别调用事件不同状态下的方法, 如onKeyDown或onKeyUp. 而这些细分的方法, 通常View类本身不处理任何事情, 而其子类可能会覆写这些方法(而不是也不应该重置调用方法)来进一步处理.
4. 此外, 在调度方法中, 也可能会触发预先设置的监听器, 如OnKeyListener接口.

通常View认可的事件包括:

* Key event
* Touch event
* GenericMotion event
* Hover event
* Drag event
* Click event
* LongClick event
* FocusChange event
* LayoutChange event
* AttachStateChange event
* SystemUiVisibilityChange event
* CreateContextMenu event
* ApplyWindowInsets event

#### 分发

* dispatchKeyEvent(KeyEvent)
* dispatchKeyEventPreIme(KeyEvent)
* dispatchKeyShortcutEvent(KeyEvent)
* dispatchGenericPointerEvent(MotionEvent)
* dispatchTrackballEvent(MotionEvent)
* dispatchPointerEvent(MotionEvent)
* dispatchTouchEvent(MotionEvent)
* dispatchGenericMotionEvent(MotionEvent)
* dispatchGenericFocusedEvent(MotionEvent)
* dispatchDragEvent(DragEvent)
* dispatchPopulateAccessibilityEvent(AccessibilityEvent)
* dispatchApplyWindowInsets(WindowInsets)
* dispatchWindowFocusChanged(boolean)
* dispatchSetPressed(boolean)
* dispatchSetSelected(boolean)
* dispatchSetActivated(boolean)
* dispatchUnhandledMove(View ,int)
* dispatchVisibilityChanged(View, int)
* dispatchWindowVisibilityChanged(int)
* dispatchWindowSystemUiVisiblityChanged(int)
* dispatchSystemUiVisibilityChanged(int)
* dispatchDisplayHint(int)
* dispatchScreenStateChanged(int)
* dispatchConfigurationChanged(Configuration)
* dispatchCollectViewAttributes(AttachInfo, int)
* dispatchAttachedToWindow(AttachInfo, int)
* dispatchDraw(Canvas)
* dispatchSaveInstanceState(SparseArray<Parcelable>)
* dispatchRestoreInstanceState(SparseArray<Parcelable>)
* dispatchNestedScroll(int, int, int, int, int[])
* dispatchNestedPreScroll(int, int, int[], int[])
* dispatchNestedFling(float, float, boolean)
* dispatchNestedPreFling(float, float)
* dispatchStartTemporaryDetach()
* dispatchFinishTemporaryDetach()
* dispatchCancelPendingInputEvents()
* dispatchGetDisplayList()

#### 

* onKeyDown(int, KeyEvent)
* onKeyUp(int, KeyEvent)
* onKeyPreIme(int, KeyEvent)
* onKeyLongPress(int, KeyEvent)
* onKeyMultiple(int, int, KeyEvent)
* onKeyShortcut(int, KeyEvent)
* onFilterTouchEventForSecurity(MotionEvent)
* onTouchEvent(MotionEvent)
* onGenericMotionEvent(MotionEvent)
* onHoverEvent(MotionEvent)
* onHoverChanged(boolean)
* onTrackballEvent(MotionEvent)
* onFocusChanged(boolean, int, Rect)
* onConfigurationChanged(Configuration)
* onScreenStateChanged(int)
* onWindowSystemUiVisibilityChanged(int)
* onPopulateAccessibilityEvent(AccessibilityEvent)
* onInitializeAccessibilityEvent(AccessibilityEvent)
* onCreateInputConnection(EditorInfo)
* onCreateContextMenu(ContextMenu)
* onCancelPendingInputEvents()
* onSaveInstanceState()
* onRestoreInstanceState(Parcelable)

#### Listener


```
    public interface OnKeyListener {
        boolean onKey(View v, int keyCode, KeyEvent event);
    }

    public interface OnTouchListener {
        boolean onTouch(View v, MotionEvent event);
    }

    public interface OnHoverListener {
        boolean onHover(View v, MotionEvent event);
    }

    public interface OnGenericMotionListener {
        boolean onGenericMotion(View v, MotionEvent event);
    }

    public interface OnLongClickListener {
        boolean onLongClick(View v);
    }

    public interface OnDragListener {
        boolean onDrag(View v, DragEvent event);
    }

    public interface OnFocusChangeListener {
        void onFocusChange(View v, boolean hasFocus);
    }

    public interface OnClickListener {
        void onClick(View v);
    }

    public interface OnCreateContextMenuListener {
        void onCreateContextMenu(ContextMenu menu, View v, ContextMenuInfo menuInfo);
    }

    public interface OnSystemUiVisibilityChangeListener {
        public void onSystemUiVisibilityChange(int visibility);
    }

    public interface OnAttachStateChangeListener {
        public void onViewAttachedToWindow(View v);
        public void onViewDetachedFromWindow(View v);
    }

    public interface OnApplyWindowInsetsListener {
        public WindowInsets onApplyWindowInsets(View v, WindowInsets insets);
    }
```

* addOnLayoutChangeListener(OnLayoutChangeListener)
* removeOnLayoutChangeListener(OnLayoutChangeListener)
* addOnAttachStateChangeListener(OnAttachStateChangeListener)
* removeOnAttachStateChangeListener(OnAttachStateChangeListener)
* setOnClickListener(OnClickListener)
* hasOnClickListeners()
* setOnLongClickListener(OnLongClickListener)
* setOnCreateContextMenuListener(OnCreateContextMenuListener)
* setOnKeyListener(OnKeyListener)
* setOnTouchListener(OnTouchListener)
* setOnGenericMotionListener(OnGenericMotionListener)
* setOnHoverListener(OnHoverListener)
* setOnDragListener(OnDragListener)
* setOnFocusChangeListener(OnFocusChangeListene)
* getOnFocusChangeListener()

#### XML属性

* android:onClick

## 2.1.5 Focus

`hasFocus`和`isFocused`的语义应该是一样的, 而源代码中的实现也的确是完全一样的. 但是, `hasFocusable`和`isFocusable`的实现并不是完全一样.

#### 方法

* setNextFocusLeftId(int)
* getNextFocusLeftId()
* setNextFocusRightId(int)
* getNextFocusRightId()
* setNextFocusUpId(int)
* getNextFocusUpId()
* setNextFocusDownId(int)
* getNextFocusDownId()
* setNextFocusForwardId(int)
* getNextFocusForwardId()

* requestFocus()
* requestFocus(int)
* requestFocus(int, Rect)
* requestFocusFromTouch()
* findFocus()
* clearFocus()
* focusSearch()
* getFocusables(int)
* addFocusables(ArrayList<View>, int)
* ddFocusables(ArrayList<View>, int, int)

* hasFocus()
* hasFocusable()
* isFocused()
* isFocusable()
* isFocusableInTouchMode()
* onFocusChanged(boolean, int, Rect)
* onFocusLost()

* setOnFocusChangeListener(View.OnFocusChangeListener)

#### XML属性

* android:nextFocusDown
* android:nextFocusForward
* android:nextFocusLeft
* android:nextFocusRight
* android:nextFocusUp

## 2.1.6 Text

#### 方法

* setTextDirection(int)
* getTextDirection()
* resolveTextDirection()
* canResolveTextDirection()
* resetResolvedTextDirection()
* isTextDirectionInherited()
* isTextDirectionResolved()
* getRawTextAlignment()
* setTextAlignment(int)
* getTextAlignment()
* resolveTextAlignment()
* canResolveTextAlignment()
* resetResolvedTextAlignment()
* isTextAlignmentInherited()
* isTextAlignmentResolved()

#### XML属性

* android:textAlignment
* android:textDirection

### 2.1.7 Thread

* post(Runnable)
* postDelayed(Runnable, long)
* postOnAnimation(Runnable)
* postOnAnimationDelayed(Runnable, long)
* removeCallbacks(Runnable)

### 2.1.8 AccessibilityEvent

* sendAccessibilityEvent(int)
* announceForAccessibility(CharSequence)
* sendAccessibilityEventUnchecked(AccessibilityEvent)
* onInitializeAccessibilityEvent(AccessibilityEvent)
* dispatchPopulateAccessibilityEvent(AccessibilityEvent)
* onPopulateAccessibilityEvent(AccessibilityEvent)

* onPopulateAccessibilityEvent(AccessibilityEvent)
* onInitializeAccessibilityNodeInfo(AccessibilityNodeInfo)
* getAccessibilityDelegate()
* setAccessibilityDelegate(AccessibilityDelegate)
* getAccessibilityNodeProvider()
* getAccessibilityViewId()
* getAccessibilityWindowId()

* requestAccessibilityFocus()
* isAccessibilityFocused()
* clearAccessibilityFocus()


## 2.2 ViewGroup

### 

* getDescendantFocusability()
* setDescendantFocusability(int)

参数:

FOCUS_BEFORE_DESCENDANTS
FOCUS_AFTER_DESCENDANTS
FOCUS_BLOCK_DESCENDANTS

###

* requestChildFocus(View, View)
* focusableViewAvailable(View)
* focusSearch(View, int)
* clearChildFocus(View)
* clearFocus()
* getFocusedChild()
* hasFocus()
* findFocus()
* hasFocusable()
* addFocusables(ArrayList<View> views, int direction, int)
* requestFocus(int direction, Rect)
* onRequestFocusInDescendants(int, Rect)

* setTouchscreenBlocksFocus(boolean)
* getTouchscreenBlocksFocus()

####

* requestSendAccessibilityEvent(View, AccessibilityEvent)
* onRequestSendAccessibilityEvent(View, AccessibilityEvent)

####

* findViewsWithText(ArrayList<View>, CharSequence, int)
* findViewByAccessibilityIdTraversal(int)

####

*  dispatchWindowFocusChanged(boolean)

####

* addTouchables(ArrayList<View>)

####

* onChildVisibilityChanged(View, int, int)
* dispatchVisibilityChanged(View, int)
* dispatchWindowVisibilityChanged(int)


####

* dispatchWindowSystemUiVisiblityChanged(int)
* dispatchSystemUiVisibilityChanged(int)
* updateLocalSystemUiVisibility(int, int)

####

* dispatchDragEvent(DragEvent)
* dispatchKeyEventPreIme(KeyEvent)
* dispatchKeyEvent(KeyEvent)
* dispatchKeyShortcutEvent(KeyEvent)
* dispatchTrackballEvent(MotionEvent)
* dispatchHoverEvent(MotionEvent)
* onInterceptHoverEvent(MotionEvent)
* dispatchGenericPointerEvent(MotionEvent)
* dispatchGenericFocusedEvent(MotionEvent)
* dispatchTouchEvent(MotionEvent)
* onInterceptTouchEvent(MotionEvent)

####

* dispatchSaveInstanceState(SparseArray<Parcelable>)
* dispatchFreezeSelfOnly(SparseArray<Parcelable>)
* dispatchRestoreInstanceState(SparseArray<Parcelable>)
* dispatchThawSelfOnly(SparseArray<Parcelable>)

####

* dispatchSetSelected(boolean)
* dispatchSetActivated(boolean)
* dispatchSetPressed(boolean)

### 2.2.1 Views

#### Find

* findViewTraversal(int)    系统隐藏
* findViewWithTagTraversal(Object)    系统隐藏
* findViewByPredicateTraversal(Predicate<View>, View)    系统隐藏
* findViewByAccessibilityIdTraversal(int)    系统隐藏
* findViewsWithText(ArrayList<View>, CharSequence, int)
* indexOfChild(View)
* getChildCount()
* getChildAt(int)

Activity类中用户通常使用`findViewById`方法来访问相应的View实例, 而实质上Activity类的`findViewById`方法调用的是DecorView的`findViewById`方法, 而DecorView虽然是ViewGroup类的子类, 但是ViewGroup类并没有覆写`findViewById`方法, 因此最终Activity类调用的是View的`findViewById`方法.

```
    public final View findViewById(int id) {
        if (id < 0) {
            return null;
        }
        return findViewTraversal(id);
    }
```

通过源代码可知, ViewGroup类虽然没有覆写`findViewById`方法, 但是覆写了`findViewTraversal`方法. 因此Activity类最终实质上是调用了ViewGroup类的`findViewTraversal`方法遍历了整个视图树, 最终返回View实例.

#### Add

* addView(View)
* addView(View, int)
* addView(View, int, int)
* addView(View, LayoutParams)
* addView(View, int, LayoutParams)

* addViewInLayout(View, int, LayoutParams)
* addViewInLayout(View, int, LayoutParams, boolean)

* addViewInner(View, int, LayoutParams, boolean)

`addViewInLayout`方法在layout过程中使用, 例如在`onLayout`方法中调用.

#### Remove

* removeView(View)
* removeViewAt(int)
* removeViews(int, int)
* removeAllViews()
* removeViewInLayout(View)
* removeViewsInLayout(int, int)
* removeAllViewsInLayout()

* removeViewInternal(int, View)

这些方法不能在`draw(Canvas)`, `onDraw(Canvas)`, `dispatchDraw(Canvas)`之类的方法中调用.

`removeViewInLayout`方法在layout过程中使用, 例如在`onLayout`方法中调用.

#### Listener

* setOnHierarchyChangeListener(OnHierarchyChangeListener)
* onViewAdded(View)
* onViewRemoved(View)

```
    public interface OnHierarchyChangeListener {
        void onChildViewAdded(View parent, View child);
        void onChildViewRemoved(View parent, View child);
    }
```

####

* removeDetachedView(View, boolean)
* attachViewToParent(View, int, LayoutParams)
* detachViewFromParent(View)
* detachViewFromParent(int)
* detachViewsFromParent(int, int)
* detachAllViewsFromParent()

### 2.2.2 Draw

####

* invalidateChild(View, Rect)
* invalidateChildInParent(int[], Rect)

####

* damageChildDeferred(View)    系统隐藏
* damageChild(View, Rect)    系统隐藏
* damageChildInParent(int, int, Rect)    系统隐藏

####

* measureChildren(int, int)
* measureChild(View, int, int)
* measureChildWithMargins(View, int, int, int, int)
* getChildMeasureSpec(int, int, int)

####

* layout(int, int, int, int)
* onLayout(boolean, int, int, int, int)

* generateLayoutParams(AttributeSet)
* generateLayoutParams(LayoutParams)
* generateDefaultLayoutParams()

LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT

####

* offsetDescendantRectToMyCoords(View, Rect)
* offsetRectIntoDescendantCoords(View, Rect)
* offsetChildrenTopAndBottom(int)    系统隐藏
* offsetRectBetweenParentAndChild(View, Rect, boolean, boolean)

####

* getChildVisibleRect(View, Rect, Point)

####

* updateViewLayout(View, LayoutParams)
* checkLayoutParams(LayoutParams)

#### LayoutTransition

* setLayoutTransition(LayoutTransition)
* getLayoutTransition()

`android.animation.LayoutTransition`类是管理ViewGroup类中布局变化时所使用Animator的容器.
1. CHANGE_APPEARING: 由于在这个容器总新增加了一个view，而导致原来的view位置发
2. CHANGE_DISAPPEARING: 由于在这个容器中移除了一个view，而导致原来的view位置发生改变所以会触发这个动画.
3. APPEARING: 动画所运行的项目出现在这个容器中时，即：view显示时的动画
生改变所以会触发这个动画。
4. DISAPPEARING: view在这个容器中消失时触发的动画
5. CHANGING: 



####

* attachLayoutAnimationParameters(View, LayoutParams, int, int)

####

* addDisappearingView(View)
* clearDisappearingChildren()

####

* startViewTransition(View)
* endViewTransition(View)



####

* onAttachedToWindow()
* onDetachedFromWindow()

####

* onAnimationStart()
* onAnimationEnd()

####

* canAnimate()
* startLayoutAnimation()
* scheduleLayoutAnimation()
* setLayoutAnimation(LayoutAnimationController)
* getLayoutAnimation()

####

* getLayoutAnimationListener()
* setLayoutAnimationListener(AnimationListener)

####

* onStartNestedScroll(View child, View target, int)
* onNestedScrollAccepted(View child, View target, int)
* onStopNestedScroll(View)
* onNestedScroll(View target, int dxConsumed, int dyConsumed,
            int dxUnconsumed, int dyUnconsumed)
* onNestedPreScroll(View target, int dx, int dy, int[])
* onNestedFling(View target, float velocityX, float velocityY, boolean consumed)
* onNestedPreFling(View target, float velocityX, float velocityY)
* getNestedScrollAxes()
####

* drawableStateChanged()
* jumpDrawablesToCurrentState()
* drawableHotspotChanged(float, float)
* onCreateDrawableState(int)
* setAddStatesFromChildren(boolean)
* addStatesFromChildren()
* childDrawableStateChanged(View)

####

* setAnimationCacheEnabled(boolean)
* isAnimationCacheEnabled()
* setAlwaysDrawnWithCacheEnabled(boolean)
* isAlwaysDrawnWithCacheEnabled()
* setChildrenDrawnWithCacheEnabled(boolean)
* isChildrenDrawnWithCacheEnabled()
* setChildrenDrawingOrderEnabled(boolean)
* isChildrenDrawingOrderEnabled()

####

* setPersistentDrawingCache(int)
* getPersistentDrawingCache()

PERSISTENT_NO_CACHE
PERSISTENT_ANIMATION_CACHE
PERSISTENT_SCROLLING_CACHE
PERSISTENT_ALL_CACHES

####

* setLayoutMode(int)
* getLayoutMode()

LAYOUT_MODE_CLIP_BOUNDS
LAYOUT_MODE_OPTICAL_BOUNDS

####

* dispatchDraw(Canvas)
* getChildDrawingOrder(int, int)
* drawChild(Canvas, View, long)

####

* getClipChildren()
* setClipChildren(boolean)
* setClipToPadding(boolean)
* getClipToPadding()


####

* getOverlay()

####

* bringChildToFront(View)
* addChildrenForAccessibility(ArrayList<View>)
* isTransformedTouchPointInView(float, float, View, PointF)
* setMotionEventSplittingEnabled(boolean)
* isMotionEventSplittingEnabled()
* isTransitionGroup()
* requestDisallowInterceptTouchEvent(boolean)
* setChildrenDrawingCacheEnabled(boolean)
* gatherTransparentRegion(Region)
* requestTransparentRegion(View)
* dispatchApplyWindowInsets(WindowInsets)

### 2.2. 子类

直接子类:

* AbsoluteLayout,
* AdapterView<T extends Adapter>
* CoordinatorLayout
* DrawerLayout
* FragmentBreadCrumbs
* FrameLayout
* GridLayout
* LinearLayout
* LinearLayoutCompat
* PagerTitleStrip
* RecyclerView
* RelativeLayout
* SlidingDrawer
* SlidingPaneLayout
* SwipeRefreshLayout
* Toolbar
* TvView
* ViewPager 

非直接子类:

AbsListView
AbsSpinner
ActionMenuView
AdapterViewAnimator
AdapterViewFlipper 
AppBarLayout
AppCompatSpinner
AppWidgetHostView
BaseCardView
BrowseFrameLayout
CalendarView
CardView
CollapsingToolbarLayout
DatePicker
DialerFilter
ExpandableListView
FragmentTabHost
Gallery
GestureOverlayView
GridView
HorizontalGridView
HorizontalScrollView
ImageCardView
ImageSwitcher
ListRowHoverCardView
ListRowView
ListView
MediaController 
NavigationView
NestedScrollView
NumberPicker
PagerTabStrip
PercentFrameLayout
PercentRelativeLayout
RadioGroup
ScrollView
SearchBar
SearchOrbView
StackView
TabHost
TabLayout
TabWidget
TableLayout
TableRow
TextInputLayout
TextSwitcher
TimePicker
TitleView
TwoLineListItem
VerticalGridView
ViewAnimator
ViewFlipper
ViewSwitcher
WebView
ZoomControls

# 3 Graphics

## 3.0

`android.graphics.Point`
`android.graphics.PointF`
`android.graphics.Rect`
`android.graphics.RectF`
`android.graphics.Region`
`android.graphics.Typeface`
`android.graphics.FontFamily`
`android.graphics.Movie`
`android.graphics.NinePatch`
`android.graphics.Picture`
`android.graphics.SurfaceTexture`
`android.graphics.Picture`
`android.graphics.LargeBitMap`
`android.graphics.Camera`
`android.graphics.BitmapFactory`

## 3.1 Path

`android.graphics.Path`类

Path是保存一个或多个线条的类.

1. 线条类型可以是直线或曲线.
2. 线条数量可以是零个, 一个或多个.
3. 存在多个线条时, 线条之间可以相连, 分离, 相交.
4. 线条存在方向. Path的方向由所有内部线条组合所决定.
5. 多个线条组成的封闭区域可以是零个, 一个或多个.
6. 一个视觉上相连续光滑的线条(直线或曲线)不一定是一条线, 也可能是由多个线条组成的视觉上的"一条线".
7. 一个视觉上由线条所封闭的区域也不一定是封闭的, 也可能是由多个线条组合起来视觉上的"封闭".

真正决定Path内部线条之间的关系与性质的是: `moveTo`(或`rMoveTo`)和 `close`的使用.

### 3.1.1 

####

* isRect(RectF)
* computeBounds(RectF, boolean)  计算路径范围
* isEmpty()           是否为空(没有线条和曲线)
* isConvex()          是否是凸面
* set(Path)

#### 起始点

* moveTo(float, float)
* rMoveTo(float, float)

添加一个新的起始点

#### 终点

* setLastPoint(float, float)

修改当前终点为新的位置

#### 平移

* offset(float, float)
* offset(float, float, Path)

所有点平移(dx, dy)个单位

#### 重置

* rewind()            清除掉线条和曲线，但是会保留内部数据结构以便重用
* reset()             清除掉线条和曲线，但是不会改变illType

#### 闭合

* close()

闭合起始点和终点

### 3.1.2

#### 直线

* lineTo(float, float)
* rLineTo(float, float)

`lineTo(x, y)`是基于坐标原点(0,0)设置目标点(x,y); 而`rLineTo(dx, dy)`是基于当前终点(lx, ly)设置目标点(lx+dx, ly+dy).

#### 弧线

* arcTo(RectF, float, float)
* arcTo(RectF, float, float, boolean)
* arcTo(float, float, float, float, float, float, boolean)

#### 二次贝赛尔曲线

* quadTo(float, float, float, float)
* rQuadTo(float, float, float, float)

存在一个控制点

#### 三次贝赛尔曲线

* cubicTo(float, float, float, float, float, float)
* rCubicTo(float, float, float, float, float, float)

存在两个控制点

#### 矩形

* addRect(RectF, Direction)
* addRect(float, float, float, float, Direction)

Direction.CW     顺时针方向
Direction.CCW    逆时针方向

`addXXX`方法内部会自动执行`moveTo`和`close`方法.

#### 椭圆

* addOval(RectF, Direction)
* addOval(float, float, float, float, Direction)

#### 圆

* addCircle(float, float, float, Direction)

#### 圆弧

* addArc(RectF, float, float)
* addArc(float, float, float, float, float, float)

和其他`addXXX`方法不同, `addArc`并不会简单地自动执行`moveTo`和`close`方法.

SkPath的实现原理: 如果扫描角度大于360度, 则执行`addOval`方法; 否则, 执行`arcTo`方法.

#### 圆角矩形

* addRoundRect(RectF, float[], Direction)
* addRoundRect(RectF, float, float, Direction)
* addRoundRect(float, float, float, float, float[], Direction)
* addRoundRect(float, float, float, float, float, float, Direction)

#### 路径

* addPath(Path)
* addPath(Path, Matrix)
* addPath(Path, float, float)

### 3.1.3

#### Op

* op(Path, Op)
* op(Path, Path, Op)

Op.DIFFERENCE              F和S的差集
Op.INTERSECT               F和S的交集
Op.UNION                   F和S的并集
Op.XOR                     F和S的补集
Op.REVERSE_DIFFERENCE      S和F的差集

如何理解Path的运算关系?

本质是基于两个Path构建两个Region, 然后执行Op, 得到一个新的Region, 最后从这个Region再得到Path.

例如: 基于Path(F)和Path(S), 得到Region(F)和Region(S), 
因此Op.DIFFERENCE的结果就是Region(F)减去被Region(S)所挡住的交集区域而得到的新区域, 
所有新区域的边界即Path(F-S).

需要注意的是: **这里的Path必须能够构成一个非空的Region**.
例如一个直线的Path对象得到的Region是一个空的Region对象, 因此没有计算的意义.
 
#### FillType 

* setFillType(FillType)
* getFillType()
* toggleInverseFillType()
* isInverseFillType()

FillType.WINDING              取路径所在区域
FillType.EVEN_ODD             取路径所在但不相交区域
FillType.INVERSE_WINDING      取路径未占区域
FillType.INVERSE_EVEN_ODD     取路径未占或相交区域

需要注意的是: **这里Path的方向也会带来不同的效果**.
例如两个相交圆组成的一个Path对象, 如果圆的方向是一致的, 那么默认填充效果是两个圆都涂满颜色;
但是, 如果圆的方向不一致, 那么默认的填充效果是两个圆但不相交的部分都涂满颜色.

#### transform

* transform(Matrix)
* transform(Matrix, Path)

变换路径

####

* incReserve(int)

设置Path预留一定空间

####

* approximate(float)

### 参考

* https://github.com/google/skia/blob/master/include/core/SkPath.h
* https://github.com/google/skia/blob/master/src/core/SkPath.cpp
* https://github.com/google/skia/blob/master/include/pathops/SkPathOps.h
* https://github.com/google/skia/tree/master/src/pathops

## 3.2 PathMeasure

`android.graphics.PathMeasure`类

* setPath(Path, boolean)

需要注意的的是: 第二个参数, 即boolean类型的`forceClosed`参数.

1. 当设置成false时, 则PathMeasure测量原始Path对象;
2. 但是设置成true时, 则PathMeasure以闭合的方式测量原始Path对象.
也就是说, Path对象本身仍然没有变化, 但是PathMeasure遍历Path时, 会以`forceClosed`的状态解析Path内部的每个线条, 即便这个线条在Path内部并没有执行'closed`操作而处于未闭合状态.

需要注意的是: PathMeasure的`forceClosed`参数和Path的`close`方法.

1. 两者之间可以说是相互独立的关系, 即`forceClosed`并不会影响`close`, 而`close`也不会影响`forceClosed`. 本质上PathMeasure的`forceClosed`参数会最终保存在本地库skia的`SkPath`对象的遍历器`SkPath::Iter`的`fForceClose`域中, 因此遍历`SkPath`时会以闭合的方式处理线条; 而Path的`close`方法最终实现是本地库skia的`SkPath`对象的方法`close`, 该方法只会闭合当前线条.

2. `forceClosed`参数和`close`方法的效用并不完全一致, 可以说`forceClosed`参数的效果等同于对Path对象内部的每一个线条终点处都调用`close`, 即`forceClosed`强制每一个线条都闭合, 而`close`只是闭合当前线条.

* isClosed()

Path的当前线条是否是闭合的.

线条是否闭合取决于: PathMeasure的`forceClosed`参数和Path的`close`方法.

* nextContour()

选择Path的下一个线条.

* getLength()

返回Path当前线条的长度.

* getPosTan(float, float[], float[])

返回Path当前线条上存在一定距离的坐标点和切线.

* getSegment(float, float, Path, boolean)

将Path当前线条上自一点到另外一定的线条复制成一个新的线条添加到新的Path中.

需要注意的是: KITKAT及早期版本上, 新Path有可能在硬件加速的Canvas上不能显示. 一个简单的变通方案是在新Path上施加一个操作, 例如`dst.rLineTo(0, 0)`.

* getMatrix(float, Matrix, int)

将Path当前线条上存在一定距离的坐标点和切线复制到Matrix对象中.

如果第三个参数是`POSITION_MATRIX_FLAG`, 则Matrix保存坐标点信息.
如果第三个参数是`TANGENT_MATRIX_FLAG`, 则Matrix保存切线信息.

### 参考

* https://github.com/google/skia/blob/master/include/core/SkPathMeasure.h
* https://github.com/google/skia/blob/master/src/core/SkPathMeasure.cpp

## 3.3 Region

#### 

* setEmpty()
* set(Region)
* set(Rect)
* set(int, int, int, int)
* setPath(Path, Region)

`setPath`的实现在skia的`SkRegion_path.cpp`源文件中, 而其他`setXXX`方法则是在`SkRegion.cpp`源文件中.

####

* isEmpty()     是否为空
* isRect()      是否是一个矩阵
* isComplex()   是否是复杂图形

一个Region对象可以分成三种类型: Empty, Rect和Complex.
 
如何理解`isComplex()`: 
由源代码可知, 可以看做是

```
 boolean isComplex() { return !isEmpty() && !isRect(); }
```

#### Bounds
 
* getBounds()
* getBounds(Rect)

#### Boundary

* getBoundaryPath()
* getBoundaryPath(Path)
  
返回Region的边界

####

* contains(int, int)

测试Region是否包含坐标点.

* quickContains(Rect)
* quickContains(int, int, int, int)

测试Region是否包含Rect.

1. 如果返回true, 则Region一定包含Rect.
2. 如果返回false, 则Region不一定不包含Rect, 即不能确定包含情况.

* quickReject(Rect)
* quickReject(int, int, int, int)

测试Region是否和Rect相交.

1. 如果返回true, 则Region一定和Rect不相交.
2. 如果返回false, 则Region不能确定相交情况.

* quickReject(Region)

测试Region是否和另一个Region相交.

1. 如果返回true, 则Region一定和Region不相交.
2. 如果返回false, 则Region不能确定相交情况.

**需要注意的是**:

1. 除了`contains`方法, 其他`quickXXX`方法都不是精确的. 因为`quickXXX`方法这里采取了一种简单的方法, 即通过当前Region的边框Rect对象, 来间接快速计算, 因此只能取得一定的测试结果.
2. 实际上在C++的SkRegion中实现了其他`contains`, 包括点, Rect, Region, 但是当前Java的Region类还并没有暴露出来.
3. 因此`quickContains`方法实际上没有意义, 只能验证当前Region对象是一个矩形的前提下是否包含一个Rect对象.

#### 

* translate(int, int)
* translate(int, int, Region)

平移

* scale(float)
* scale(float, Region)

缩放 
  
####

* union(Rect)
* op(Rect, Op)
* op(int, int, int, int, Op)
* op(Region, Op)
* op(Rect, Region, Op)
* op(Region, Region, Op)

Op.DIFFERENCE              F和S的差集
Op.INTERSECT               F和S的交集
Op.UNION                   F和S的并集
Op.XOR                     F和S的补集
Op.REVERSE_DIFFERENCE      S和F的差集
Op.REPLACE                 S

### 参考

* https://skia.org/user/api/skregion
* https://github.com/google/skia/blob/master/include/core/SkRegion.h
* https://github.com/google/skia/blob/master/src/core/SkRegion.cpp
* https://github.com/google/skia/blob/master/src/core/SkRegion_path.cpp

## 3.2 Matrix

`android.graphics.Matrix`类

Matrix是一个3*3的float型矩阵:

| `MSCALE_X` | `MSKEW_X`  | `MTRANS_X` |
| `MSKEW_Y`  | `MSCALE_Y` | `MTRANS_Y` |
| `MPERSP_0` | `MPERSP_1` | `MPERSP_2` |

初始时, 则:

| 1.0f | 0.0f | 0.0f |
| 0.0f | 1.0f | 0.0f |
| 0.0f | 0.0f | 1.0f |

当`setTranslate(dx, dy)`时, 则:

| 1.0f | 0.0f |  dx  |
| 0.0f | 1.0f |  dy  |
| 0.0f | 0.0f | 1.0f |

当`setScale(dx, dy)`时, 则:

|  sx  | 0.0f | 0.0f |
| 0.0f |  sy  | 0.0f |
| 0.0f | 0.0f | 1.0f |

当`setSkew(sx, sy)`时, 则:

| 1.0f |  sx  | 0.0f |
|  sy  | 1.0f | 0.0f |
| 0.0f | 0.0f | 1.0f |

当`setSinCos(sinV, cosV)`时, 则:

| cosV | -sinV | 0.0f |
| sinV | cosV  | 0.0f |
| 0.0f | 0.0f  | 1.0f |

而`setRotate(float)`时, 则实质上是调用`setSinCos(float, float)`, 也就是说旋转的效果其实是缩放效果和倾斜效果的叠加.

### 3.2.1 Basic

#### rotate

* setRotate(float)
* preRotate(float)
* postRotate(float)

* setRotate(float, float, float)
* preRotate(float, float, float)
* postRotate(float, float, float)

旋转

#### scale

* setScale(float, float)
* preScale(float, float)
* postScale(float, float)
* setScale(float, float, float, float)
* preScale(float, float, float, float)
* postScale(float, float, float, float)

缩放

#### translate

* setTranslate(float, float)
* preTranslate(float, float)
* postTranslate(float, float)

平移

#### skew

* setSkew(float, float)
* preSkew(float, float)
* postSkew(float, float)
* setSkew(float, float, float, float)
* preSkew(float, float, float, float)
* postSkew(float, float, float, float)

错切

####

* setSinCos(float, float)
* setSinCos(float, float, float, float)

####

* setConcat(Matrix, Matrix)
* preConcat(Matrix)
* postConcat(Matrix)

### 3.2.2 Map

* mapPoints(float[])
* mapPoints(float[], float[])
* mapPoints(float[], int, float[], int, int)

* mapVectors(float[])
* mapVectors(float[], float[])
* mapVectors(float[], int, float[], int, int)

* mapRect(RectF)
* mapRect(RectF, RectF)

* mapRadius(float)

### 3.2.3 其他

* set(Matrix)
* reset()
* isIdentity()
* isAffine()
* rectStaysRect()
* setValues(float[])
* getValues(float[])
* setRectToRect(RectF, RectF, ScaleToFit)
* setPolyToPoly(float[], int, float[], int, int)
* invert(Matrix)

### 3.2.4 参考

* https://github.com/google/skia/blob/master/include/core/SkMatrix.h
* https://github.com/google/skia/blob/master/src/core/SkMatrix.cpp
* http://developer.android.com/reference/android/graphics/Matrix.html
* http://blog.csdn.net/flash129/article/details/8234599

## 3.3 Bitmap

`android.graphics.Bitmap`类.

### 3.3.1 Basic

* setWidth(int)
* setHeight(int)
* getWidth()
* getHeight()
* setConfig(Config)
* reconfigure(int, int, Config)
* getDensity()
* setDensity(int)

* getScaledWidth(Canvas)
* getScaledHeight(Canvas)
* getScaledWidth(DisplayMetrics)
* getScaledHeight(DisplayMetrics)
* getScaledWidth(int)
* getScaledHeight(int)


### 3.3.2 Create

* createBitmap(Bitmap)
* createBitmap(Bitmap, int, int, int, int)
* createBitmap(Bitmap, int, int, int, int, Matrix, boolean)
* createBitmap(int width, int height, Config config)
* createBitmap(DisplayMetrics, int, int , Config) 
* createBitmap(int, int, Config, boolean)
* createBitmap(DisplayMetrics, int, int, Config, boolean)
* createBitmap(int[], int, int, int, int, Config)
* createBitmap(DisplayMetrics, int[], int, int, int, int, Config)
* createBitmap(int[], int, int, Config)
* createBitmap(DisplayMetrics, int[], int, int, Config)
* createScaledBitmap(Bitmap, int, int, boolean)

BitmapFactory类可以从不同的路径创建一个新的Bitmap对象:

* decodeFile(String)
* decodeFile(String, Option)
* decodeResourceStream(Resources, TypedValue, InputStream, Rect, Options)
* decodeResource(Resources, int)
* decodeResource(Resources, int, Options)
* decodeByteArray(byte[], int, int)
* decodeByteArray(byte[], int, int, Options)
* decodeStream(InputStream)
* decodeStream(InputStream, Rect, Options)
* decodeFileDescriptor(FileDescriptor) 
* decodeFileDescriptor(FileDescriptor, Rect, Options)

或许BitmapFactory类中的`decodeXXX`方法可以直接移动到Bitmap类中, 而不需要一个专门的BitmapFactory类.

此外, BitmapRegionDecoder类也可以创建一个新的Bitmap对象:

* decodeRegion(Rect, Options)

BitmapRegionDecoder类主要用于从一个很大的原始图像中截取部分图像.

### 3.3.3 Copy

* copy(Config, boolean)
* copyPixelsToBuffer(Buffer)
* copyPixelsFromBuffer(Buffer)

* extractAlpha()
* extractAlpha(Paint, int[])

### 3.3.4 Pixel

* getPixel(int, int)
* setPixel(int, int, int)
* getPixels(int[], int, int, int, int, int, int)
* setPixels(int[], int, int, int, int, int, int)

### 3.3.5 Compress

* compress(CompressFormat, int, OutputStream)

CompressFormat.JPEG
CompressFormat.PNG
CompressFormat.WEBP

### 3.3.5 其他

* isMutable()
* recycle()
* isRecycled()
* getGenerationId()
* getRowBytes()
* getByteCount()

* isPremultiplied()
* setPremultiplied(boolean)

* hasMipMap()
* setHasMipMap(boolean)

* eraseColor(int)

### 3.3.6 参考

* http://developer.android.com/reference/android/graphics/Bitmap.html
* https://github.com/google/skia/blob/master/include/core/SkBitmap.h
* https://github.com/google/skia/blob/master/src/core/SkBitmap.cpp

## 3.4 Paint

`android.graphics.Paint`类

###

#### Flag

* getFlags()
* setFlags(int)

ANTI_ALIAS_FLAG, FILTER_BITMAP_FLAG, DITHER_FLAG, UNDERLINE_TEXT_FLAG, STRIKE_THRU_TEXT_FLAG, FAKE_BOLD_TEXT_FLAG, LINEAR_TEXT_FLAG, SUBPIXEL_TEXT_FLAG, EMBEDDED_BITMAP_TEXT_FLAG, AUTO_HINTING_TEXT_FLAG, VERTICAL_TEXT_FLAG

抗锯齿效果

* isAntiAlias()
* setAntiAlias(boolean)

防抖动效果

* isDither()
* setDither(boolean)

* isFilterBitmap()
* setFilterBitmap(boolean)

#### Hinting

* getHinting()
* setHinting(int)

HINTING_OFF, HINTING_ON

#### Color

* getColor()
* setColor(int)
* setARGB(int, int, int, int)

Color.BLACK      黑色
Color.DKGRAY     灰黑色
Color.GRAY       灰色
Color.LTGRAY     浅灰色
Color.WHITE      白色
Color.RED        红色
Color.GREEN      绿色
Color.BLUE       蓝色
Color.YELLOW     黄色
Color.CYAN       青绿色
Color.MAGENTA	 红紫色
Color.TRANSPARENT透明

#### Alpha

* getAlpha()
* setAlpha(int)

#### Style

* getStyle()
* setStyle(Style)

Style.FILL
Style.STROKE
STYLE.FILL_AND_STROKE

#### ShadowLayer

* setShadowLayer(float, float, float, int)
* clearShadowLayer()
* hasShadowLayer()

图形下面设置阴影层，产生阴影效果

#### StrokeWidth

* getStrokeWidth()
* setStrokeWidth(float)

空心边框宽度

#### StrokeMiter

* getStrokeMiter()
* setStrokeMiter(float)

#### Cap

* getStrokeCap()
* setStrokeCap(Cap)

笔触风格

Cap.BUTT    两端头部不超出
Cap.ROUND   两端头部超出一个圆弧
Cap.SQUARE  两端头部超出一个方形

#### Join

* getStrokeJoin()
* setStrokeJoin(Join)

结合处的样式

JOIN.MITER  结合处为锐角
Join.ROUND  结合处为圆弧
Join.BEVEL  结合处为直线

### Text

#### Typeface

* getTypeface()
* setTypeface(Typeface)

文本字体

常用的字体类型:

* Typeface.DEFAULT        常规字体类型
* Typeface.DEFAULT_BOLD   黑体字体类型
* Typeface.MONOSPACE      等宽字体类型
* Typeface.SANS_SERIF     sans serif字体类型
* Typeface.SERIF          serif字体类型

常用的字体风格:

* Typeface.BOLD          粗体
* Typeface.BOLD_ITALIC   粗斜体
* Typeface.ITALIC        斜体
* Typeface.NORMAL        常规

#### TextAlign

* getTextAlign()
* setTextAlign(Align)

文本对齐

Align.LEFT, Align.CENTER, Align.RIGHT

#### TextLocale

* getTextLocale()
* setTextLocale(Locale)

#### TextSize

* getTextSize()
* setTextSize(float)

文本大小

#### FontMetrics

* getFontMetrics()
* getFontMetrics(FontMetrics)
* getFontMetricsInt()
* getFontMetricsInt(FontMetrics)
* getFontSpacing()
* ascent()
* descent()

#### TextScaleX

* getTextScaleX()
* setTextScaleX(float)

文本沿水平水平缩放

#### TextSkewX

* getTextSkewX()
* setTextSkewX(float)

文本沿水平方向倾斜

#### LetterSpacing

* getLetterSpacing()
* setLetterSpacing(float)

行间距

#### LinearText

* isLinearText()
* setLinearText(boolean)

线性文本

#### SubpixelText

* isSubpixelText()
* setSubpixelText(boolean)

亚像素文本, 有助于文本在LCD屏幕上的显示效果

#### UnderlineText

* isUnderlineText()
* setUnderlineText(boolean)

下划线

#### StrikeThruText

* isStrikeThruText()
* setStrikeThruText(boolean)

删除线

#### FakeBoldText

* isFakeBoldText()
* setFakeBoldText(boolean)

文本加粗

#### FontFeatureSettings

* getFontFeatureSettings()
* setFontFeatureSettings(String)

http://dev.w3.org/csswg/css-fonts/#propdef-font-feature-settings
blog.sqisland.com/2014/11/android-stacked-fractions.html

#### measureText

* measureText(char[], int, int)
* measureText(String, int, int)
* measureText(String)
* measureText(CharSequence, int, int)

* breakText(char[], int, int, float, float[])
* breakText(CharSequence, int, int, boolean measureForwards, float, float[]) 
* breakText(String, boolean, float, float[])

* getTextWidths(char[], int, int, float[])
* getTextWidths(CharSequence, int, int, float[])
* getTextWidths(String, int, int, float[])
* getTextWidths(String, float[])

文本宽度

#### TextBounds

* getTextBounds(String, int, int, Rect)
* getTextBounds(char[], int, int, Rect)

文本边界

**measureText和getTextBounds得到的宽度不一致**:
http://stackoverflow.com/questions/7549182/android-paint-measuretext-vs-gettextbounds

#### TextPath

* getTextPath(char[], int, int, float, float, Path)
* getTextPath(String, int, int, float, float, Path)

###

#### ColorFilter

* getColorFilter()
* setColorFilter(ColorFilter)

颜色滤镜

其子类:

ColorMatrixColorFilter
LightingColorFilter
PorterDuffColorFilter

#### MaskFilter

* getMaskFilter()
* setMaskFilter(MaskFilter)

边缘滤镜

其子类:

BlurMaskFilter    模糊遮罩滤镜
EmbossMaskFilter  浮雕遮罩滤镜

而
BlurMaskFilter.Blur.NORMAL 从边缘向内部和外部模糊
BlurMaskFilter.Blur.OUTER  边缘内部没有效果，从边缘向外部模糊, 将图像变透明
BlurMaskFilter.Blur.SOLID  边缘内部加粗, 从边缘向外部模糊
BlurMaskFilter.Blur.INNER  边缘外部没有效果, 从边缘向内部模糊

#### PathEffect

* getPathEffect()
* setPathEffect(PathEffect)

路径效果

其子类:

CornerPathEffect       夹角平滑化
DiscretePathEffect     离散化
DashPathEffect         虚线化
PathDashPathEffect     特定形状虚线化
ComposePathEffect      组合
SumPathEffect          叠加

#### Shader

* getShader()
* setShader(Shader)

渲染器

其模式:

Shader.TileMode.CLAMP
Shader.TileMode.REPEAT     横向和纵向重复渲染平铺
Shader.TileMode.MIRROR     横向和纵向镜像渲染平铺

其直接子类:

BitmapShader        图像渲染器
LinearGradient      线性渲染器
RadialGradient      辐射渲染器
SweepGradient       扫描渲染器
ComposeShader       混合渲染器

#### Xfermode

* getXfermode()
* setXfermode(Xfermode)

其直接子类:

AvoidXfermode (已弃用)
PixelXorXfermode (已弃用)
PorterDuffXfermode

图形重叠时的处理方式:

PorterDuff.Mode.CLEAR)        清除模式, 两图都不显示 [0, 0]
PorterDuff.Mode.SRC)          仅显示源图      [Sa, Sc]
PorterDuff.Mode.DST)          仅显示目标图      [Da, Dc]
PorterDuff.Mode.SRC_OVER)     都显示, 源图在上 [Sa+(1-Sa)*Da, Rc=Sc+(1-Sa)*Dc]
PorterDuff.Mode.DST_OVER)     都显示, 目标图在上 [Sa+(1-Sa)*Da, Rc=Dc+(1-Da)*Sc]
PorterDuff.Mode.SRC_IN)       仅显示交集部分, 源图在上 [Sa*Da, Sc*Da]
PorterDuff.Mode.DST_IN)       仅显示交集部分, 目标图在上 [Sa*Da, Sa*Dc]
PorterDuff.Mode.SRC_OUT)      仅显示源图非交集部分 [Sa*(1-Da), Sc*(1-Da)] 
PorterDuff.Mode.DST_OUT)      仅显示目标图非交集部分 [Da*(1-Sa), Dc*(1-Sa)]
PorterDuff.Mode.SRC_ATOP)     仅显示源图非交集部分和目标图交集部分 [Da, Sc*Da+(1-Sa)*Dc]
PorterDuff.Mode.DST_ATOP)     仅显示目标图非交集部分和源图交集部分 [Sa, Sa*Dc+Sc*(1-Da)]
PorterDuff.Mode.XOR)          仅显示源图非交集部分和目标图非交集部分 [Sa+Da-2*Sa*Da, Sc*(1-Da)+(1-Sa)*Dc]
PorterDuff.Mode.DARKEN)       都显示, 交集部分颜色加深 [Sa+Da-Sa*Da, Sc*(1-Da)+Dc*(1-Sa)+min(Sc, Dc)]
PorterDuff.Mode.LIGHTEN)      都显示, 交集部分颜色加亮 [Sa+Da-Sa*Da, Sc*(1-Da)+Dc*(1-Sa)+max(Sc, Dc)]
PorterDuff.Mode.MULTIPLY)     仅显示交集部分, 源图和目标图相乘 [Sa * Da, Sc * Dc]
PorterDuff.Mode.SCREEN)       都显示,  [Sa+Da-Sa*Da, Sc+Dc-Sc*Dc]
PorterDuff.Mode.ADD)
PorterDuff.Mode.OVERLAY)

其中: Sa(Source alpha), 即源图Alpha通道; Sc(Source color), 即源图颜色; Da(Destination alpha), 即目标图Alpha通道; Dc(Destination color), 即目标图颜色; [X, Y], X即处理得到的新图Alpha通道, Y即处理道德的新图颜色.

### 参考

* http://developer.android.com/reference/android/graphics/Paint.html
* https://github.com/google/skia/blob/master/include/core/SkPaint.h
* https://github.com/google/skia/blob/master/src/core/SkPaint.cpp
* https://skia.org/user/api/skpaint
* http://blog.csdn.net/column/details/androidcustomview.html

## 3.1 Canvas

`android.graphics.Canvas`类.


### 3.1.1 Basic

* isHardwareAccelerated()
* isOpaque()
* getWidth()
* getHeight()
* getDensity()
* setDensity(int)
* getMaximumBitmapWidth()
* getMaximumBitmapHeight()

#### 

* setBitmap(Bitmap)

### 3.1.2 Transformation

#### scale

* scale(float, float)
* scale(float, float, float, float)

#### rotate

* rotate(float)
* rotate(float, float, float)

#### skew

* skew(float, float)

#### concat

* concat(Matrix) 

#### translate

* translate(float, float)

### 3.1.3 Anvanced

#### clip

* clipPath(Path)
* clipPath(Path, Region.Op)
* clipRect(Rect, Region.Op)
* clipRect(RectF, Region.Op)
* clipRect(int, int, int, int)
* clipRect(float, float, float, float)
* clipRect(RectF)
* clipRect(float, float, float, float, Region.Op)
* clipRect(Rect)
* clipRegion(Region)              已弃用
* clipRegion(Region, Region.Op)   已弃用
* getClipBounds(Rect)
* getClipBounds()

#### save

* save()
* save(int)
* saveLayer(RectF, Paint, int)
* saveLayer(RectF, Paint)
* saveLayer(float, float, float, float, Paint, int)
* saveLayer(float, float, float, float, Paint)
* saveLayerAlpha(RectF, int, int)
* saveLayerAlpha(RectF, int)
* saveLayerAlpha(float, float, float, float, int, int)
* saveLayerAlpha(float, float, float, float, int)
* getSaveCount()

`save(int)`参数的值, 保存当前的matrix和clip在一个私有栈中.

* MATRIX_SAVE_FLAG
* CLIP_SAVE_FLAG
* HAS_ALPHA_LAYER_SAVE_FLAG
* FULL_COLOR_LAYER_SAVE_FLAG
* CLIP_TO_LAYER_SAVE_FLAG
* ALL_SAVE_FLAG

`save()`实质是save(MATRIX_SAVE_FLAG|CLIP_SAVE_FLAG)

如何理解`saveLayer`方法:

1. 网上说法是: 
>Canvas 在一般的情况下可以看作是一张画布，所有的绘图操作如drawBitmap, drawCircle都发生在这张画布上，这张画板还定义了一些属性比如Matrix，颜色等等。
>但是如果需要实现一些相对复杂的绘图操作，比如多层动画，地图（地图可以有多个地图层叠加而成，比如：政区层，道路层，兴趣点层）。
>Canvas提供了图层（Layer）支持，缺省情况可以看作是只有一个图层Layer。如果需要按层次来绘图，Android的Canvas可以使用SaveLayerXXX, Restore 来创建一些中间层，对于这些Layer是按照“栈结构“来管理的：
2. 而Canvas API中也说明了`saveLayer`和`save`相同, 而`saveLayer`则会额外分配了离屏位图(offscreen bitmap).
3. 但是这个方法到底有什么作用? 
网上所举出的例子并不能指出`saveLayer`的意义, 利用`save`同样可以完成例子, 相反`saveLayer`会存在额外创建离屏位图的消耗.
4. 目前还没有完全理解`saveLayer`的优越性.

#### restore

* restore()
* restoreToCount(int)

## 3.1.4 Draw

#### 填充

* drawRGB(int, int, int) 
* drawARGB(int, int, int, int)
* drawColor(int)
* drawColor(int, PorterDuff.Mode)
* drawPaint(Paint)

#### 点

* drawPoint(float, float, Paint paint)
* drawPoints(float[], int, int, Paint) 
* drawPoints(float[], Paint)

#### 弧形

* drawLine(float, float, float, float, Paint)
* drawLines(float[], int, int, Paint)
* drawLines(float[] pts, Paint)

#### 矩形

* drawRect(RectF, Paint)
* drawRect(Rect r, Paint)
* drawRect(float, float, float, float, Paint)

#### 椭圆

* drawOval(RectF, Paint)
* drawOval(float, float, float, float, Paint)

#### 圆

* drawCircle(float, float, float, Paint)

#### 圆弧

* drawArc(RectF, float, float, boolean, Paint)
* drawArc(float, float, float, float, float, float, boolean, Paint)

#### 圆角矩形

* drawRoundRect(RectF, float, float, Paint)
* drawRoundRect(float, float, float, float, float, float, Paint)

#### 路径

* drawPath(Path, Paint)

#### Bitmap

* drawBitmap(Bitmap, float, float, Paint)
* drawBitmap(Bitmap, Rect, RectF, Paint)
* drawBitmap(Bitmap, Rect, Rect, Paint)
* drawBitmap(Bitmap, Matrix, Paint)
* drawBitmapMesh(Bitmap, int, int, float[], int, int[], int, Paint)

#### Vertices

* drawVertices(VertexMode, int, float[], int, float[], int, int[],
int, short[], int, int, Paint)

http://blog.csdn.net/mapdigit/article/details/7792166

#### 文本

* drawText(char[], int, int, float, float, Paint)
* drawText(String, float, float, Paint)
* drawText(String, int, int, float, float, Paint)
* drawText(CharSequence, int, int, float, float, Paint)
* drawTextOnPath(char[], int, int, Path, float, float, Paint)
* drawTextOnPath(String, Path, float, float, Paint)
* drawTextRun(char[], int, int, int, int, float, float, boolean, Paint)           系统隐藏
* drawTextRun(CharSequence, int, int, int, int, float, float, boolean, Paint)     系统隐藏

#### 9-Patch

* drawPatch(NinePatch, Rect, Paint)       系统隐藏
* drawPatch(NinePatch, RectF, Paint)      系统隐藏

#### 图片

* drawPicture(Picture)
* drawPicture(Picture, RectF)
* drawPicture(Picture, Rect)

按照`android.graphics.Picture`类的介绍:

1. Picture类会记录绘制调用(drawing call). 然后之后真正的绘制时刻, 启动这些预先保存的绘制调用.
2. 通常情况下, 相比相同的API调用, 通过Picture类来绘制图像会更快. 这是因为Picture类不会产生任何方法调用(method-call)消耗. 

### 3.1.5 参考

* https://github.com/google/skia/blob/master/include/core/SkCanvas.h
* https://github.com/google/skia/blob/master/src/core/SkCanvas.cpp
* https://skia.org/user/api/skcanvas
* http://developer.android.com/reference/android/graphics/Canvas.html
* https://github.com/android/platform_frameworks_base/blob/master/core/jni/android_graphics_Canvas.cpp

## 参考

* https://github.com/android/platform_frameworks_base/tree/master/core/jni/android/graphics
* https://github.com/android/platform_frameworks_base/tree/master/core/jni
* https://github.com/google/skia/
* https://skia.org/

## 3.6 Drawable

`android.graphics.drawable.Drawable`抽象类

其直接子类以及对应的XML标签:

* BitmapDrawable                   <bitmap />
* ColorDrawable                    <color />
* GradientDrawable                 <shape />
* LayerDrawable                    <layer-list />
* NinePatchDrawable                <nine-patch />
* DrawerArrowDrawable              android.support.v7
* RoundedBitmapDrawable            android.support.v4
* PictureDrawable
* ShapeDrawable
* DrawableContainer                
* VectorDrawable                   API >= 21
* AnimatedVectorDrawable           API >= 21

**需要注意的是**: 

1. `<shape />`标签对应的是GradientDrawable类, 而不是ShapeDrawable类.
2. GradientDrawable类的低版本实现可能有问题, 在Android 4.4.4上不能显示.
3. RotateDrawable类的类似, 在Android 4.4.4上不能显示.
4. RotateDrawable类虽然在Android L上能够实现, 但是部分函数存在[bug](https://code.google.com/p/android/issues/detail?id=186620&q=RotateDrawable&colspec=ID%20Status%20Priority%20Owner%20Summary%20Stars%20Reporter%20Opened), 应该只有在Android M上才能完全正常显示.


其非直接子类以及相应的XML标签:

* AnimationDrawable                <animation-list />
* ClipDrawable                     <clip />
* InsetDrawable                    <insert />
* LevelListDrawable                <level-list />
* RotateDrawable                   <rotate />
* ScaleDrawable                    <scale />
* StateListDrawable                <select />
* TransitionDrawable               <transition />
* PaintDrawable
* RippleDrawable                   API >= 21
* AnimatedStateListDrawable

### draw

* draw(Canvas)      抽象方法

### Drawable.Callback

```
public static interface Callback {
        public void invalidateDrawable(Drawable who);

        public void scheduleDrawable(Drawable who, Runnable what, long when);

        public void unscheduleDrawable(Drawable who, Runnable what);
    }
```

* setCallback(Callback)
* getCallback()

View类可以通过`getBackground()`和`setBackground(Drawable)`方法设置背景, 但是Drawable类内部并没有保存View类的实例, 因此Drawable类本身难以调用View类的任何方法. 不过, Drawable声明了Callback接口, 而View则实现了该接口, 从而Drawable类能够通过Callback接口的三个方法反作用于View本身.

* invalidateSelf()
* scheduleSelf(Runnable, long)
* unscheduleSelf(Runnable)

如果Drawable类的子类希望调用Callback接口的`invalidateDrawable(Drawable)`方法, 则可以调用Drawable类的`invalidateSelf()`方法;

如果Drawable类的子类希望调用Callback接口的`scheduleDrawable(Drawable, Runnable, long)`方法, 则可以调用Drawable类的`scheduleSelf(Runnable, long)`方法;

如果Drawable类的子类希望调用Callback接口的`unscheduleDrawable(Drawable, Runnable)`方法, 则可以调用Drawable类的`unscheduleSelf(Runnable)`方法.

实际上View和Drawable之间可以不采用间接Callback的方式, 而是采用直接invalidate来强制刷新. 这里这么做的原因应该是追求性能, 即由Drawable类本身根据不同的情况来确定是否需要重绘, 从未避免不必要的刷新工作, 例如有的Drawable子类一直不会改变, 而有的Drawable子类会响应不同的输入事件反馈.

### create

* createFromStream(InputStream, String)
* createFromResourceStream(Resources, TypedValue, InputStream, String srcName)
* createFromResourceStream(Resources, TypedValue, InputStream, String, BitmapFactory.Options opts)
* createFromXml(Resources, XmlPullParser)
* createFromXml(Resources, XmlPullParser, Theme)
* createFromXmlInner(Resources, XmlPullParser, AttributeSet)
* createFromXmlInner(Resources, XmlPullParser, AttributeSet, Theme)
* createFromPath(String)

以上是静态方法, 实际上这些静态方法最终还是会调用以下的类方法.

* inflate(Resources, XmlPullParser, AttributeSet)
* inflate(Resources, XmlPullParser, AttributeSet, Theme)

#### Bounds

* setBounds(int left, int top, int right, int bottom)
* setBounds(Rect bounds)
* copyBounds(Rect bounds)
* copyBounds()
* getBounds()
* getDirtyBounds()
* getTransparentRegion()
* onBoundsChange(Rect)

####

* setChangingConfigurations(int)
* getChangingConfigurations()

####

* setDither(boolean)
* setFilterBitmap(boolean)

#### Alpha

* setAlpha(int)   抽象方法
* getAlpha()

虽然`getAlpha()`方法不是抽象方法, 只是简单的返回默认值0xFF, 但是子类应该覆写返回合适的值.

#### ColorFilter

* setColorFilter(ColorFilter)
* getColorFilter()
* clearColorFilter()

#### Tint

* setTint(int)
* setTintList(ColorStateList)
* setTintMode(PorterDuff.Mode)
* updateTintFilter(PorterDuffColorFilter, ColorStateList, PorterDuff.Mode)
* parseTintMode(int, Mode) 系统隐藏

如果支持着色特性, 子类应该覆写`setTintList`和`setTintMode`方法. 而`setTint`方法默认实现仍然是调用`setTintList`, 因此子类通常不需要覆写.

#### State

* isStateful() 
* setState(int[])
* getState()
* jumpToCurrentState()
* getCurrent()
* onStateChange(int[])

如果支持State特性, 子类应该覆写.

`isStateful()`应该返回`true`, 表明当前Drawable子类支持State特性.

此外, 即便当前Drawable子类支出State特性, 仍然需要确定View是否支持State特性.
即是否会传递当前状态信息给Drawable子类. 而这关键在于View的"enabled", "focusable", "clickable"和"longClickable"四个变量. 
View默认都设置`false`; 而View子类会根据设计默认设置其他值. 
因此默认情况下, 不同的View控件可能支持State的程度也不一样.
但是, 可以通过编程设定或XML文件设定强制当前的View控件支持State特性.

#### Level

* setLevel(int)
* getLevel()
* onLevelChange(int)

如果支持Level特性, 子类应该覆写

#### Visiable

* setVisiable(boolean, boolean)
* isVisibled()

#### AutoMirrored

* setAutoMirrored(boolean)
* isAutoMirrored()

如果支持自动镜像特性, 子类应该覆写

#### Theme

* applyTheme(Theme)
* canApplayTheme()

如果支持主题特性, 子类应该覆写

####

* getIntrinsicWidth()
* getIntrinsicHeight()

* getMinimumWidth()
* getMinimumHeight()

View使用wrap_content时, 通过`getMinimumWidth()`和`getMinimumHeight()`提供最小尺寸, 而`getMinimumWidth`则会调用`getIntrinsicWidth()`, 默认返回-1, `getMinimumHeight()`则调用`getIntrinsicHeight()`, 默认返回-1.

通常子类需要覆写`getIntrinsicWidth()`和`getIntrinsicHeight()`方法.

#### Hotspot

* setHotspot(float, float)
* setHotspotBounds(int, int, int, int)
* getHotspotBounds(Rect)     系统隐藏

####

* getPadding(Rect)

####

* getOutline(Outline)

#### 

* getOpacity()        抽象方法
* resolveOpacity(int, int)

通常`getOpacity()`返回值应该是:

* PixelFormat.UNKNOWN
* PixelFormat.TRANSLUCENT
* PixelFormat.TRANSPARENT
* PixelFormat.OPAQUE

####

* mutate()

#### ConstantState

* getConstantState()

```
public static abstract class ConstantState {

        public abstract Drawable newDrawable();

        public Drawable newDrawable(Resources res);

        public Drawable newDrawable(Resources res, Theme theme);

        public abstract int getChangingConfigurations();

        public Bitmap getBitmap();

        public boolean canApplyTheme();
    }
```

### View Background

通常Drawable对象是作为View类的背景.

以下是与Drawable相关的View类的方法:

* setBackgroundColor(int)
* setBackgroundResource(int)
* setBackground(Drawable)
* getBackground()

* getHotspotBounds(Rect)
* drawableHotspotChanged(float, float)

* getSuggestedMinimumHeight()
* getSuggestedMinimumWidth()

* setBackgroundTintList(ColorStateList)
* getBackgroundTintList()
* setBackgroundTintMode(PorterDuff.Mode)
* getBackgroundTintMode()

* drawBackground(Canvas)

* refreshDrawableState()
* onCreateDrawableState(int)
* verifyDrawable(Drawable)
* drawableStateChanged()
* getDrawableState()
* mergeDrawableStates(int[], int[])
* jumpDrawablesToCurrentState()

* resolveDrawables()
* onResolveDrawables(int)
* resetResolvedDrawables()
* isDrawablesResolved()

每次View的状态发送改变, 则会调用`refreshDrawableState`方法来调用内部`mBackground`成员变量的`setState`方法, 而方法的参数则是调用`getDrawableState`方法来获得.

* invalidateDrawable(Drawable)
* scheduleDrawable(Drawable, Runnable, long)
* unscheduleDrawable(Drawable, Runnable)
* unscheduleDrawable(Drawable)

* setOutlineProvider(ViewOutlineProvider)
* getOutlineProvider()
* invalidateOutline()
* rebuildOutline()

此外, 虽然Drawable不一定必须设置成View类的背景对象, 也可以独立使用在其他场景;
但是由于Drawable的设计目标应该就是应用于View类的背景, 因此脱离View而应用在其他场景就会存在一定局限性.

### 参考

* http://developer.android.com/reference/android/graphics/drawable/Drawable.html
* http://developer.android.com/guide/topics/resources/drawable-resource.html

# 4 Animation

## 4.1 AnimationDrawable

也称"Frame Animation".

`android.graphics.drawable.AnimationDrawable`

**原理**

1. View类的background变量实质是Drawable的实例. 通过`getBackground()`和`setBackground(Drawable)`可以访问和设置View类的背景. View类的`draw(Canvas)`方法内部则会调用`drawBackground(Canvas)`方法来绘制背景, 而`drawBackground(Canvas)`方法实质上则仍然是调用Drawable类的`draw(Canvas)`方法来启动绘制工作.

2. 通常情况下背景会被理解成静态不动的图像, 但是通过AnimationDrawable类可以发现View的背景其实可以是动态改变的. 首先AnimationDrawable类的父类是DrawableContainer类, 即可以保存多个Drawable实例, 从而支持View设置不同的背景. 而AnimationDrawable类作为其子类, 则是会动态地在不同的时刻, 通过DrawableContainer类的`selectDrawable(int)`方法, 选择不同的背景作为绘制时刻的绘制对象. 而DrawableContainer类的另外一个子类StateListDrawable类, 则是支持基于View的不同状态而设置不同的背景.

## 4.2 Animation

补间动画, 也称"Tween Animation".

android.view.animation.Animation

**原理**

虽然类的名称是"Animation", 但是实际上Animation类本身并没有做任何的绘画工作! 即使存在一些类似的方法, 如`start`, 但是该方法的实现并没有真正地启动绘画工作.
通过源代码可以发现, 这里的Animation机制其实主要基于View类和ViewGroup类的主动和Animation类的被动:

1. View的`draw(Canvas, ViewGroup, long)`方法内部调用`drawAnimation(ViewGroup, long, Animation, boolean)`, 但是`drawAnimation`方法内部仍然并没有启动动画工作, 而仅仅调用了Animation类的`getTransformation(long, Transformation, float)`方法. 这时候, Animation抽象类的子类, 会基于不同的绘画机制, 将接下来将要进行的绘画命令保存在`getTransformation`的第二个参数中. 而Transformation类则包存了Matrix和Alpha两个变量.

2. 真正的绘制工作实际上的确是在View的`draw(Canvas, ViewGroup, long)`方法内部中, 但是是在`drawAnimation(ViewGroup, long, Animation, boolean)`方法的之后, 分别是基于Matrix的绘制:

```
if (transformToApply != null) {
    if (concatMatrix) {
        if (usingRenderNodeProperties) {
            renderNode.setAnimationMatrix(transformToApply.getMatrix());
        } else {
            // Undo the scroll translation, apply the transformation matrix,
            // then redo the scroll translate to get the correct result.
            canvas.translate(-transX, -transY);
            canvas.concat(transformToApply.getMatrix());
            canvas.translate(transX, transY);
        }
        parent.mGroupFlags |= ViewGroup.FLAG_CLEAR_TRANSFORMATION;
    }
```

和基于Alpha的绘制:

```
        if (usingRenderNodeProperties) {
            renderNode.setAlpha(alpha * getAlpha() * getTransitionAlpha());
        } else  if (layerType == LAYER_TYPE_NONE) {
            final int scrollX = hasDisplayList ? 0 : sx;
            final int scrollY = hasDisplayList ? 0 : sy;
            canvas.saveLayerAlpha(scrollX, scrollY,
    scrollX + (mRight - mLeft), scrollY + (mBottom - mTop),
    multipliedAlpha, layerFlags);
        }
```

3. 而View的`draw(Canvas, ViewGroup, long)`方法实际上是被ViewGroup类的`drawChild(Canvas, View, long) `方法所调用. 因此也可以说Animation类的`getTransformation(long, Transformation, float)`方法是被ViewGroup类的`drawChild(Canvas, View, long) `方法所调用.

4. View本身存在Matrix和Alpha变量, 但是如果View存在Animation变量, 那么Animation变量内部的Matrix和Alpha变量优先级会高, 因此View就会优先绘制Animation.

**限制**

1. 

2. 


### 4.2.1 Duration

#### 域

#### 方法

* setDuration(long)
* getDuration()
* scaleCurrentDuration(float)
* restrictDuration(long)

#### XML属性

* android:duration

### 4.2.2 Start

#### 域

#### 方法

* setStartTime(long)
* setStartOffset(long)
* getStartTime()

#### XML属性

* android:startOffset

### 4.2.3 Repeat

#### 方法

* setRepeatCount(int)
* setRepeatMode(int)
* getRepeatCount()
* getRepeatMode()

#### XML属性

* android:repeatCount
* android:repeatMode

### 4.2.4 Fill

#### 域

setFillAfter方法的作用是设置一个动画效果执行完毕后，View对象是否保留在终止的位置

#### 方法

* setFillEnabled(boolean)
* setFillBefore(boolean)
* setFillAfter(boolean)
* getFillEnabled()
* getFillBefore()
* getFillAfter()

#### XML属性

* android:fillEnabled
* android:fillBefore
* android:fillAfter

### 4.2.5 Interpolator

#### 域

#### 方法

* setInterpolator(Interpolator)
* setInterpolator(Context, int)
* getInterpolator()
* ensureInterpolator()

#### XML属性

* android:interpolator
* android:fillAfter
* android:startOffset
* android:duration″
* android:repeatCount

### 4.2.6 start

* start() 
* startNow() 
* hasStarted()
* initialize(int, int, int, int)
* isInitialized()
* cancel()
* detach()
* hasEnded()
* reset() 

### 4.2.7 Transformation

* getTransformation(long, Transformation)
* getTransformation(long, Transformation, float)
* applyTransformation(float, Transformation)

### 4.2.8 Listener

```
 public static interface AnimationListener {
        void onAnimationStart(Animation animation);

        void onAnimationEnd(Animation animation);

        void onAnimationRepeat(Animation animation);
    }
```

### 4.2.9 其他

* setBackgroundColor(int)
* getBackgroundColor()
* setZAdjustment(int)
* getZAdjustment()
* setDetachWallpaper(boolean)
* getDetachWallpaper()
* willChangeTransformationMatrix()
* willChangeBounds()

Animation其直接子类:

* AlphaAnimation
* RotateAnimation
* ScaleAnimation
* TranslateAnimation 
* AnimationSet

### 4.2.10 AlphaAnimation

渐变透明度动画效果

#### 方法

* applyTransformation(float, Transformation)
* willChangeTransformationMatrix()
* willChangeBounds()

#### XML属性

* android:fromAlpha
* android:toAlpha

### 4.2.11 RotateAnimation

渐变位置移动动画效果

#### 方法

* applyTransformation(float, Transformation)

#### XML属性

* android:fromDegrees
* android:toDegrees
* android:pivotX
* android:pivotY”

### 4.2.12 ScaleAnimation

渐变尺寸伸缩动画效果

#### 方法

* applyTransformation(float, Transformation)

#### XML属性

* android:toXScale
* android:fromYScale
* android:toYScale
* android:pivotX
* android:pivotY

### 4.2.13 TranslateAnimation

渐变旋转动画效果

#### 方法

* applyTransformation(float, Transformation)

#### XML属性

* android:fromXDelta
* android:toXDelta
* android:fromYDelta
* android:toYDelta

### 4.2.14 AnimationSet

透明度动画效果

#### 方法

* getAnimations()
* addAnimation(Animation)

#### XML属性

### 参考

* http://developer.android.com/guide/topics/graphics/view-animation.html#tween-animation
* http://developer.android.com/guide/topics/resources/animation-resource.html#View


## AnimationUtils

`android.view.animation.AnimationUtils`类

## Animator

### ValueAnimator

属性动画, 也称"Property Animation".

`android.animation.ValueAnimator`类, 其父类是`android.animation.Animator`抽象类

**原理**

定义一个动画的基本特征, 如动画的开始事件, 持续时间, 重复次数等, 也定义了动画的控制流程, 如动画的开始, 暂停, 停止等.

1. 首先, 动画生命周期存在多个时刻, 由系统控制, 启动以下计算过程;
2. 在某个时刻, 通过动画的基本特征, 如持续时间等, 以及当前时刻的时间, 计算出时间因子AnimatedFraction;
3. 然后, 通过TimeInterpolator, 计算出因子Fraction;
4. 然后, 通过TypeEvaluator和PropertyValuesHolder, 计算出当前的Value;
5. 最后, 启动监听器AnimatorUpdateListener, 用户可以通过ValueAnimator类的`getAnimatedValue`方法获取计算出的当前时刻对应的Value值, 然后基于这个Value值, 动态改变视图的相关属性, 同时刷新视图.

#### Duration

* setDuration(long)
* getDuration()

#### StartDelay

* setStartDelay(long)
* getStartDelay()

#### PlayTime

* setCurrentPlayTime(long)
* getCurrentPlayTime()

#### RepeatCount

* setRepeatCount(int)
* getRepeatCount()

参数是:

ValueAnimator.INFINITE
0
数字

默认值是0, 即不重复.

#### RepeatMode

* setRepeatMode(int)
* getRepeatMode()

参数是:

ValueAnimator.RESTART
ValueAnimator.REVERSE

#### TimeInterpolator

* setInterpolator(TimeInterpolator)
* getInterpolator()

####

* start()
* cancel()
* end()
* resume()
* pause()
* reverse()
* isRunning()
* isStarted()
* isPaused()

#### Values

* setIntValues(int...)
* setFloatValues(float...)
* setObjectValues(Object...)
* setValues(PropertyValuesHolder...)
* getValues()

实质上`setIntValues`, `setFloatValues`和`setObjectValues`方法最终还是调用`setValues`方法.

#### Listeners

* addUpdateListener(AnimatorUpdateListener)
* removeAllUpdateListeners()
* removeUpdateListener(AnimatorUpdateListener)

* addListener(AnimatorListener)
* removeListener(AnimatorListener)
* getListeners()

```
   public static interface AnimatorListener {
        void onAnimationStart(Animator animation);
        void onAnimationEnd(Animator animation);
        void onAnimationCancel(Animator animation);
        void onAnimationRepeat(Animator animation);
    }
```

* addPauseListener(AnimatorPauseListener)
* removePauseListener(AnimatorPauseListener)
* removeAllListeners()

```
    public static interface AnimatorPauseListener {
        void onAnimationPause(Animator animation);
        void onAnimationResume(Animator animation);
    }
```

####

* setupStartValues()
* setupEndValues()
* setTarget(Object)

继承自父类, 但是没有实现.

#### FrameDelay

* setFrameDelay(long)
* getFrameDelay()

####

* getAnimatedFraction()
* getAnimatedValue()
* getAnimatedValue(String)

#### TypeEvaluator

* setEvaluator(TypeEvaluator)

`android.animation.TypeEvaluator`接口

```
public interface TypeEvaluator<T> {
    public T evaluate(float fraction, T startValue, T endValue);
}
```

其直接子类包括:

* ArgbEvaluator
* FloatArrayEvaluator
* FloatEvaluator
* IntArrayEvaluator
* IntEvaluator
* PointFEvaluator
* RectEvaluator

### ObjectAnimator

`android.animation.ObjectAnimator`类, 是ValueAnimator的子类.

**原理**

其基本原理和ValueAnimator一致, 但是简化了第5步中的用户操作.
原来用户需要利用监听器和当前Value值手动改变视图的属性值, 
而ObjectAnimator中用户只需要设置好视图中的属性名称即可.

这是因为ObjectAnimator利用了反射机制, 动态调用了属性名称所对应的方法, 同时自动将当前Value值作为参数. 因此这样也对View类或其它类本身有了更高的要求, 即类需要有setter和getter方法, 而且方法参数类型和返回值类型需要和Value相一致; 否则反射会失败.

示例代码:

```
	ObjectAnimator animation = ObjectAnimator.ofFloat(view, "rotation", 0f, 360f);
	animation.setInterpolator(new AccelerateDecelerateInterpolator());
	animation.setDuration(1000);
	animation.start();
```

* setTarget(Object)

虽然ObjectAnimator类的父类ValueAnimator类没有实现该方法, 但是ObjectAnimator类却实现了, 而且使用ObjectAnimator类时, 必须调用这个方法. 因为这里使用了反射方法, 因此必须设置是哪个类实例. 当然使用静态方法时, 如`ObjectAnimator.ofFloat`, 可以省略, 因为实质上已内部调用.

### AnimatorSet

`android.animation.ObjectAnimator`类, 是Animator的子类.

示例代码:

```
	ObjectAnimator animation1 = ObjectAnimator.ofFloat(target, "rotation", 0f, 360f);
	ObjectAnimator animation2 =  ObjectAnimator.ofFloat(target, "scaleX", 1f, 0.9f);
	ObjectAnimator animation3 =  ObjectAnimator.ofFloat(target, "scaleY", 1f, 0.9f);

	AnimatorSet animationSet = new AnimatorSet ();
	animationSet.play(animation1).after(animation2).with(animation3);
	animationSet.setDuration(1000);
	animationSet.start();
```

### ViewPropertyAnimator

`android.view.ViewPropertyAnimator`类, 并不是Animator的子类, 但是是View类的专有帮助类, 利用ValueAnimator类方便使用View的Animator化.

例如

如果要同时实现View的属性`x`和`y`的动画效果,

1. 可以这样:

```
ObjectAnimator animX = ObjectAnimator.ofFloat(myView, "x", 50f);
ObjectAnimator animY = ObjectAnimator.ofFloat(myView, "y", 100f);
AnimatorSet animSetXY = new AnimatorSet();
animSetXY.playTogether(animX, animY);
animSetXY.start();
```

2. 也可以这样:

```
PropertyValuesHolder pvhX = PropertyValuesHolder.ofFloat("x", 50f);
PropertyValuesHolder pvhY = PropertyValuesHolder.ofFloat("y", 100f);
ObjectAnimator.ofPropertyValuesHolder(myView, pvhX, pvyY).start();
```

3. 但是使用ViewPropertyAnimator类, 可以进一步简化成这样:

```
	myView.animate().x(50f).y(100f).start();
```

### AnimatorInflater

`android.animation.AnimatorInflater`类

从资源目录`res`的`anim`目录下的XML文件, 返回Animator类的实例.

静态方法:

* loadAnimator(Context, int)
* loadAnimator(Resources, Theme, int)    系统隐藏
* loadAnimator(Resources, Theme, int, float)    系统隐藏

* loadStateListAnimator(Context, int)

`loadStateListAnimator`方法返回的是`android.animation.StateListAnimator`类的实例, 但是StateListAnimator没有父类, 并不是Animator的子类.


|  XML标签            |  Java类          |
|  <animator />       |  ValueAnimator   |
|  <set/>             |  AnimatorSet     |
|  <objectAnimator /> |  ObjectAnimator  |


###

```
<animator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:valueFrom="1"
    android:valueTo="0"
    android:valueType="floatType"
    android:repeatCount="1"
    android:repeatMode="reverse"/>
```

Java代码:

```
	ValueAnimator animator = (ValueAnimator)AnimatorInflater.loadAnimator(this, R.animator.bigger);
	animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
		
		@Override
		public void onAnimationUpdate(ValueAnimator animation) {
			// TODO Auto-generated method stub
			float scale = (float)animation.getAnimatedValue();
			target.setScaleX(scale);
			target.setScaleY(scale);
			target.invalidate();
		}
	});
	animator.start();
```

###

```
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:valueTo="200"
    android:valueType="floatType"
    android:propertyName="y"
    android:repeatCount="1"
    android:repeatMode="reverse"/>
```

Java代码

```
	ObjectAnimator objectAnimator = (ObjectAnimator) AnimatorInflater.loadAnimator(myContext, R.anim.property_animator);
	objectAnimator.setTarget(myObject);
	objectAnimator.start();
```

###

```
<set android:ordering="sequentially">
    <set>
        <objectAnimator
            android:propertyName="x"
            android:duration="500"
            android:valueTo="400"
            android:valueType="intType"/>
        <objectAnimator
            android:propertyName="y"
            android:duration="500"
            android:valueTo="300"
            android:valueType="intType"/>
    </set>
    <objectAnimator
        android:propertyName="alpha"
        android:duration="500"
        android:valueTo="1f"/>
</set>
```

Java代码:

```
	AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext, R.anim.property_animator);
	set.setTarget(myObject);
	set.start();
```

## Interpolator

`android.view.animation.Interpolator`接口或者`android.animation.TimeInterpolator`接口定义了动画了中的插值器.

参阅http://my.oschina.net/banxi/blog/135633

| **Interpolator** | **效果** |
|------------------|------------|
| AccelerateDecelerateInterpolator | 开始和接收增长缓慢, 但是中间增长迅速             |
| AccelerateInterpolator           | 开始增长缓慢, 但是渐渐加速                       |
| AnticipateInterpolator           | 开始先反向回退, 然后再正向增长                   |
| AnticipateOvershootInterpolator  | 开始先反向回退, 然后开始增长, 但是会增长过头, 最后再次回退到最终值 |
| BounceInterpolator               | 阶段性增长, 开始增长迅速, 后面增长缓慢           |
| BakedBezierInterpolator          |                                                |
| CycleInterpolator                | 正弦周期性增长                                  |
| DecelerateInterpolator           | 开始增长迅速, 后面增长缓慢                       |
| LinearInterpolator               | 线性均匀增长                                    |
| OvershootInterpolator            | 开始增长, 但是会增长过头, 最后再次回退到最终值    |

## 参考

* http://developer.android.com/guide/topics/graphics/drawable-animation.html
* http://developer.android.com/guide/topics/graphics/view-animation.html
* http://developer.android.com/guide/topics/graphics/prop-animation.html

# 5 XML

## 5.1 Resource

`android.content.res.Resources`类.

* getText(int)
* getText(int, CharSequence)
* getString(int)
* getString(int, Object ...)

* getTextArray(int)
* getStringArray(int)
* getIntArray(int)
* obtainTypedArray(int)

* getQuantityText(int, int)
* getQuantityString(int, int, Object ...)
* getQuantityString(int, int)

* getDimension(int)
* getDimensionPixelOffset(int)
* getDimensionPixelSize(int)

* getFraction(int, int, int)

* getDrawable(int)
* getDrawable(int. Theme)
* getDrawableForDensity(int, int)
* getDrawableForDensity(int, int, Theme)

* getMovie(int)

* getColor(int)
* getColorStateList(int)

* getBoolean(int)

* getLayout(int)

* getAnimation(int)

* getXML(int)

* openRawResource(int)
* openRawResource(int, TypedValue)
* openRawResourceFd(int)

* getValue(int, TypedValue, boolean)
* getValueForDensity(int, int, TypedValue, boolean)
* getValue(String, TypedValue, boolean)

* newTheme()

* obtainAttributes(AttributeSet, int[])

* updateConfiguration(Configuration, DisplayMetrics)
* getDisplayMetrics()
* getConfiguration()

* getIdentifier(String, String, String)
* getResourceName(int)
* getResourcePackageName(int)
* getResourceTypeName(int)
* getResourceEntryName(int)

* parseBundleExtras(XmlResourceParser, Bundle)
* parseBundleExtra(String, AttributeSet, Bundle)

* getAssets()

* flushLayoutCache()
* finishPreloading()


### res

`res`目录是Android项目的资源文件目录, 存储了原始资源文件. 当项目编译时, Android工具也会编译这里的原始资源文件得到二进制资源文件. 因此不会在R.java(或R.class)文件中自动生成ID值.

|   R.anim         |   res/anim/                |   补间动画资源      | getAnimation   |
|   R.animator     |   res/animator             |   属性动画资源      |
|   R.layout       |   res/layout/              |   布局资源          |   getLayout   |
|   R.drawable     |   res/drawable/            |   图像和帧动画资源   |   getDrawable   |
|   R.raw          |   res/raw/                 |   原始资源          |   openRawResource, getMovie   |
|   R.xml          |   res/xml/                 |   XML资源           |   getXML   |
|   R.menu         |   res/menu/                |   菜单资源          |
|   R.transition   |   res/transition           |   transition资源    |
|   R.mipmap       |   res/mipmap/              |   mipmap资源        |
|   R.interpolator |   res/interpolator         |   interpolator资源  |
|   R.fraction     |
|   R.color        |   res/color/               |   StateList资源     |   getColorStateList   |
|   R.color        |   res/values/colors.xml    |   颜色资源   |   getColor   |
|   R.array        |   res/value/arrays.xml     |   数组资源   |   getIntArray, getStringArray, obtainTypedArray |
|   R.id           |   res/value/ids.xml        |
|   R.integer      |   res/values/intergers.xml |   int资源    |   getInteger          |
|   R.bool         |   res/values/bools.xml     |   bool资源   |   getBoolean          |
|   R.dimen        |   res/values/dimens.xml    |   尺寸资源   |   getDimension        |
|   R.string       |   res/values/strings.xml   |   字符串资源 |   getString getText   | 
|   R.style        |   res/values/styles.xml    |   样式资源   |
|   R.attr         |   res/values/attrs.xml     |   属性资源   |
|   R.styleable    |   res/values/attrs.xml     |   属性资源   |
|   R.plurals      |   res/values/plurals.xml   |   plurals资源|   getQuantityString   |

#### R.attr示例

1. 定义属性

res/values/attrs.xml:

```
<resources>
  <attr name="customText" format="string"/>
</resources>
```

2. 属性赋值

res/layout/example.xml

```
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res/com.example">
   <com.example.CustomTextView
        android:id="@+id/example"
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:customText="Android Text"
        app:text="Custom Text"/>
</LinearLayout>
```

3. Java代码中

```
public class CustomTextView extends TextView {
	
  private String getAppText(Context ctx, AttributeSet attrs) {
    String namespace = "http://schemas.android.com/apk/res/com.example";
    String attr = "customText";
    String apptext = attrs.getAttributeValue(namespace, attr);
    return apptext;
  }
}
```

或者是:

```
public class CustomTextView extends TextView {
	
  private String getAppText(Context context, AttributeSet attrs) {
    int attrsIdArray[] = {R.attr.customText};
    int textOffset = 0
    TypedArray t = context.obtainStyledAttributes(attrs,attrsIdArray);
    String apptext = t.getString(textOffset);
    t.recycle();
    return apptext;
  }
}
```

#### R.styleable示例

1. 定义属性

res/values/attrs.xml:

```
<resources>
  <declare-styleable name="CustomTextView">
    <attr name="customText" format="string"/>
  </declare-styleable>
</resources>
```

2. 属性赋值

res/layout/example.xml

```
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res/com.example">
   <com.example.CustomTextView
        android:id="@+id/example"
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:text="Android Text"
        app:customText="Custom Text"/>
</LinearLayout>
```

3. Java代码中

```
public class CustomTextView extends TextView {
	
  private String getAppText(Context context, AttributeSet attrs) {
     TypedArray t = context.obtainStyledAttributes(attrs, R.styleable.MyTextView);
     String apptext = t.getString(R.styleable.MyTextView_customText);
     t.recycle();
     return apptext;
  }
}
```

#### R.style示例

1. 定义属性

res/values/attrs.xml:

```
<resources>
  <declare-styleable name="CustomTextView">
    <attr name="customText" format="string"/>
  </declare-styleable>
</resources>
```

2. 属性赋值

2.1 定义默认属性值

res/values/styles.xml

```
<style name="Hello">
  <item name="app:customText">Hello World</item>
</style>
```

2.2 使用默认属性值

res/layout/example.xml

```
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res/com.example">
   <com.example.CustomTextView
        style="@style/Hello"
        android:id="@+id/example"
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:text="Android Text"/>
</LinearLayout>
```

需要指出的是: 这里`@style/style_name`里的`style_name`可以是任何名称, 但是必须与`style`标签内的`name`所指定的值相一致即可.

3. Java代码中

```
public class CustomTextView extends TextView {
	
  private String getAppText(Context context, AttributeSet attrs) {
     TypedArray t = context.obtainStyledAttributes(attrs,R.styleable.MyTextView);
     String apptext = t.getString(R.styleable.MyTextView_customText);
     return apptext;
  }
}
```

#### Theme

这里额Theme本质上仍然是style, 即一组属性值, 但是不同之处在于: 传统的style是面向View的, 而Theme是面向Activity或Application的. 因此如果采用了Theme, 则Activity或Application的每一个View都会具有Theme中定义的属性值. 此外, 应用Theme时, 在AndroidManifest.xml文件的`<activity>`或`<application>`元素内设置`android:theme`属性.

1. 定义属性

res/values/attrs.xml:

```
<resources>
  <declare-styleable name="CustomTextView">
    <attr name="customText" format="string"/>
  </declare-styleable>

  <attr name="customTextViewStyle" format="reference"/>

</resources>
```

2. 属性赋值

2.1 定义默认属性值

res/values/styles.xml

```
<resources>
  <style name="Widget.CustomTextView1" parent="@android:style/Widget.TextView">
    <item name="customText">Hello world CustomTextView 1 !</item>
  </style>

  <style name="Widget.CustomTextView2" parent="@android:style/Widget.TextView">
    <item name="customText">Hello world CustomTextView 2 !</item>
  </style>
</resources>
```

2.2 注册主题

res/values/styles.xml

```
<resources>
  <style name="AppTheme1" parent="AppBaseTheme">
    <item name="customTextViewStyle">@style/Widget.CustomTextView1</item>
  </style>
    
  <style name="AppTheme2" parent="AppBaseTheme">
    <item name="customTextViewStyle">@style/Widget.CustomTextView2</item>
  </style>
</resources>
```

因为已经主题里面注册了默认属性值, 因此不需要在布局文件中使用`@style/name`.

res/layout/example.xml

```
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res/com.example">

   <com.example.CustomTextView
        android:id="@+id/example"
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:text="Android Text"/>
</LinearLayout>
```

3. 设置主题

AndroidManifest.xml

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.linlinjava.androidtutorials"
    android:versionCode="1"
    android:versionName="1.0" >

    <application android:theme="@style/AppTheme1" >
      <activity  android:name=".CustomActivity" android:theme="@style/AppTheme1"/>
    </application>
</manifest>
```

当然这里的主题也可以使用代码设置.

Application类存在`setTheme`方法, 而Activity类也存在`setTheme`方法.
需要注意的是`setTheme`方法必须先在Activity的`setContentView`方法调用.

此外, 这里的主题也可以使用代码动态设置.

实例代码:

```
public class CustomActivity extends Activity {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		
		int theme = getIntent().getIntExtra("theme", 0);
		if (theme != 0) {
			setTheme(theme);
			// or "getApplication().setTheme(theme);" for whole application			
		};

		Button button1 = (Button)findViewById(R.id.button1);
		button1.setOnClickListener(new View.OnClickListener() {
			
			@Override
			public void onClick(View v) {
				Activity activity = CustomActivity.this;
				activity.finish();
				Intent intent = new Intent(activity, activity.getClass());
				intent.putExtra("theme", R.style.AppTheme1);
				activity.startActivity(intent);
			}
		});
	}
}

```

####

1. Android系统资源文件存储路径: <ANDROID_SDK_HOME>/platforms/<android-sdk>/data/res. 例如`values/attrs.xml`定义了`Theme`属性集合, 而在`values/themes.xml`中则定义了相应的`Theme`的默认属性值集合. 而`values/attrs.xml`定义了`AlertDialog`属性集合, 而在`values/styles.xml`中则定义了相应的`AlertDialog`的默认属性值集合.
2. 引用资源时使用`@`还是`?`: 设置style时可以使用`@`或`?`, 使用`@`则表示使用在其他地方已预定义的值; 而使用`?`则表示使用当前内部定义的值.

如下例所示:

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
 <style name="CustomTheme">
    <item name="android:windowNoTitle">true</item>
    <item name="windowFrame">@drawable/screen_frame</item>
    <item name="windowBackground">@drawable/screen_background_white</item>
    <item name="panelForegroundColor">#FF000000</item>
    <item name="panelBackgroundColor">#FFFFFFFF</item>
    <item name="panelTextColor">?panelForegroundColor</item>
    <item name="panelTextSize">14</item>
    <item name="menuItemTextColor">?panelTextColor</item>
    <item name="menuItemTextSize">?panelTextSize</item>
 </style>
</resources>
```

### asset

`assets`目录, 也存储了Android项目的资源文件, 但是这些资源文件不会被编译, 而仅仅是简单地打包到最终的apk二进制安装包中. 此外, 当apk安装以后, `assets`文件夹并不会自动解压到设备中, 因此通常采用`AssetManager`类来读取文件.

而与`res/raw`不同的是, `assets`支持任意深度的子目录.

## 5.1 XmlPullParser

Android中解析xml文件, 可以采用三种解析方式: SAX, Pull和Dom.
由于Android UI部分主要基于XML Pull的资源配置方案, 因此这里只关注Pull方式.

`org.xmlpull.v1.XmlPullParser`接口

```
public interface XmlPullParser {

    String NO_NAMESPACE = "";

    int START_DOCUMENT = 0;

    int END_DOCUMENT = 1;

    int START_TAG = 2;

    int END_TAG = 3;

    int TEXT = 4;

    int CDSECT = 5;

    int ENTITY_REF = 6;

    int IGNORABLE_WHITESPACE = 7;

    int PROCESSING_INSTRUCTION = 8;

    int COMMENT = 9;

    int DOCDECL = 10;

    String [] TYPES = {
            "START_DOCUMENT",
            "END_DOCUMENT",
            "START_TAG",
            "END_TAG",
            "TEXT",
            "CDSECT",
            "ENTITY_REF",
            "IGNORABLE_WHITESPACE",
            "PROCESSING_INSTRUCTION",
            "COMMENT",
            "DOCDECL"
    };

    String FEATURE_PROCESS_NAMESPACES = "http://xmlpull.org/v1/doc/features.html#process-namespaces";

    String FEATURE_REPORT_NAMESPACE_ATTRIBUTES = "http://xmlpull.org/v1/doc/features.html#report-namespace-prefixes";

    String FEATURE_PROCESS_DOCDECL = "http://xmlpull.org/v1/doc/features.html#process-docdecl";

    String FEATURE_VALIDATION = "http://xmlpull.org/v1/doc/features.html#validation";

    void setFeature(String name, boolean State) throws XmlPullParserException;

    boolean getFeature(String name);

    void setProperty(String name, Object value) throws XmlPullParserException;

    Object getProperty(String name);

    void setInput(Reader in) throws XmlPullParserException;

    void setInput(InputStream inputStream, String inputEncoding) throws XmlPullParserException;

    String getInputEncoding();

    void defineEntityReplacementText( String entityName, String replacementText ) throws XmlPullParserException;

    int getNamespaceCount(int depth) throws XmlPullParserException;

    String getNamespacePrefix(int pos) throws XmlPullParserException;

    String getNamespaceUri(int pos) throws XmlPullParserException;

    String getNamespace (String prefix);

    int getDepth();

    String getPositionDescription ();

    int getLineNumber();

    int getColumnNumber();

    boolean isWhitespace() throws XmlPullParserException;

    String getText ();

    char[] getTextCharacters(int [] holderForStartAndLength);

    String getNamespace ();

    String getName();

    String getPrefix();

    boolean isEmptyElementTag() throws XmlPullParserException;

    int getAttributeCount();

    String getAttributeNamespace (int index);

    String getAttributeName (int index);

    String getAttributePrefix(int index);

    String getAttributeType(int index);

    boolean isAttributeDefault(int index);

    String getAttributeValue(int index);

    String getAttributeValue(String namespace, String name);

    int getEventType() throws XmlPullParserException;

    int next() throws XmlPullParserException, IOException;

    int nextToken() throws XmlPullParserException, IOException;

    void require(int type, String namespace, String name) throws XmlPullParserException, IOException;

    String nextText() throws XmlPullParserException, IOException;

    int nextTag() throws XmlPullParserException, IOException;
}
```

简单的示例:

```
import org.xmlpull.v1.XmlPullParser;
import org.xmlpull.v1.XmlPullParserException
import org.xmlpull.v1.XmlPullParserFactory

public class SimpleXmlPullApp
{

     public static void main (String args[])
         throws XmlPullParserException, IOException
     {
         XmlPullParserFactory factory = XmlPullParserFactory.newInstance();
         factory.setNamespaceAware(true);
         XmlPullParser xpp = factory.newPullParser();

         xpp.setInput(new StringReader( "<foo>Hello World!</foo>" ) );
         int eventType = xpp.getEventType();
         while (eventType != XmlPullParser.END_DOCUMENT) {
          if(eventType == XmlPullParser.START_DOCUMENT) {
              System.out.println("Start document");
          } else if(eventType == XmlPullParser.START_TAG) {
              System.out.println("Start tag "+xpp.getName());
          } else if(eventType == XmlPullParser.END_TAG) {
              System.out.println("End tag "+xpp.getName());
          } else if(eventType == XmlPullParser.TEXT) {
              System.out.println("Text "+xpp.getText());
          }
          eventType = xpp.next();
         }
         System.out.println("End document");
     }
 }
```

## 5.2 AttributeSet

`android.util.AttributeSet`接口

```
public interface AttributeSet {
    public int getAttributeCount();

    public String getAttributeName(int index);

    public String getAttributeValue(int index);

    public String getAttributeValue(String namespace, String name);

    public String getPositionDescription();

    public int getAttributeNameResource(int index);

    public int getAttributeListValue(String namespace, String attribute, String[] options, int defaultValue);

    public boolean getAttributeBooleanValue(String namespace, String attribute, boolean defaultValue);

    public int getAttributeResourceValue(String namespace, String attribute, int defaultValue);

    public int getAttributeIntValue(String namespace, String attribute, int defaultValue);

    public int getAttributeUnsignedIntValue(String namespace, String attribute, int defaultValue);

    public float getAttributeFloatValue(String namespace, String attribute, float defaultValue);

    public int getAttributeListValue(int index, String[] options, int defaultValue);

    public boolean getAttributeBooleanValue(int index, boolean defaultValue);

    public int getAttributeResourceValue(int index, int defaultValue);

    public int getAttributeIntValue(int index, int defaultValue);

    public int getAttributeUnsignedIntValue(int index, int defaultValue);

    public float getAttributeFloatValue(int index, float defaultValue);

    public String getIdAttribute();

    public String getClassAttribute();

    public int getIdAttributeResourceValue(int defaultValue);

    public int getStyleAttribute();
}

```

## 5.3 XmlResourceParser

`android.content.res.XmlResourceParser`接口

```
public interface XmlResourceParser extends XmlPullParser, AttributeSet, AutoCloseable {
    public void close();
}
```

虽然前面SimpleXmlPullApp示例代码中采用了XmlPullParserFactory类获取了XmlPullParser接口实现类KXmlParser类的对象:

```
XmlPullParserFactory factory = XmlPullParserFactory.newInstance();
XmlPullParser xpp = factory.newPullParser();
```

但是`res`目录下的资源文件在最终实际设备中已经不是原始的xml文件, 而是编译得到的二进制文件. 因此这里如果仍然采用XmlPullParserFactory来获取XmlPullParser接口的实现对象其实是不正确的.

实际上`res`目录下的资源文件解析接口是XmlResourceParser, 而XmlResourceParser接口的最终实现是`android.content.res.XmlBlock`类的内部类`android.content.res.XmlBlock.Parser`.

在Resources类中存在一个方法`loadXmlResourceParser(int id, String type)`, 会基于资源id和资源类型, 返回一个XmlResourceParser接口的实现类`android.content.res.XmlBlock.Parser`的对象.

当然如果把一个普通的xml文件放在`assets`目录下, 那么应该通过XmlPullParserFactory类取XmlPullParser接口的实现类对象, 然后解析普通xml文件.

## 5.3 Theme

`android.content.res.Resources`内部类`android.content.res.Resources.Theme`类.

* applyStyle(int, boolean)
* setTo(Theme)

* obtainStyledAttributes(int[])
* obtainStyledAttributes(int, int[])
* obtainStyledAttributes(AttributeSet, int[], int, int)
* resolveAttributes(int[], int[])       系统隐藏
* resolveAttribute(int, TypedValue, boolean)
* getAllAttributes()  系统隐藏
* getResources()
* getDrawable(int)
* getTheme()     系统隐藏


## 5.5 inflate

* inflate(Resources, XmlPullParser, AttributeSet)
* inflate(Resources, XmlPullParser, AttributeSet, Theme)

通常一个类如果支持XML赋值, 那么默认都会实现以上的inflate方法(虽然没有规定必须命名成inflate). 

通过前面论述可知, 虽然这里存在XmlPullParser接口和AttributeSet接口, 但是实际传入的变量都是XmlResourceParser接口的实现类对象.

1. Drawable类的inflate方法, 代码示例:

```
public abstract class Drawable {

   public static Drawable createFromXml(Resources r, XmlPullParser parser, Theme theme)
            throws XmlPullParserException, IOException {
        AttributeSet attrs = Xml.asAttributeSet(parser);

        int type;
        while ((type=parser.next()) != XmlPullParser.START_TAG &&
                type != XmlPullParser.END_DOCUMENT) {
            // Empty loop
        }

        if (type != XmlPullParser.START_TAG) {
            throw new XmlPullParserException("No start tag found");
        }

        Drawable drawable = createFromXmlInner(r, parser, attrs, theme);

        if (drawable == null) {
            throw new RuntimeException("Unknown initial tag: " + parser.getName());
        }

        return drawable;
    }

    public static Drawable createFromXmlInner(Resources r, XmlPullParser parser, AttributeSet attrs,
            Theme theme) throws XmlPullParserException, IOException {
      final Drawable drawable;

        final String name = parser.getName();
        if (name.equals("selector")) {
            drawable = new StateListDrawable();
        } else if (name.equals("animated-selector")) {
            drawable = new AnimatedStateListDrawable();
        } else if (name.equals("level-list")) {
            drawable = new LevelListDrawable();
        } else if (name.equals("layer-list")) {
            drawable = new LayerDrawable();
        } else if (name.equals("transition")) {
            drawable = new TransitionDrawable();
        } else if (name.equals("ripple")) {
            drawable = new RippleDrawable();
        } else if (name.equals("color")) {
            drawable = new ColorDrawable();
        } else if (name.equals("shape")) {
            drawable = new GradientDrawable();
        } else if (name.equals("vector")) {
            drawable = new VectorDrawable();
        } else if (name.equals("animated-vector")) {
            drawable = new AnimatedVectorDrawable();
        } else if (name.equals("scale")) {
            drawable = new ScaleDrawable();
        } else if (name.equals("clip")) {
            drawable = new ClipDrawable();
        } else if (name.equals("rotate")) {
            drawable = new RotateDrawable();
        } else if (name.equals("animated-rotate")) {
            drawable = new AnimatedRotateDrawable();
        } else if (name.equals("animation-list")) {
            drawable = new AnimationDrawable();
        } else if (name.equals("inset")) {
            drawable = new InsetDrawable();
        } else if (name.equals("bitmap")) {
            //noinspection deprecation
            drawable = new BitmapDrawable(r);
            if (r != null) {
               ((BitmapDrawable) drawable).setTargetDensity(r.getDisplayMetrics());
            }
        } else if (name.equals("nine-patch")) {
            drawable = new NinePatchDrawable();
            if (r != null) {
                ((NinePatchDrawable) drawable).setTargetDensity(r.getDisplayMetrics());
             }
        } else {
            throw new XmlPullParserException(parser.getPositionDescription() +
                    ": invalid drawable tag " + name);
        }

        drawable.inflate(r, parser, attrs, theme);
        return drawable;
    }

  public void inflate(Resources r, XmlPullParser parser, AttributeSet attrs, Theme theme) throws XmlPullParserException, IOException {
        final TypedArray a;
        if (theme != null) {
            a = theme.obtainStyledAttributes(
                    attrs, com.android.internal.R.styleable.Drawable, 0, 0);
        } else {
            a = r.obtainAttributes(attrs, com.android.internal.R.styleable.Drawable);
        }

        mVisible = attrs.getBoolean(com.android.internal.R.styleable.Drawable_visible, mVisible);

        a.recycle();
    }
}
```

通过源代码可知, Drawable类虽然存在`createFromXml`和`createFromXmlInner`静态方法, 但是因为所支持的XML文件的特定标签(如`selector`)和Drawable类是在代码中写死的, 所以实际上这些静态方法只能够支持创建系统Drawable类的对象, 不能创建自定义Drawable类的对象. 此外由于Resources的`getDrawable`方法也是基于这些静态方法的, 因此`getDrawable`方法也不能创建自定义Drawable类的XML对象.

2. 而View类也存在inflate方法, 代码示例:

```
  public static View inflate(Context context, int resource, ViewGroup root) {
        LayoutInflater factory = LayoutInflater.from(context);
        return factory.inflate(resource, root);
    }
```

当然这里的inflate方法是基于`android.view.LayoutInflater`抽象类.

与Drawable类的做法不同, View类的`inflate`方法或者Resources类的`getLayout`方法所读取的XML布局文件中, 每一个标签都是类名, 然后在代码中通过放射的方法实例化类对象, 因此能够支持自定义类的XML对象. 当然这里仍然要求XML标签中的每一个自定义类都必须是View的子类.

因此基于这个思路, 为了支持自定义Drawable类的XML对象, 一种可行的方案是: 在`res/xml`目录中存放Drawable类的XML文件, XML文件中的标签必须是自定义Drawable类的类名(或者采用标签是`drawable`, 同时采用`class`属性记录类名), 从而可以基于反射的方式来实例化XML对象. 但是即便这种方案可行, 仍然会受限于Android本身的框架. 例如, 由于只能放置于`res/xml`, 因此在很多XML文件中使用`@drawable/name`的地方不能使用, 只能通过代码设置属性. 因此, 另一方面, 这也许说明了, Android框架应该只是希望开发者自定义View来构建Android UI.

## 参考

* http://www.xmlpull.org
* http://developer.android.com/guide/topics/resources/index.html
* http://developer.android.com/guide/topics/resources/more-resources.html
* http://developer.android.com/guide/topics/ui/themes.html

# 6 事件分发

## Input 模型

1. `com.android.server.SystemServer`类实例化`com.android.server.input.InputManagerService`类, 继而初始化Native层的输入系统, 包括EventHub, InputReader和InputDispatcher. 因此Java层InputManagerService开始接收硬件设备底层的输入事件. 同时, SystemServer类也会实例化`com.android.server.wm.WindowManagerService`类. 

2. `android.view.ViewRootImpl`类的`setView`方法中, 会调用WindowSession类实例的`addToDisplay`方法. 而`addToDisplay`方法则是从当前应用进程调用了SystemServer进程的WindowManagerService类实例的`addWindow`方法, 并调用了openInputChannelPair创建了一对InputChannel实例, 实际上就是管道. 在SystemServer进程中一个InputChannel实例调用InputManagerService类实例的`registerInputChannel`方法, 因此一旦InputManagerService类接收到输入事件则写入事件数据, 而另外一个InputChannel实例则会读取并重定向第三个InputChannel, 而当前应用进程中所创建的一个InputChannel实例即为第三个InputChannel, 因此当前应用进程就可以读取到事件数据, 并交付给`enqueueInputEvent`方法处理.

3. ViewRootImpl类处理事件数据分成多个阶段:
NativePreImeInputStage   ----   本地代码
ViewPreImeInputStage     ----   View的`dispatchKeyEventPreIme`
ImeInputStage            ----   InputMethodManager的`dispatchInputEvent`
EarlyPostImeInputStage   ----   FallbackEventHandler的`preDispatchKeyEvent`
NativePostImeInputStage  ----   本地代码
ViewPostImeInputStage    ----   View的`dispatchKeyEvent`, `dispatchKeyShortcutEvent`, `dispatchUnhandledMove`, `dispatchPointerEvent`, `dispatchTrackballEvent`, `dispatchGenericMotionEvent`
SyntheticInputStage      ----   内部类SyntheticTrackballHandler, SyntheticJoystickHandler, SyntheticTouchNavigationHandler, SyntheticKeyboardHandler

这里的View指的是PhoneWindow类的内部类DecorView, 而DecorView是所有View的根容器.

4. DecorView类只有`dispatchUnhandledMove`, `dispatchPointerEvent`则是执行传统的父类View实现, 而`dispatchKeyEvent`, `dispatchKeyShortcutEvent`, , `dispatchTrackballEvent`, `dispatchGenericMotionEvent`则不执行传统的ViewGroup实现, 这些方法会执行Activity类的`dispatchKeyEvent`, `dispatchKeyShortcutEvent`, `dispatchTrackballEvent`, `dispatchGenericMotionEvent`. 但是DecorView类的
`dispatchPointerEvent`实现最终还是调用了DecorView类的`dispatchTouchEvent`, 因此也是执行了Activity类的`dispatchTouchEvent`方法.

5. Activity类处理事件时, 可能会传递给PhoneWindow类, 执行`superDispatchKeyEvent`, `superDispatchKeyShortcutEvent`, `superDispatchTouchEvent`, `superDispatchTrackballEvent`, `superDispatchGenericMotionEvent`.

6. PhoneWindow类的处理时, 会再次传递给DecorView类, 执行`superDispatchKeyEvent`, `superDispatchKeyShortcutEvent`, `superDispatchTouchEvent`, `superDispatchTrackballEvent`, `superDispatchGenericMotionEvent`方法.

7. DecorView类则是调用父类ViewGroup类的实现.

6. ViewGroup类处理事件时, 可能会传递给父类View, 也有可能传递给合适的View孩子成员, 也有可能屏蔽事件.

7. View类处理事件.

个人感觉DecorView类这里的逻辑比较混乱, 感觉应该是Input框架设计者的失误, 特别是PointerEvent和MotionEvent之间的混乱. 但是基本的逻辑总结以下:
(1) View的`dispatchPointerEvent`方法不用管, 而且是系统隐藏的, 估计说不定会被删除. 
(2) 底层的事件信息来到应用的Activity会分流成五种:KeyEvent, KeyShortcutEvent, TrackballEvent, MotionEvent和GenericMotionEvent, 分别对应了五种`dispatchXXX`方法. 
(3) DecorView虽然是ViewGroup和View的子类, 但是DecorView接收到事件以后, 会传递给Activity类处理.
(4) Activity处理以后, 可能交付给ViewGroup处理, 再可能交付给View处理.

* https://source.android.com/devices/input/overview.html
* https://github.com/android/platform_frameworks_base/blob/d59921149bb5948ffbcb9a9e832e9ac1538e05a0/services/core/jni/com_android_server_input_InputManagerService.cpp
* https://android.googlesource.com/platform/frameworks/native/+/master
* http://www.cnblogs.com/samchen2009/p/3368158.htm

## Motion

### MotionEvent

`android.view.MotionEvent`类.

####

* setAction(int)
* getAction()
* getActionMasked()
* getActionIndex()

`getAction`会返回触摸的动作和触控点.
而`getActionMasked`返回触摸动作, `getActionIndex`返回触摸触控点.

`getActionMasked`返回值, 也就是触控动作主要存在以下类型:

* ACTION_DOWN
* ACTION_UP
* ACTION_MOVE
* ACTION_CANCEL
* ACTION_OUTSIDE
* ACTION_POINTER_DOWN
* ACTION_POINTER_UP

* ACTION_HOVER_MOVE
* ACTION_SCROLL
* ACTION_HOVER_ENTER
* ACTION_HOVER_EXIT

其中前7项出现在`onTouchEvent(MotionEvent)`. 而后4项出现在`onGenericMotionEvent(MotionEvent)`中.

* isTouchEvent()

当触控动作是`ACTION_HOVER_MOVE`, `ACTION_SCROLL`, `ACTION_HOVER_ENTER`和`ACTION_HOVER_EXIT`时, 则返回false, 否则返回true. 也就是说虽然都是MotionEvent, 但是其实存在两类, 一类是TouchEvent, 另一类是GenericMotionEvent.

* getPointerId(int)
* findPointerIndex()
* getPointerCount()

####

* getX()
* getY()
* getPressure()
* getSize()
* getTouchMajor()
* getTouchMinor()
* getToolMajor()
* getToolMinor()
* getOrientation()
* getAxisValue(int)

返回第一个触控点的X, Y, Pressure, Size, TouchMajor, TouchMinor, ToolMajor, ToolMinor, Orientation和 AxisValue值.

* getX(int)
* getY(int)
* getPressure(int)
* getSize(int)
* getTouchMajor(int)
* getTouchMinor(int)
* getToolMajor(int)
* getToolMinor(int)
* getOrientation(int)
* getAxisValue(int, int)
* getPointerCoords(int, PointerCoords) 
* getPointerProperties(int, PointerProperties)

返回给定索引触控点的X, Y, Pressure, Size, TouchMajor, TouchMinor, ToolMajor, ToolMinor, Orientation, AxisValue, PointerCoords和PointerProperties值.

#### History

* getHistorySize()

* getHistoricalEventTime(int)
* getHistoricalEventTimeNano(pos)
* getHistoricalX(int)
* getHistoricalY(int)
* getHistoricalPressure(int)
* getHistoricalSize(int)
* getHistoricalTouchMajor(int)
* getHistoricalTouchMinor(int)
* getHistoricalToolMajor(int)
* getHistoricalToolMinor(int)
* getHistoricalOrientation(int)
* getHistoricalAxisValue(int, int)

* getHistoricalX(int, int)
* getHistoricalY(int, int)
* getHistoricalPressure(int, int)
* getHistoricalSize(int, int)
* getHistoricalTouchMajor(int)
* getHistoricalTouchMinor(int)
* getHistoricalToolMajor(int)
* getHistoricalToolMinor(int)
* getHistoricalOrientation(int)
* getHistoricalAxisValue(int, int)
* getPointerCoords(int, PointerCoords) 

####

* getRawX()
* getRawY()

`getX`是相当于父View左上角X轴上的位置, 而`getRawX`是相对于屏幕左上角的X轴上的位置.
`getY`是相当于父View左上角Y轴上的位置, 而`getRawY`是相对于屏幕左上角的Y轴上的位置.

* getXPrecision()
* getYPrecision()

####

* offsetLocation(float, float)
* setLocation(float, float)
* transform(Matrix)

####

* addBatch(long, float, float, float, float, int)
* addBatch(long, PointerCoords[], int)
* addBatch(MotionEvent)      系统隐藏

####

* getEdgeFlags()
* setEdgeFlags(int)

返回值或参数是:

EDGE_LEFT
EDGE_TOP
EDGE_RIGHT
EDGE_BOTTOM

#### ToolType

* getToolType(int)

Tool是指和触屏设备交互的设备, 例如手指, 触笔等.
一些触屏设备能够识别这些Tool.

返回值:

TOOL_TYPE_UNKNOWN
TOOL_TYPE_FINGER
TOOL_TYPE_STYLUS
TOOL_TYPE_MOUSE
TOOL_TYPE_ERASER

#### ButtonState

* getButtonState()
* isButtonPressed()

返回值是:

BUTTON_PRIMARY
BUTTON_SECONDARY
BUTTON_TERTIARY
BUTTON_FORWARD
BUTTON_BACK

#### MeteState

* getMetaState()

#### Device

* isFromSource(int)
* getSource()
* setSource(int)

返回值和参数是:

InputDevice.SOURCE_UNKNOWN
InputDevice.SOURCE_KEYBOARD               SOURCE_CLASS_BUTTON
InputDevice.SOURCE_DPAD                   SOURCE_CLASS_BUTTON
InputDevice.SOURCE_GAMEPAD                SOURCE_CLASS_BUTTON
InputDevice.SOURCE_TOUCHSCREEN            SOURCE_CLASS_POINTER
InputDevice.SOURCE_MOUSE                  SOURCE_CLASS_POINTER
InputDevice.SOURCE_STYLUS                 SOURCE_CLASS_POINTER
InputDevice.SOURCE_TRACKBALL              SOURCE_CLASS_TRACKBALL
InputDevice.SOURCE_TOUCHPAD               SOURCE_CLASS_POINTER
InputDevice.SOURCE_TOUCH_NAVIGATION       SOURCE_CLASS_NONE
InputDevice.SOURCE_JOYSTICK               SOURCE_CLASS_JOYSTICK
InputDevice.SOURCE_HDMI                   SOURCE_CLASS_BUTTON
InputDevice.SOURCE_ANY

虽然没有相关方法可以直接使用, 但是source实质是实际上会分成几类:

InputDevice.SOURCE_CLASS_NONE
InputDevice.SOURCE_CLASS_BUTTON
InputDevice.SOURCE_CLASS_POINTER
InputDevice.SOURCE_CLASS_TRACKBALL
InputDevice.SOURCE_CLASS_POSITION
InputDevice.SOURCE_CLASS_JOYSTICK

* getDevice()
* getDeviceId()

####

* getDownTime()
* setDownTime(long)    系统隐藏
* getEventTime()
* getEventTimeNano()    系统隐藏
* getFlags()
* isTainted()    系统隐藏
* setTainted(boolean)    系统隐藏
* isWithinBoundsNoHistory(float, float, float, float)    系统隐藏
* clampNoHistory(float, float, float, float)     系统隐藏
* getPointerIdBits()    系统隐藏
* split(int)    系统隐藏

### Activity Touch

* dispatchTouchEvent(MotionEvent)
* onTouchEvent(MotionEvent)

```
    public boolean dispatchTouchEvent(MotionEvent ev) {
        if (ev.getAction() == MotionEvent.ACTION_DOWN) {
            onUserInteraction();
        }
        if (getWindow().superDispatchTouchEvent(ev)) {
            return true;
        }
        return onTouchEvent(ev);
    }

    public void onUserInteraction() {
    }

    public boolean onTouchEvent(MotionEvent event) {
        if (mWindow.shouldCloseOnTouch(this, event)) {
            finish();
            return true;
        }

        return false;
    }
```

### ViewGroup Touch

* dispatchTouchEvent(MotionEvent)
* onInterceptTouchEvent(MotionEvent)     // onTouchEvent(MotionEvent)

需要指出的是: 

1. `onTouchEvent`方法没有覆写, 但是因为`dispatchTouchEvent`覆写中并没有使用该方法, 因此可以说这个方法在ViewGroup中是多余的, 另外一方面, `dispatchTouchEvent`覆写中使用了新的方法`onInterceptTouchEvent`.

```
   public boolean dispatchTouchEvent(MotionEvent ev) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onTouchEvent(ev, 1);
        }

        boolean handled = false;
        if (onFilterTouchEventForSecurity(ev)) {
            final int action = ev.getAction();
            final int actionMasked = action & MotionEvent.ACTION_MASK;

            // Handle an initial down.
            if (actionMasked == MotionEvent.ACTION_DOWN) {
                // Throw away all previous state when starting a new touch gesture.
                // The framework may have dropped the up or cancel event for the previous gesture
                // due to an app switch, ANR, or some other state change.
                cancelAndClearTouchTargets(ev);
                resetTouchState();
            }

            // Check for interception.
            final boolean intercepted;
            if (actionMasked == MotionEvent.ACTION_DOWN
                    || mFirstTouchTarget != null) {
                final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
                if (!disallowIntercept) {
                    intercepted = onInterceptTouchEvent(ev);
                    ev.setAction(action); // restore action in case it was changed
                } else {
                    intercepted = false;
                }
            } else {
                // There are no touch targets and this action is not an initial down
                // so this view group continues to intercept touches.
                intercepted = true;
            }

            // Check for cancelation.
            final boolean canceled = resetCancelNextUpFlag(this)
                    || actionMasked == MotionEvent.ACTION_CANCEL;

            // Update list of touch targets for pointer down, if needed.
            final boolean split = (mGroupFlags & FLAG_SPLIT_MOTION_EVENTS) != 0;
            TouchTarget newTouchTarget = null;
            boolean alreadyDispatchedToNewTouchTarget = false;
            if (!canceled && !intercepted) {
                if (actionMasked == MotionEvent.ACTION_DOWN
                        || (split && actionMasked == MotionEvent.ACTION_POINTER_DOWN)
                        || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
                    final int actionIndex = ev.getActionIndex(); // always 0 for down
                    final int idBitsToAssign = split ? 1 << ev.getPointerId(actionIndex)
                            : TouchTarget.ALL_POINTER_IDS;

                    // Clean up earlier touch targets for this pointer id in case they
                    // have become out of sync.
                    removePointersFromTouchTargets(idBitsToAssign);

                    final int childrenCount = mChildrenCount;
                    if (newTouchTarget == null && childrenCount != 0) {
                        final float x = ev.getX(actionIndex);
                        final float y = ev.getY(actionIndex);
                        // Find a child that can receive the event.
                        // Scan children from front to back.
                        final ArrayList<View> preorderedList = buildOrderedChildList();
                        final boolean customOrder = preorderedList == null
                                && isChildrenDrawingOrderEnabled();
                        final View[] children = mChildren;
                        for (int i = childrenCount - 1; i >= 0; i--) {
                            final int childIndex = customOrder
                                    ? getChildDrawingOrder(childrenCount, i) : i;
                            final View child = (preorderedList == null)
                                    ? children[childIndex] : preorderedList.get(childIndex);
                            if (!canViewReceivePointerEvents(child)
                                    || !isTransformedTouchPointInView(x, y, child, null)) {
                                continue;
                            }

                            newTouchTarget = getTouchTarget(child);
                            if (newTouchTarget != null) {
                                // Child is already receiving touch within its bounds.
                                // Give it the new pointer in addition to the ones it is handling.
                                newTouchTarget.pointerIdBits |= idBitsToAssign;
                                break;
                            }

                            resetCancelNextUpFlag(child);
                            if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
                                // Child wants to receive touch within its bounds.
                                mLastTouchDownTime = ev.getDownTime();
                                if (preorderedList != null) {
                                    // childIndex points into presorted list, find original index
                                    for (int j = 0; j < childrenCount; j++) {
                                        if (children[childIndex] == mChildren[j]) {
                                            mLastTouchDownIndex = j;
                                            break;
                                        }
                                    }
                                } else {
                                    mLastTouchDownIndex = childIndex;
                                }
                                mLastTouchDownX = ev.getX();
                                mLastTouchDownY = ev.getY();
                                newTouchTarget = addTouchTarget(child, idBitsToAssign);
                                alreadyDispatchedToNewTouchTarget = true;
                                break;
                            }
                        }
                        if (preorderedList != null) preorderedList.clear();
                    }

                    if (newTouchTarget == null && mFirstTouchTarget != null) {
                        // Did not find a child to receive the event.
                        // Assign the pointer to the least recently added target.
                        newTouchTarget = mFirstTouchTarget;
                        while (newTouchTarget.next != null) {
                            newTouchTarget = newTouchTarget.next;
                        }
                        newTouchTarget.pointerIdBits |= idBitsToAssign;
                    }
                }
            }

            // Dispatch to touch targets.
            if (mFirstTouchTarget == null) {
                // No touch targets so treat this as an ordinary view.
                handled = dispatchTransformedTouchEvent(ev, canceled, null,
                        TouchTarget.ALL_POINTER_IDS);
            } else {
                // Dispatch to touch targets, excluding the new touch target if we already
                // dispatched to it.  Cancel touch targets if necessary.
                TouchTarget predecessor = null;
                TouchTarget target = mFirstTouchTarget;
                while (target != null) {
                    final TouchTarget next = target.next;
                    if (alreadyDispatchedToNewTouchTarget && target == newTouchTarget) {
                        handled = true;
                    } else {
                        final boolean cancelChild = resetCancelNextUpFlag(target.child)
                                || intercepted;
                        if (dispatchTransformedTouchEvent(ev, cancelChild,
                                target.child, target.pointerIdBits)) {
                            handled = true;
                        }
                        if (cancelChild) {
                            if (predecessor == null) {
                                mFirstTouchTarget = next;
                            } else {
                                predecessor.next = next;
                            }
                            target.recycle();
                            target = next;
                            continue;
                        }
                    }
                    predecessor = target;
                    target = next;
                }
            }

            // Update list of touch targets for pointer up or cancel, if needed.
            if (canceled
                    || actionMasked == MotionEvent.ACTION_UP
                    || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
                resetTouchState();
            } else if (split && actionMasked == MotionEvent.ACTION_POINTER_UP) {
                final int actionIndex = ev.getActionIndex();
                final int idBitsToRemove = 1 << ev.getPointerId(actionIndex);
                removePointersFromTouchTargets(idBitsToRemove);
            }
        }

        if (!handled && mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(ev, 1);
        }
        return handled;
    }

    public boolean onInterceptTouchEvent(MotionEvent ev) {
        return false;
    }
```

### View Touch

* dispatchTouchEvent(MotionEvent)
* onTouchEvent(MotionEvent)
* requestFocus()

```
    public boolean dispatchTouchEvent(MotionEvent event) {
        boolean result = false;

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onTouchEvent(event, 0);
        }

        final int actionMasked = event.getActionMasked();
        if (actionMasked == MotionEvent.ACTION_DOWN) {
            // Defensive cleanup for new gesture
            stopNestedScroll();
        }

        if (onFilterTouchEventForSecurity(event)) {
            //noinspection SimplifiableIfStatement
            ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnTouchListener != null
                    && (mViewFlags & ENABLED_MASK) == ENABLED
                    && li.mOnTouchListener.onTouch(this, event)) {
                result = true;
            }

            if (!result && onTouchEvent(event)) {
                result = true;
            }
        }

        if (!result && mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }

        // Clean up after nested scrolls if this is the end of a gesture;
        // also cancel it if we tried an ACTION_DOWN but we didn't want the rest
        // of the gesture.
        if (actionMasked == MotionEvent.ACTION_UP ||
                actionMasked == MotionEvent.ACTION_CANCEL ||
                (actionMasked == MotionEvent.ACTION_DOWN && !result)) {
            stopNestedScroll();
        }

        return result;
    } 

    public boolean onTouchEvent(MotionEvent event) {
        final float x = event.getX();
        final float y = event.getY();
        final int viewFlags = mViewFlags;

        if ((viewFlags & ENABLED_MASK) == DISABLED) {
            if (event.getAction() == MotionEvent.ACTION_UP && (mPrivateFlags & PFLAG_PRESSED) != 0) {
                setPressed(false);
            }
            // A disabled view that is clickable still consumes the touch
            // events, it just doesn't respond to them.
            return (((viewFlags & CLICKABLE) == CLICKABLE ||
                    (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE));
        }

        if (mTouchDelegate != null) {
            if (mTouchDelegate.onTouchEvent(event)) {
                return true;
            }
        }

        if (((viewFlags & CLICKABLE) == CLICKABLE ||
                (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE)) {
            switch (event.getAction()) {
                case MotionEvent.ACTION_UP:
                    boolean prepressed = (mPrivateFlags & PFLAG_PREPRESSED) != 0;
                    if ((mPrivateFlags & PFLAG_PRESSED) != 0 || prepressed) {
                        // take focus if we don't have it already and we should in
                        // touch mode.
                        boolean focusTaken = false;
                        if (isFocusable() && isFocusableInTouchMode() && !isFocused()) {
                            focusTaken = requestFocus();
                        }

                        if (prepressed) {
                            // The button is being released before we actually
                            // showed it as pressed.  Make it show the pressed
                            // state now (before scheduling the click) to ensure
                            // the user sees it.
                            setPressed(true, x, y);
                       }

                        if (!mHasPerformedLongPress) {
                            // This is a tap, so remove the longpress check
                            removeLongPressCallback();

                            // Only perform take click actions if we were in the pressed state
                            if (!focusTaken) {
                                // Use a Runnable and post this rather than calling
                                // performClick directly. This lets other visual state
                                // of the view update before click actions start.
                                if (mPerformClick == null) {
                                    mPerformClick = new PerformClick();
                                }
                                if (!post(mPerformClick)) {
                                    performClick();
                                }
                            }
                        }

                        if (mUnsetPressedState == null) {
                            mUnsetPressedState = new UnsetPressedState();
                        }

                        if (prepressed) {
                            postDelayed(mUnsetPressedState,
                                    ViewConfiguration.getPressedStateDuration());
                        } else if (!post(mUnsetPressedState)) {
                            // If the post failed, unpress right now
                            mUnsetPressedState.run();
                        }

                        removeTapCallback();
                    }
                    break;

                case MotionEvent.ACTION_DOWN:
                    mHasPerformedLongPress = false;

                    if (performButtonActionOnTouchDown(event)) {
                        break;
                    }

                    // Walk up the hierarchy to determine if we're inside a scrolling container.
                    boolean isInScrollingContainer = isInScrollingContainer();

                    // For views inside a scrolling container, delay the pressed feedback for
                    // a short period in case this is a scroll.
                    if (isInScrollingContainer) {
                        mPrivateFlags |= PFLAG_PREPRESSED;
                        if (mPendingCheckForTap == null) {
                            mPendingCheckForTap = new CheckForTap();
                        }
                        mPendingCheckForTap.x = event.getX();
                        mPendingCheckForTap.y = event.getY();
                        postDelayed(mPendingCheckForTap, ViewConfiguration.getTapTimeout());
                    } else {
                        // Not inside a scrolling container, so show the feedback right away
                        setPressed(true, x, y);
                        checkForLongClick(0);
                    }
                    break;

                case MotionEvent.ACTION_CANCEL:
                    setPressed(false);
                    removeTapCallback();
                    removeLongPressCallback();
                    break;

                case MotionEvent.ACTION_MOVE:
                    drawableHotspotChanged(x, y);

                    // Be lenient about moving outside of buttons
                    if (!pointInView(x, y, mTouchSlop)) {
                        // Outside button
                        removeTapCallback();
                        if ((mPrivateFlags & PFLAG_PRESSED) != 0) {
                            // Remove any future long press/tap checks
                            removeLongPressCallback();

                            setPressed(false);
                        }
                    }
                    break;
            }

            return true;
        }

        return false;
    }
```

针对View的Touch有以下行为:

1. View首先必须是ENABLE状态(例如`setEnable`方法), 否则会忽略Touch事件.
2. View的`dispatchTouchEvent`首先尝试让OnTouchListener处理, 然后可能执行`onTouchEvent`方法.
3. View`onTouchEvent`会进一步把MotionEvent分成Click事件(OnLongClickListener监听器), LongClick事件(OnClickListener监听器). 当然, View必须提前设置自身的状态CLICKABLE(例如`setClickable`方法), LONG_CLICKABLE(例如`setLongClickable`方法), 否则也会忽略.
4. View的Pressed状态, 是在`ACTION_DOWN`时即设置, 而且View的Pressed状态不会作为一个事件暴露给用户处理, 因此没有相应的监听器, 但是View的自定义子类时, 可以覆写`dispatchSetPressed`方法处理Pressed状态的改变.
5. View的Focus事件(OnFocusChangeListener监听器)有可能会在Touch中触发. 当然, View必须提前设置自身的状态FOCUSABLE(例如`setFocusable`方法)和FOCUSABLE_IN_TOUCH_MODE(例如`setFocusableInTouchMode`方法).
6. 如果只是一个View的用户, 那么设置相应的监听器即可; 但是, 如果是自定义一个View的子类, 那么就不应该使用这些监听器, 而是覆写一些方法, 例如`performClick`, `performLongClick`.

### 参考

* https://source.android.com/devices/input/touch-devices.html
* http://developer.android.com/reference/android/view/MotionEvent.html
* https://github.com/android/platform_frameworks_base/blob/master/native/android/input.cpp
* https://github.com/android/platform_frameworks_base/blob/master/core/jni/android_view_MotionEvent.h
* https://github.com/android/platform_frameworks_base/blob/master/core/jni/android_view_MotionEvent.cpp
* http://developer.android.com/guide/topics/ui/ui-events.html

## GeneralMotionEvent

### Activity 

* dispatchGenericMotionEvent(MotionEvent)
* onGenericMotionEvent(MotionEvent)

```
    public boolean dispatchGenericMotionEvent(MotionEvent ev) {
        onUserInteraction();
        if (getWindow().superDispatchGenericMotionEvent(ev)) {
            return true;
        }
        return onGenericMotionEvent(ev);
    }

    public boolean onGenericMotionEvent(MotionEvent event) {
        return false;
    }
```

### ViewGroup

* dispatchGenericMotionEvent(MotionEvent)
* dispatchHoverEvent(MotionEvent)
* onInterceptHoverEvent(MotionEvent)      //   onHoverEvent(MotionEvent)
* dispatchGenericPointerEvent(MotionEvent)
* dispatchGenericFocusedEvent(MotionEvent)
* dispatchGenericMotionEventInternal(MotionEvent)
* onGenericMotionEvent(MotionEvent)

需要指出的是: 

1. `dispatchGenericMotionEvent`是父类View的实现, ViewGroup并没有覆写.
2. `onHoverEvent(MotionEvent)`并没有覆写, 但是因为`dispatchHoverEvent`覆写中没有使用该方法, 因此这个方法可以说在ViewGroup中是多余的, 另外一方面`dispatchHoverEvent`覆写中使用了新的`onInterceptHoverEvent`方法.
3. `dispatchGenericMotionEventInternal`和`onGenericMotionEvent`也是父类View的实现, ViewGroup并没有覆写.

```
   public boolean dispatchGenericMotionEvent(MotionEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onGenericMotionEvent(event, 0);
        }

        final int source = event.getSource();
        if ((source & InputDevice.SOURCE_CLASS_POINTER) != 0) {
            final int action = event.getAction();
            if (action == MotionEvent.ACTION_HOVER_ENTER
                    || action == MotionEvent.ACTION_HOVER_MOVE
                    || action == MotionEvent.ACTION_HOVER_EXIT) {
                if (dispatchHoverEvent(event)) {
                    return true;
                }
            } else if (dispatchGenericPointerEvent(event)) {
                return true;
            }
        } else if (dispatchGenericFocusedEvent(event)) {
            return true;
        }

        if (dispatchGenericMotionEventInternal(event)) {
            return true;
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }
        return false;
    }

    protected boolean dispatchHoverEvent(MotionEvent event) {
         // ViewGroup覆写了父类View的dispatchHoverEvent实现, 但是代码较长, 故省略.
         // 其中调用了ViewGroup特有的`onInterceptHoverEvent`方法
    }

    public boolean onInterceptHoverEvent(MotionEvent event) {
        return false;
    }

    protected boolean dispatchGenericPointerEvent(MotionEvent event) {
        // Send the event to the child under the pointer.
        final int childrenCount = mChildrenCount;
        if (childrenCount != 0) {
            final float x = event.getX();
            final float y = event.getY();

            final ArrayList<View> preorderedList = buildOrderedChildList();
            final boolean customOrder = preorderedList == null
                    && isChildrenDrawingOrderEnabled();
            final View[] children = mChildren;
            for (int i = childrenCount - 1; i >= 0; i--) {
                int childIndex = customOrder ? getChildDrawingOrder(childrenCount, i) : i;
                final View child = (preorderedList == null)
                        ? children[childIndex] : preorderedList.get(childIndex);
                if (!canViewReceivePointerEvents(child)
                        || !isTransformedTouchPointInView(x, y, child, null)) {
                    continue;
                }

                if (dispatchTransformedGenericPointerEvent(event, child)) {
                    if (preorderedList != null) preorderedList.clear();
                    return true;
                }
            }
            if (preorderedList != null) preorderedList.clear();
        }

        // No child handled the event.  Send it to this view group.
        return super.dispatchGenericPointerEvent(event);
    }

    protected boolean dispatchGenericFocusedEvent(MotionEvent event) {
        // Send the event to the focused child or to this view group if it has focus.
        if ((mPrivateFlags & (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS))
                == (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS)) {
            return super.dispatchGenericFocusedEvent(event);
        } else if (mFocused != null && (mFocused.mPrivateFlags & PFLAG_HAS_BOUNDS)
                == PFLAG_HAS_BOUNDS) {
            return mFocused.dispatchGenericMotionEvent(event);
        }
        return false;
    }


    private boolean dispatchGenericMotionEventInternal(MotionEvent event) {
        //noinspection SimplifiableIfStatement
        ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnGenericMotionListener != null
                && (mViewFlags & ENABLED_MASK) == ENABLED
                && li.mOnGenericMotionListener.onGenericMotion(this, event)) {
            return true;
        }

        if (onGenericMotionEvent(event)) {
            return true;
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }
        return false;
    }

    public boolean onGenericMotionEvent(MotionEvent event) {
        return false;
    }
```

### View

* dispatchGenericMotionEvent(MotionEvent)
* dispatchHoverEvent(MotionEvent)
* onHoverEvent(MotionEvent)
* dispatchGenericPointerEvent(MotionEvent)
* dispatchGenericFocusedEvent(MotionEvent)
* dispatchGenericMotionEventInternal(MotionEvent)
* onGenericMotionEvent(MotionEvent)

```
   public boolean dispatchGenericMotionEvent(MotionEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onGenericMotionEvent(event, 0);
        }

        final int source = event.getSource();
        if ((source & InputDevice.SOURCE_CLASS_POINTER) != 0) {
            final int action = event.getAction();
            if (action == MotionEvent.ACTION_HOVER_ENTER
                    || action == MotionEvent.ACTION_HOVER_MOVE
                    || action == MotionEvent.ACTION_HOVER_EXIT) {
                if (dispatchHoverEvent(event)) {
                    return true;
                }
            } else if (dispatchGenericPointerEvent(event)) {
                return true;
            }
        } else if (dispatchGenericFocusedEvent(event)) {
            return true;
        }

        if (dispatchGenericMotionEventInternal(event)) {
            return true;
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }
        return false;
    }

    protected boolean dispatchHoverEvent(MotionEvent event) {
        ListenerInfo li = mListenerInfo;
        //noinspection SimplifiableIfStatement
        if (li != null && li.mOnHoverListener != null
                && (mViewFlags & ENABLED_MASK) == ENABLED
                && li.mOnHoverListener.onHover(this, event)) {
            return true;
        }

        return onHoverEvent(event);
    }

  public boolean onHoverEvent(MotionEvent event) {
        final int action = event.getActionMasked();
        if (!mSendingHoverAccessibilityEvents) {
            if ((action == MotionEvent.ACTION_HOVER_ENTER
                    || action == MotionEvent.ACTION_HOVER_MOVE)
                    && !hasHoveredChild()
                    && pointInView(event.getX(), event.getY())) {
                sendAccessibilityHoverEvent(AccessibilityEvent.TYPE_VIEW_HOVER_ENTER);
                mSendingHoverAccessibilityEvents = true;
            }
        } else {
            if (action == MotionEvent.ACTION_HOVER_EXIT
                    || (action == MotionEvent.ACTION_MOVE
                            && !pointInView(event.getX(), event.getY()))) {
                mSendingHoverAccessibilityEvents = false;
                sendAccessibilityHoverEvent(AccessibilityEvent.TYPE_VIEW_HOVER_EXIT);
            }
        }

        if (isHoverable()) {
            switch (action) {
                case MotionEvent.ACTION_HOVER_ENTER:
                    setHovered(true);
                    break;
                case MotionEvent.ACTION_HOVER_EXIT:
                    setHovered(false);
                    break;
            }

            dispatchGenericMotionEventInternal(event);
            return true;
        }

        return false;
    }

     protected boolean dispatchGenericPointerEvent(MotionEvent event) {
        return false;
    }

    protected boolean dispatchGenericFocusedEvent(MotionEvent event) {
        return false;
    }

    private boolean dispatchGenericMotionEventInternal(MotionEvent event) {
        //noinspection SimplifiableIfStatement
        ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnGenericMotionListener != null
                && (mViewFlags & ENABLED_MASK) == ENABLED
                && li.mOnGenericMotionListener.onGenericMotion(this, event)) {
            return true;
        }

        if (onGenericMotionEvent(event)) {
            return true;
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }
        return false;
    }

    public boolean onGenericMotionEvent(MotionEvent event) {
        return false;
    }

```

针对View的Touch有以下行为:

1. View首先必须是ENABLE状态(例如`setEnable`方法), 否则会忽略Touch事件.
2. View的GenericMotionEvent会进一步分成HoverEvent(OnHoverListener监听器), GenericPointerEvent(没有监听器), GenericFocusedEvent(没有监听器), GenericMotionEvent(OnGenericMotionListener监听器)四种. View的HOVERABLE状态取决于ENABLE, CLICKABLE, LONGCLICKABLE状态(见`isHoverable`方法).
3. 需要指出的是, 虽然View存在OnFocusChangeListener监听器, 但是这个监听器并不是在`dispatchGenericFocusedEvent`方法中所触发的, 因此也不属于这里的GenericFocusedEvent.
4. 此外, 由于`dispatchHoverEvent`方法优先于`dispatchGenericMotionEventInternal`方法执行, 因此即使OnGenericMotionListener监听器触发返回true, 也不会阻止OnHoverListener监听器; 相反OnHoverListener监听器可以阻止OnGenericMotionListener监听器.
5. GenericMotion事件在手机或平板上似乎难以展示.

## Key

## KeyEvent

键盘事件

####

* keyCodeToString(int)
* keyCodeFromString(String)

* getDeviceId()
* getSource()
* getSource(int)
* getMetaState()




#### 

* getAction()

返回值是以下值:

ACTION_DOWN
ACTION_UP
ACTION_MULTIPLE

* getKeyCode()
* getDisplayLabel()
* getUnicodeChar()
* getUnicodeChar(int)
* getNumber()

* getCharacters()
* getScanCode()
* getRepeatCount()
* getDownTime()
* getEventTime()
* getEventTimeNano()    系统隐藏

####

* getFlags()
* isCanceled()
* startTracking()
* isTracking()
* isLongPress()

FLAG:

FLAG_SOFT_KEYBOARD
FLAG_KEEP_TOUCH_MODE
FLAG_FROM_SYSTEM
FLAG_EDITOR_ACTION
FLAG_CANCELED
FLAG_VIRTUAL_HARD_KEY
FLAG_LONG_PRESS
FLAG_CANCELED_LONG_PRESS
FLAG_TRACKING
FLAG_FALLBACK
FLAG_PREDISPATCH
FLAG_START_TRACKING

####

* isSystem ()
* isPrintingKey()
* isWakeKey()    系统隐藏
* isGamepadButton(int)
* isConfirmKey(int)    系统隐藏
* isMediaKey(int)    系统隐藏
* isSystemKey(int)    系统隐藏
* isWakeKey(int)    系统隐藏
* isMetaKey(int)    系统隐藏
* isModifierKey(int)

####

* isAltPressed()
* isShiftPressed()
* isSymPressed()
* isCtrlPressed()
* isMetaPressed()
* isFunctionPressed()
* isCapsLockOn()
* isNumLockOn()
* isScrollLockOn()

* getMatch(char[])
* getMatch(chat[], int)



* getKeyCharacterMap()

####

* hasNoModifiers)
* hasModifiers(int)
* normalizeMetaState(int)
* metaStateHasNoModifiers(int)
* metaStateHasModifiers(int, int)
* metaStateToString(int)   系统隐藏
* getModifiers()
* getModifierMetaStateMask()
* isModifierKey(int)

####

* dispatch(Callback, DispatcherState, Object)


```
    public interface Callback {
        boolean onKeyDown(int keyCode, KeyEvent event);
        boolean onKeyLongPress(int keyCode, KeyEvent event);
        boolean onKeyUp(int keyCode, KeyEvent event);
        boolean onKeyMultiple(int keyCode, int count, KeyEvent event);
    }
```

### 参考

* https://github.com/android/platform_frameworks_base/blob/master/core/jni/android_view_KeyEvent.h
* https://github.com/android/platform_frameworks_base/blob/master/core/jni/android_view_KeyEvent.cpp

### DecorView

需要指出的是, Activity类的事件是来自PhoneWindow类的DecorView内部类的`dispatchKeyEvent`方法, 因此如果Key事件最后没有被Activity, ViewGroup和View所处理, 那么PhoneWindow类的DecorView内部类会调用PhoneWindow类的`onKeyDown`和`onKeyUp`处理一些. 个人觉得这里的框架设计有点不合理, `onKeyDown`和`onKeyUp`这两个方法应该直接作为DecorView类的成员方法即可, 而不是应该PhoneWindow类的成员方法.

```
    private final class DecorView extends FrameLayout implements RootViewSurfaceTaker {
        public boolean dispatchKeyEvent(KeyEvent event) {
            final int keyCode = event.getKeyCode();
            final int action = event.getAction();
            final boolean isDown = action == KeyEvent.ACTION_DOWN;

            if (isDown && (event.getRepeatCount() == 0)) {
                // First handle chording of panel key: if a panel key is held
                // but not released, try to execute a shortcut in it.
                if ((mPanelChordingKey > 0) && (mPanelChordingKey != keyCode)) {
                    boolean handled = dispatchKeyShortcutEvent(event);
                    if (handled) {
                        return true;
                    }
                }

                // If a panel is open, perform a shortcut on it without the
                // chorded panel key
                if ((mPreparedPanel != null) && mPreparedPanel.isOpen) {
                    if (performPanelShortcut(mPreparedPanel, keyCode, event, 0)) {
                        return true;
                    }
                }
            }

            if (!isDestroyed()) {
                final Callback cb = getCallback();
                final boolean handled = cb != null && mFeatureId < 0 ? cb.dispatchKeyEvent(event)
                        : super.dispatchKeyEvent(event);
                if (handled) {
                    return true;
                }
            }

            return isDown ? PhoneWindow.this.onKeyDown(mFeatureId, event.getKeyCode(), event)
                    : PhoneWindow.this.onKeyUp(mFeatureId, event.getKeyCode(), event);
        }
}

public class PhoneWindow extends Window implements MenuBuilder.Callback {
    protected boolean onKeyDown(int featureId, int keyCode, KeyEvent event) {
        final KeyEvent.DispatcherState dispatcher =
                mDecor != null ? mDecor.getKeyDispatcherState() : null;
        //Log.i(TAG, "Key down: repeat=" + event.getRepeatCount()
        //        + " flags=0x" + Integer.toHexString(event.getFlags()));

        switch (keyCode) {
            case KeyEvent.KEYCODE_VOLUME_UP:
            case KeyEvent.KEYCODE_VOLUME_DOWN: {
                int direction = keyCode == KeyEvent.KEYCODE_VOLUME_UP ? AudioManager.ADJUST_RAISE
                        : AudioManager.ADJUST_LOWER;
                // If we have a session send it the volume command, otherwise
                // use the suggested stream.
                if (mMediaController != null) {
                    mMediaController.adjustVolume(direction, AudioManager.FLAG_SHOW_UI);
                } else {
                    MediaSessionLegacyHelper.getHelper(getContext()).sendAdjustVolumeBy(
                            mVolumeControlStreamType, direction,
                            AudioManager.FLAG_SHOW_UI | AudioManager.FLAG_VIBRATE);
                }
                return true;
            }
            case KeyEvent.KEYCODE_VOLUME_MUTE: {
                getAudioManager().handleKeyDown(event, mVolumeControlStreamType);
                return true;
            }
            // These are all the recognized media key codes in
            // KeyEvent.isMediaKey()
            case KeyEvent.KEYCODE_MEDIA_PLAY:
            case KeyEvent.KEYCODE_MEDIA_PAUSE:
            case KeyEvent.KEYCODE_MEDIA_PLAY_PAUSE:
            case KeyEvent.KEYCODE_MUTE:
            case KeyEvent.KEYCODE_HEADSETHOOK:
            case KeyEvent.KEYCODE_MEDIA_STOP:
            case KeyEvent.KEYCODE_MEDIA_NEXT:
            case KeyEvent.KEYCODE_MEDIA_PREVIOUS:
            case KeyEvent.KEYCODE_MEDIA_REWIND:
            case KeyEvent.KEYCODE_MEDIA_RECORD:
            case KeyEvent.KEYCODE_MEDIA_FAST_FORWARD: {
                if (mMediaController != null) {
                    if (mMediaController.dispatchMediaButtonEvent(event)) {
                        return true;
                    }
                }
                return false;
            }

            case KeyEvent.KEYCODE_MENU: {
                onKeyDownPanel((featureId < 0) ? FEATURE_OPTIONS_PANEL : featureId, event);
                return true;
            }

            case KeyEvent.KEYCODE_BACK: {
                if (event.getRepeatCount() > 0) break;
                if (featureId < 0) break;
                // Currently don't do anything with long press.
                if (dispatcher != null) {
                    dispatcher.startTracking(event, this);
                }
                return true;
            }

        }

        return false;
    }

    protected boolean onKeyUp(int featureId, int keyCode, KeyEvent event) {
        final KeyEvent.DispatcherState dispatcher =
                mDecor != null ? mDecor.getKeyDispatcherState() : null;
        if (dispatcher != null) {
            dispatcher.handleUpEvent(event);
        }
        //Log.i(TAG, "Key up: repeat=" + event.getRepeatCount()
        //        + " flags=0x" + Integer.toHexString(event.getFlags()));

        switch (keyCode) {
            case KeyEvent.KEYCODE_VOLUME_UP:
            case KeyEvent.KEYCODE_VOLUME_DOWN: {
                // If we have a session send it the volume command, otherwise
                // use the suggested stream.
                if (mMediaController != null) {
                    mMediaController.adjustVolume(0, AudioManager.FLAG_PLAY_SOUND
                            | AudioManager.FLAG_VIBRATE);
                } else {
                    MediaSessionLegacyHelper.getHelper(getContext()).sendAdjustVolumeBy(
                            mVolumeControlStreamType, 0,
                            AudioManager.FLAG_PLAY_SOUND | AudioManager.FLAG_VIBRATE);
                }
                return true;
            }
            case KeyEvent.KEYCODE_VOLUME_MUTE: {
                // Similar code is in PhoneFallbackEventHandler in case the window
                // doesn't have one of these.  In this case, we execute it here and
                // eat the event instead, because we have mVolumeControlStreamType
                // and they don't.
                getAudioManager().handleKeyUp(event, mVolumeControlStreamType);
                return true;
            }
            // These are all the recognized media key codes in
            // KeyEvent.isMediaKey()
            case KeyEvent.KEYCODE_MEDIA_PLAY:
            case KeyEvent.KEYCODE_MEDIA_PAUSE:
            case KeyEvent.KEYCODE_MEDIA_PLAY_PAUSE:
            case KeyEvent.KEYCODE_MUTE:
            case KeyEvent.KEYCODE_HEADSETHOOK:
            case KeyEvent.KEYCODE_MEDIA_STOP:
            case KeyEvent.KEYCODE_MEDIA_NEXT:
            case KeyEvent.KEYCODE_MEDIA_PREVIOUS:
            case KeyEvent.KEYCODE_MEDIA_REWIND:
            case KeyEvent.KEYCODE_MEDIA_RECORD:
            case KeyEvent.KEYCODE_MEDIA_FAST_FORWARD: {
                if (mMediaController != null) {
                    if (mMediaController.dispatchMediaButtonEvent(event)) {
                        return true;
                    }
                }
                return false;
            }

            case KeyEvent.KEYCODE_MENU: {
                onKeyUpPanel(featureId < 0 ? FEATURE_OPTIONS_PANEL : featureId,
                        event);
                return true;
            }

            case KeyEvent.KEYCODE_BACK: {
                if (featureId < 0) break;
                if (event.isTracking() && !event.isCanceled()) {
                    if (featureId == FEATURE_OPTIONS_PANEL) {
                        PanelFeatureState st = getPanelState(featureId, false);
                        if (st != null && st.isInExpandedMode) {
                            // If the user is in an expanded menu and hits back, it
                            // should go back to the icon menu
                            reopenMenu(true);
                            return true;
                        }
                    }
                    closePanel(featureId);
                    return true;
                }
                break;
            }

            case KeyEvent.KEYCODE_SEARCH: {
                /*
                 * Do this in onKeyUp since the Search key is also used for
                 * chording quick launch shortcuts.
                 */
                if (getKeyguardManager().inKeyguardRestrictedInputMode()) {
                    break;
                }
                if (event.isTracking() && !event.isCanceled()) {
                    launchDefaultSearch();
                }
                return true;
            }
        }

        return false;
    }
}
```

### Activity

* dispatchKeyEvent(KeyEvent)

```
  public boolean dispatchKeyEvent(KeyEvent event) {
        onUserInteraction();

        // Let action bars open menus in response to the menu key prioritized over
        // the window handling it
        if (event.getKeyCode() == KeyEvent.KEYCODE_MENU &&
                mActionBar != null && mActionBar.onMenuKeyEvent(event)) {
            return true;
        }

        Window win = getWindow();
        if (win.superDispatchKeyEvent(event)) {
            return true;
        }
        View decor = mDecor;
        if (decor == null) decor = win.getDecorView();
        return event.dispatch(this, decor != null
                ? decor.getKeyDispatcherState() : null, this);
    }
```

Activity处理顺序:

1. 首先, Activity类处理KeyEvent.KEYCODE_MENU事件.
2. 接着, Activity会传递给PhoneWindow类的DecorView内部类, DecorView类会处理`KeyEvent.KEYCODE_BACK`的`ACTION_UP`事件, 因此之后的ViewGroup类和View类将接收不到`KeyEvent.KEYCODE_BACK`的`ACTION_UP`事件. 然后DecorView类会传递事件到ViewGroup类和View类.

```
        public boolean superDispatchKeyEvent(KeyEvent event) {
            // Give priority to closing action modes if applicable.
            if (event.getKeyCode() == KeyEvent.KEYCODE_BACK) {
                final int action = event.getAction();
                // Back cancels action modes first.
                if (mActionMode != null) {
                    if (action == KeyEvent.ACTION_UP) {
                        mActionMode.finish();
                    }
                    return true;
                }
            }

            return super.dispatchKeyEvent(event);
        }
```

3. 最后, Activity类实现了KeyEvent.Callback接口, 即的`onKeyDown`, `onKeyLongPress`, `onKeyUp`和`onKeyMultiple`方法, 因此通过调用KeyEvent类实例的`dispatch`方法, 实质上仍然是调用自身的这四个方法.

```
   public boolean onKeyDown(int keyCode, KeyEvent event)  {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            if (getApplicationInfo().targetSdkVersion
                    >= Build.VERSION_CODES.ECLAIR) {
                event.startTracking();
            } else {
                onBackPressed();
            }
            return true;
        }

        if (mDefaultKeyMode == DEFAULT_KEYS_DISABLE) {
            return false;
        } else if (mDefaultKeyMode == DEFAULT_KEYS_SHORTCUT) {
            if (getWindow().performPanelShortcut(Window.FEATURE_OPTIONS_PANEL,
                    keyCode, event, Menu.FLAG_ALWAYS_PERFORM_CLOSE)) {
                return true;
            }
            return false;
        } else {
            // Common code for DEFAULT_KEYS_DIALER & DEFAULT_KEYS_SEARCH_*
            boolean clearSpannable = false;
            boolean handled;
            if ((event.getRepeatCount() != 0) || event.isSystem()) {
                clearSpannable = true;
                handled = false;
            } else {
                handled = TextKeyListener.getInstance().onKeyDown(
                        null, mDefaultKeySsb, keyCode, event);
                if (handled && mDefaultKeySsb.length() > 0) {
                    // something useable has been typed - dispatch it now.

                    final String str = mDefaultKeySsb.toString();
                    clearSpannable = true;

                    switch (mDefaultKeyMode) {
                    case DEFAULT_KEYS_DIALER:
                        Intent intent = new Intent(Intent.ACTION_DIAL,  Uri.parse("tel:" + str));
                        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                        startActivity(intent);
                        break;
                    case DEFAULT_KEYS_SEARCH_LOCAL:
                        startSearch(str, false, null, false);
                        break;
                    case DEFAULT_KEYS_SEARCH_GLOBAL:
                        startSearch(str, false, null, true);
                        break;
                    }
                }
            }
            if (clearSpannable) {
                mDefaultKeySsb.clear();
                mDefaultKeySsb.clearSpans();
                Selection.setSelection(mDefaultKeySsb,0);
            }
            return handled;
        }
    }

    public boolean onKeyUp(int keyCode, KeyEvent event) {
        if (getApplicationInfo().targetSdkVersion
                >= Build.VERSION_CODES.ECLAIR) {
            if (keyCode == KeyEvent.KEYCODE_BACK && event.isTracking()
                    && !event.isCanceled()) {
                onBackPressed();
                return true;
            }
        }
        return false;
    }

    public boolean onKeyMultiple(int keyCode, int repeatCount, KeyEvent event) {
        return false;
    }

    public boolean onKeyLongPress(int keyCode, KeyEvent event) {
        return false;
    }

    public void onBackPressed() {
        if (mActionBar != null && mActionBar.collapseActionView()) {
            return;
        }

        if (!mFragments.popBackStackImmediate()) {
            finishAfterTransition();
        }
    }

```


### ViewGroup

* dispatchKeyEvent(KeyEvent)

```
    public boolean dispatchKeyEvent(KeyEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onKeyEvent(event, 1);
        }

        if ((mPrivateFlags & (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS))
                == (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS)) {
            if (super.dispatchKeyEvent(event)) {
                return true;
            }
        } else if (mFocused != null && (mFocused.mPrivateFlags & PFLAG_HAS_BOUNDS)
                == PFLAG_HAS_BOUNDS) {
            if (mFocused.dispatchKeyEvent(event)) {
                return true;
            }
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 1);
        }
        return false;
    }
```

其中, `mFocused`成员变量是View类实例, 也就是当前ViewGroup类实例中只有获得焦点的那个视图才能处理Key事件. 因此对于触屏设备, 需要预先设置当前状态是FOCUSABLE和FOCUSABLE_IN_TOUCH_MODE, 否则即使点击View类实例或直接调用`requestFocus`方法也不能获得焦点(见`requestFocusNoSearch`方法).

### View

* dispatchKeyEvent(KeyEvent)

```
   public boolean dispatchKeyEvent(KeyEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onKeyEvent(event, 0);
        }

        // Give any attached key listener a first crack at the event.
        //noinspection SimplifiableIfStatement
        ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnKeyListener != null && (mViewFlags & ENABLED_MASK) == ENABLED
                && li.mOnKeyListener.onKey(this, event.getKeyCode(), event)) {
            return true;
        }

        if (event.dispatch(this, mAttachInfo != null
                ? mAttachInfo.mKeyDispatchState : null, this)) {
            return true;
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 0);
        }
        return false;
    }

```

而这里View也实现了KeyEvent.Callbacl接口, 因此View也会处理一些事情.

```
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        boolean result = false;

        if (KeyEvent.isConfirmKey(keyCode)) {
            if ((mViewFlags & ENABLED_MASK) == DISABLED) {
                return true;
            }
            // Long clickable items don't necessarily have to be clickable
            if (((mViewFlags & CLICKABLE) == CLICKABLE ||
                    (mViewFlags & LONG_CLICKABLE) == LONG_CLICKABLE) &&
                    (event.getRepeatCount() == 0)) {
                setPressed(true);
                checkForLongClick(0);
                return true;
            }
        }
        return result;
    }

    public boolean onKeyUp(int keyCode, KeyEvent event) {
        if (KeyEvent.isConfirmKey(keyCode)) {
            if ((mViewFlags & ENABLED_MASK) == DISABLED) {
                return true;
            }
            if ((mViewFlags & CLICKABLE) == CLICKABLE && isPressed()) {
                setPressed(false);

                if (!mHasPerformedLongPress) {
                    // This is a tap, so remove the longpress check
                    removeLongPressCallback();
                    return performClick();
                }
            }
        }
        return false;
    }

    public boolean onKeyMultiple(int keyCode, int repeatCount, KeyEvent event) {
        return false;
    }

    public boolean onKeyLongPress(int keyCode, KeyEvent event) {
        return false;
    }
```

1. 如果不设置当前View的状态是FOCUSABLE和`FOCUSABLE_IN_TOUCH_MODE`, 那么Key事件将不会传递到View;
2. 如果设置当前View的状态是FOCUSABLE, 则软键盘的Key事件可以传递到View;
3. 只有设置当前View的状态是FOCUSABLE和`FOCUSABLE_IN_TOUCH_MODE`, 那么一部分系统Key事件(如`KEYCODE_BACK`, `KEYCODE_VOLUME_DOWN`和`KEYCODE_VOLUME_UP`)才能传递到View, 但是还有一部分系统Key事件(如`KEYCODE_HOME`和`KEYCODE_POWER`)不能传递到View.

## KeyShortcut

### DecorView

* dispatchKeyShortcutEvent(KeyEvent)

```
       public boolean dispatchKeyShortcutEvent(KeyEvent ev) {
            // If the panel is already prepared, then perform the shortcut using it.
            boolean handled;
            if (mPreparedPanel != null) {
                handled = performPanelShortcut(mPreparedPanel, ev.getKeyCode(), ev,
                        Menu.FLAG_PERFORM_NO_CLOSE);
                if (handled) {
                    if (mPreparedPanel != null) {
                        mPreparedPanel.isHandled = true;
                    }
                    return true;
                }
            }

            // Shortcut not handled by the panel.  Dispatch to the view hierarchy.
            final Callback cb = getCallback();
            handled = cb != null && !isDestroyed() && mFeatureId < 0
                    ? cb.dispatchKeyShortcutEvent(ev) : super.dispatchKeyShortcutEvent(ev);
            if (handled) {
                return true;
            }

            // If the panel is not prepared, then we may be trying to handle a shortcut key
            // combination such as Control+C.  Temporarily prepare the panel then mark it
            // unprepared again when finished to ensure that the panel will again be prepared
            // the next time it is shown for real.
            if (mPreparedPanel == null) {
                PanelFeatureState st = getPanelState(FEATURE_OPTIONS_PANEL, true);
                preparePanel(st, ev);
                handled = performPanelShortcut(st, ev.getKeyCode(), ev,
                        Menu.FLAG_PERFORM_NO_CLOSE);
                st.isPrepared = false;
                if (handled) {
                    return true;
                }
            }
            return false;
        }
```

### Activity

* dispatchKeyShortcutEvent(KeyEvent)
* onKeyShortcut(int, KeyEvent)

```
    public boolean dispatchKeyShortcutEvent(KeyEvent event) {
        onUserInteraction();
        if (getWindow().superDispatchKeyShortcutEvent(event)) {
            return true;
        }
        return onKeyShortcut(event.getKeyCode(), event);
    }

    public boolean onKeyShortcut(int keyCode, KeyEvent event) {
        return false;
    }
```

### ViewGroup

* dispatchKeyShortcutEvent(KeyEvent)

```
    public boolean dispatchKeyShortcutEvent(KeyEvent event) {
        if ((mPrivateFlags & (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS))
                == (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS)) {
            return super.dispatchKeyShortcutEvent(event);
        } else if (mFocused != null && (mFocused.mPrivateFlags & PFLAG_HAS_BOUNDS)
                == PFLAG_HAS_BOUNDS) {
            return mFocused.dispatchKeyShortcutEvent(event);
        }
        return false;
    }
```

### View

* dispatchKeyShortcutEvent(KeyEvent)
* onKeyShortcut(int, KeyEvent)

```
    public boolean dispatchKeyShortcutEvent(KeyEvent event) {
        return onKeyShortcut(event.getKeyCode(), event);
    }

    public boolean onKeyShortcut(int keyCode, KeyEvent event) {
        return false;
    }
```



## Trackball

轨迹球

### Activity

* dispatchTrackballEvent(MotionEvent)
* onTrackballEvent(MotionEvent)

```
    public boolean dispatchTrackballEvent(MotionEvent ev) {
        onUserInteraction();
        if (getWindow().superDispatchTrackballEvent(ev)) {
            return true;
        }
        return onTrackballEvent(ev);
    }

    public boolean onTrackballEvent(MotionEvent event) {
        return false;
    }
```

### ViewGroup

* dispatchTrackballEvent(MotionEvent)

```
   public boolean dispatchTrackballEvent(MotionEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onTrackballEvent(event, 1);
        }

        if ((mPrivateFlags & (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS))
                == (PFLAG_FOCUSED | PFLAG_HAS_BOUNDS)) {
            if (super.dispatchTrackballEvent(event)) {
                return true;
            }
        } else if (mFocused != null && (mFocused.mPrivateFlags & PFLAG_HAS_BOUNDS)
                == PFLAG_HAS_BOUNDS) {
            if (mFocused.dispatchTrackballEvent(event)) {
                return true;
            }
        }

        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onUnhandledEvent(event, 1);
        }
        return false;
    }
```

### View

* dispatchTrackballEvent(MotionEvent)
* onTrackballEvent(MotionEvent)

```
    public boolean dispatchTrackballEvent(MotionEvent event) {
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onTrackballEvent(event, 0);
        }

        return onTrackballEvent(event);
    }

    public boolean onTrackballEvent(MotionEvent event) {
        return false;
    }
```

## GestureDetector

`android.view.GestureDetector`类

### OnGestureListener

```
    public interface OnGestureListener {
        boolean onDown(MotionEvent e);
        void onShowPress(MotionEvent e);
        boolean onSingleTapUp(MotionEvent e);
        boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY);
        void onLongPress(MotionEvent e);
        boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY);
    }
```

onDown: 每一次Touch的Down时触发
onShowPress: Touch的Down以后, 短时间内一直没有Move和Up时触发
onSingleTapUp: Touch的Down以后, 没有onScroll, 也没有onLongPress, 当Up时触发
onScroll: Touch的Down以后, 每一次产生Scroll时触发.
onLongPress: Touch的Down以后, 长时间内一直没有Move和Up时触发.
onFling: Touch的Down以后, Move以后, 当Up时触发

### OnDoubleTapListener

```

   public interface OnDoubleTapListener {
        boolean onSingleTapConfirmed(MotionEvent e);
        boolean onDoubleTap(MotionEvent e);
        boolean onDoubleTapEvent(MotionEvent e);
    }
```

onSingleTapConfirmed: 单击触发. 如果系统一段时间内没有收到第二次点击, 即判定触发.
onDoubleTap: 双击触发
onDoubleTapEvent: 当双击之间所有的事件都会触发

###

* onTouchEvent(MotionEvent)

如果View收到Touch事件以后, 调用GestureDetector类实例的`onTouchEvent`方法, 则开始启动手势分析过程, 然后触发相应的监听器的相关方法.

示例代码:

```
	GestureDetector	gd = new GestureDetector(this, new MyGestureListener());
		
	view.setOnTouchListener(new View.OnTouchListener() {
			
		@Override
		public boolean onTouch(View v, MotionEvent event) {
			// TODO Auto-generated method stub
			gd.onTouchEvent(event);
			return true;
		}
	});
```

## ScaleGestureDetector

`android.view.ScaleGestureDetector`类, 检测两个手指的缩放手势.

### OnScaleGestureListener

```
public interface OnScaleGestureListener {
        public boolean onScale(ScaleGestureDetector detector);
        public boolean onScaleBegin(ScaleGestureDetector detector);
        public void onScaleEnd(ScaleGestureDetector detector);
    }
```

###

* onTouchEvent(MotionEvent)

如果View收到Touch事件以后, 调用ScaleGestureDetector类实例的`onTouchEvent`方法, 则开始启动手势分析过程, 然后触发相应的监听器的相关方法.

示例代码:

```
	ScaleGestureDetector sgd = new ScaleGestureDetector(this, new MyScaleGestureListener());
		
	view.setOnTouchListener(new View.OnTouchListener() {
			
		@Override
		public boolean onTouch(View v, MotionEvent event) {
			// TODO Auto-generated method stub
			sgd.onTouchEvent(event);
			return true;
		}
	});
```

###

* setQuickScaleEnabled(boolean)
* isQuickScaleEnabled()

快速缩放, 即单个手指快速第二次点击但不释放时, 随手指移动而缩放.

Android 4.3以后默认启动.

###

* isInProgress()

###

* getFocusX()
* getFocusY()

###

* getCurrentSpan()
* getCurrentSpanX()
* getCurrentSpanY()

* getPreviousSpan()
* getPreviousSpanX()
* getPreviousSpanY()

###

* getScaleFactor()

### 

* getTimeDelta()
* getEventTime()

## 其他

View还存在一些其他事件的分发.

* dispatchDragEvent(DragEvent)
* dispatchPopulateAccessibilityEvent(AccessibilityEvent)
* dispatchWindowFocusChanged(boolean)
* dispatchSetPressed(boolean)
* dispatchSetSelected(boolean)
* dispatchSetActivated(boolean)
* dispatchVisibilityChanged(View, int)
* dispatchSystemUiVisibilityChanged(int)
* dispatchDisplayHint(int)
* dispatchAttachedToWindow(AttachInfo, int)
* dispatchCancelPendingInputEvents()

## 参考

* https://github.com/android/platform_frameworks_base/tree/master/core/jni
* https://android.googlesource.com/platform/frameworks/native/+/master
* http://developer.android.com/training/gestures/index.html

# 6 Material Design

## 6.1 elevation

## 6.2 Material Theme

## 6.3 

## 参考

* https://developer.android.com/training/material/index.html
* http://www.google.com/design/spec/material-design/introduction.html

# 7 Activity & Fragment

## 

1. PhoneWindow

2. DecorView

3. SystemLayout

4. ContentParent

5. ContentView

# 8 Performance

## systrace

## hierarchyviewer

## Instrumentation

`android.app.Instrumentation`类

* sendCharacterSync(int)
* sendKeyDownUpSync(int)
* sendPointerSync(MotionEvent)
* sendStringSync(String)

``` 
Instrumentation inst=new Instrumentation();
inst.sendPointerSync(MotionEvent.obtain(SystemClock.uptimeMillis(),SystemClock.uptimeMillis(), MotionEvent.ACTION_DOWN, 10, 10, 0));
inst.sendPointerSync(MotionEvent.obtain(SystemClock.uptimeMillis(),SystemClock.uptimeMillis(), MotionEvent.ACTION_UP, 10, 10, 0));
```

## 参考

* http://developer.android.com/tools/debugging/systrace.html
* http://developer.android.com/tools/debugging/debugging-ui.html

# 参考

1. [Guide: User Interface][1]
2. [Guide: Animation and Graphics][3]
3. [Guide: App Resources][2]
4. [Guide: App Components][4]
5. [AndroidUI基础教程][5]
6. [深入理解Android: 卷I]第八章[6]
7. [Skia官网][7]
8. [Skia源码][8]
9. [Material Design][9]

[1]: http://developer.android.com/guide/topics/ui/overview.html
[2]: http://developer.android.com/guide/topics/graphics/index.html
[3]: http://developer.android.com/guide/topics/resources/index.html
[4]: http://developer.android.com/guide/components/index.html
[5]: http://book.douban.com/subject/19985909/
[6]: http://book.douban.com/subject/6802440/
[7]: https://skia.org/
[8]: https://github.com/google/skia/
[9]: http://www.google.com/design/spec/material-design/introduction.html
