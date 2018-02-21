# How to Add a Splash Screen to a React Native App (iOS and Android)

I’m often asked about that last mile of developing a React Native app (actually getting it into the app store). There’s more to it than just building your app and sending it off to Apple/Google — you’ve got to add icons, splash screens, write descriptions, and more before sending it off for review.

Today we'll cover splash screens on iOS and Android.

In this tutorial we’ll be working in the platform IDEs and writing a bit of native code. Don’t worry, I’ll walk you through everything. The final code is [available on Github](https://github.com/spencercarli/react-native-splash-screen-demo).

I’ll be starting by creating a new React Native app `react-native init SplashExample`.

> Protip: Set up source control before following along! Not only is it a good way to revert changes when you make a mistake but it's also a great way to see exactly what changed to enable the splash screen.

## Splash Screen Assets

In a previous version of this tutorial I showed how to simply use a large image to create the splash screen. This can have issues scaling between the ever growing number of screen resolutions.

So, instead, we'll start with a single image that looks a lot like our icon. The idea here is two fold:

1. Make it look like the icon the user taps grows to fill the entire screen making for a seemless transition.
2. Keep it simple. A splash screen should only show for a few milliseconds. It shouldn't have a lot of information on it - it is, afterall, a dreaded loading screen.

With all of this in mind we'll start with a asquare image at three sizes (@1x = 200px, @2x = 400px, @3x = 600px).

TK: You can get those images from Github.

TK: Add example image

## Preparing the App

If you've tried to set up a splash screen in React Native then you may have experience a white screen flash before your content loads. If your app has a white background this is often not very noticable, but it's still a pain. Today we're going to make it _very_ noticable if we have any flashes of white. To do that we'll set a dark background color in our app.

Replace App.js with

```javascript
import React, { Component } from 'react';
import {
  Platform,
  StyleSheet,
  Text,
  View,
  StatusBar
} from 'react-native';

const instructions = Platform.select({
  ios: 'Press Cmd+R to reload,\n' +
    'Cmd+D or shake for dev menu',
  android: 'Double tap R on your keyboard to reload,\n' +
    'Shake or press menu button for dev menu',
});

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <StatusBar
          barStyle="light-content"
        />
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit App.js
        </Text>
        <Text style={styles.instructions}>
          {instructions}
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#4F6D7A',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
    color: '#F5FCFF',
  },
  instructions: {
    textAlign: 'center',
    color: '#F5FCFF',
    marginBottom: 5,
  },
});
```

All this did was set the background color to #4F6D7A, the text color to #F5FCFF, and use light text in the status bar.

![App](/screenshots/01-app.png)

## Adding Splash Screens (iOS)

First open the project in Xcode.

```
open ios/SplashExample.xcodeproj
```

We then want to add a new image asset inside of Xcode. You can access that in the left navigator - SplashExample > SplashExample > Imagex.xcassets and then pressing the "+" in the second left most navigator. Call it "SplashIcon"

![Add image assets](/screenshots/02-image-assets.png)

Then go ahead and dage the three images you downloaded earlier. They'll automatically attach themselves to the right pixel density.

![Image assets](/screenshots/03-image-assets-added.png)

You can then open `LaunchScreen.xib`, again availble in the left most navigator. You should see this.

![LaunchScreen.xib](/screenshots/04-launch-screen-xib.png)

Select the two text elements ("SplashExample" and "Powered by React Native") and delete them.

Next we'll set the background color of the View. Select "View" in the second left most navigator. Then, in the right most navigator, choose "Background" which will pop up a picker dialog.

![Background color picker](/screenshots/05-background-color-picker.png)

> Protip: It took forever to find this but if you choose the second option in the color picker and then choose "RGB Sliders" you can then enter a hex value.

![Color picker tip](/screenshots/06-picker-tip.png)

Go ahead and set the background color to #4F6D7A.

We then want to add an Image View to our splash screen. You can find that by pressing the third option in the bottom right menu and searching for image. When you've got that drag it onto the blue View.

![Image view](/screenshots/07-image-view.png)

You then want to set the image source from the top right. When you select the image dropdown any images from Images.xcassets should show up - choose SplashIcon.

![Set image](/screenshots/08-set-image.png)

Now to fix proportions set the "Content Mode" to "Aspect Fit". This is available in the right most menu, second down.

![Aspect fit](/screenshots/09-aspect-fit.png)

Next, we need to make sure our icon stays centered in the screen regardless of what device the app is running on. To do this, in the right most navigator, choose the fifth icon at the top (the one that looks like a ruler).

![Ruler](/screenshots/10-ruler.png)

Within the "Autoresizing" section you'll want to disable the outter red lines and enable the inner red arrows. This will allow the image to stay centered regardless of the screen dimensions.

![Autoresizing](/screenshots/11-autoresizing.png)

That leaves us with the following:

![Initial result](/screenshots/12-result.gif)

Now to handle that pesky white screen flash...

First, why does that happen? That's when the Javascript is loading & the bridge is initializing. Atleast that's the way I think about it. The splash screen displays when the app is first booted up (React Native). Then while React Native is initializing we get that white screen so what we need to do is "hold" the splash screen a bit longer than the default behaviour.

That's where we'll use `react-native-splash-screen`.

First install the package and then link it.

```
yarn add react-native-splash-screen@3.0.6
react-native link react-native-splash-screen
```

We then need to configure the package. From Xcode open the `AppDelegat.m` file.

We'll then add `#import "SplashScreen.h"` with the other imports and then add `[SplashScreen show];` just above `return YES;` in the `didFinishLaunchingWithOptions` method.

![AppDelegate.m](/screenshots/13-app-delegate.png)

Finally, in `App.js`, we need to tell the splash screen to hide once our app is ready. We'll do that in the `componentDidMount` lifecycle hook.

```javascript
import SplashScreen from 'react-native-splash-screen';

export default class App extends Component {
  componentDidMount() {
    SplashScreen.hide()
  }

  ...
}
```

![The result!](/screenshots/14-result-2.gif)

Woo! Looking good. One last nitpick. Let's set the statusbar color to light when the splash screen is shown. Open Info.plist in Xcode and add a new row to it. The key should be "UIStatusBarStyle" and the value (a string) to "UIStatusBarStyleLightContent".

![Set status bar to white](/screenshots/15-status-bar.png)

And that leaves us with perfection

![Perfect result](/screenshots/16-perfect-result.gif)
