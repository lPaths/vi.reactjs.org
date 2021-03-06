---
id: events
title: SyntheticEvent
permalink: docs/events.html
layout: docs
category: Reference
---

Tài liệu này nhằm giải thích `SyntheticEvent` trong Hệ thống Event của React. Xem [Handling Events](/docs/handling-events.html) để biết thêm chi tiết.

## Tổng quan {#overview}

Các hàm xử lý sự kiện sẽ được truyền vào một instance của `SyntheticEvent` (một lớp bọc các event để triệt tiêu sự khác nhau về event của các trình duyệt). Nó có giao diện (interface) tương tự như một event của trình duyệt, bao gồm `stopPropagation()`, và `preventDefault()` và hoạt động giống nhau trên mọi trình duyệt.

Nếu bạn cần lấy event từ trình duyệt vì một lý do nào đó, chỉ cần sử dụng thuộc tính `nativeEvent` là được. Mọi `SyntheticEvent` object đều có những thuộc tính sau:

```javascript
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
DOMEventTarget target
number timeStamp
string type
```

> Lưu ý:
>
> Từ bản v0.14, hàm xử lý event trả về `false` sẽ không ngừng sự lan truyền của event đó. `e.stopPropagation()` hoặc `e.preventDefault()` phải được gọi để ngăn event đó đi tiếp.

### Gộp Event {#event-pooling}

Một `SyntheticEvent` sẽ được gộp lại nghĩa là `SyntheticEvent` object sẽ được sử dụng lại và tất cả thuộc tính trong object đó sẽ bị gán null sau khi hàm xử lý event chạy xong.
Việc làm này nhằm tăng hiệu suất.
Vì vậy, bạn không thể dùng truy cập sự kiện theo phương pháp không đồng bộ. Ví dụ:

```javascript
function onClick(event) {
  console.log(event); // => object có thuộc tính sẽ bị null sau khi sử dụng.
  console.log(event.type); // => "click"
  const eventType = event.type; // => "click"

  setTimeout(function() {
    console.log(event.type); // => null
    console.log(eventType); // => "click"
  }, 0);

  // Không chạy. this.state.clickEvent sẽ là một object có tất cả thuộc tính null.
  this.setState({clickEvent: event});

  // Bạn vẫn có thể truy xuất các thuộc tính của event.
  this.setState({eventType: event.type});
}
```

> Lưu ý:
>
> Nếu bạn muốn giữ lại thuộc tính của event sau khi chạy hàm xử lý, bạn phải gọi `event.persist()` để tách event object ra (event object mới sẽ được tạo) và giữ lại các thuộc tính của nó.

## Các Events được hỗ trợ {#supported-events}

React chuẩn hoá các event để chúng hoạt động giống nhau trên các trình duyệt khác nhau.

Các hàm xử lý event được thực thi trong lúc event bubble lên. Nếu muốn gán hàm xử lý vào lúc capture xuống của event, thêm hậu tố Capture vào sau tên event, ví dụ `onClickCapture`.

- [Clipboard Events](#clipboard-events)
- [Composition Events](#composition-events)
- [Keyboard Events](#keyboard-events)
- [Focus Events](#focus-events)
- [Form Events](#form-events)
- [Mouse Events](#mouse-events)
- [Pointer Events](#pointer-events)
- [Selection Events](#selection-events)
- [Touch Events](#touch-events)
- [UI Events](#ui-events)
- [Wheel Events](#wheel-events)
- [Media Events](#media-events)
- [Image Events](#image-events)
- [Animation Events](#animation-events)
- [Transition Events](#transition-events)
- [Events khác](#other-events)

* * *

## Tài liệu tham khảo {#reference}

### Clipboard Events {#clipboard-events}

Tên Event:

```
onCopy onCut onPaste
```

Thuộc tính:

```javascript
DOMDataTransfer clipboardData
```

* * *

### Composition Events {#composition-events}

Tên Event:

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

Thuộc tính:

```javascript
string data

```

* * *

### Event bàn phím {#keyboard-events}

Tên Event:

```
onKeyDown onKeyPress onKeyUp
```

Thuộc tính:

```javascript
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

Thuộc tính `key` sẽ có những giá trị như trong [DOM Level 3 Events spec](https://www.w3.org/TR/uievents-key/#named-key-attribute-values).

* * *

### Focus Events {#focus-events}

Tên Event:

```
onFocus onBlur
```

Những event focus sẽ hoạt động trên tất cả React DOM, không chỉ trong form.

Thuộc tính:

```javascript
DOMEventTarget relatedTarget
```

* * *

### Form Events {#form-events}

Tên Event:

```
onChange onInput onInvalid onSubmit
```

Xem thêm thông tin về onChange event [Forms](/docs/forms.html).

* * *

### Event chuột {#mouse-events}

Tên Event:

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

Event `onMouseEnter` và `onMouseLeave` phát ra từ element được rời đi tới element được đi vào thay vì bubble như bình thường và không có giai đoạn capture.

Thuộc tính:

```javascript
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
```

* * *

### Pointer Events {#pointer-events}

Tên Event:

```
onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture
onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut
```

Event `onPointerEnter` và `onPointerLeave` phát ra từ element được rời đi tới element được đi vào thay vì bubble như bình thường và không có giai đoạn capture.

Thuộc tính:

Như trong [W3 spec](https://www.w3.org/TR/pointerevents/), Pointer events được thêm vào từ [Mouse Events](#mouse-events) và có những thuộc tính sau:

```javascript
number pointerId
number width
number height
number pressure
number tangentialPressure
number tiltX
number tiltY
number twist
string pointerType
boolean isPrimary
```

Lưu ý về hỗ trợ trình duyệt

Pointer events chưa được hỗ trợ trong tất cả trình duyệt (tại thời điểm viết bài này, những trình duyệt được hỗ trợ: Chrome, Firefox, Edge, and Internet Explorer)). React không cố để polyfill cho những trình duyệt khác vì nó sẽ làm tăng dung lượng `react-dom` một cách đáng kể.

Nếu bạn cần pointer event, chúng tôi khuyến khích sử dụng pointer event polyfill từ bên thứ ba cho nó.

* * *

### Selection Events {#selection-events}

Tên Event:

```
onSelect
```

* * *

### Touch Events {#touch-events}

Tên Event:

```
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

Thuộc tính:

```javascript
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

* * *

### UI Events {#ui-events}

Tên Event:

```
onScroll
```

Thuộc tính:

```javascript
number detail
DOMAbstractView view
```

* * *

### Wheel Events {#wheel-events}

Tên Event:

```
onWheel
```

Thuộc tính:

```javascript
number deltaMode
number deltaX
number deltaY
number deltaZ
```

* * *

### Media Events {#media-events}

Tên Event:

```
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted
onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay
onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend
onTimeUpdate onVolumeChange onWaiting
```

* * *

### Image Events {#image-events}

Tên Event:

```
onLoad onError
```

* * *

### Animation Events {#animation-events}

Tên Event:

```
onAnimationStart onAnimationEnd onAnimationIteration
```

Thuộc tính:

```javascript
string animationName
string pseudoElement
float elapsedTime
```

* * *

### Transition Events {#transition-events}

Tên Event:

```
onTransitionEnd
```

Thuộc tính:

```javascript
string propertyName
string pseudoElement
float elapsedTime
```

* * *

### Other Events {#other-events}

Tên Event:

```
onToggle
```
