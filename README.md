# Add To Mobile Wallet

## Introduction

Mobile wallets are a convenient way to pay with your debit cards using your smartphone.

Use Visa's In-App Provisioning SDK to enable this capability for your end-users.

Contact Unit for the SDK's binaries.

## Prerequirements

Before you start with the technical integration, please follow the prerequirements below:

• The minimum supported Android SDK API version is 26.

• The deployment target is Android SDK API version is 30.

• The minimum supported Kotlin version is 1.5.10.

• The minimum supported Java version is 1.8.

`TODO`

## Integration steps

### Overview

This section describes the technical steps needed to integrate with Visa's SDK and add a Unit card to the mobile Wallet.

`TODO`


### Add Visa SDK to your project

1. Download the push provisioning package from Android Studio, which consists of the following dependencies:
- VisaPushProvisioning-3.0.0.aar - VisaInAppCore-3.0.0.aar
- TMXProfiling-6.1-67.aar
- TMXProfilingConnections-6.1-67.aar
- samsungpaysdk-2.6.00.jar
- play-services-tapandpay-17.0.1.aar - dexguard-runtime-9.0.6.aar

Note: If a version of the VDES DK prior to v 2.0.0 hasbeen previously implemented, clients must delete all of the older libraries and add the new ones in the libs folder.

### Add to Wallet flow

#### Overview

The following diagram demonstrates the Add to Wallet flow.
Below the diagram, you will find an explanation for each step in the flow.<br/><br/>
`TODO: Add the diagram asset`

![](./Docs/assets/add_to_wallet_flow_diagram.png)

#### Step 1 - Gradle Dependencies:
 In order to add the VDE SDK to the project, obtain the VDE SDK and all dependent libraries. Add these under the libs folder and declare them as Gradle dependencies (in case the libs folder doesn't already exist - create it). Make sure to add the open source
 dependencies mentioned in the following Gradle sample.
 
 
//Only if App is developed in kotlin and using kotlin-plugins for development then below can be used for kotlin coroutines dependencies as well, no need to add separate dependency.
 
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
android { compileSdkVersion 30 defaultConfig {
minSdkVersion 26
targetSdkVersion 30 }
}
dependencies {
//In-App Provisioning SDK
implementation files('libs/VisaPushProvisioning-3.0.0.aar')
implementation files('libs/VisaInAppCore-3.0.0.aar')
implementation files('libs/TMXProfiling-6.1-67.aar')
implementation files('libs/TMXProfilingConnections-6.1-67.aar')
//Samsung Pay
implementation files('libs/samsungpaysdk-2.6.00.jar')
//Google Pay
implementation files('libs/play-services-tapandpay-17.0.1.aar')
implementation "com.google.android.gms:play-services-base:17.0.0"
//okhttp covers for okttp, okio dependency implementation "com.squareup.okhttp3:okhttp:3.12.0"
implementation "com.squareup.okhttp3:logging-interceptor:3.12.0" implementation "net.minidev:json-smart:2.3"
//For TrustDefender
implementation "commons-io:commons-io:2.6"
implementation "commons-codec:commons-codec:1.13"
//Nimbus
implementation "com.nimbusds:nimbus-jose-jwt:8.2.1" implementation "com.google.code.gson:gson:2.8.5"
implementation "androidx.lifecycle:lifecycle-process:$any-latest-version"
//If kotlin plugins are not added then below dependencies require to add to kotlin coroutines used by SDK.
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:$any-latest-version"
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:$any-latest-version" }

#### Step 2 - Hold a reference to VisaPushProvisioningInterface

` var pushProvisioningInterface: VisaPushProvisioningInterface? = null `

#### Step 3 - Initialize the VisaPushProvisioningInterface instance and implement the VisaPushProvisioningListener

Initialize the VisaPushProvisioningInterface instance in the init() method of your class:
```kotlin
pushProvisioningInterface = VisaPushProvisioningInterfaceFactory.createPushProvisioningInterface(this)
```

Declare the VisaPushProvisioningListener methods, we will implement them later:

```kotlin
   override fun initializationSuccess(
   pushProvisioningInterface: VisaPushProvisioningInterface,
   response: VPInitResponse
   ) { }

    override fun initializationFailure(
    pushProvisioningInterface: VisaPushProvisioningInterface,
    error: VPError
    ) { }
    
    override fun cardProvisioningSuccess(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        response: VPCardProvisioningResponse
    ) { }
    
    override fun cardProvisioningFailure(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        error: VPError
    ) { }
    
     override fun supportedWalletSuccess(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        response: VPSupportedWalletResponse
    ) { }
    
     override fun supportedWalletFailure(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        error: VPError
    ) { } 
```
    


#### Step 4 - Initialize Visa SDK and get the signedNonce
Call to `initialize()` method
`pushProvisioningInterface?.initialize()`

Handle initialization callbacks of the VisaPushProvisioningListener


`override fun initializationSuccess(
   pushProvisioningInterface: VisaPushProvisioningInterface,
   response: VPInitResponse
   ) { 
   val signedNonce = response.signedNonce
   }
   
   override fun initializationFailure(
    pushProvisioningInterface: VisaPushProvisioningInterface,
    error: VPError
    ) {
    //handle error
    }`

#### Step 5 - Get wallet payload from Unit API
Get the mobile wallet encrypted payload for a specified Unit card from Unit API.
Read more details in [Unit get mobile wallet payload docs](https://docs.unit.co/cards-add-to-mobile-wallet#get-mobile-wallet-payload).

#### Step 6 - Get supported wallets
Call to `getSupportedWallets(request: VPSupportedWalletRequest)` method using the payload from step 5


`
val vpSupportedWalletRequest = VPSupportedWalletRequest(payload)
 pushProvisioningInterface?.getSupportedWallets(vpSupportedWalletRequest)`
 
 Handle `getSupportedWallets` callbacks of the `VisaPushProvisioningListener`

override fun supportedWalletSuccess(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        response: VPSupportedWalletResponse
    ) { 
    val wallet = response.wallets
    }
    
     override fun supportedWalletFailure(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        error: VPError
    ) {
     // handle error
    }    
`

#### Step 7 - Start card provisioning
Call to `startCardProvisioning(context: Context, request: VPCardProvisioningRequest)` method:

`val vpCardProvisioningRequest = VPCardProvisioningRequest(wallet.code, wallet.name)
pushProvisioningInterface?.startCardProvisioning(context, vpCardProvisioningRequest)`

Handle `startCardProvisioning` callbacks of the `VisaPushProvisioningListener`

`override fun cardProvisioningSuccess(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        response: VPCardProvisioningResponse
    ) {
  // update wallet status for Wallet
  // provisioning flow completed, you can update the UI}
    
    override fun cardProvisioningFailure(
        pushProvisioningInterface: VisaPushProvisioningInterface,
        error: VPError
    ) {
      // handle error
}`

