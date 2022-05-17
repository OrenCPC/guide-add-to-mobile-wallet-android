# Add To Mobile Wallet

`TO DO: Table of Contents`


## Introduction

Mobile wallets are a convenient way to pay with your debit cards using your smartphone.

Use Visa's In-App Provisioning SDK to enable this capability for your end-users.

Contact Unit for the SDK's binaries.

## Google Developer Portal

### Prerequirements 

1. Ensure that the client (and any mobile app providers/developers) have executed any required CTA agreements, as well as the Google NDA, via the Google Developer Portal
2. Verify that the client BIN(s) are token eligible and set up to support the Google Pay wallet type (Unit can assist with this step)
3. Create an account in the Google Developer Portal, if one does not already exist //ask about the developer portal
4. Review all detailed tasks required for In-App Provisioning outlined in the Google Developer Portal //will be understood after 3 will be clear
#### 5. Follow the first 2 steps in Google's step-by-step [instructions](https://developers.google.com/pay/clients/apis/push-provisioning/android/launch-process) for apps that intend to use Google Pay: 


##### Step 1: UX/branding review
          
a. Review and adhere to [Google's branding and user experience requirements](https://developers.google.com/pay/issuers/apis/push-provisioning/android/branding-guidelines)
                    
b. You may download [Google's sample app](https://developers.google.com/pay/issuers/apis/push-provisioning/android/android-sample-app
) to further understand the branding guidelines:
<img width="831" alt="image" src="https://user-images.githubusercontent.com/98814559/168742114-9de62ac9-a472-4daa-9204-1e0c40f484eb.png">

                    
c. Watch the flow diagram: <img width="925" alt="image" src="https://user-images.githubusercontent.com/98814559/168740199-876a02e4-9977-4e89-922b-2194f5bb867f.png">

d. Complete the user experience branding review with Google (watch the requirements at [launch page](https://developers.google.com/pay/issuers/apis/push-provisioning/android/launch-process#step_1_uxbranding_review) and then fill                    [this form](https://support.google.com/faqs/contact/pp_api_ux)

          

##### Step 2: [Push Provisioning API access](https://developers.google.com/pay/issuers/apis/push-provisioning/android/launch-process#step_2_push_provisioning_api_access)

Complete this [form](https://support.google.com/faqs/contact/pp_api_allowlist)
with the following information:

          - Select "BIN Sponsor or Program Manager working on behalf of a financial company or card issuer
    
          - Enter your Bank Partner's name (not Unit)
    
          - Answer "Does the financial company or card issuer have a signed CTA agreement with Google?" with Yes
    
          - Network will be Visa

*Note: you will complete the other steps after app development (when ready to begin testing)*

## Integration steps

### Overview

This section describes the technical steps needed to integrate with Visa's SDK and add a Unit card to the mobile Wallet.

### Prerequirements

Before you start with the technical integration, please follow the prerequirements below:

 - The minimum supported Android SDK API version is 26.

 - The deployment target is Android SDK API version is 30.

 - The minimum supported Kotlin version is 1.5.10.

 - The minimum supported Java version is 1.8.

 - The SDK requires to use a physical Android device and not a virtual device. 

 - Make sure you have the relevant wallets to which you will add cards installed on your device

### Add VDE (Visa Digital Enablement) SDK to your project

1. Get the push provisioning package from Unit, which consists of the following dependencies:
   - VisaPushProvisioning-3.0.0.aar
   - VisaInAppCore-3.0.0.aar
   - TMXProfiling-6.1-67.aar
   - TMXProfilingConnections-6.1-67.aar
   - samsungpaysdk-2.6.00.jar
   - play-services-tapandpay-17.0.1.aar 
   - dexguard-runtime-9.0.6.aar
 
2. Add the dependencies from the previous step under the [libs](https://stackoverflow.com/a/35241990) folder of your project
    - In case the libs folder doesn't already exist you can [create it](https://stackoverflow.com/a/25681629)


3. Gradle Dependencies:

   a. Declare the following Gradle dependencies. Make sure to add the open source dependencies mentioned:
  
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

   b. If your project uses **Proguard**, the following additional instructions should be followed:
   
   - These **Proguard** rules should be used:
      
```kotlin
#Add below in your proguard file for Pro SDK
-keep class kotlin.Metadata { *; }

-keep class com.threatmetrix.** { *; }

-keep class com.visa.** { *; }

#If app supporting Samsung Pay then add below in your proguard file
-keep class com.samsung.android.sdk.** { *; }

#If app supporting Google Pay then add below in your proguard file
-keep class androidx.lifecycle.** { *; }
```
   - Gradle Dependencies for use with **Proguard:**
   
```kotlin
//Includes all .jar and .aar from libs folder
implementation fileTree(dir: 'libs', include: ['*.jar', '*.aar'])

//okhttp covers for okttp, okio dependency
implementation "com.squareup.okhttp3:okhttp:3.12.0" implementation "com.squareup.okhttp3:logging-interceptor:3.12.0" implementation "net.minidev:json-smart:2.3"

//For TrustDefender
implementation "commons-io:commons-io:2.6" implementation "commons-codec:commons-codec:1.13"

//Nimbus
implementation "com.nimbusds:nimbus-jose-jwt:8.2.1"
implementation "com.google.code.gson:gson:2.8.5"

//Kotlin Coroutines
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.3.3" implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.3"

//If App is supporting Google Pay below must be included implementation 'com.google.android.material:material:1.2.1'
implementation "com.google.android.gms:play-services-base:17.0.0" implementation 'androidx.lifecycle:lifecycle-extensions:2.1.0'

```

4. Additional instructions for **Samsung Pay**
   a. If the target device includes Android 11 and above, add the below in the app's AndroidManifest.xml file. If this is not added, the VDE SDK may return a 'NOT_INSTALLED' status.
   
```kotlin
<queries>
          <package android:name="com.samsung.android.spay" />
</queries>
```
   
   
   b.

 
 
`TO DO: Should you add the samsung dependency if the company doesn't support Samsung?`




### Add to Wallet flow

#### Overview

The following diagram demonstrates the Add to Wallet flow.
Below the diagram, you will find an explanation for each step in the flow.<br/><br/>

<img width="1373" alt="image" src="https://user-images.githubusercontent.com/98814559/168770935-aa2158a4-baf4-437a-8e1e-873b710cacbc.png">

#### Step 1 - Hold a reference to VisaPushProvisioningInterface

```kotlin
var pushProvisioningInterface: VisaPushProvisioningInterface? = null 
```

#### Step 2 - Initialize the VisaPushProvisioningInterface instance and implement the VisaPushProvisioningListener interface in your class

Initialize the `VisaPushProvisioningInterface` instance in the `init()` method of your class:

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
}

override fun cardProvisioningFailure(
    pushProvisioningInterface: VisaPushProvisioningInterface,
    error: VPError
) {
    // handle error
}
```

