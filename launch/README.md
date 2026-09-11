# Swift Chat launch kit

Drafts for the channels that need a human account. Post in this order over a week; each one links to swift-chat.dev.

## Show HN

Title: Show HN: Swift Chat – the iOS Messages app as a SwiftUI view

Body:

I've built chat screens for a lot of iOS apps (Polymarket, Whop, others) and every time the same 20% took 80% of the effort: bubble tails that land on the right message, the keyboard that follows your finger, the scroll that feels like Messages instead of a UITableView, typing dots, read receipts.

Swift Chat is that 20% as one SwiftUI view. You hand it any collection of Identifiable values and a closure that turns each one into a Message; sending, typing, attachments and pagination are modifiers. A UIKit collection view does the work underneath, so it holds up with thousands of messages.

Site: https://swift-chat.dev
Repo: https://github.com/unionst/swift-chat
Everything it does is on one page, including an llms.txt so your coding agent can add it for you.

Happy to answer anything about the keyboard handling, which was the hardest part by a mile.

## Reddit (r/SwiftUI, r/iOSProgramming)

Title: I made the Messages app as a drop-in SwiftUI view: tails, typing dots, read receipts, keyboard handling

Body: same as Show HN, shorter. Lead with the hero GIF or the swift-chat.png render. Reply to every comment in the first two hours.

## X / Threads / Mastodon

The Messages app, as a SwiftUI view.

Tails that land on the right bubble. Typing dots. Read receipts. Attachments. The keyboard that follows your finger.

Chat(messages) { Message($0.text, role: $0.role, timestamp: $0.sentAt) }

iOS 18+. swift-chat.dev

(attach the hero video or swift-chat.png)

## Swift Forums (Related Projects)

Title: Swift Chat: an iMessage-faithful chat UI package for SwiftUI

Body: two paragraphs from the Show HN text, plus the install snippet and a link to the modifier reference on the site.

## Newsletters to email

- iOS Dev Weekly (Dave Verwer): submit via the site's link form.
- SwiftLee weekly, Swift Weekly Brief, iOS Goodies: each has a submit form or GitHub issue.
- Indie Dev Monday: reply to their call for projects.

## Awesome lists (open a PR)

- https://github.com/chinsyo/awesome-swiftui
- https://github.com/vlondon/awesome-swiftui
- https://github.com/matteocrippa/awesome-swift (UI › Chat section)

One line each: `Swift Chat - The Messages app as a SwiftUI view: tails, typing indicators, read receipts, attachments, keyboard handling. https://swift-chat.dev`

## Directories

- Context7: https://context7.com/add-library (repo has a context7.json; submit the GitHub URL).
- Swift Package Index: PR already open, https://github.com/SwiftPackageIndex/PackageList/pull/15156
- Product Hunt: schedule for a Tuesday. Tagline "The Messages app, as a SwiftUI view." First comment is the Show HN text.

## Follow-ups that compound

- A 15-second screen recording of the real thing on device, posted everywhere the text goes.
- One blog post on unionst.com: "How Swift Chat handles the keyboard", the deepest technical part. That post is what gets cited.
- Answer Stack Overflow questions tagged swiftui + chat with a working snippet and a link.
