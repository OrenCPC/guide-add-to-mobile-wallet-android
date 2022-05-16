# Add To Mobile Wallet

`TO DO: Table of Contents`


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

`TODO: Add forms and other prerequirements`

## Integration steps

### Overview

This section describes the technical steps needed to integrate with Visa's SDK and add a Unit card to the mobile Wallet.



### Add Visa SDK to your project

1. Download the push provisioning package from Android Studio, which consists of the following dependencies:
- VisaPushProvisioning-3.0.0.aar
- VisaInAppCore-3.0.0.aar
- TMXProfiling-6.1-67.aar
- TMXProfilingConnections-6.1-67.aar
- samsungpaysdk-2.6.00.jar
- play-services-tapandpay-17.0.1.aar 
- dexguard-runtime-9.0.6.aar

Note: If a version of the VDE SDK prior to v 2.0.0 has been previously implemented, clients must delete all of the older libraries and add the new ones in the libs folder.

#### Gradle Dependencies:
 In order to add the VDE SDK to the project, obtain the VDE SDK and all dependent libraries. Add these under the libs folder (in case the libs folder doesn't already exist - create it).
 
 
 ![image](https://user-images.githubusercontent.com/98814559/168608922-f19066b5-f282-417c-a534-3fe780d10caf.png)

 Declare them as Gradle dependencies. Make sure to add the open source
 dependencies mentioned in the following Gradle sample.
 
 ``` kotlin
 dependencies {

    // In-App Provisioning SDK
    implementation files('libs/VisaPushProvisioning-3.0.0.aar')
    implementation files('libs/VisaInAppCore-3.0.0.aar')
    implementation files('libs/TMXProfiling-6.1-67.aar')
    implementation files('libs/TMXProfilingConnections-6.1-67.aar')

    // Samsung Pay
    implementation files('libs/samsungpaysdk-2.6.00.jar')

    //dexguard
    implementation files ('libs/dexguard-runtime-9.0.6.aar')

    // Google Pay
    implementation files('libs/play-services-tapandpay-17.0.1.aar')
    implementation "com.google.android.gms:play-services-base:17.0.0"
    implementation 'com.google.android.gms:play-services-instantapps:17.0.0'

    //Nimbus
    implementation "com.nimbusds:nimbus-jose-jwt:9.22"

    implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'

    implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.appcompat:appcompat:1.4.1'

    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.google.code.gson:gson:2.8.6'
    implementation 'com.squareup.retrofit2:converter-gson:2.5.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:3.12.1'

    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'

    //Kotlin Coroutines
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.5.2"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.5.2"

}
 
 ```
 
 
`TO DO: Add the correct dependencies`


`TO DO: Visa SDK Configuration?`

### Add to Wallet flow

#### Overview

The following diagram demonstrates the Add to Wallet flow.
Below the diagram, you will find an explanation for each step in the flow.<br/><br/>

![](./Docs/assets/add_to_wallet_flow_diagram.png)


#### Step 1 - Hold a reference to VisaPushProvisioningInterface

` var pushProvisioningInterface: VisaPushProvisioningInterface? = null `

#### Step 2 - Initialize the VisaPushProvisioningInterface instance and implement the VisaPushProvisioningListener

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
    


#### Step 3 - Initialize Visa SDK and get the signedNonce
Call to `initialize()` method
```kotlin 
pushProvisioningInterface?.initialize()
```

Handle initialization callbacks of the VisaPushProvisioningListener


```kotlin 
override fun initializationSuccess(
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
    }
```

#### Step 4 - Get wallet payload from Unit API
Get the mobile wallet encrypted payload for a specified Unit card from Unit API.
Read more details in [Unit get mobile wallet payload docs](https://docs.unit.co/cards-add-to-mobile-wallet#get-mobile-wallet-payload).

#### Step 5 - Get supported wallets
Call to `getSupportedWallets(request: VPSupportedWalletRequest)` method using the payload from step 5


```kotlin 
val vpSupportedWalletRequest = VPSupportedWalletRequest(payload)
 pushProvisioningInterface?.getSupportedWallets(vpSupportedWalletRequest)
```
 
 Handle `getSupportedWallets` callbacks of the `VisaPushProvisioningListener`
 
 ```kotlin
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
```

#### Step 6 - Start card provisioning
Call to `startCardProvisioning(context: Context, request: VPCardProvisioningRequest)` method:

```kotlin 
val vpCardProvisioningRequest = VPCardProvisioningRequest(wallet.code, wallet.name)
pushProvisioningInterface?.startCardProvisioning(context, vpCardProvisioningRequest)
```

Handle `startCardProvisioning` callbacks of the `VisaPushProvisioningListener`

```kotlin 
override fun cardProvisioningSuccess(
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
      }
```

