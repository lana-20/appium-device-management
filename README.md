# appium-device-management

Some Appium features have to do with the device itself, rather than any particular app. You may or may not find that you need these features in the course of your testing, as they go beyond simple UI automation. But whatever the case, it's important to know what you can do with Appium.

The first set of features has to do with managing files. What kind of files exactly? Here I'm talking about files that live on the device itself.

[Device File Interaction](https://github.com/lana-20/appium-device-file-interaction)

[Android File System](https://github.com/lana-20/adb-shell#file-system)

Another aspect of mobile devices that differs from web browsers is that we can lock and unlock them. In fact, it's often important to be sure that, after we unlock a device, our app behaves the same as it did before locking it. The mobile operating systems often put apps to sleep, and so they need to be designed in such a way that when they wake back up, they don't crash or do unexpected things.

The way to use Appium to lock a device is to call, simply enough, <code>driver.lock()</code>. If you call it without any parameters, the device will simply lock, and that's it. Or, it can be called with a numeric parameter, denoting a certain number of seconds. After this amount of time has elapsed, Appium will attempt to unlock the device automatically. Of course, this will only work with a positive number. If you send in a negative number it's the same as not sending any number at all, the device screen will lock permanently until you unlock it again. This latter feature, where the device comes back automatically after a certain number of seconds, can be useful for testing the case where your app needs to come back and refresh itself after the device is unlocked automatically. Of course, you can also manage this in your own test code, using the next method we'll discuss.

And that is <code>driver.unlock()</code>, which as you can guess will attempt to unlock the device. There are a few limitations to this command. It works just as expected on emulators and simulators, but real devices are more complex. Real devices can be secured with various types of protection, and obviously Appium is not able to get around device lock screens in any way that a user themselves wouldn't be able to do. So if you are automating a real device, and it has some kind of security set on the lock screen, on iOS you're completely out of luck. On Android, however, it's possible to actually automate the lock screen, as long as we already know the password or pin.

To use Appium to unlock an Android device screen that's protected with a password, pin, or pattern, the first thing we need to do is ensure we use two new capabilities when we start our session. The first is <code>unlockType</code>, and it tells Appium which unlock strategy to expect it should use. This should be a string, and should be the word <code>password</code>, <code>pin</code>, <code>pattern</code> or <code>fingerprint</code>. The second capability is called <code>unlockKey</code>, and it is the particular value that Appium should use to unlock the device. In other words, if the unlock type is <code>password</code>, then the value of the <code>unlockKey</code> capability should be the actual password itself. In the case of the fingerprint, it would be the id number of one of the stored fingerprints. Obviously, this would work in the context of fake fingerprints you can use on emulators, not real fingerprints. And so on. When a session has these capabilities set, and you call <code>driver.unlock()</code>, Appium will use the strategy you've defined to try to unlock an Android screen (by aumating it - by actually typing in the password or actually swiping around in the form of the pattern you specify. These capabilities have no effect on iOS.

The last bit of functionality regarding locking is the command <code>driver.is_locked()</code>, which will return True if the device is locked and False otherwise, so you can use Appium to check the screen lock state.

One unique feature of mobile devices versus desktop computers is that mobile devices can be used in a variety of screen orientations. You can hold them in portrait mode, or turn them sideways to landscape mode in order to watch videos or play games, for example. Using Appium, we can both detect the orientation as well as set the orientation (though of course we can't change the physical orientation of a real device...just the screen orientation).

To detect the current orientation, simply retrieve the <code>orientation</code> property on the driver object, by writing driver.orientation. This is a property, not a command. It will return a string matching either LANDSCAPE (in all caps) or PORTRAIT (in all caps).

To set the orientation, just set the same property to one of those two string values, LANDSCAPE or PORTRAIT (in all caps again).

Another very important aspect of mobile devices is their ability to detect their location on the surface of the earth using GPS. Most mobile devices have GPS receivers that can be used to share the device's latitude and longitude with apps, to support location-based features. During our automation, it is obviously a requirement for these kinds of apps to be able to set the location of the device for the purpose of testing.

While it's not possible to change the location of a real device, it is possible to set the location of an emulator and simulator.

To determine the location of a device using Appium, simply use the <code>driver.location</code> property. Accessing this property will get you back a dictionary with keys <code>latitude</code>, <code>longitude</code>, and <code>altitude</code>. Altitude is not actually supported, but the key is there because it's part of the specification. On emulators and simulators, the values will of course not necessarily correspond to the actual physical location of your computer, but on a real device it will return the physical location.

Let's talk about setting the location of the device for the purposes of testing. While it's not possible to change the location of a real device, it is possible to set the location of an emulator and simulator, using the <code>driver.set_location</code> command. It's a function that takes three parameters: the first is a number representing the <code>latitude</code>, the second is <code>longitude</code>, and the third is <code>altitude</code>. Again, I recommend just setting the <code>altitude</code> to 0 since neither the Android nor iOS platforms actually do anything with that particular bit of information.

Now, let's talk about some of the fun we can have with keyboards. This section is really only for Android, which allows us to do some interesting things with keyboards.

First of all, one common problem that you might run into when automating an Android emulator is that the keyboard provided by default doesn't necessarily have the ability to produce arbitrary unicode symbols. If you find that you need to type text in a language that requires these symbols, you should set the <code>unicodeKeyboard</code> capability to true, and Appium will install a specially-designed Android keyboard for the emulator ahead of testing, so that your text input will work as planned.

We also have a special command for Android devices only, called <code>driver.press_keycode()</code>. This takes three parameters. The first is called <code>code</code>, the second is called <code>metastate</code>, and the third is called <code>flags</code>. What is all this business about keycodes?




