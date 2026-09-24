# Stack Overflow answers

Five questions that still get traffic and that Swift Chat solves. Each answer solves the question natively first, then one line on the package. Post from your own SO account, in your voice. Answer 1 first; it has the most views and the accepted answer is out of date.

---

## 1. Possible to achieve ScrollView interactive keyboard dismissal in SwiftUI?

https://stackoverflow.com/q/57383192 (6.9K views, accepted answer is a UIKit workaround from 2019)

Since iOS 16 this is one modifier:

```swift
ScrollView {
    LazyVStack { ... }
}
.scrollDismissesKeyboard(.interactively)
```

`.interactively` matches `UIScrollView.keyboardDismissMode = .interactive`: the keyboard follows your finger as you drag down past it. `.immediately` dismisses on the first scroll, `.never` disables it.

Two things it does not do, if you're building a chat screen:

1. It doesn't move the transcript with the keyboard. As the keyboard drops, the content stays where it was and a gap opens underneath. Messages does the opposite: the list rides down with the keyboard, frame by frame. SwiftUI doesn't expose the keyboard's live frame, so the only way to get that is a `UICollectionView` (or `UIScrollView`) with a keyboard-frame observer that adjusts `contentOffset` by the delta each frame.
2. The drag has to start on the keyboard. In Messages you can grab the input bar's top edge and pull; the bar, transcript and keyboard travel together. That also needs UIKit.

If you want both without writing the collection view yourself, Swift Chat (https://github.com/unionst/swift-chat) does exactly that under a SwiftUI API; the keyboard handling was most of the work.

---

## 2. SwiftUI chat app: the woes of reversed List and Context Menu

https://stackoverflow.com/q/61726424 (3.4K views)

Don't flip the list. The `scaleEffect(x: 1, y: -1)` trick is why the context menu preview renders upside down: the menu snapshots the cell, and the cell is inverted.

On iOS 17 and later you get bottom-first scrolling without flipping anything:

```swift
ScrollView {
    LazyVStack {
        ForEach(messages) { message in
            MessageRow(message)
                .contextMenu {
                    Button("Copy") { copy(message) }
                }
        }
    }
    .scrollTargetLayout()
}
.defaultScrollAnchor(.bottom)
```

`defaultScrollAnchor(.bottom)` starts the scroll view at the bottom and keeps it pinned there when content grows while the user is at the bottom. The rows are the right way up, so `contextMenu` works normally.

If you need iOS 16, keep the flip but put the context menu on an unflipped overlay: flip the row's content back with a second `scaleEffect(x: 1, y: -1)` on the inner view, and attach `.contextMenu` to that inner view rather than the row.

One more gotcha: a SwiftUI `contextMenu` inside a hosted cell steals taps meant for the cell. Swift Chat (https://github.com/unionst/swift-chat) sidesteps this by delivering the long-press menu through `UICollectionView`'s own context-menu path, which is worth knowing if you go the UIKit route.

---

## 3. How to make List reversed in SwiftUI

https://stackoverflow.com/q/58510507 (7.8K views)

If "reversed" means "start at the bottom like a chat," you no longer need `List` or the rotation trick. iOS 17:

```swift
ScrollView {
    LazyVStack(spacing: 8) {
        ForEach(messages) { message in
            MessageRow(message)
        }
    }
    .scrollTargetLayout()
}
.defaultScrollAnchor(.bottom)
```

The view opens scrolled to the bottom, new items at the end keep it pinned when the user is already there, and the user's position is preserved if they've scrolled up. That is the Messages behavior the older answers were emulating with `CGAffineTransform(rotationAngle: .pi)`.

If you truly need reverse layout order (newest element first in the array, rendered at the bottom), iterate `messages.reversed()` inside the same structure.

For the rest of a chat screen (bubble grouping, tails, typing dots, pagination at the top), Swift Chat (https://github.com/unionst/swift-chat) is a free package that wraps all of it in one view.

---

## 4. SwiftUI ScrollView Keyboard Avoidance

https://stackoverflow.com/q/66428889 (9.3K views)

The scroll position is lost because the keyboard shrinks the safe area, the `ScrollView` gets shorter, and SwiftUI keeps `contentOffset` from the top. You want it kept from the bottom. Two modifiers do that on iOS 17:

```swift
struct ChatScreen: View {
    @State private var draft = ""

    var body: some View {
        ScrollView {
            LazyVStack {
                ForEach(messages) { MessageRow($0) }
            }
            .scrollTargetLayout()
        }
        .defaultScrollAnchor(.bottom)
        .safeAreaInset(edge: .bottom) {
            TextField("Message", text: $draft)
                .textFieldStyle(.roundedBorder)
                .padding()
                .background(.bar)
        }
    }
}
```

`defaultScrollAnchor(.bottom)` anchors the content to the bottom edge, so when the keyboard raises the safe area the last message stays visible instead of sliding under the input. `safeAreaInset` puts the input bar in the safe area rather than in a `VStack`, which is what makes the scroll view resize instead of getting covered.

That gives you WhatsApp's "keep the bottom visible while typing." What it won't give you is the iMessage feel where the transcript rides the keyboard down during an interactive dismiss; that needs a keyboard-frame observer on a UIKit scroll view. Swift Chat (https://github.com/unionst/swift-chat) does that part if you'd rather not.

---

## 5. Bottom-first scrolling in SwiftUI

https://stackoverflow.com/q/58101865 (2.6K views, the top answer is the rotation hack)

The exact behavior you describe, "shifts down if an item is added when the user is at the bottom, holds position if they scrolled up," is what `defaultScrollAnchor(.bottom)` does on iOS 17:

```swift
ScrollView {
    LazyVStack {
        ForEach(messages) { MessageRow($0) }
    }
    .scrollTargetLayout()
}
.defaultScrollAnchor(.bottom)
```

No rotation, no `ScrollViewReader`, no `UIViewRepresentable`. When the user is at the bottom, appending to `messages` keeps them at the bottom. When they've scrolled up, the offset is preserved and the new row lands below the fold.

If you also want a "scroll to bottom" button to appear only when the user has scrolled away, `onScrollGeometryChange` (iOS 18) tells you the distance from the bottom. Swift Chat (https://github.com/unionst/swift-chat) bundles that button, plus the rest of a chat transcript, as a free package if you want to skip building it.
