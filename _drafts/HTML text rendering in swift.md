---
layout: post
title: Rendering HTML using NSAttributedString - Swift
description: Using NSAttributedString to render HTML text without having to involve WebViews in Swift
---

Apparently, `NSAttributedString` supports HTML text rendering. A quick example:

~~~swift
// Get the html text you want to load
let text = 
"""
<h1>Introduction</h1><br>
<p>Welcome to the sample page on html rendering with <i>NSAttributedString</i> in <b>Swift</b><br>
You can find more details on <em>NSAttributedString</em> from the apple developer docs <a href=\"https://developer.apple.com\">here</a>
</p>
"""
// Wrapping in a do catch block to catch errors from constructor
do{
    let attrText = try NSAttributedString(data: text.data(using: .utf8)!,
                   options: [
                       .documentType: NSAttributedString.DocumentType.html,
                       .characterEncoding: String.Encoding.utf8.rawvalue
                   ]
                   documentAttributes: nil)
    // where textview is an IBOutlet/reference to a UITextView/UILabel/class that supports attributed strings.                   
    textview.attributedText = attrText
}
catch{
    print("HTML rendering failed with error \(error)")
}
~~~

This produces the following output in a `UITextView`:

![HTML rendered in UITextView](/assets/posts/images/html_text.png)

### Changing the font styles of the htnl

To adjust the font size of the text, you can simply wrap the html in a span element with the font size and font style.


{:.note}
The first time you try to render a HTML string using `NSAttributedString` it can take a while to load because of the services that need to be started for the rendering to happen. The subsequent renders are much faster.

Reference:-
* Stackoverflow posts
* Swift by Sundell 
* https://stackoverflow.com/questions/21166752/why-does-the-initial-call-to-nsattributedstring-with-an-html-string-take-over-10