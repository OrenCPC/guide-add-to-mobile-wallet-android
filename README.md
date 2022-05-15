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

### Add to Wallet flow

#### Overview

The following diagram demonstrates the Add to Wallet flow.
Below the diagram, you will find an explanation for each step in the flow.<br/><br/>
`TODO: Add the diagram asset`

![](./Docs/assets/add_to_wallet_flow_diagram.png)

### Add Visa SDK to your project

1. Download the push provisioning package from Android Studio, which consists of the following dependencies:
Visa Confidential 32
- VisaPushProvisioning-3.0.0.aar - VisaInAppCore-3.0.0.aar
- TMXProfiling-6.1-67.aar
- TMXProfilingConnections-6.1-67.aar
- samsungpaysdk-2.6.00.jar
- play-services-tapandpay-17.0.1.aar - dexguard-runtime-9.0.6.aar

