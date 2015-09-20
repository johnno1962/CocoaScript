
# SafeScript

When you're as bad a typist as I am one of the frustrations of using dynamically typed
scripting languages is when a run time error occurs that could have been picked up 
by a compiler. Type inference has also reduced the burden coding in a type-safe
language so perhaps it is time to see if a language such as Swift can be
pressed into service in a scripting environment.

From it's beginning you've been able [script in swift](http://nomothetis.svbtle.com/swift-for-scripting)
and others have had [some success](https://realm.io/news/swift-scripting/)
but it's fairly heavy going without autocompletion and dependency management.
`SafeScript` is small binary and a couple of scripts that looks to address 
these problems. Pods are specified in a comment after an import statement 
in your script and are downloaded automatically when a script is run.
For autocompletion, scripts are converted into a mini Xcode framework
project with the correct framework search path.

```Swift
    #!/usr/bin/env safescript

    import Cocoa
    import Alamofire // pod
    import Box // pod 'Box', :head

    print( "Hello SafeScript" )
```

This is overseen by the `safescript` binary and a script `prepare.rb` that is
run before the script proper. prepare.rb loads pods, rebuilds the script's
framework if required then jumps into it's main.swift to start execution.

As all of Cocoa is available, a UI component can be added to a script by
adding a `MainMenu.xib` and AppDelegate.swift to the script project.

```Swift
    #!/usr/bin/env safescript

    import Cocoa
    import WebKit

    if Process.arguments.count < 2 {
        print( "Please specify URL" )
        exit(0)
    }

    let url = Process.arguments[1]

    NSApplicationMain( 0,  UnsafeMutablePointer<UnsafeMutablePointer<Int8>>(nil) )

    class AppDelegate: NSObject, NSApplicationDelegate {

        @IBOutlet weak var window: NSWindow!
        @IBOutlet weak var webView: WebView!

        func applicationDidFinishLaunching(aNotification: NSNotification) {
            // Insert code here to initialize your application
            NSApp.applicationIconImage = NSImage( named:"Swift" )
            webView.mainFrame.loadRequest(NSURLRequest(URL: NSURL(string: url)!))
            NSApplication.sharedApplication().activateIgnoringOtherApps( true )
        }

        func applicationWillTerminate(aNotification: NSNotification) {
            // Insert code here to tear down your application
        }

    }
```

To use SafeScript, download and build this project and make sure that `$HOME/bin`
is in your UNIX `PATH`. You can then type `safescript path_to_script` and it creates
a blank script, an Xcode framework project then builds and runs it. If you prefer 
editing in Xcode type `path_to_script -edit` to open the auto-created project.
To get started there is a small example script `browse` in the project directory.

To use dependencies the `CocoaPods` gem and it's `Rome` plugin need to be installed.

```
    $ sudo gem install cocoapods
    $ sudo gem install cocoapods-rome
```

Use a !pod comment in framework import to force updating a particular pod later.

### Reloader

SafeScript contains an implementation of code injection. If you are running a
UI script and update one of it's sources it will be built into a bundle
and loaded applying any changes to class method implementations without restart.

That's about it. What is missing is an implementation of easy to use
i/o classes such as File, Dir and FileUtils in ruby which could be 
made available as a pod in due course. As scripts are frameworks they
can be imported into each other which is another way to shared code.

The author can be reached on Twitter
[@Injection4Xcode](https://twitter.com/#!/@Injection4Xcode).

### MIT License

Copyright (c) 2015 John Holdsworth

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
