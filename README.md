# CoinfyWallet - A Bitcoin & Lightning Wallet

This project is a fork of BlueWallet, a popular Bitcoin wallet for iOS and Android. This project was submitted as a result of a hackathon on [Bitcoin++ 2025 - Florianopolis, Brazil](https://btcplusplus.dev/conf/floripa).

The code of the main project features are packaged on these two PRs:

## Change on Send Transaction screen to warn about UTXOs mixing

[PR](https://github.com/BlueWallet/BlueWallet/pull/7619)

- The main logic changes were implemented on `class/wallets/legacy-wallet.ts`:

There are two algorithms used to select coins: `coinSelectSplit` and `coinSelect`. We added a param `coinfyLambda` that, if present, changes the selection algorithm to `coinSelectCoinfy`.

`coinSelectCoinfy` split the UTXOs based on its memo, which is stored on Wallet app, not on blockchain.
If memo contains the `dirty` keyword, the UTXO is added on dirty UTXOs set.
If memo contains the `clean` keyword, the UTXO is added on clean UTXOs set.
If memo contains the `none` keyword or memo is empty, the UTXO is added on none UTXOs set.

Example of a list of UTXO with memos:

<img width="376" alt="Captura de Tela 2025-02-22 às 08 34 26" src="https://github.com/user-attachments/assets/2e823bd8-50bc-45e2-b4e5-8f00fca58416" />

Then `coinSelectCoinfy` algorithm tries to select only safe UTXOs: `clean` and `none` using the `coinSelect` algorithm. If they contain enough balance, the algorithm creates the new transaction and returns it.

The next step is trying to select only dirty UTXOs using the `coinSelect` algorithm. If they contain enough balance, the algorithm creates the new transaction and returns it.

Example of a scenario without mixed UTXOs:

<img width="376" alt="Captura de Tela 2025-02-22 às 08 41 17" src="https://github.com/user-attachments/assets/022600aa-1c7f-400e-b064-e0df44938101" />


Otherwise, it will need to mix safe and dirty UTXOs. If they contain enough balance, the algorithm creates the new transaction and returns it, adding a warn message about UTXO mixing and informing the amount of clean sats necessary to be added on wallet in order to enable not mixing them.

Example of a scenario with mixed UTXOs:

<img width="378" alt="Captura de Tela 2025-02-22 às 08 42 47" src="https://github.com/user-attachments/assets/03d437df-640f-4a98-891f-67b3b0ec8c92" />

- There are many design changes on the files of `components` folder
- And finally on the `screen/send/SendDetails.tsx` screen, the graphic changes were made on create the UTXO mixing warn and a button that will open the Privacy configuration screen, which will gather the params necessary for [Bitcoin Coin selection with leverage](https://github.com/rodrigovilar/Coinfy/pull/1).


## WIP - Change on Coin selection algorithm to use selection with leverage

[PR](https://github.com/rodrigovilar/Coinfy/pull/1)

This WIP task aims to implement the [Bitcoin Coin Selection with Leverage
](https://ui.adsabs.harvard.edu/abs/2019arXiv191101330D/abstract) algorithm, proposed by Daniel J. Diroff in order to select coins that Privacy concerns for big changes on UTXOs.

The WIP is written on `class/wallets/legacy-wallet.ts`.


## BUILD & RUN IT

Please refer to the engines field in package.json file for the minimum required versions of Node and npm. It is preferred that you use an even-numbered version of Node as these are LTS versions.

To view the version of Node and npm in your environment, run the following in your console:

```
node --version && npm --version
```

* In your console:

```
git clone https://github.com/rodrigovilar/Coinfy.git
cd Coinfy
git pull
git checkout initial-interface
npm install
```

Please make sure that your console is running the most stable versions of npm and node (even-numbered versions).

* To run on Android:

You will now need to either connect an Android device to your computer or run an emulated Android device using AVD Manager which comes shipped with Android Studio. To run an emulator using AVD Manager:

1. Download and run Android Studio
2. Click on "Open an existing Android Studio Project"
3. Open `build.gradle` file under `Coinfy/android/` folder
4. Android Studio will take some time to set things up. Once everything is set up, go to `Tools` -> `AVD Manager`.
    * 📝 This option [may take some time to appear in the menu](https://stackoverflow.com/questions/47173708/why-avd-manager-options-are-not-showing-in-android-studio) if you're opening the project in a freshly-installed version of Android Studio.
5. Click on "Create Virtual Device..." and go through the steps to create a virtual device
6. Launch your newly created virtual device by clicking the `Play` button under `Actions` column

Once you connected an Android device or launched an emulator, run this:

```
npx react-native run-android
```

The above command will build the app and install it. Once you launch the app it will take some time for all of the dependencies to load. Once everything loads up, you should have the built app running.

* To run on iOS:

```
npx pod-install
npm start
```

In another terminal window within the Coinfy folder:
```
npx react-native run-ios
```
**To debug Coinfy on the iOS Simulator, you must choose a Rosetta-compatible iOS Simulator. This can be done by navigating to the Product menu in Xcode, selecting Destination Architectures, and then opting for "Show Both." This action will reveal the simulators that support Rosetta.
**

* To run on macOS using Mac Catalyst:

```
npx pod-install
npm start
```

Open ios/BlueWallet.xcworkspace. Once the project loads, select the scheme/target BlueWallet. Click Run.

## TESTS

```bash
npm run test
```

## LICENSE

MIT
