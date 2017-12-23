uiautomator
===========

[![Join the chat at https://gitter.im/xiaocong/uiautomator](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/xiaocong/uiautomator?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![MIT License](https://img.shields.io/github/license/xiaocong/uiautomator.svg)](http://opensource.org/licenses/MIT)
[![build](https://img.shields.io/travis/xiaocong/uiautomator/master.svg)](https://travis-ci.org/xiaocong/uiautomator)
[![Coverage Status](https://img.shields.io/coveralls/xiaocong/uiautomator/master.svg)](https://coveralls.io/r/xiaocong/uiautomator?branch=master)
[![pypi](https://img.shields.io/pypi/v/uiautomator.svg)](https://pypi.python.org/pypi/uiautomator)
[![downloads](https://img.shields.io/pypi/dm/uiautomator.svg)](https://pypi.python.org/pypi/uiautomator)

This module is a Python wrapper of Android [uiautomator][] testing framework. It works on Android 4.1+ simply with Android device attached via adb, no need to install anything on Android device.
这模块是 [uiautomator][] 的 python 封装。只要通过 adb 连接上手机，就能轻松在 Android 4.1 以上的设备上运行，无需安装任何东西。

```python
from uiautomator import device as d

d.screen.on()
d(text="Clock").click()
```

## 安装

    $ pip install uiautomator


### 前提工作

- Install [Android SDK](http://developer.android.com/sdk/index.html), and set `ANDROID_HOME` environment to the correct path.
- 安装 [Android SDK](http://developer.android.com/sdk/index.html)，并且设置好 `ANDROID_HOME` 的环境变量。
- Enable ADB setting on device and connect your android device using usb with your PC.
- 
- Allow apps to install from unknown sources on device settings.

### import uiautomator

- If `ANDROID_SERIAL` is defined in environment, or there is only one device connected:
- 如果 `ANDROID_SERIAL` 已经被定义到环境变量，或只有一部设备被连接上：

  ```python
  from uiautomator import device as d
  ```

- Speficy the serial number when retrieving the device object
- 当检索到设备后需要给 Device 对象指定一个设备id(序列号)

  ```python
  from uiautomator import Device

  d = Device('014E05DE0F02000E')
  ```

- Speficy the adb server host and port running on other computer
- 若要运行在其他电脑上，需要去指定 adb server 的 host 与端口

    Although adb supports `-a` option since SDK 4.3, but now it has a bug on it. The only way to start adb server listenning on all interfaces instead of localhost, is `adb -a -P 5037 fork-server server &`
    虽然 SDK 4.3 的 adb 仍然支持 `-a` 选项，但现在这地方有个 bug。可以通过 `adb -a -P 5037 fork-server server &` 这个办法，去启动 adb server 去监听所有的接口从而代替使用 localhost，这是当前唯一的办法。

  ```python
  from uiautomator import Device

  d = Device('014E05DE0F02000E', adb_server_host='192.168.1.68', adb_server_port=5037)
  ```

---
**Notes**: In below examples, we use `d` represent the android device object.
**注意**： 在下面的例子，我们会使用 `d` 去代替 android 设备对象。 

### 目录
**[Basic API Usages](#basic-api-usages)** 
**[基本的 API 使用](#basic-api-usages)** 
  - **[Key Event Actions of the device](#key-event-actions-of-the-device)**  
  - **[设备的键盘事件操作](#key-event-actions-of-the-device)** 
  - **[Gesture interaction of the device](#gesture-interaction-of-the-device)** 
  - **[设备的手势交互](#gesture-interaction-of-the-device)**  
  - **[Screen Actions of the device](#screen-actions-of-the-device)** 
  - **[设备的屏幕操作](#screen-actions-of-the-device)** 

**[Watcher introduction](#watcher)**
**[Watcher 的介绍](#watcher)**

**[Handler introduction](#handler)**
**[Handler 的介绍](#handler)**

**[Selector introduction ](#selector)** 
**[Selector 的介绍 ](#selector)** 
  - **[How to selector the Child and sibling UI object](#child-and-sibling-ui-object)**
  - **[如何去选取元素子对象以及同级的对象](#child-and-sibling-ui-object)**
  - **[Get the specific UI object status and its information](#get-the-selected-ui-object-status-and-its-information)**
  - **[获取被指定元素的状态和他的信息](#get-the-selected-ui-object-status-and-its-information)**
  - **[Perform the click action on the seleted UI object](#perform-the-click-action-on-the-seleted-ui-object)**
  - **[对选中元素进行点击操作](#perform-the-click-action-on-the-seleted-ui-object)**
  - **[Gesture action for the specific ui object](#gesture-action-for-the-specific-ui-object)**
  - **[特定元素的手势操作](#gesture-action-for-the-specific-ui-object)**

### Basic API Usages
### 基本的 API 使用

This part show the normal actions of the device through some simple examples.
这部分通过简单的例子去介绍一些简单的操作。

* Retrieve the device info
* 获取设备的信息

  ```python
  d.info
  ```

  Below is a possible result:
  以下是展示的结果：

  ```
  { u'displayRotation': 0,
    u'displaySizeDpY': 640,
    u'displaySizeDpX': 360,
    u'currentPackageName': u'com.android.launcher',
    u'productName': u'takju',
    u'displayWidth': 720,
    u'sdkInt': 18,
    u'displayHeight': 1184,
    u'naturalOrientation': True
  }
  ```

#### Key Event Actions of the device
#### 设备的键盘事件操作

* Turn on/off screen
* 唤起/关闭 屏幕

  ```python
  # Turn on screen
  d.screen.on()
  # Turn off screen
  d.screen.off()
  ```

  Alternative method is:
  另一种使用方法：

  ```python
  # wakeup the device
  d.wakeup()
  # sleep the device, same as turning off the screen.
  d.sleep()
  ```

* Check if the screen is on or off
* 检查当前屏幕是否已被唤醒

    ```python
    if d.screen == "on":  # of d.screen != "off"
        # do something in case of screen on
        pass
    if d.screen == "off":  # of d.screen != "on"
        # do something in case of screen off
        pass
    ```

* Press hard/soft key
* 使用 物理键/虚拟键 

  ```python
  # press home key
  d.press.home()
  # press back key
  d.press.back()
  # the normal way to press back key
  d.press("back")
  # press keycode 0x07('0') with META ALT(0x02) on
  d.press(0x07, 0x02)
  ```

* Next keys are currently supported:
* 以下是当前支持的按键：

  - `home`
  - `back`
  - `left`
  - `right`
  - `up`
  - `down`
  - `center`
  - `menu`
  - `search`
  - `enter`
  - `delete`(or `del`)
  - `recent`(recent apps)
  - `volume_up`
  - `volume_down`
  - `volume_mute`
  - `camera`
  - `power`

  You can find all key code definitions at [Android KeyEvent](http://developer.android.com/reference/android/view/KeyEvent.html).
  你可以在 [Android KeyEvent](http://developer.android.com/reference/android/view/KeyEvent.html) 找到更多的 key code。

### Gesture interaction of the device
### 设备的手势交互

* Click the screen
* 点击

  ```python
  # click (x, y) on screen
  d.click(x, y)
  ```

* Long click the screen
* 长按

  ```python
  # long click (x, y) on screen
  d.long_click(x, y)
  ```

* Swipe
* 滑动

  ```python
  # swipe from (sx, sy) to (ex, ey)
  d.swipe(sx, sy, ex, ey)
  # swipe from (sx, sy) to (ex, ey) with 10 steps
  d.swipe(sx, sy, ex, ey, steps=10)
  ```

* Drag
* 拖拽

  ```python
  # drag from (sx, sy) to (ex, ey)
  d.drag(sx, sy, ex, ey)
  # drag from (sx, sy) to (ex, ey) with 10 steps
  d.drag(sx, sy, ex, ey, steps=10)
  ```

### Screen Actions of the device
### 设备的屏幕操作

* Retrieve/Set Orientation
* 恢复/设置 屏幕方向

  The possible orientation is:
  以下是屏幕的方向
  -   `natural` 或 `n`
  -   `left` 或 `l`
  -   `right` 或 `r`
  -   `upsidedown` 或 `u` (不能被设置)

  ```python
  # retrieve orientation, it may be "natural" or "left" or "right" or "upsidedown"
  orientation = d.orientation
  # set orientation and freeze rotation.
  # notes: "upsidedown" can not be set until Android 4.3.
  d.orientation = "l" # or "left"
  d.orientation = "r" # or "right"
  d.orientation = "n" # or "natural"
  ```

* Freeze/Un-Freeze rotation
* 允许/禁止 旋转屏幕

  ```python
  # freeze rotation
  d.freeze_rotation()
  # un-freeze rotation
  d.freeze_rotation(False)
  ```

* Take screenshot
* 截屏

  ```python
  # take screenshot and save to local file "home.png", can not work until Android 4.2.
  d.screenshot("home.png")
  ```

* Dump Window Hierarchy
* 将 Window 的层级 dump 下来

  ```python
  # dump the widown hierarchy and save to local file "hierarchy.xml"
  d.dump("hierarchy.xml")
  # or get the dumped content(unicode) from return.
  xml = d.dump()
  ```

* Open notification or quick settings
* 打开“通知栏”或“快速设置”

  ```python
  # open notification, can not work until Android 4.3.
  d.open.notification()
  # open quick settings, can not work until Android 4.3.
  d.open.quick_settings()
  ```

* Wait for idle or window update
* 等待 idle 或 window 更新

  ```python
  # wait for current window to idle
  d.wait.idle()
  # wait until window update event occurs
  d.wait.update()
  ```

### Watcher

You can register [watcher](http://developer.android.com/tools/help/uiautomator/UiWatcher.html) to perform some actions when a selector can not find a match.
当 selector 找不到能可匹配的元素时，你可以去注册一个 [watcher](http://developer.android.com/tools/help/uiautomator/UiWatcher.html) 去执行一些操作。

* Register Watcher
* 注册一个 Watcher

  When a selector can not find a match, uiautomator will run all registered watchers.
  当 selector 找不到可匹配的元素，uiautomator 就会运行所有已注册的 watcher。

  - Click target when conditions match
  - 当条件匹配则对目标进行点击

  ```python
  d.watcher("AUTO_FC_WHEN_ANR").when(text="ANR").when(text="Wait") \
                               .click(text="Force Close")
  # d.watcher(name) ## creates a new named watcher.
  #  .when(condition)  ## the UiSelector condition of the watcher.
  #  .click(target)  ## perform click action on the target UiSelector.
  ```

  - Press key when conditions match
  - 当条件匹配则点击按键
  ```python
  d.watcher("AUTO_FC_WHEN_ANR").when(text="ANR").when(text="Wait") \
                               .press.back.home()
  # Alternative way to define it as below
  d.watcher("AUTO_FC_WHEN_ANR").when(text="ANR").when(text="Wait") \
                               .press("back", "home")
  # d.watcher(name) ## creates a new named watcher.
  #  .when(condition)  ## the UiSelector condition of the watcher.
  #  .press.<keyname>.....<keyname>.()  ## press keys one by one in sequence.
  #  Alternavie way defining key sequence is press(<keybname>, ..., <keyname>)
  ```

* Check if the named watcher triggered
* 检查被指定的 wathcer 是否被触发

  A watcher is triggered, which means the watcher was run and all its conditions matched.
  当一个 watcher 被触发，也就意味着你的 watcher 在运行中且触发的条件配匹配到了。

  ```python
  d.watcher("watcher_name").triggered
  # true in case of the specified watcher triggered, else false
  ```

* Remove named watcher
* 移除指定的 wathcer

  ```python
  # remove the watcher
  d.watcher("watcher_name").remove()
  ```

* List all watchers
* 列出所有的 watcher

  ```python
  d.watchers
  # a list of all registered wachers' names
  ```

* Check if there is any watcher triggered
* 检查是否有哪个 watcher 被触发了

  ```python
  d.watchers.triggered
  #  true in case of any watcher triggered
  ```

* Reset all triggered watchers
* 充值所有被触发的 watcher

  ```python
  # reset all triggered watchers, after that, d.watchers.triggered will be false.
  d.watchers.reset()
  ```

* Remvoe watchers
* 移除所有的 watcher

  ```python
  # remove all registered watchers
  d.watchers.remove()
  # remove the named watcher, same as d.watcher("watcher_name").remove()
  d.watchers.remove("watcher_name")
  ```

* Force to run all watchers
* 直接运行所有的 watcher

  ```python
  # force to run all registered watchers
  d.watchers.run()
  ```

### Handler
### Handler

The functionality of handler is same as Watcher, except it is implemented ourside of Android uiautomator. The most different usage between handler and watcher is, handler can use customized callback function.
handler 通过 Android uiautomator 以外的方式去实现，除此以外它的功能其实就跟 Watcher 差不多了。handler 与 watcher 之间用法的最大不同，就是 handler 可以使用自定义的回调函数。


```python
def fc_close(device):
  if device(text='Force Close').exists:
    device(text='Force Close').click()
  return True  # return True means to break the loop of handler callback functions.

# turn on the handler callback function
d.handlers.on(fc_close)

# turn off the handler callback function
d.handlers.off(fc_close)
```

### Selector

Selector is to identify specific ui object in current window.
Selector 就是用于在当前的 window 下标记被指定的 ui 对象。

```python
# To seleted the object ,text is 'Clock' and its className is 'android.widget.TextView'
d(text='Clock', className='android.widget.TextView')
```

Selector supports below parameters. Refer to [UiSelector java doc](http://developer.android.com/tools/help/uiautomator/UiSelector.html) for detailed information.
Selector 支持一下参数。更多相关细节请参考 [UiSelector java doc](http://developer.android.com/tools/help/uiautomator/UiSelector.html)。

*  `text`, `textContains`, `textMatches`, `textStartsWith`
*  `className`, `classNameMatches`
*  `description`, `descriptionContains`, `descriptionMatches`, `descriptionStartsWith`
*  `checkable`, `checked`, `clickable`, `longClickable`
*  `scrollable`, `enabled`,`focusable`, `focused`, `selected`
*  `packageName`, `packageNameMatches`
*  `resourceId`, `resourceIdMatches`
*  `index`, `instance`

#### Child and sibling UI object
#### 如何去选取 UI 子对象以及同级的对象

* child
* 子对象

  ```python
  # get the child or grandchild
  d(className="android.widget.ListView").child(text="Bluetooth")
  ```

* sibling
* 同级对象

  ```python
  # get sibling or child of sibling
  d(text="Google").sibling(className="android.widget.ImageView")
  ```

* child by text or description or instance
* 通过 text 或 description 或实例去指定子对象

  ```python
  # get the child match className="android.widget.LinearLayout"
  # and also it or its child or grandchild contains text "Bluetooth"
  d(className="android.widget.ListView", resourceId="android:id/list") \
   .child_by_text("Bluetooth", className="android.widget.LinearLayout")

  # allow scroll search to get the child
  d(className="android.widget.ListView", resourceId="android:id/list") \
   .child_by_text(
      "Bluetooth",
      allow_scroll_search=True,
      className="android.widget.LinearLayout"
    )
  ```

  - `child_by_description` is to find child which or which's grandchild contains
      the specified description, others are the same as `child_by_text`.
  - `child_by_description` 用于找到包含指定 description 的子对象或某个父对象的子对象，`child_by_text` 有同样的功能。

  - `child_by_instance` is to find child which has a child UI element anywhere
      within its sub hierarchy that is at the instance specified. It is performed
      on visible views without **scrolling**.
  - `child_by_instance` 就是在指定的实例的任何一个地方的子类层级结构中去查找子元素。它只能执行在可视的视图中，所以**滚动列表**以外的地方就无法查看了。
  ******** 上面这句还得斟酌下****************

  See below links for detailed information:
  查看以下链接去获取更多相关信息：

  -   [UiScrollable](http://developer.android.com/tools/help/uiautomator/UiScrollable.html), `getChildByDescription`, `getChildByText`, `getChildByInstance`
  -   [UiCollection](http://developer.android.com/tools/help/uiautomator/UiCollection.html), `getChildByDescription`, `getChildByText`, `getChildByInstance`

  Above methods support chained invoking, e.g. for below hierarchy
  以上方法支持都支持链式调用，例如以下层级结构

  ```xml
  <node index="0" text="" resource-id="android:id/list" class="android.widget.ListView" ...>
    <node index="0" text="WIRELESS & NETWORKS" resource-id="" class="android.widget.TextView" .../>
    <node index="1" text="" resource-id="" class="android.widget.LinearLayout" ...>
      <node index="1" text="" resource-id="" class="android.widget.RelativeLayout" ...>
        <node index="0" text="Wi‑Fi" resource-id="android:id/title" class="android.widget.TextView" .../>
      </node>
      <node index="2" text="ON" resource-id="com.android.settings:id/switchWidget" class="android.widget.Switch" .../>
    </node>
    ...
  </node>
  ```
  ![settings](https://raw.github.com/xiaocong/uiautomator/master/docs/img/settings.png)

  We want to click the switch at the right side of text 'Wi‑Fi' to turn on/of Wi‑Fi.
  As there are several switches with almost the same properties, so we can not use like
  `d(className="android.widget.Switch")` to select the ui object. Instead, we can use
  code below to select it.  
  我们想把名字叫‘wi-fi’的开关打开，就要把开关拨到右边。
  然而大部分的开关都有相同的属性，所以我们不能使用诸如`d(className="android.widget.Switch")` 的方式去选择 ui 对象，我们应该使用以下去查找，如以下代码所示：

  ```python
  d(className="android.widget.ListView", resourceId="android:id/list") \
    .child_by_text("Wi‑Fi", className="android.widget.LinearLayout") \
    .child(className="android.widget.Switch") \
    .click()
  ```

* relative position
* 相对位置

  Also we can use the relative position methods to get the view: `left`, `right`, `top`, `bottom`.
  同样我们也可以使用相对位置去找到想要的 view：`left`, `right`, `top`, `bottom`。

  -   `d(A).left(B)`, means selecting B on the left side of A.
  -   `d(A).right(B)`, means selecting B on the right side of A.
  -   `d(A).up(B)`, means selecting B above A.
  -   `d(A).down(B)`, means selecting B under A.

  So for above case, we can write code alternatively:
  突然发现前面我们说过的那个wifi的例子，我们还能用另一种方式去实现：

  ```python
  ## select "switch" on the right side of "Wi‑Fi"
  d(text="Wi‑Fi").right(className="android.widget.Switch").click()
  ```

* Multiple instances
* 多个实例

  Sometimes the screen may contain multiple views with the same e.g. text, then you will
  have to use "instance" properties in selector like below:
  有时在一个视图中可能会包含多个有着相同属性（例如:text）的元素，所以你不得在 selector 中使用 “instance” 属性，如下代码所示：

  ```python
  d(text="Add new", instance=0)  # which means the first instance with text "Add new"
  ```

  However, uiautomator provides list like methods to use it.
  然而也提供一个这样的列表方法去供我们使用。
  ```python
  # get the count of views with text "Add new" on current screen
  d(text="Add new").count

  # same as count property
  len(d(text="Add new"))

  # get the instance via index
  d(text="Add new")[0]
  d(text="Add new")[1]
  ...

  # iterator
  for view in d(text="Add new"):
      view.info  # ...
  ```

  **Notes**: when you are using selector like a list, you must make sure the screen
  keep unchanged, else you may get ui not found error.
  **注意**：当你使用 selector 对列表进行操作时，你必须确保当前屏幕内的视图不会改变，否则你会出现超找不到元素的异常。

#### Get the selected ui object status and its information
#### 获取被指定 UI 对象的状态和他的信息

* Check if the specific ui object exists
* 检查指定的元素是否存在

  ```python
  d(text="Settings").exists # True if exists, else False
  d.exists(text="Settings") # alias of above property.
  ```

* Retrieve the info of the specific ui object
* 检索指定元素的信息

  ```python
  d(text="Settings").info
  ```

  Below is a possible result:
  下面是返回的结果：

  ```
  { u'contentDescription': u'',
    u'checked': False,
    u'scrollable': False,
    u'text': u'Settings',
    u'packageName': u'com.android.launcher',
    u'selected': False,
    u'enabled': True,
    u'bounds': {u'top': 385,
                u'right': 360,
                u'bottom': 585,
                u'left': 200},
    u'className': u'android.widget.TextView',
    u'focused': False,
    u'focusable': True,
    u'clickable': True,
    u'chileCount': 0,
    u'longClickable': True,
    u'visibleBounds': {u'top': 385,
                       u'right': 360,
                       u'bottom': 585,
                       u'left': 200},
    u'checkable': False
  }
  ```
* Set/Clear text of editable field
* 设置/清除 输入框的文字

  ```python
  d(text="Settings").clear_text()  # clear the text
  d(text="Settings").set_text("My text...")  # set the text
  ```

#### Perform the click action on the seleted ui object
#### 对选中的 UI 对象进行点击操作

* Perform click on the specific ui object
* 对指定的元素点击

  ```python
  # click on the center of the specific ui object
  d(text="Settings").click()
  # click on the bottomright corner of the specific ui object
  d(text="Settings").click.bottomright()
  # click on the topleft corner of the specific ui object
  d(text="Settings").click.topleft()
  # click and wait until the new window update
  d(text="Settings").click.wait()
  ```

* Perform long click on the specific ui object
* 对指定的元素长按

  ```python
  # long click on the center of the specific ui object
  d(text="Settings").long_click()
  # long click on the bottomright corner of the specific ui object
  d(text="Settings").long_click.bottomright()
  # long click on the topleft corner of the specific ui object
  d(text="Settings").long_click.topleft()
  ```

#### Gesture action for the specific ui object
#### 指定元素的手势操作

* Drag the ui object to another point or ui object 
* 拖拽某个元素到其他位置，或者移动到某个元素处

  ```python
  # notes : drag can not be set until Android 4.3.
  # drag the ui object to point (x, y)
  d(text="Settings").drag.to(x, y, steps=100)
  # drag the ui object to another ui object(center)
  d(text="Settings").drag.to(text="Clock", steps=50)
  ```

* Swipe from the center of the ui object to its edge
* 从元素的中心滑动到它的边缘位置

  Swipe supports 4 directions:
  Siwpe 支持4个方位：
  -   `left`
  -   `right`
  -   `top`
  -   `bottom`

  ```python
  d(text="Settings").swipe.right()
  d(text="Settings").swipe.left(steps=10)
  d(text="Settings").swipe.up(steps=10)
  d(text="Settings").swipe.down()
  ```

* Two point gesture from one point to another
* 双指手势操作，从一点移动到另一点

  ```python
  d(text="Settings").gesture((sx1, sy1), (sx2, sy2)) \
                    .to((ex1, ey1), (ex2, ey2))
  ```

* Two point gesture on the specific ui object
* 对指定的元素去双指手势操作

  Supports two gestures:
  支持的双指手势
  - `In`, from edge to center
  - `Out`, from center to edge
  - `In`, 从外到里
  - `Out`, 从里到外

  ```python
  # notes : pinch can not be set until Android 4.3.
  # from edge to center. here is "In" not "in"
  d(text="Settings").pinch.In(percent=100, steps=10)
  # from center to edge
  d(text="Settings").pinch.Out()
  ```

* 3 point gesture
* 3 指手势操作

  ```python
  d().gestureM((sx1, sy1), (sx2, sy2),(sx3, sy3)) \
                    .to((ex1, ey1), (ex2, ey2),(ex3,ey3))
  d().gestureM((100,200),(300,200),(600,200),(100,600),(300,600),(600,900))
  ```

* Wait until the specific ui object appears or gone
* 等待指定元素的出现或消失

  ```python
  # wait until the ui object appears
  d(text="Settings").wait.exists(timeout=3000)
  # wait until the ui object gone
  d(text="Settings").wait.gone(timeout=1000)
  ```

* Perform fling on the specific ui object(scrollable)
* 在滑动的列表元素上执行 fling 操作

  Possible properties:
  以下为可用属性：
  - `horiz` or `vert`
  - `forward` or `backward` or `toBeginning` or `toEnd`

  ```python
  # fling forward(default) vertically(default) 
  d(scrollable=True).fling()
  # fling forward horizentally
  d(scrollable=True).fling.horiz.forward()
  # fling backward vertically
  d(scrollable=True).fling.vert.backward()
  # fling to beginning horizentally
  d(scrollable=True).fling.horiz.toBeginning(max_swipes=1000)
  # fling to end vertically
  d(scrollable=True).fling.toEnd()
  ```

* Perform scroll on the specific ui object(scrollable)
* 对可滑动的元素执行 scroll 操作

  Possible properties:
  以下为可用属性：
  - `horiz` or `vert`
  - `forward` or `backward` or `toBeginning` or `toEnd`, or `to`

  ```python
  # scroll forward(default) vertically(default)
  d(scrollable=True).scroll(steps=10)
  # scroll forward horizentally
  d(scrollable=True).scroll.horiz.forward(steps=100)
  # scroll backward vertically
  d(scrollable=True).scroll.vert.backward()
  # scroll to beginning horizentally
  d(scrollable=True).scroll.horiz.toBeginning(steps=100, max_swipes=1000)
  # scroll to end vertically
  d(scrollable=True).scroll.toEnd()
  # scroll forward vertically until specific ui object appears
  d(scrollable=True).scroll.to(text="Security")
  ```

---

## Contribution

- Fork the repo, and clone to your computer.
- Checkout a new branch from `develop` branch
- Install requirements: `pip install -r requirements.txt`
- Make your changes, and update tests. Don't forget adding your name at the end of 'Contributors' section
- Pass all tests and your code must be covered: `tox`.
- Commit your changes and submit pull request to `develop` branch.

## Contributors

- Xiaocong He ([@xiaocong][])
- Yuanyuan Zou ([@yuanyuan][])
- Qian Jin ([@QianJin2013][])
- Xu Jingjie ([@xiscoxu][])
- Xia Mingyuan ([@mingyuan-xia][])

[@xiaocong]: https://github.com/xiaocong
[@yuanyuan]: https://github.com/yuanyuanzou
[@QianJin2013]: https://github.com/QianJin2013
[@xiscoxu]: https://github.com/xiscoxu
[@mingyuan-xia]: https://github.com/mingyuan-xia

## Issues & Discussion

If you have any bug reports or annoyances please report them to our issue tracker at [github issues][].

## Notes

- Android [uiautomator][] works on Android 4.1+, so before using it, make sure your device is Android4.1+.
- Some methods are only working on Android 4.2/4.3, so you'd better read detailed [java documentation of uiautomator](http://developer.android.com/tools/help/uiautomator/index.html) before using it.
- The module uses [uiautomator-jsonrpc-server](https://github.com/xiaocong/android-uiautomator-server) as its daemon to communicate with devices.
- The module is only tested on python2.7/3.2/3.3/pypy.

## FAQ

- Could not start JSONRPC server: `raise IOError("RPC server not started!")`

    It may be caused by network, device, or environment. So when you meet the issue, please follow below steps and try to manually start the JSONRPC server.

    1. Follow steps at [uiautomator-jsonrpc-server](https://github.com/xiaocong/android-uiautomator-server#build) to start jsonrpc server.

    2. Check if jsonrpc server is ok:

            curl -d '{"jsonrpc":"2.0","method":"deviceInfo","id":1}' localhost:9008/jsonrpc/0

        If you see message like `{"jsonrpc":"2.0","id":1,"result":{"currentPackageName":"android","displayHeight":1280,"displayRotation":0,"displaySizeDpX":0,"displaySizeDpY":0,"displayWidth":720,"productName":"falcon","sdkInt":17,"naturalOrientation":true}}`, it means the server is up.

    If you can manually start the jsonrpc server, but your script always meets `IOError("RPC server not started!")`, please submit an issue at [github issues][].

- Error `httplib.BadStatusLine: ''`

    JsonRPC server needs to access temp directory on device, but on some low tier devices, it may meet error during accessing temp files without SD-CARD attached. So if you met the error, please insert a SD-CARD and then try again.

## License

MIT

[uiautomator]: http://developer.android.com/tools/testing/testing_ui.html "Android ui testing"
[github issues]: https://github.com/xiaocong/uiautomator/issues
