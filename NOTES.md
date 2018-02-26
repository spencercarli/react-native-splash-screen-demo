## iOS

- Initialize app
- Set background color to #4F6D7A
- Set text color to #F5FCFF
- Install react-native-splash-screen
- Link react-native-splash-screen
- Configure native & js react-native-splash-screen
- Add icon to image assets in xcode
- Set background color of view in LaunchScreen.xib to #4F6D7A
- Add image view in LaunchScreen.xib and center it vertically and horizontally (two internal arrows)
- In info.plist set statusbar color to white (UIStatusBarStyle -> UIStatusBarStyleLightContent)

## Android

- Intialize app
- Set background color to #4F6D7A
- Set text color to #F5FCFF
- Add images to drawable

### Necessary for cold start
Based on https://www.bignerdranch.com/blog/splash-screens-the-right-way/

- Create background_splash.xml in res/drawable
- Create SplashTheme in styles.xml
- Create new activity in AndroidManfest.xml. Set that as your initial activity and use the SplashTheme for it
- Setup SplashActivity.java to forward to your MainActivity

### For when the app launches and RN has to initialize
- Install react-native-splash-screen @3.0.6
- Link react-native-splash-screen
- Configure native & js react-native-splash-screen
- Setup the launch_screen.xml
- Set primary_dark in styles.xml?
