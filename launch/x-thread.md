# X launch thread (modeled on @jmtrivedi's Wave thread, May 31 2022)

## Post 1 (link card, no media; Rate·fm uses a middle dot so X doesn't auto-link it)
I'm making Swift Chat free: iMessage, rebuilt line for line as a SwiftUI view!

Swift Chat gives you photos, video and files in bubbles, tapback reactions, group chats with avatars, typing indicators, read receipts, and the keyboard you drag with your finger, out of the box.

And if you've used Rate·fm, Tally, or Jester, you've already seen it in action: their chats are all built with Swift Chat!

https://swift-chat.dev

## Reply 1 (one technical detail + side-by-side GIF: launch/side-by-side.gif)
Swift Chat isn't a SwiftUI List with bubbles in it. Underneath it's a UICollectionView, so the keyboard is corrected frame by frame as you drag it, and every bubble sits on a spring.

The left is dragging the keyboard down from the input bar, and the right is flinging the list:

## Reply 2 (getting started + link)
It's really easy to get started with Swift Chat.

Add the package in Xcode, then Chat { Message("Hey!", role: .user(id: "alex"), timestamp: .now) } is a finished conversation. Every modifier is on one page.

https://github.com/unionst/swift-chat

## Reply 3 (sign-off)
Finally, reach out if you have any questions, and feel free to open a GitHub issue if you find a bug or have a feature request!
