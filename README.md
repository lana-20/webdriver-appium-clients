# WebDriver and Appium clients

*In any client-server architecture, the client is an important component. In this unit we discuss WebDriver clients, which are the libraries used in test scripts to enable Selenium and Appium automation.*

How does a Selenium or Appium client work? How do Selenium or Appium themselves work? Let's first talk about how the clients work.

As we've been discussing all along, a Webdriver client is anything that can produce the appropriate HTTP requests to a Webdriver server, whether Selenium or Appium. But it would be very difficult for us as testers to write all our test automation code using raw HTTP commands. It would be much nicer if there were a higher level, more user-friendly, more natural way of running Appium and Selenium commands. And there is! Pretty much every programming language out there has the ability to run HTTP commands for you. This means that it's possible to write code libraries, usable by anyone who uses that programming language, which encapsulate all the details about the WebDriver Protocol so that you don't have to think about them anymore. And that's exactly what the Selenium and Appium teams have done! They've released a set of client libraries that allow a much easier and more high level interaction with Appium and Selenium servers. These libraries are open source and free for anyone to use, and they are what we want to use to write our actual tests with.

These libraries go by a number of different names. Sometimes they are called "client bindings," to express their ability to bind connections between a particular test script and an Appium or Selenium server. Sometimes I just call them "clients," even though technically the concept of a client is more generic and refers to everything on the other side of the network from the server. I trust it will always be clear when we're talking about the client libraries released by the Appium and Selenium teams.

Let's look at an example. Here's the HTTP request finding an element, that we saw in a previous unit:

POST /session/1234-5678-9012-3456/element HTTP/1.1
Content-type: application/json

{
    "using": "css selector",
    "value": "#submitForm"
}
And, here was the response:

HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value": {
        "element-6066-11e4-a52e-4f735466cecf": "1234-5789-0abc-defg"
    }
}
Again, what's going on here is that we're constructing the appropriate request with the parameters we want to the server, and it is responding with a JSON representation of an element that it found. Instead of all this, what if we were to use the Python webdriver library? In that case, all we'd need to do would be to write this in a Python script:

element = session.find_element_by_css_selector('#submitForm')
Wow! That's it! Just one line. In this Python code example, we assume we already have an object named session, the same way we assumed it in the HTTP example a moment ago as well. And then what we're doing is calling a method on this object, named find_element_by_css_selector. That's a pretty transparent name, right? Then we pass in a parameter which is exactly the same as the 'value' parameter in the HTTP example.

What do we get back from the call to this method? Well, we get back an object which we can name whatever we like. Rather than a strange and obscure JSON string, the Python client library takes the element ID and wraps it up in an object, which we can then conveniently call methods on!

element.click()
element.send_keys('hello world')
For example, we could call element.click to click the element we previously found. And we could call element.send_keys with some text in order to send keystrokes into an element. But what happened to the element ID? Why don't we need to include it or the session ID in these commands? The magic is that we are including them in the HTTP requests to interact with the element. But we as the users of the client library no longer need to worry about that. The client library abstracts those things away and stores them on the objects themselves, so that we don't have to worry our minds about them.

This is what's beautiful about client libraries--they essentially create an interface for using Appium and Selenium which is totally natural for the programming language that they're written for. And this means that the Python client library might look totally different than the Java client library. In fact, we want this to be the case, because Python and Java are totally different languages with totally different conventions about what makes a good library! The same series of steps in the Java client might look like this, for example:

WebElement element = session.findElement(By.cssSelector("#submitForm"));
element.click();
element.sendKeys("hello world");
Similar enough to see that we are still working with webdriver, but different enough to be natural for Java!

Thankfully, all the clients libraries you'll ever need have already been written, so you don't have to do the hard work of designing a library that constructs the HTTP requests, and parses the HTTP responses, appropriate for the WebDriver protocol. But, maybe you'll have some ideas for a better library and design one anyway! At this point, you at least know how they all work under the hood. There's no magic--just code that is translated to HTTP requests, and responses which are translated from HTTP back into code objects.

One more note about client libraries! Remember when we talked about how Appium had to extend the WebDriver protocol with new commands? Well, that has some consequences for client libraries and how we use them. If you want, you can take a client library written for vanilla WebDriver, produced by the Selenium team, and use it with Appium. You'll be able to start and stop sessions, find elements, tap on them, and so on. But, you won't have access to any of the commands that Appium has defined as extensions to the protocol. This is because the standard client libraries for Selenium don't know or care about Appium's extensions.

For this reason, the Appium team implemented a new set of client libraries, for all the major programming languages, which include the Selenium client libraries within them. So they can handle all the normal WebDriver protocol commands, without reimplementing them, but they can also handle all Appium's extension commands. That's the main thing to understand about Appium client libraries. And if you use Appium client libraries, you are also using Selenium client libraries, since the Appium libraries are built on top of the Selenium ones! If you're working on a test project that involves mobile automation, you can therefore usually get away with using just the Appium client library for working with both Selenium and Appium.




