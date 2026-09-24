# Draft: How Swift Chat drags the keyboard

For unionst.com/blog. Reword, then I'll add it as a post (code blocks and the two device videos included) and deploy. Slug: `how-swift-chat-drags-the-keyboard`.

---

**Title:** How Swift Chat drags the keyboard

**Summary:** The one iMessage behavior nobody gets right, and the four things it took to get it right.

---

In Messages you can put a finger on the keyboard and pull it down. The transcript comes with it. The input bar comes with it. Let go halfway and it springs back. It feels like one object.

In every chat app built on a SwiftUI `ScrollView`, the keyboard drops and the messages stay put. A gap opens. Then the layout snaps. Most people can't say what's wrong, but they can feel it.

This is what it took to make Swift Chat do it the Messages way. Four things.

## 1. Let UIKit own the scroll

`ScrollView` can dismiss the keyboard interactively since iOS 16 (`.scrollDismissesKeyboard(.interactively)`), but it never tells you where the keyboard is mid-drag. Without that number you can't move the content with it.

`UIScrollView` does tell you. Swift Chat's transcript is a `UICollectionView` with

```swift
collectionView.keyboardDismissMode = .interactive
```

and the SwiftUI you write is only the API on top. That one decision is why the rest of this is possible.

## 2. Correct the offset frame by frame

While the keyboard is being dragged, UIKit posts `keyboardWillChangeFrameNotification` on every frame. Swift Chat remembers the keyboard's last top edge and pushes the scroll view by exactly the amount it moved:

```swift
func handleKeyboardWillChangeFrame(_ notification: Notification) {
    guard
        view.window != nil,
        collectionView.isTracking,
        let frame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect
    else { return }

    let newMinY = frame.minY

    if let prevMinY = previousKeyboardMinY {
        let delta = prevMinY - newMinY
        if delta > 0 {
            collectionView.contentOffset.y += delta
        }
    }

    previousKeyboardMinY = newMinY
}
```

Two guards matter. `collectionView.isTracking` means a finger is down, so this only fires during a drag and not during the animated show and hide, which have their own paths. And `delta > 0` means the keyboard is rising; when it's falling, the content inset does the work and the offset is left alone, which is what stops the transcript from jittering when the drag reverses.

## 3. Start the drag from the input bar

In Messages the grab handle isn't the keyboard. You can start the drag on the input bar's top edge, and the transcript, the bar and the keyboard travel together as one welded assembly. Miss that and it feels like three views coming apart.

Swift Chat's input bar is the collection view's keyboard accessory, so a pan that starts on it is a pan on the keyboard as far as UIKit is concerned. No custom gesture recognizer, no forwarding. Getting it there without breaking the SwiftUI input bar's own text field was most of a week.

[video: keyboard-drag.mp4, 60fps, "A slow drag started on the bar, not the keyboard. The messages ride down with it the whole way."]

## 4. Handle the cancel

Lift your finger before the keyboard is gone and it comes back. Now the show notification fires while the user is technically still scrolling. If you treat that as a fresh show, the transcript jumps to the bottom mid-gesture.

```swift
func handleKeyboardWillShow(_ notification: Notification) {
    guard view.window != nil, !isInInteractiveTransition else { return }
    ...
    if isFirstAppearance && !pagination.isPaginationOn {
        scrollToBottom(animated: false)
    }
}
```

`isFirstAppearance` is the whole fix: scroll to the bottom only when the keyboard is arriving from fully hidden, never when it's snapping back from a cancelled drag. The hide path has the mirror of it, preserving `contentOffset` when the user is still scrolling or the view is decelerating.

## And the bar changes width

One more thing that isn't about dragging but happens at the same moment. At rest the input bar sits flush against the bottom of the screen and runs nearly edge to edge. Raise the keyboard and it narrows, pulling in on both sides.

A rounded rectangle flush against a rounded screen has to share the screen's corner radius or the two curves fight. The moment the bar floats above the keyboard, the correct radius changes, and so does the correct width. Apple does this. Almost nobody notices, and everybody notices when it's wrong.

[video: concentric-bar.mp4, "Watch the left and right edges of the bar, not the keyboard."]

## Use it

All of this is inside one view:

```swift
import SwiftChat

Chat(messages) { message in
    Message(message.text, role: message.role, timestamp: message.sentAt)
}
.onChatSend { text, media in
    await send(text, media)
}
```

Swift Chat is free, iOS 18 and later. Package at https://github.com/unionst/swift-chat, every modifier at https://unionst.com/swiftchat.
