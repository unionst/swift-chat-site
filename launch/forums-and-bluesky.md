# Swift Forums (forums.swift.org › Related Projects)

Title: Swift Chat: iMessage rebuilt line for line as a SwiftUI view (free, 1.0)

Body:

I spent about a year rebuilding iMessage in SwiftUI. Not something similar, the real thing: bubbles that only get a tail at the end of a run, typing dots that breathe in a wave, Delivered fading into Read, the keyboard you drag down from the top of the input bar, bubbles on springs when you fling the list. Underneath it is a UICollectionView, because List and ScrollView can't do the four things Messages does without thinking.

This week I made it free and tagged 1.0 as Swift Chat.

```swift
import SwiftChat

Chat(messages) { message in
    Message(message.text, role: message.role, timestamp: message.sentAt)
        .messageStatus(message.status)
}
.chatTypingIndicators(typing)
.chatInputCapabilities([.photoLibrary, .files])
.onChatSend { text, media in
    await conversation.send(text, media)
}
```

Photos, video and files in bubbles, tapback reactions, group chats with avatars, typing indicators, read receipts, pagination. iOS 18+, Swift 6.

- Package: https://github.com/unionst/swift-chat
- Site with every modifier on one page: https://unionst.com/swiftchat
- The post-mortem on why nobody wanted it the first time: https://sage.me/imessage

Happy to answer anything about the keyboard handling, which was the hardest part by a mile.

# Bluesky / Mastodon (same text, link card does the rest)

I spent a year rebuilding iMessage line for line in SwiftUI. Tails, typing dots, Delivered fading into Read, the keyboard you drag with your finger.

Then I shipped it and nobody wanted it. So this week I made it free.

github.com/unionst/swift-chat
