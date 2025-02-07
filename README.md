##Build instructions
Running Web App
yarn
yarn web
You're all set!

##iOS/Android Build
Native Environment Setup
This is NOT required when developing for web.

Set up your environment using the expo instructions.
make sure that the JAVA_HOME points to the zulu-17 directory in your .zshrc or .bashrc file: export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home. DO NOT use another JDK or you will encounter build errors.
If you're running macOS, make sure you are running the correct versions of Ruby and Cocoapods:-
If you are using Apple Silicon and this is the first time you are building for RN 0.74+, you may need to run:
arch -arm64 brew install llvm
sudo gem install ffi
Check if you've installed Cocoapods through homebrew. If you have, remove it:
brew info cocoapods
If output says Installed:
brew remove cocoapods
If you have not installed rbenv:
brew install rbenv
rbenv install 2.7.6
rbenv global 2.7.6
Add eval "$(rbenv init - zsh)" to your ~/.zshrc
From inside the project directory:
bundler install (this will install Cocoapods)
After initial setup:
Copy google-services.json.example to google-services.json or provide your own google-services.json. (A real firebase project is NOT required)
npx expo prebuild -> you will also need to run this anytime app.json or native package.json deps change
Running the Native App
iOS: yarn ios
Xcode must be installed for this to run.
A simulator must be preconfigured in Xcode settings.
if no iOS versions are available, install the iOS runtime at Xcode > Settings > Platforms.
if the simulator download keeps failing you can download it from the developer website.
Apple Developer
xcode-select -s /Applications/Xcode.app
xcodebuild -runFirstLaunch
xcrun simctl runtime add "~/Downloads/iOS_17.4_Simulator_Runtime.dmg" (adapt the path to the downloaded file)
In addition, ensure Xcode Command Line Tools are installed using xcode-select --install.
Expo will require you to configure Xcode Signing. Follow the linked instructions. Error messages in Xcode related to the signing process can be safely ignored when installing on the iOS Simulator; Expo merely requires the profile to exist in order to install the app on the Simulator.
Make sure you do have a certificate: open Xcode > Settings > Accounts > (sign-in) > Manage Certificates > + > Apple Development > Done.
If you still encounter issues, try rm -rf ios before trying to build again (yarn ios)
Android: yarn android
Install "Android Studio"
Make sure you have the Android SDK installed (Android Studio > Tools > Android SDK).
In "SDK Platforms": "Android x" (where x is Android's current version).
In "SDK Tools": "Android SDK Build-Tools" and "Android Emulator" are required.
Add export ANDROID_HOME=/Users/<your_username>/Library/Android/sdk to your .zshrc or .bashrc (and restart your terminal).
Setup an emulator (Android Studio > Tools > Device Manager).
Web: yarn web (see the top of this file).
After you do yarn ios and yarn android once, you can later just run yarn web and then press either i or a to open iOS and Android emulators respectively which is much faster. However, if you make native changes, you'll have to do yarn prebuild -p ios and yarn prebuild -p android and then yarn ios and yarn android again before you can continue with the same workflow.

Tips
Copy the .env.example to .env and fill in any necessary tokens. (The Sentry token is NOT required; see instructions below if you want to enable Sentry.)
To run on the device, add --device to the command (e.g. yarn android --device). To build in production mode (slower build, faster app), also add --variant release.
If you want to use Expo EAS on your own builds without ejecting from Expo, make sure to change the owner and extra.eas.projectId properties. If you do not have an Expo account, you may remove these properties.
npx react-native info Checks what has been installed.
If the Android simulator frequently hangs or is very sluggish, bump its memory limit
The Android simulator won't be able to access localhost services unless you run adb reverse tcp:{PORT} tcp:{PORT}
For instance, the locally-hosted dev-wallet will need adb reverse tcp:3001 tcp:3001
For some reason, the typescript compiler chokes on platform-specific files (e.g. foo.native.ts) but only when compiling for Web thus far. Therefore we always have one version of the file that doesn't use a platform specifier, and that should be the Web version. (More info.)
Running E2E Tests
Start in various console tabs:
yarn e2e:mock-server
yarn e2e:metro
Run once: yarn e2e:build
Each test run: yarn e2e:run
Adding Sentry
Adding Sentry is NOT required. You can keep SENTRY_AUTH_TOKEN= in .env which will build the app without Sentry.

However, if you're a part of the Bluesky team and want to enable Sentry, fill in SENTRY_AUTH_TOKEN in your .env. It can be created on the Sentry dashboard using these instructions.

If you change SENTRY_AUTH_TOKEN, you need to do yarn prebuild before running yarn ios or yarn android again.

Adding bitdrift
Adding bitdirft is NOT required. You can keep BITDRIFT_API_KEY= in .env which will avoid initializing bitdrift during startup.

However, if you're a part of the Bluesky team and want to enable bitdrift, fill in BITDRIFT_API_KEY in your .env to enable bitdrift.

Adding and Updating Locales
yarn intl:build -> you will also need to run this anytime ./src/locale/{locale}/messages.po change
Running the Backend Locally
This is NOT required for app development but if you also want to develop the Bluesky backend locally too, you'll need this.

Start the dev servers
git clone git@github.com:bluesky-social/atproto.git
cd atproto
brew install pnpm
optional: brew install jq
pnpm i
pnpm build
Start the docker daemon (on MacOS this entails starting the Docker Desktop app)
Launch a Postgres database on port 5432
cd packages/dev-env && pnpm start
Then, when logging in or creating an account, point it to the localhost port of the devserver.

Go-Server Build
The Go server in this repository is only used for serving the web app in production. Usually you won't need to touch it.

Prerequisites
Go
Yarn
Steps
To run the build with Go, use staging credentials, your own, or any other account you create.

cd social-app
yarn && yarn build-web
cd bskyweb/
go mod tidy
go build -v -tags timetzdata -o bskyweb ./cmd/bskyweb
./bskyweb serve --appview-host=https://public.api.bsky.app
On build success, access the application at http://localhost:8100/. Subsequent changes require re-running the above steps in order to be reflected.

## Contributions

> While we do accept contributions, we prioritize high quality issues and pull requests. Adhering to the below guidelines will ensure a more timely review.

**Rules:**

- We may not respond to your issue or PR.
- We may close an issue or PR without much feedback.
- We may lock discussions or contributions if our attention is getting DDOSed.
- We're not going to provide support for build issues.

**Guidelines:**

- Check for existing issues before filing a new one please.
- Open an issue and give some time for discussion before submitting a PR.
- Stay away from PRs like...
  - Changing "Post" to "Skeet."
  - Refactoring the codebase, e.g., to replace MobX with Redux or something.
  - Adding entirely new features without prior discussion. 

Remember, we serve a wide community of users. Our day-to-day involves us constantly asking "which top priority is our top priority." If you submit well-written PRs that solve problems concisely, that's an awesome contribution. Otherwise, as much as we'd love to accept your ideas and contributions, we really don't have the bandwidth. That's what forking is for!

## Forking guidelines

You have our blessing 🪄✨ to fork this application! However, it's very important to be clear to users when you're giving them a fork.

Please be sure to:

- Change all branding in the repository and UI to clearly differentiate from Bluesky.
- Change any support links (feedback, email, terms of service, etc) to your own systems.
- Replace any analytics or error-collection systems with your own so we don't get super confused.

## Security disclosures

If you discover any security issues, please send an email to security@bsky.app. The email is automatically CCed to the entire team and we'll respond promptly.

## Are you a developer interested in building on atproto?

Bluesky is an open social network built on the AT Protocol, a flexible technology that will never lock developers out of the ecosystems that they help build. With atproto, third-party integration can be as seamless as first-party through custom feeds, federated services, clients, and more.

## License (MIT)

See [./LICENSE](./LICENSE) for the full license.

## P.S.

We ❤️ you and all of the ways you support us. Thank you for making Bluesky a great place!
