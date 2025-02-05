# Sharry — Share Extension Sample App

## Problem

The share extension defined in this project is supposed to activate for CSV files (which works fine) and for selected text (which does not work).

It should also activate when the user selects text in an app like Notes or TextEdit 
and selects "Share…" from the context menu.
**This does not work.**

It is not clear how the `NSExtensionActivationRule` in the `Info.plist` should be formattated 
to achieve activation of the extnsion with specific file types and user selected text.


## How to test

In the attached XCode project run the "ShareExtension-Mac" target on macOS.
You might need to change the code signing identity first.

When asked for the app to attached to, select Finder to validate that the extension activates
correctly for CSV files (attached in "Sample Files" folder).

To test the user text activate, run the "ShareExtension-Mac" on Notes or TextEdit.
Select some text there, open the context menu, and select "Share…".
Here the app will not appear / activate.



## Details
This is related to a question on the Apple Developer Forums
https://developer.apple.com/forums/thread/772230


The supported file types have been defined in as a predicate query according to Apple’s example on
https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html#//apple_ref/doc/uid/TP40014214-CH21-SW8

This works all fine on iOS. But on macOS, the app is not shown as a target in the share sheet when the user selects text in other apps and tries to share that text via the context menu.

This is how the Info.plist of the Mac share extension looks like:

```XML
...
<plist version="1.0">
<dict>
	<key>NSExtension</key>
	<dict>
		<key>NSExtensionAttributes</key>
		<dict>
			<key>NSExtensionActivationRule</key>
			<string>SUBQUERY (
    extensionItems,
    $extensionItem,
    SUBQUERY (
        $extensionItem.attachments,
        $attachment,
        ANY $attachment.registeredTypeIdentifiers UTI-CONFORMS-TO "public.plain-text" ||
        ANY $attachment.registeredTypeIdentifiers UTI-CONFORMS-TO "public.delimited-values-text"
    ).@count == $extensionItem.attachments.@count
).@count &gt;= 1</string>
	</dict>
	...
</dict>
</plist>
```

There is a `NSExtensionActivationSupportsText` but it seems this cannot be combined with a subquery rule.
