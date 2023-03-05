# Appium Device Management

Some Appium features have to do with the device itself, rather than any particular app. You may or may not find that you need these features in the course of your testing, as they go beyond simple UI automation. But whatever the case, it's important to know what you can do with Appium.

The first set of features has to do with managing files. What kind of files exactly? Here I'm talking about files that live on the device itself.

## [Device File Interaction](https://github.com/lana-20/appium-device-file-interaction)

[Android File System](https://github.com/lana-20/adb-shell#file-system)

## Locking and Unlocking

| Command | Explanation |
|----|----|
| driver.lock(), driver.lock(5), driver.lock(-1) | Lock the screen and come back after a certain number of seconds |
| driver.unlock() | Unlock the screen |
| unlockType / unlockKey | Caps for use with <code>unlock()</code>. If used, attempts to unlock via Ul instead of helper app. Types: "password'", "pin'", "pattern", "fingerprint" |
| driver.is_locked() | Check whether the screen is locked |

Another aspect of mobile devices that differs from web browsers is that we can lock and unlock them. In fact, it's often important to be sure that, after we unlock a device, our app behaves the same as it did before locking it. The mobile operating systems often put apps to sleep, and so they need to be designed in such a way that when they wake back up, they don't crash or do unexpected things.

The way to use Appium to lock a device is to call, simply enough, <code>driver.lock()</code>. If you call it without any parameters, the device will simply lock, and that's it. Or, it can be called with a numeric parameter, denoting a certain number of seconds. After this amount of time has elapsed, Appium will attempt to unlock the device automatically. Of course, this will only work with a positive number. If you send in a negative number it's the same as not sending any number at all, the device screen will lock permanently until you unlock it again. This latter feature, where the device comes back automatically after a certain number of seconds, can be useful for testing the case where your app needs to come back and refresh itself after the device is unlocked automatically. Of course, you can also manage this in your own test code, using the next method we'll discuss.

And that is <code>driver.unlock()</code>, which as you can guess will attempt to unlock the device. There are a few limitations to this command. It works just as expected on emulators and simulators, but real devices are more complex. Real devices can be secured with various types of protection, and obviously Appium is not able to get around device lock screens in any way that a user themselves wouldn't be able to do. So if you are automating a real device, and it has some kind of security set on the lock screen, on iOS you're completely out of luck. On Android, however, it's possible to actually automate the lock screen, as long as we already know the password or pin.

To use Appium to unlock an Android device screen that's protected with a password, pin, or pattern, the first thing we need to do is ensure we use two new capabilities when we start our session. The first is <code>unlockType</code>, and it tells Appium which unlock strategy to expect it should use. This should be a string, and should be the word <code>password</code>, <code>pin</code>, <code>pattern</code> or <code>fingerprint</code>. The second capability is called <code>unlockKey</code>, and it is the particular value that Appium should use to unlock the device. In other words, if the unlock type is <code>password</code>, then the value of the <code>unlockKey</code> capability should be the actual password itself. In the case of the fingerprint, it would be the id number of one of the stored fingerprints. Obviously, this would work in the context of fake fingerprints you can use on emulators, not real fingerprints. And so on. When a session has these capabilities set, and you call <code>driver.unlock()</code>, Appium will use the strategy you've defined to try to unlock an Android screen (by aumating it - by actually typing in the password or actually swiping around in the form of the pattern you specify). These unlock capabilities have no effect on iOS, we can't automate lock screen in iOS at all.

The last bit of functionality regarding locking is the command <code>driver.is_locked()</code>, which will return True if the device is locked and False otherwise, so you can use Appium to check the screen lock state.

## Device Orientation

| Command | Explanation |
|----|----|
| driver.orientation | Get the current orientation ("LANDSCAPE", or "PORTRAIT") |

One unique feature of mobile devices versus desktop computers is that mobile devices can be used in a variety of screen orientations. You can hold them in portrait mode, or turn them sideways to landscape mode in order to watch videos or play games, for example. Using Appium, we can both detect the orientation as well as set the orientation (though of course we can't change the physical orientation of a real device...just the screen orientation).

To detect the current orientation, simply retrieve the <code>orientation</code> property on the driver object, by writing driver.orientation. This is a property, not a command. It will return a string matching either LANDSCAPE (in all caps) or PORTRAIT (in all caps).

To set the orientation, just set the same property to one of those two string values, LANDSCAPE or PORTRAIT (in all caps again).

## Geolocation

- Most mobile devices come with GPS receivers
- Location-based mobile apps require that their automation be able to detect and emulate actual physical positions of the device in various points on the globe
- Geolocation setting works with emulators/simulators, but not real devices in general

| Command | Explanation |
|----|----|
| location = driver.location | Get the current geo location. Returns a dict with keys latitude, longitude, and altitude. (Altitude not supported on iOS or Android) |
|driver.set_location(lat, long, alt)|Set the current geo location with latitude and longitude (altitude not supported for now, use value of 0)|

Another very important aspect of mobile devices is their ability to detect their location on the surface of the earth using GPS. Most mobile devices have GPS receivers that can be used to share the device's latitude and longitude with apps, to support location-based features. During our automation, it is obviously a requirement for these kinds of apps to be able to set the location of the device for the purpose of testing.

While it's not possible to change the location of a real device, it is possible to set the location of an emulator and simulator.

To determine the location of a device using Appium, simply use the <code>driver.location</code> property. Accessing this property will get you back a dictionary with keys <code>latitude</code>, <code>longitude</code>, and <code>altitude</code>. Altitude is not actually supported, but the key is there because it's part of the specification. On emulators and simulators, the values will of course not necessarily correspond to the actual physical location of your computer, but on a real device it will return the physical location.

Let's talk about setting the location of the device for the purposes of testing. While it's not possible to change the location of a real device, it is possible to set the location of an emulator and simulator, using the <code>driver.set_location</code> command. It's a function that takes three parameters: the first is a number representing the <code>latitude</code>, the second is <code>longitude</code>, and the third is <code>altitude</code>. Again, I recommend just setting the <code>altitude</code> to 0 since neither the Android nor iOS platforms actually do anything with that particular bit of information.

## Keycodes

| Command | Explanation |
|----|----|
|unicodeKeyboard|Android-only! Set this capability to True to use a keyboard supporting unicode input. Replaces existing keyboard. Can also use resetKeyboard cap (set to True) to have Appium reset to the original keyboard on session quit.|
|driver.press_keycode(code, metastate, flags)|Android-only! Use int constants from Android source to trigger low-level key event injection. Access any keys including hardware.|

Now, let's talk about some of the fun we can have with keyboards. This section is really only for Android, which allows us to do some interesting things with keyboards.

First of all, one common problem that you might run into when automating an Android emulator is that the keyboard provided by default doesn't necessarily have the ability to produce arbitrary unicode symbols. If you find that you need to type text in a language that requires these symbols, you should set the <code>unicodeKeyboard</code> capability to true, and Appium will install a specially-designed Android keyboard for the emulator ahead of testing, so that your text input will work as planned.

We also have a special command for Android devices only, called <code>driver.press_keycode()</code>. This takes three parameters. The first is called <code>code</code>, the second is called <code>metastate</code>, and the third is called <code>flags</code>. What is all this business about keycodes?

![image](https://user-images.githubusercontent.com/70295997/222956640-a6266839-c85c-4e7d-abd5-3439594a7f40.png)

Well, Android has a special numeric code that it has assigned to every possible key that can be triggered on the device, including hardware keys. So whether we're talking about the software key for the letter 'A', or the hardware key that turns the device volume up, there's a keycode for that! Above captioned is a snippet from the Android developer documentation that discusses Keycodes. There you can find out that there is a special constant named <code>KEYCODE_BACK</code>, which represents the "back" button on a device. This may be a hardware button on some devices or a software button on others. But regardless, the constant value for this key is the number 4. This number 4 is what you would use as the <code>code</code> argument to the <code>press_keycode()</code> command. In most cases, this is all you need.

<img width="200" alt="Screenshot 2023-03-05 at 3 31 35 AM" src="https://user-images.githubusercontent.com/70295997/222957911-a03550d3-9f85-4ca7-a4c2-a73141ffc0b2.png">

You might also want to include something called a Meta State. An example of a meta state would be that the alt key is held down while you press a different key, in order to produce a special symbol. Meta states also are represented by constant numeric values. So the meta state that corresponds to the alt key being held down, for example, is the number 2.

<img width="360" alt="Screenshot 2023-03-05 at 3 34 03 AM" src="https://user-images.githubusercontent.com/70295997/222958013-39916f41-bd9c-4d6b-ac97-c26f06d17f9b.png">

Finally, you might also need to include something called a Flag. Flags are bits of information set by the system when executing key events, that are then passed to applications so applications can determine what exactly happened during an event. Using Appium, you can specify which flags the application receives from the system, mimicking any kind of keypress. This is a very advanced feature, and you probably won't need to worry about flags. But if you want to use them, each flag is also a special type of integer which can be combined with other flags to form a single number using the pipe operator, which is a way of merging two numbers according to certain binary rules. So if we wanted to say that our key event had two flags, namely the "long press" flag and the "from system" flag, we could look at the numbers representing each. The long press flag is 128, and the from system flag is 8. Then, we could construct a call to <code>driver.press_keycode()</code> where the flag parameter is <code>8 | 128</code>. We haven't seen this pipe operator before, and it's not used very frequently. In the case of android key flags, this is basically the same as <code>8 + 128</code>, in other words, <code>136</code>. All we need to know here is that the number 136 is actually an unambiguous way of declaring that there are 2 flags that the keycode's event should contain. In this example, the other numbers that are being passed as params are the number 4, representing the Back key, and the number 1 representing that we have a Meta state of the alt key being held down.

<img width="532" alt="Screenshot 2023-03-05 at 3 41 12 AM" src="https://user-images.githubusercontent.com/70295997/222958321-3476b441-1009-469d-9b89-8785508bbdd8.png">
<img width="532" alt="Screenshot 2023-03-05 at 3 41 29 AM" src="https://user-images.githubusercontent.com/70295997/222958335-197b73f3-b854-4d26-ba44-7a3e53e50f88.png">
<img width="532" alt="Screenshot 2023-03-05 at 3 42 40 AM" src="https://user-images.githubusercontent.com/70295997/222958379-4d99eb2c-6f4a-410d-b06c-03b391830783.png">


So, why do we want to use the press keycode command anyway? The most common use will just be to trigger hardware keys, or maybe to simulate an external keyboard used while playing a game.

## Biometrics

OK, moving right along through our parade of device functions! Next up: biometrics! This section is for iOS in particular, since many iOS apps have features that are protected by biometric security. What is biometric security? Essentially, it is security which is keyed to certain aspects of the physical biology of the user. In the case of iOS, this is either a fingerprint or the scan of someone's face. The fingerprint security feature for iOS is called "Touch ID", and the face scan security feature is called "Face ID". Apps can hook into Touch ID and Face ID if the user has them turned on, to protect certain areas of the app, like a login.

Obviously, it would be good to test this functionality if your app supports it, and Appium provides a way to work with biometric security for iOS simulators. This does not work on real devices, for the obvious reason that if Appium could get around biometric security on a real device, it would pose a security hazard to the users of the device.

What functions do we have available to support automation of biometric security?

The first method we have is a <code>mobile:</code> execute script method, called <code>isBiometricEnrolled</code>. This mobile method takes no parameters and will simply return a true or false boolean value to let you know whether the device is enrolled in a biometric scheme. This is important to know, because before you attempt to automate a biometric match using touch ID or face ID, the device must be enrolled. So, how do you enroll it if it's not enrolled?

By using another mobile method, called <code>enrollBiometric</code>. This method does take a parameter dictionary, with a single key called <code>isEnabled</code>. This key should have a value of either true or false, denoting whether we want to enroll or unenroll the device in the biometric program. Which program will the device be enrolled in? Well, every iOS device either supports Touch ID or Face ID, but not both, so you'll have to know what kind of device you're automating in order to know whether you just enrolled in Touch ID or Face ID!

The next piece of the puzzle is to figure out how to actually trigger a biometric match. To do this we use the <code>sendBiometricMatch</code> mobile method. It takes two parameters, which combine into a single Python dictionary, with a key called <code>type</code> and a key called <code>match</code>. <code>type</code> refers to the type of biometric match we want, whether Touch ID or Face ID, and should be exactly equal to the string <code>touchId</code> or <code>faceId</code>. The <code>match</code> key should have a boolean value, where true denotes a positive match and false denotes a negative match. What do I mean by positive and negative matches? Well, a positive match is going to simulate a successful authentication of the biometric security. This is what you use when you want to see if your app correctly handles when a user passes the biometric test. And a negative match is used to simulate a failed authentication. It's what you use when you want to test how your app handles the situation where a user doesn't pass the biometric test. You might want to test that the user is not logged in in that case, for example.

## Clipboard

OK. Given that our devices are basically tiny but powerful computers, they also have an internal memory system known as the clipboard. This is the same system that lets you copy and paste content on your desktop computer. And indeed, many apps take advantage of the copying and pasting features built into iOS and Android in order to function properly. You can actually use Appium to send data straight to the device's clipboard, to populate it with content that you can then read from your app in order to test your app's behavior. So let's take a look at the commands we have available.

First, we have <code>driver.get_clipboard()</code>. This method will retrieve whatever data is in the device clipboard at the moment, and takes a single optional keyword argument called <code>content_type</code>, which by default is set to plain text. If you know that the content you are copying is a different kind, such as a URL or an image on iOS, then you can use the <code>ClipboardContentType</code> class to find constants you can use as the value for <code>content_type</code>. This will make sure to interpret the content correctly for you. On Android, only the text content type is supported.

We can also set the clipboard content ourselves, using <code>driver.set_clipboard()</code>. This method takes a single required parameter, which is the content you want to set in the clipboard. This would either be your text string, or on iOS if you want to paste image data it should be a byte array. It also takes an optional <code>content_type</code> keyword argument, which serves the same purpose as with <code>get_clipboard()</code>: it tells Appium what sort of content you are sending, so that the iOS systems receiving the data will interpret it correctly as text or URL or image.

## Device Info

OK, we come now to the last stop on our tour of the huge variety of Appium device management features. This one is only for Android, and it allows us to get a lot of interesting information about the device under test. It is another execute script "mobile" method, called <code>deviceInfo</code>. It doesn't take any parameters, and will return a multi-level Python dictionary with lots of information about the device. I think the best way to illustrate what this does is to look at it in a concrete example. I'll create a new file called <code>device_info_android.py</code>. In the <code>try</code> block, I'll place a single line that just prints out the device info:

    print(driver.execute_script("mobile: deviceInfo"))

And that's it! All we want to do is see what kind of dictionary of data this returns to us. And I'm also going to delete a bunch of the unnecessary imports up at the top of this file, since we don't need them any more. Alright. Let's give this a shot and run it. I'll switch over to my terminal and ensure that I have my emulator running as well as the Appium server. Now, in the mobile directory of our project, I can run this script:

    python device_info_android.py

OK, it's running. Now let's wait for the output. Here it is:

    {'androidId': 'b1a26baa1a9be88b', 'apiVersion': '29', 'bluetooth': None, 'brand': 'google', 'carrierName': 'Android', 'displayDensity': 420, 'locale': 'en_US', 'manufacturer': 'Google', 'model': 'Android SDK built for x86', 'networks': [{'capabilities': {'SSID': None, 'linkDownBandwidthKbps': 102400, 'linkUpstreamBandwidthKbps': 51200, 'networkCapabilities': 'NET_CAPABILITY_MMS,NET_CAPABILITY_SUPL,NET_CAPABILITY_DUN,NET_CAPABILITY_FOTA,NET_CAPABILITY_IMS,NET_CAPABILITY_CBS,NET_CAPABILITY_INTERNET,NET_CAPABILITY_NOT_RESTRICTED,NET_CAPABILITY_TRUSTED,NET_CAPABILITY_NOT_VPN,NET_CAPABILITY_VALIDATED,NET_CAPABILITY_NOT_ROAMING,NET_CAPABILITY_FOREGROUND,NET_CAPABILITY_NOT_CONGESTED,NET_CAPABILITY_NOT_SUSPENDED', 'signalStrength': -2147483648, 'transportTypes': 'TRANSPORT_CELLULAR'}, 'detailedState': 'CONNECTED', 'extraInfo': 'epc.tmobile.com', 'isAvailable': True, 'isConnected': True, 'isFailover': False, 'isRoaming': False, 'state': 'CONNECTED', 'subtype': 13, 'subtypeName': 'LTE', 'type': 0, 'typeName': 'MOBILE'}], 'platformVersion': '10', 'realDisplaySize': '1080x1920', 'timeZone': 'America/Vancouver'}

Wow, that's a lot of stuff! We can see we have information about the android ID of the device, the API version, the manufacturer, whether the network is connected and how fast it is, the screen size, timezone and so on. Why might you need this information? Well, you might write a test which is sensitive to some of these factors, for example the timezone, and may need to behave differently based on some of them. At the beginning of your test you could retrieve this info from the device in order to inform your test how to execute correctly. This would be especially useful in a cloud execution environment where you might not have total control over which type of device you get for a given test.

And since this is just a Python dictionary, you can walk through it just like you would any other Python dictionary in your code!

Alright, that's it for device management. There are actually quite a few more features than we've explored in this unit, so as always, head to appiumpro.com or the Appium documentation to hunt around for more that might be useful for your particular use case.


