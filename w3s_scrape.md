
URL: https://developers.circle.com/w3s/docs/programmable-wallets-primitives
Title: Web3 Services Primitives
Description: undefined
Keywords: undefined

Discover the essential primitives that power Circle's Web3 Services:

- Environments: Understand the differences between Testnet (Sandbox) and Mainnet (Production) environments that facilitate the development and deployment.
- Wallets: Explore the core functionality of wallets, facilitating secure storage and management of digital assets for your end-users.
- Wallet Sets: Understand the concept behind wallet sets, which enable efficient management of multiple developer-controlled wallets.
- Users: Streamline user interactions within Circle's Web3 ecosystem by simplifying pin and security question management for web3 wallets.
- Smart Contracts: Explore smart contracts and our features that enable deployment and interaction.
- Transactions: Delve into the transactional capabilities offered by Circle's Web3 Services, empowering asset transfers, contract interactions, and deployments.
- Paymaster Policies: Understand Gas Station Paymaster policies and how they provide control for the sponsoring of gas fees.
- Monitored Tokens: Enhanced control over token visibility, allowing selective filtering of tokens to enhance user experience.
By understanding these foundational components, you can unlock the full potential of Circle's Web3 Services and enhance your decentralized web experiences.

Video Tutorial: Understanding Programmable Wallets Primitives
##
Environments
[](#environments)
Circle supports two environments: Testnet (Sandbox) and Mainnet (Production). When you initially sign up, you will be provided access to Testnet, which is free to use. Once ready to promote to Mainnet, select “Access Mainnet” from the developer dashboard and start the onboarding process.

- Testnet: Circle's Testnet environment acts as a Sandbox, enabling developers to safely test the functionality of Web3 Services without the risks associated with a Production environment. Connected to blockchain Testnets, it provides a secure space to build applications and conduct testing before deployment.
- Mainnet: The Mainnet environment represents the Production environment where real user transactions and interactions occur. It is connected to the live blockchain network, ensuring the handling of real-world tokens. Developers should consider potential differences in blockchain confirmation times and gas fees when migrating from the Testnet to the Mainnet environment.
##
Wallets
[](#wallets)
A Web3 Wallet, a decentralized or blockchain wallet, is a digital wallet that allows users to securely store, manage, and interact with their crypto assets and digital identities on the Web3 ecosystem. Unlike traditional wallets, Web3 wallets integrate with decentralized applications (dApps), enabling users to authenticate, sign transactions, and interact with blockchain-based services. With features like encryption and decentralized key management, Web3 wallets provide users with control, privacy, and security over their digital assets and interactions within the decentralized web.

Wallet attributes:

- Address:  An on-chain identifier specific to a blockchain. It's worth noting that multiple wallets can share the same address on different EVM blockchains.
- Custody Type: Determines the control over the invocation of the wallet's private key, distinguishing between user-controlled and developer-controlled wallets. Explore the [Programmable Wallets infrastructure models](/w3s/docs/programmable-wallets-infrastructure-models) for further insights.
- Account Type: Specifies whether the wallet is of the externally owned account (EOA) type or smart contract account (SCA) type.
- State: Indicates the current state of the wallet, signifying whether it is active or frozen.
- Blockchain: Identifies the blockchain and network to which the wallet belongs, providing context and compatibility information.
##
Wallet sets
[](#wallet-sets)
A wallet set is a [hierarchical deterministic (HD) wallet](https://www.investopedia.com/terms/h/hd-wallet-hierarchical-deterministic-wallet.asp), where all the children's wallets are created and managed by the same unique cryptographic private key. For each developer-controlled wallet created, you must provide a wallet set from which the wallet will be created.

Wallet set attributes: 

- Wallets: A wallet set can contain one or many wallets. A wallet set can have wallets with addresses on multiple blockchains. On EVM chains, a wallet set can have wallets on different chains sharing the same address.
##
Users
[](#users)
A User represents an end-user of your application, allowing you to map their account to associated wallets, tokens, and transactions. The userId, provided during account creation, is a unique identifier connecting the user to these components.

User attributes:

- PIN: A Personal Identification Number (PIN) enhances security by authenticating users when signing wallet transactions. Users must set up a unique numerical code PIN to access their account and perform sensitive actions.
- Security Questions: Users must set up security questions for account recovery, providing additional protection.
- User Token: The user token is a 60-minute session token utilized for requests requiring a user challenge, such as entering a PIN code. After expiration, a new user token is generated via the same endpoint.
- Challenge: Challenges act as checkpoints, prompting users to enter their PIN code to authorize critical actions like transactions or executing smart contracts. During user creation, the initial challenge guides users in setting their PIN code and Recovery Method, enabling account recovery if necessary.
- Status: Denotes whether the user is enabled or disabled, indicating their account's current operational state.
##
Smart contracts
[](#smart-contracts)
Smart contracts are the cornerstone of your deployed and on-chain imported contracts within the Smart Contract Platform (SCP). To interact with a smart contract on-chain, it is necessary to import it first. All smart contracts deployed via the web3 services APIs can be readily interacted with by default. Once imported or deployed, developers can leverage the functionality provided by the smart contract. SCP offers seamless accessibility to smart contracts through the developer console and APIs.

With SCP, developers can perform a wide range of tasks, including deploying NFT contracts, creating on-chain loyalty programs, interacting with popular DeFi projects like Uniswap with just a few clicks, seamlessly integrating Circle contracts (such as CCTP) into their apps, and deploying custom contracts written specifically for their application needs.

###
ABI (Application Binary Interface)
[](#abi-application-binary-interface)
A smart contract Application Binary Interface (ABI) is a standardized interface description for interacting with a smart contract deployed on a blockchain. It defines the smart contract's structure, data types, and functions, enabling seamless communication between the contract and external entities, such as user interfaces, other contracts, or off-chain applications.

The smart contract ABI serves as a contract's public interface, outlining how to interact with it. It typically includes the following components:

- Function Signatures: The names of the functions within the smart contract, along with their input and output parameter types. These signatures define the available operations that can be performed on the contract.
- Event Definitions: Events provide information about specific occurrences or state changes within the contract. The ABI includes event definitions, specifying their names and the data associated with each event.
- Data Structures: If the contract uses complex data structures or custom types, the ABI includes their definitions. Examples can include structs, arrays, or mappings.
The ABI allows developers and external applications to interact with a smart contract without understanding the contract's underlying implementation details or source code. It provides a standardized and language-agnostic way to communicate with smart contracts, facilitating interoperability and seamless integration with various platforms, tools, and services.

Smart contract attributes:

- ID: Once a smart contract is imported or deployed, Circle will assign an ID. This ID will be used in subsequent requests to read and execute the contract.
- ABI Function Signature: Used to specify the desired function to be read or executed. By providing the function signature, developers can effectively read data from the contract or trigger actions defined by that function.
- ABI Parameters: The parameters provided to the ABI function signature. Parameters are provided to the ABI function signature in the same order as received.
- ABI JSON: The contract's ABI in a JSON stringified format.
- Functions: Read and execute functions supported by the contract. The names of the functions within the smart contract, along with their input and output parameter types. These signatures define the available operations that can be performed on the contract.
- Events: Events the contract can emit. Events provide information about specific occurrences or state changes within the contract. The ABI includes event definitions, specifying their names and the data associated with each event.
- Transactions: List of on-chain transactions executed on the contract. Currently, it is only available in the developer dashboard.
##
Transactions
[](#transactions)
A transaction encompasses all blockchain activities, such as token transfers (inbound or outbound), smart contract deployment, and smart contract execution.

Transactions also include metadata that outlines their state. Once a transaction is created, Circle monitors the transactions on its respective networks containing a state field. You can find more information about transaction states in [this guide](doc:asynchronous-states-and-statuses).

Each transaction includes an Operation field specifying the transfer's purpose or use case. The table below highlights the various operations and their corresponding destination address:

Operation Type	Operation Field Value	Description of Destination Address Field Value
Transfer	TRANSFER	Externally owned account (EOA) that receives the transfer.
Execute	CONTRACT_EXECUTION	Contract address.
Deploy	CONTRACT_DEPLOY	Deployment of a transaction.
##
Paymaster policies
[](#paymaster-policies)
The Gas Station Paymaster Policy is a set of rules and configurations that govern the gas fee sponsorship functionality, defining parameters and limitations for subsidizing gas fees on a blockchain network for your application end-users.

To enable gas fee sponsorship, a policy must be activated. For Testnet, Circle automatically preconfigures a policy for you at the time of account creation. For Mainnet, you are required to configure them. Please see Policy Management for more details.

Policy Attributes:

- Network: Specifies the blockchain network where the policy applies, ensuring compatibility and accurate gas fee calculation.
- Maximum Daily Spend: Sets the maximum allocated budget in USD for gas fee sponsorship within a 24-hour period. This limit helps manage and control the expenditure on gas fees.
- Maximum Transaction Spend: Optionally defines a cap on the amount of gas fees sponsored per individual transaction in USD. This allows for precise control over the financial commitment made for each transaction.
- Maximum Operations per Day: Optionally limits the number of daily operations (e.g., transactions) that can be sponsored. This parameter helps maintain a predetermined frequency and volume of sponsored operations.
- Blocklist: Optionally limits wallets that are eligible for gas sponsorship. This means that transactions initiated from these wallets will not have their gas fees covered. The blocklist allows for the selective exclusion of certain wallet addresses from receiving gas sponsorship.
##
Monitor tokens
[](#monitor-tokens)
The Monitored Tokens feature in the Programmable Wallet empowers developers with enhanced control over viewing token balances and transactions. This feature enables developers to easily manage and filter out tokens unrelated to their use case from our API responses, thereby selectively screening which tokens end-users can see, providing a more streamlined and personalized experience. Using the APIs, developers can easily add tokens via their unique token IDs. 

By default, the system does not have any Monitored Tokens. This means that all tokens are monitored without specific filtering or restrictions. The system will track and monitor all tokens by default, providing comprehensive visibility and monitoring for all token activities.

Currently, the monitor token supports native coins, ERC-20, ERC-721, and ERC-1155.

Monitor tokens attributes:

- Token ID: Each token deposited into the programmable wallet generates a specific token ID, which developers can obtain by inspecting the tokens in wallets. 
- Token: Associated attributes for the token, including names, the contract standard, the blockchain, decimal point, symbol, and the token’s address.
Updated 24 days ago


URL: https://developers.circle.com/w3s/docs/programmable-wallets-infrastructure-models
Title: Infrastructure Models and Wallet Types
Description: undefined
Keywords: undefined

This article describes the supported wallet infrastructure models, or wallet types, to help you determine which type best meets the needs of your end-users. Each wallet type has specific requirements and provides specific benefits. Use the information in this topic to choose the wallet type that best supports your business objectives.

Video Tutorial: Understanding the Types of Programmable Wallets
##
Wallet types (infrastructure models)
[](#wallet-types-infrastructure-models)
Infrastructure models govern who controls the digital assets in a specific wallet. Businesses and individuals need to understand the characteristics of each type of infrastructure to determine the correct wallet for their needs.

Circle Programmable Wallets and Wallet Signing Service offer two infrastructure models: user-controlled wallets and developer-controlled wallets. Each model provides distinct advantages to suit different requirements.

###
User-controlled wallets
[](#user-controlled-wallets)
User-controlled wallets grant full autonomy to app users, enabling them to have complete control over their wallets. Users set up a PIN code for authorization and have sole access to their private keys. This ensures privacy and prevents unauthorized access or movement of assets. The user-controlled wallet setup prioritizes security while providing a familiar and seamless Web2-like user experience.

We currently support Web, iOS, Android and React Native SDKs. Start building user-controlled wallets with the [quickstart guide](/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart)!


###
Developer-controlled wallets
[](#developer-controlled-wallets)
Developer-controlled wallets allow developers to retain control over their users' wallets while streamlining user interactions with the Web3 ecosystem. These wallets facilitate various blockchain interactions on behalf of the users, such as sending and receiving digital assets, smart contract interactions, and NFT minting. With developer-controlled wallets, users can enjoy the benefits of the blockchain without needing to understand its complexities or manage private keys.

Get started with the [Developer-Controlled Wallets quickstart](/w3s/docs/developer-controlled-wallet-quickstart).


##
Choosing your wallet type (infrastructure model)
[](#choosing-your-wallet-type-infrastructure-model)
The critical distinction between user-controlled and developer-controlled wallets is which party controls the ability to access the private key used to interact with the blockchain.

User-controlled and developer-controlled wallets can make transactions, interact with smart contracts, and sign messages. In the case of a user-controlled wallet, the user controls these abilities. In the case of a developer-controlled wallet, the developer has this control.

##
Why choose user-controlled wallets?
[](#why-choose-user-controlled-wallets)
User-controlled wallets give your app's users access and control over the underlying private keys of their wallets. Circle provides a Web2-like user experience, using a PIN code mechanism and a set of security questions for account recovery, abstracting the complexity of seed phrases from your users. Additionally, Circle uses best-in-class security approaches to power its Programmable Wallets with secure multi-party computation.

###
PIN code responsibility for user-controlled wallets
[](#pin-code-responsibility-for-user-controlled-wallets)
Users invoke usage of their private keys through their specified PIN code, which is then used to encrypt and decrypt access to the private keys upon invoking a transaction on the blockchain. Your users will be ultimately responsible for accessing their wallets, so they must understand the need to remember their PIN code and the answers to their security questions.

With user-controlled wallets, your users have access to the speed and security of the blockchain but with a much more familiar user experience.

##
Why choose developer-controlled wallets?
[](#why-choose-developer-controlled-wallets)
Developer-controlled wallets allow you to create and manage asset flows on behalf of your users, providing them with easy-to-use access to Web3 functionality. These wallets allow you to control virtually all blockchain interactions for your users, like sending and receiving digital assets, smart contract interactions, and NFT minting.

By using developer-controlled wallets, your users do not need to understand the complexities of the Web3 ecosystem or worry about losing their assets because of a lost seed phrase to access the speed of the blockchain.

This article explores the various options available for wallets, taking into account both the account type and the infrastructure model. The infrastructure model determines who controls the assets in the wallet, while the account type determines the underlying structure of the wallet itself. Understanding these concepts is essential for developers to make informed decisions that align with their specific use cases.

Updated 1 day ago

-
[Account Types](/w3s/docs/programmable-wallets-account-types)

URL: https://developers.circle.com/w3s/docs/gas-station-paymaster
Title: Paymaster
Description: undefined
Keywords: undefined

Circle’s Paymasters are smart contracts that sponsor network fees for users of your SCA wallets on your behalf. Circle deploys these paymasters on every chain, so you don’t have to. If a transaction meets the policies you set up, Circle's off-chain service lets the paymaster sponsor that transaction.

The following are the on-chain addresses of Circle's Paymasters:

Blockchain	Network	Blockchain Address
Ethereum	Mainnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://etherscan.io/address/0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25)
Ethereum	Sepolia Testnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://sepolia.etherscan.io/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Polygon	Mainnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://polygonscan.com/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Polygon	Amoy Testnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://www.oklink.com/amoy/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/android
Title: Android SDK
Description: undefined
Keywords: undefined

The Circle Android SDK enables your mobile application to provide user-controlled programmable wallets. By integrating this SDK, your users can securely input sensitive data like PINs or security answers. The SDK encrypts the request body using a secret key, protecting your users' information.

📘 The Web and Mobile SDKs preserve the user keyshare with the individual, giving them complete control. You must use the SDKs with the user-controlled wallet product. Additionally, the Web and Mobile SDKs support only the user-controlled wallet product.

At Circle, we understand the importance of end-to-end security for your application and the need to create a tailored and seamless user experience for your end-users. Hence, Circle’s SDK also exposes functionality for you to customize the description and layout: 

-
UI Title and Subtitle Customization: Modify the title and subtitle to reflect your brand identity or provide specific instructions.

-
Custom PIN Code Input Layout: Adjust the layout and styling of the PIN code input field to align with your application's design guidelines.

-
Question List Configuration: Set the list of security questions displayed in the User Wallet UI, allowing users to choose from a predefined set.

-
SDK Initialization: Initialize the Web SDK by setting the endpoint server, ensuring seamless communication between your application and Circle’s services.

-
Predefined Error Messages: Customize the error messages displayed to users, providing a more personalized experience and guidance.

-
ChallengeID Acceptance and Operation Retrieval: Easily accept the challengeId and retrieve any relevant operations within the SDK.

💡Tip: See the [Android SDK UI Customization API](/w3s/docs/android-sdk-ui-customization-api) article to customize the SDK.

##
Install the SDKs
[](#install-the-sdks)
The Android SDK supports: 

-
The Android SDK supports Android API level 21+. Earlier versions are not supported.

-
Recommend using the latest version of Android Studio.

###
Maven (recommended)
[](#maven-recommended)
Add the maven repository to your gradle file. It's suggested that load settings from local.properties:


repositories {
	...
	maven { 
        	Properties properties = new Properties()
		      // Load local.properties.
        	properties.load(new File(rootDir.absolutePath + "/local.properties").newDataInputStream())
			
        	url properties.getProperty('pwsdk.maven.url')
        	credentials {
        			username properties.getProperty('pwsdk.maven.username')
        			password properties.getProperty('pwsdk.maven.password')
		}
	}
}
Add the maven setting values in the local.properties file:


pwsdk.maven.url=https://maven.pkg.github.com/circlefin/w3s-android-sdk
pwsdk.maven.username=<GITHUB_USERNAME>
# Fine-grained personal access tokens or classic with package write permission.
pwsdk.maven.password=<GITHUB_PAT> 
Add the dependency:


dependencies {
	implementation 'circle.programmablewallet:sdk:version'
}
###
Manual
[](#manual)
You can also manually set up the Android SDK by downloading it from [GitHub: circlefin/w3s-android-sdk](https://github.com/circlefin/w3s-android-sdk).

##
SDK architecture
[](#sdk-architecture)
You must use Web, iOS, or Android SDKs to access the user-controlled Programmable Wallet product. The SDK secures, manages, and communicates with your server to ensure your user’s keyshare, always stays with them and is not exposed to your servers.

To learn more, see [SDK Architecture for User-Controlled Wallets](/w3s/docs/sdk-architecture-for-user-controlled-wallets).


##
SDK API references
[](#sdk-api-references)
###
WalletSdk
[](#walletsdk)
object WalletSdk

Public Methods	
@Throws(Throwable::class)
Unit	init(context: Context?, config: WalletSdk.Configuration?)
Configure the Circle endpoint for SDK to connect, throw Throwable if the parameters are null or endpoint and appId’s format are invalid.
Unit	setLayoutProvider(provider: LayoutProvider?)
Set a LayoutProvider derived class for customization, e.g. error code message, font and color.
Ignore when the parameter is null.
Unit	setViewSetterProvider( provider: ViewSetterProvider? )
Set a ViewSetterProvider derived class for image customization.
@Throws(Throwable::class)
Unit	setSecurityQuestions( questions: Array<SecurityQuestion?>? )
Set the security question list, throw Throwable when the SecurityQuestion array is empty or contains any question whose title length is not 2~512.
Unit	execute( activity: Activity?, userToken: String?, encryptionKey: String?, challengeId: Array<String?>?, callback: Callback<ExecuteResult>? )
Execute the operations by challengeId and call the callback after sending the request to Circle endpoint.
Ignore when callback is null.
Callback will receive onError() when parameters are null or invalid.
Unit	addEventListener(listener: EventListener?)
Register an EventListener for the app to handle events, e.g. forgot PIN.
Ignore when the parameter is null.
Unit	removeEventListener(listener: EventListener?)
Unregister an EventListener.
Ignore when the parameter is null.
Unit	moveTaskToFront(context: Context?)
Bring the SDK UI to foreground. If the app launches another Activity in onEven() and onError() and makes the SDK UI in background, use this API to go back to the SDK UI.
Ignore when the parameter is null.
Unit	setBiometricsPin( activity: Activity?, userToken: String?, encryptionKey: String?, callback: Callback<ExecuteResult>? )
Setup BiometricsForPin and call the callback after operation.
Ignore when callback is null.
Callback will receive onError() when parameters are null or invalid.
Unit	setCustomUserAgent(userAgent: String?)
Set custom user agent value.
Ignore when the parameter is null.
String	sdkVersion()
Get SDK version.
####
EventListener
[](#eventlistener)
EventListener interface that receives events when an event is triggered.

interface EventListener

Public Methods	
abstract Unit	onEvent(event: ExecuteEvent)
Called when the event triggered.
####
ExecuteEvent
[](#executeevent)
ExecuteEvent, see WalletSdk.addEventListener().

enum ExecuteEvent

Enum values
forgotPin
####
WalletSdk.Configuration
[](#walletsdkconfiguration)
SDK Configuration for WalletSdk init.

data class Configuration

Constructors
constructor(endPoint: String?, appId: String?)
Init with Circle endpoint. SDK will connect to this endpoint.
constructor( endpoint: String?, appId: String?, settingsManagement: SettingsManagement? )
Init with Circle endpoint. SDK will connect to this endpoint. The SettingsManagement can bring extra setting flags to use.
####
SettingsManagement
[](#settingsmanagement)
SettingsManagement use to bring extra setting flags to Configuration that would be used when initial WalletSdk.
data class SettingsManagement

Constructors
constructor(isEnableBiometricsPin: Boolean = false)
Properties	
Boolean	isEnableBiometricsPin
Flag that decides whether to use biometrics to protect PIN or not.
###
LayoutProvider
[](#layoutprovider)
LayoutProvider helps perform customization during runtime.

open class LayoutProvider

Public Methods	
open TextConfig?	getTextConfig(key: String)
Define strings with specific configurations for general string customization. Returned TextConfig will replace strings.xml, colors.xml, and styles.xml values.

All keys are listed in [C Index Table](/w3s/docs/android-sdk-ui-customization-api#c-index-table---resourcekey).
open Array<IconTextConfig?>?	getIconTextConfigs( key: Resource.IconTextsKey )
Define icon and string sets with specific configurations for icon text list item customization.

All keys are listed in [B Index Table](/w3s/docs/android-sdk-ui-customization-api#b-index-table---resourceicontextskey).
open
Array<TextConfig?>?	getTextConfigs( key: Resource.TextsKey )
Define strings with specific configurations for special text customization.

All keys are listed in [A Index Table](/w3s/docs/android-sdk-ui-customization-api#a-index-table---resourcetextskey).
open String?	getErrorString(code: ApiError.ErrorCode)
Define the error description.

All error codes are listed in ApiError.ErrorCode.
open String?	getDateFormat()
Get display date format, e.g. the answer of a security question in which inputType is datePicker.

Only those 3 strings are valid values:
1. “YYYY-MM-DD”,
2. “DD/MM/YYYY”
3. “MM/DD/YYYY”

If it returns an invalid value, the default value would be “YYYY-MM-DD”.

All supported formats are listed Resource.DateFormat.
open Boolean	isDebugging()
true: default value, check returned value, and print warn level log
false: skip checking and turn off the log.
####
Resource.DateFormat
[](#resourcedateformat)
interface DateFormat

Constants	
String	YYYYMMDD_HYPHEN = "YYYY-MM-DD"
DDMMYYYY_SLASH = "DD/MM/YYYY"
MMDDYYYY_SLASH = "MM/DD/YYYY"
Available values of LayoutProvider.getDateFormat().
####
Resource.Key
[](#resourcekey)
interface Key

Static Fields	
String	circlepw_show_pin = "circlepw_show_pin"
circlepw_hide_pin = "circlepw_hide_pin"
...
See [C Index Table](/w3s/docs/android-sdk-ui-customization-api#c-index-table---resourcekey).
####
TextConfig
[](#textconfig)
Data class for text customization.

data class TextConfig

Constructors
constructor( text: String?, gradientColors: IntArray?, font: Typeface? )
constructor(text: String?, textColor: Int, font: Typeface?)
constructor(text: String?)
Properties	
String?	text

Text to display.
[@CorlorInt](https://developer.android.com/reference/androidx/annotation/ColorInt) IntArray?	gradientColors
Array of Gradient text color. Only used by
TextsKey.enterPinCodeHeadline,
TextsKey.securityIntroHeadline,
TextsKey.newPinCodeHeadline
[@CorlorInt](https://developer.android.com/reference/androidx/annotation/ColorInt) IntArray?	textColor
Text color.
[Typeface?](https://developer.android.com/reference/android/graphics/Typeface#createFromAsset(android.content.res.AssetManager,%20java.lang.String))	font
Font.
####
IconTextConfig
[](#icontextconfig)
Data class for icon text list item customization.

data class IconTextConfig

Constructors
constructor(setter: IImageViewSetter?, textConfig: TextConfig?)
Properties	
IImageViewSetter?	setter
The ImageView setter for image customization.
TextConfig?	textConfig
Text config for text customization.
####
Resource.TextsKey
[](#resourcetextskey)
enum TextsKey

Enum Values
securityQuestionHeaders,
securitySummaryQuestionHeaders,
enterPinCodeHeadline,
securityIntroHeadline,
newPinCodeHeadline,
securityIntroLink,
recoverPinCodeHeadline

See [A Index Table](/w3s/docs/android-sdk-ui-customization-api#a-index-table---resourcetextskey).
####
Resource.IconTextsKey
[](#resourceicontextskey)
enum IconTextsKey

Enum Constants
securityConfirmationItems

See [B Index Table](/w3s/docs/android-sdk-ui-customization-api#b-index-table---resourceicontextskey).
###
IImageViewSetter
[](#iimageviewsetter)
The ImageView setter interface for image customization.

interface IImageViewSetter

Public Methods	
abstract Unit	apply(iv: [ImageView?](https://developer.android.com/reference/android/widget/ImageView) )
Called when the ImageView needs to be set.
####
LocalImageSetter
[](#localimagesetter)
The implemented ImageView setter for image customization with local image.

class LocalImageSetter: IImageViewSetter

Constructors
constructor(drawableId: Int)
Properties	
@[DrawableRes](https://developer.android.com/reference/androidx/annotation/DrawableRes) Int	drawableId
The resource ID of drawable.
Public Methods	
Unit	apply(iv: [ImageView](https://developer.android.com/reference/android/widget/ImageView))
Set drawable to the imageView with drawableId.
####
RemoteImageSetter
[](#remoteimagesetter)
The implemented ImageView setter for image customization with a remote image.

class RemoteImageSetter: IImageViewSetter

Constructors
constructor(drawableId: Int)
constructor(drawableId: Int, url: String?)
Properties	
@[DrawableRes](https://developer.android.com/reference/androidx/annotation/DrawableRes) Int	drawableId
The resource ID of drawable.
String	url
Image URL.
Public Methods	
Unit	apply(iv: [ImageView](https://developer.android.com/reference/android/widget/ImageView))
Set a remote image from the URL to the ImageView. Use the drawable as the placeholder.
###
IToolbarSetter
[](#itoolbarsetter)
The Toolbar setter interface for image customization.

interface IToolbarSetter

Public Methods	
abstract Unit	apply(toolbar: [Toolbar?](https://developer.android.com/reference/androidx/appcompat/widget/Toolbar))
Called when the Toolbar needs to be set.
####
LocalToolbarImageSetter
[](#localtoolbarimagesetter)
class LocalToolbarImageSetter: IToolbarSetter

Constructors
constructor(drawableId: Int)
Properties	
@[DrawableRes](https://developer.android.com/reference/androidx/annotation/DrawableRes) Int	drawableId
The resource ID of drawable.
Public Methods	
Unit	apply(toolbar: [Toolbar?](https://developer.android.com/reference/androidx/appcompat/widget/Toolbar))
Set drawable as navigation icon to the Toolbar.
####
RemoteToolbarImageSetter
[](#remotetoolbarimagesetter)
The implemented Toolbar setter for image customization with a remote image.

class RemoteToolbarImageSetter: IToolbarSetter

Constructors
constructor(drawableId: Int)
constructor(drawableId: Int, url: String?)
Properties	
@[DrawableRes](https://developer.android.com/reference/androidx/annotation/DrawableRes) Int	drawableId
The resource ID of drawable.
String?	url
Image URL.
Public Methods	
Unit	apply(toolbar: [Toolbar?](https://developer.android.com/reference/androidx/appcompat/widget/Toolbar))
Set a remote image from the URL to the toolbar. Use drawable as the placeholder.
###
ViewSetterProvider
[](#viewsetterprovider)
ViewSetterProvider supports performing image customization during runtime.

open class ViewSetterProvider extends Object

Public Methods	
open IImageViewSetter?	getImageSetter(type: Resource.Icon)
Return implemented IImageViewSetter for performing general image customization.
e.g. LocalImageSetter, RemoteImageSetter

All keys are listed in [D Index Table](/w3s/docs/android-sdk-ui-customization-api#d-index-table).
open IToolbarSetter?	getToolbarImageSetter( type: Resource.ToolbarIcon )
Return implemented IToolbarSetter for performing Toolbar image customization.
e.g. LocalToolbarImageSetter, RemoteToolbarImageSetter

All keys are listed in [E Index Table](/w3s/docs/android-sdk-ui-customization-api#e-index-table).
####
Resource.Icon
[](#resourceicon)
enum Icon

Enum Values
securityIntroMain,
selectCheckMark,
dropdownArrow,
errorInfo,
securityConfirmMain,
biometricsAllowMain,
showPin,
hidePin,
alertWindowIcon

[D Index Table](/w3s/docs/android-sdk-ui-customization-api#d-index-table).
####
Resource.ToolbarIcon
[](#resourcetoolbaricon)
enum ToolbarIcon

Enum Values
close,
back

[E Index Table](/w3s/docs/android-sdk-ui-customization-api#e-index-table).
###
SecurityQuestion
[](#securityquestion)
Data class for security questions customization.
See WalletSdk.setSecurityQuestions().

data class SecurityQuestion

Constructors
constructor(title: String)
constructor(title: String, inputType: SecurityQuestion.InputType)
Properties	
String	title
The question string.
SecurityQuestion.InputType	inputType
The input type of the question. Support text input and timePicker.
####
SecurityQuestion.InputType
[](#securityquestioninputtype)
public enum InputType

Enum Values
text,
datePicker
###
Callback
[](#callback)
A generic callback interface for SDK API calls

interface Callback<R>

Public Methods	
abstract Boolean	onError(error: Throwable)
The callback is triggered when a failure occurs in operation or is canceled by the user.
Return true - The app will handle the following step, SDK will keep the Activity.
Return false - The app won’t handle the following step, SDK will finish the Activity.
abstract Init	onResult(R result)
R - Type of result such as ExecuteResult
Callback when the operation is executed successfully. Finish the Activity after this callback is triggered.
abstract Boolean	onWarning(ExecuteWarning warning, R result)
The callback is triggered when operation executed with warning.
R - Type of result such as ExecuteResult
Return true - App will handle the following step, SDK will keep the Activity.
Return false - App won’t handle the following step, SDK will finish the Activity.
####
ExecuteWarning
[](#executewarning)
enum ExcuteWarning

Properties	
Int	warningType
Warning type.
String	warningString
Description of the warning type.
Enum Values
biometricsUserSkip(155711, "User skipped the setting of using biometrics to protect PIN this time."),
biometricsUserDisableForPin(155712, "User disabled the function of using biometrics to protect PIN."),
biometricsUserLockout(155713, "Too many attempts. Try again later."),
biometricsUserLockoutPermanent(155714, "Too many attempts. Biometrics sensor disabled."),
biometricsUserNotAllowPermission(155715, "User didn't grant the permission to use biometrics"), //IOS ONLY
biometricsInternalError(155716, "Biometrics internal error - %s");
####
ExecuteResult
[](#executeresult)
data class ExecuteResult

Constructors
constructor( resultType: ExecuteResultType, status: ExecuteResultStatus, data: ExecuteResultData = ExecuteResultData() )
Properties	
ExecuteResultType	resultType
The type of the operation that the challenge represents. The possible values are :
UNKNOWN(-1),
SET_PIN(0),
RESTORE_PIN(1),
CHANGE_PIN(2),
SET_SECURITY_QUESTIONS(3),
CREATE_WALLET(4),
CREATE_TRANSACTION(5),
ACCELERATE_TRANSACTION(6),
CANCEL_TRANSACTION(7),
CONTRACT_EXECUTION(8),
SIGN_MESSAGE(9),
SIGN_TYPEDDATA(10),
SET_BIOMETRICS_PIN(1000)
ExecuteResultStatus	status
The status of the execution. The possible values are :
UNKNOWN(-1)
PENDING(0)
IN_PROGRESS(1)
COMPLETE(2)
FAILED(3)
EXPIRED(4)
ExecuteResultData	data
The data of the execution.
####
ExecuteResultType
[](#executeresulttype)
enum ExecuteResultType

Properties
Int	value
The int value of the enum.
Enum Values
UNKNOWN(-1),
SET_PIN(0),
RESTORE_PIN(1),
CHANGE_PIN(2),
SET_SECURITY_QUESTIONS(3),
CREATE_WALLET(4),
CREATE_TRANSACTION(5),
ACCELERATE_TRANSACTION(6),
CANCEL_TRANSACTION(7),
CONTRACT_EXECUTION(8),
SIGN_MESSAGE(9),
SIGN_TYPEDDATA(10),
SET_BIOMETRICS_PIN(1000)
####
ExecuteResultStatus
[](#executeresultstatus)
enum ExecuteResultStatus

Properties	
Int	value
The int value of the enum.
Enum Constants
UNKNOWN (-1)
PENDING(0)
IN_PROGRESS(1)
COMPLETED(2)
FAILED(3)
EXPIRED(4)
####
ExecuteResultData
[](#executeresultdata)
data class ExecuteResultData

Constructors
constructor(signature: String? = null)
Properties	
String?	signature
The signature for SIGN_MESSAGE and SIGN_TYPEDDATA.
###
APIError
[](#apierror)
Error class for PW SDK

abstract class ApiError:[Throwable](https://developer.android.com/reference/java/lang/Throwable)

Properties	
abstract ApiError.ErrorCode	code
Error code.
open String	message
Get human-readable error message
####
ApiError.ErrorCode
[](#apierrorerrorcode)
public enum ErrorCode

Properties	
Int	value
The int value of the enum.
Enum Values
unknown(-1),
success(0),
apiParameterMissing(1),
apiParameterInvalid(2),
forbidden(3),
unauthorized(4),
retry(9),
customerSuspended(10),
pending(11),
invalidSession(12),
invalidPartnerId(13),
invalidMessage(14),
invalidPhone(15),
walletIdNotFound(156001),
tokenIdNotFound(156002),
transactionIdNotFound(156003),
entityCredentialNotFound(156004),
walletSetIdNotFound(156005),
userAlreadyExisted(155101),
userNotFound(155102),
userTokenNotFound(155103),
userTokenExpired(155104),
invalidUserToken(155105),
userWasInitialized(155106),
userHasSetPin(155107),
userHasSetSecurityQuestion(155108),
userWasDisabled(155109),
userDoesNotSetPinYet(155110),
userDoesNotSetSecurityQuestionYet(155111),
incorrectUserPin(155112),
incorrectDeviceId(155113),
incorrectAppId(155114),
incorrectSecurityAnswers(155115),
invalidChallengeId(155116),
invalidApproveContent(155117),
invalidEncryptionKey(155118),
userPinLocked(155119),
securityAnswersLocked(155120),
walletIsFrozen(155501),
maxWalletLimitReached(155502),
walletSetIdMutuallyExclusive(155503),
metadataUnmatched(155504),
userCanceled(155701),
launchUiFailed(155702),
pinCodeNotMatched(155703),
insecurePinCode(155704),
hintsMatchAnswers(155705);
biometricsSettingNotEnabled(155708)
deviceNotSupportBiometrics(155709)
biometricsKeyPermanentlyInvalidated(155710)
biometricsUserSkip(155711)
biometricsUserDisableForPin(155712)
biometricsUserLockout(155713)
biometricsUserLockoutPermanent(155714)
biometricsUserNotAllowPermission(155715)
biometricsInternalError(155716)
###
Static Customized String
[](#static-customized-string)
####
res/values/strings.xml
[](#resvaluesstringsxml)
XML

<resources>
    <string name="circlepw_continue">Continue</string>
    <string name="circlepw_next">Next</string>
    <string name="circlepw_question_label">Question:</string>
</resources>
###
Static Customized UI Layout
[](#static-customized-ui-layout)
####
res/values/color.xml
[](#resvaluescolorxml)
XML

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="circlepw_text_main">#1A1A1A</color>
    <color name="circlepw_text_auxiliary">#3D3D3D</color>
    <color name="circlepw_text_action">#136FD8</color>
    <color name="circlepw_text_action_pressed">#B3136FD8</color>
</resources>
####
res/values/styles.xml
[](#resvaluesstylesxml)
XML

<resources>
    <style name="circlepw_heading3">
        <item name="android:fontFamily">@font/avenir_heavy</item>
        <item name="android:fontWeight" tools:targetApi="o">600</item>
        <item name="android:textSize">24sp</item>
    </style>
    <style name="circlepw_heading4">
        <item name="android:fontFamily">@font/avenir_heavy</item>
        <item name="android:fontWeight" tools:targetApi="o">600</item>
        <item name="android:textSize">20sp</item>
    </style>
</resources>
####
res/values/dimens.xml
[](#resvaluesdimensxml)
XML

<resources>
    <dimen name="circlepw_bottom_bt_margin_top">10dp</dimen>
    <dimen name="circlepw_intro_title_margin_bottom">24dp</dimen>
    <dimen name="circlepw_intro_custom_grab_icon_size">48dp</dimen>
</resources>
###
Sample Code
[](#sample-code)
Java

// Set endpoint and app ID
try {
    WalletSdk.init(getApplicationContext(), new WalletSdk.Configuration(endpoint, appId));
} catch (Throwable e){
    e.printStackTrace();
    return;
}

// setViewSetterProvider
WalletSdk.setViewSetterProvider(new MyViewSetterProvider());
// setLayoutProvider
WalletSdk.setLayoutProvider(new MyLayoutProvider(getApplicationContext()));
// Regester EventListener
WalletSdk.addEventListener(new EventListener() {
    @Override
       public void onEvent(ExecuteEvent event) {
           switch (event){
                  case forgotPin:
		     startActivity(forgotPinIntent);  
                  break;
           }
       }
});

// setSecurityQuestion
SecurityQuestion[] questions = new SecurityQuestion[]{
                    new SecurityQuestion("What is your father’s middle name?"),
                    new SecurityQuestion("What is your favorite sports team?"),
                    new SecurityQuestion("What is your mother’s maiden name?"),
                    new SecurityQuestion("What is the name of your first pet?"),
                    new SecurityQuestion("What is the name of the city you were born in?"),
                    new SecurityQuestion("What is the name of the first street you lived on?"),
                    new SecurityQuestion("When is your father’s birthday?", SecurityQuestion.InputType.datePicker)};
WalletSdk.setSecurityQuestions(questions);
Java

// There are two ways to use Configuration. Below is the way with SettingsManagement.
// We can use SettingsManagement to bring the setting flag. SettingsManagement provide the setting flag 
// EnableBiometricsPin to enable/disable the function "Use biometrics to protect PIN" currently. 
// But if we do not use the settingsManagement, the default value of EnableBiometricsPin is false.
try {
    SettingsManagement settingsManagement = new SettingsManagement();
    settingsManagement.setEnableBiometricsPin(true); //Set "true" to enable, "false" to disable
    WalletSdk.init(getApplicationContext(), new WalletSdk.Configuration(endpoint, appId, settingsManagement));
} catch (Throwable e){
    e.printStackTrace();
    return;
}

Java

// Execute
WalletSdk.execute(getActivity(), userToken, encryptionKey, 
          new String[]{challengeId}, 
          new Callback<ExecuteResult>() {
                @Override
                public boolean onError(Throwable error) {
                      if(error instanceof ApiError){
                              ApiError apiError = (ApiError) error;
                              if(apiError.getCode() == ApiError.ErrorCode.userCanceled){
				// App won't handle next step, SDK finishes the Activity.
                                  return false;                         
                               }
                        	     startActivity(errorUiIntent);  
			     // App will handle next step, SDK keeps the Activity.
                               return true; 
                       }

                }


                @Override
                public void onResult(ExecuteResult result) {
                       // success
                }
                

                @Override
                public boolean onWarning(ExecuteWarning warning, ExecuteResult executeResult) {
  		       // App won't handle next step, SDK finishes the Activity.
                        return false;
                }
});

Java

// Extend LayoutProvider and override
class MyLayoutProvider extends LayoutProvider {
        Context context;
        Typeface typeface;
        int color;
        public MyLayoutProvider(Context context) {
            this.context = context;
        }

        @Override
        public TextConfig getTextConfig(String key) {
            switch (key){
                case Resource.Key.circlepw_security_intros_description:
                    typeface = ResourcesCompat.getFont(context, R.font.en);
                    color = Color.parseColor("#105AAB");
                    return new TextConfig("customized description", color, typeface);
            }
            return super.getTextConfig(key);
        }


@Override
public IconTextConfig[] getIconTextConfigs(Resource.IconTextsKey key) {
            switch (key){
                case securityConfirmationItems: // SQ confirmation introduction items
                    return new IconTextConfig[]{
                            new IconTextConfig(
                                    new LocalImageSetter(R.drawable.intro_item0_icon),
                                    new TextConfig("This is the only way to recover my account access. ")),
                            new IconTextConfig(
                                    new RemoteImageSetter(R.drawable.error, "https://path/icon2.svg"),
                                    new TextConfig("Circle won’t store my answers so it’s my responsibility to remember them.")),
                            new IconTextConfig(
                                    new RemoteImageSetter(R.drawable.error, "https://path/icon3.svg"),
                                    new TextConfig("I will lose access to my wallet and my digital assets if I forget my answers. "))
                    };
}
   return super.getIconTextConfigs(key);
       }
@Override
        public TextConfig[] getTextConfigs(Resource.TextsKey key) {
            switch (key){
                case securityQuestionHeaders:
                    return new TextConfig[]{
                            new TextConfig("Choose your 1st question"),
                            new TextConfig("Choose your 2nd question")
                    };
                case securitySummaryQuestionHeaders:
                    return new TextConfig[]{
                            new TextConfig("1st Question"),
                            new TextConfig("2nd Question")
                    };
                case newPinCodeHeadline:
                case enterPinCodeHeadline:
                    return new TextConfig[]{
                            new TextConfig("Enter your "),
                            new TextConfig("PIN", getHeadingColor(), null),
                    };
                case securityIntroHeadline:
                    return new TextConfig[]{
                            new TextConfig("Set up your "),
                            new TextConfig("Recovery Method", getHeadingColor(), null),
                    };
            }
            return super.getTextConfigs(key);
        }

        private int getHeadingColor(){
              return Color.parseColor("#0073C3");
        }

        @Override
        public String getErrorString(ApiError.ErrorCode code) {
            switch (code){
                case userCanceled:
                    return context.getString(R.string.user_canceled);
            }
            return super.getErrorString(code);
        }

        @Override
        public String getDateFormat(){
            return Resource.DateFormat.MMDDYYYY_SLASH; // "​​MM/DD/YYYY"
        }

        @Override
        public boolean isDebugging(){
            return BuildConfig.DEBUG;
        }

    }

Java

// Extend ViewSetterProvider and override
    class MyViewSetterProvider extends ViewSetterProvider{

        @Override
        public IToolbarSetter getToolbarImageSetter(Resource.ToolbarIcon type) {
            switch (type){
                case back:
                    return new LocalToolbarImageSetter(R.drawable.ic_back);
                case close:
                    return new RemoteToolbarImageSetter(R.drawable.error, "https://path/close.svg");
            }
            return super.getToolbarImageSetter(type);
        }
	@Override
        public IImageViewSetter getImageSetter(Resource.Icon type) {
            switch (type){
                case securityIntroMain:
                    return new LocalImageSetter(R.drawable.main);
                case selectCheckMark:
                    return new RemoteImageSetter(R.drawable.error, "https://path/check.svg");
                case dropdownArrow:
                    return new RemoteImageSetter(R.drawable.error, "https://path/arrow.svg");
                case errorInfo:
                    return new RemoteImageSetter(R.drawable.error, "https://path/errorinfo.svg");
                case securityConfirmMain:
                    return new RemoteImageSetter(R.drawable.error, "https://path/main2.svg");
            }
            return super.getImageSetter(type);
        }
    }

Java

// setBiometricsPin
WalletSdk.setBiometricsPin(this, userToken, encryptionKey, new Callback<ExecuteResult>() {
            @Override
            public boolean onError(Throwable error) {
                error.printStackTrace();
                if (error instanceof ApiError) {
                    ApiError apiError = (ApiError) error;
                    switch (apiError.getCode()) {
                        case incorrectUserPin:
                        case userPinLocked:
                        case securityAnswersLocked:
                        case incorrectSecurityAnswers:
                        case pinCodeNotMatched:
                        case insecurePinCode:
                            return true; // App will handle next step, SDK will keep the Activity.
                    }
                }
                return false; // App won't handle next step, SDK will finish the Activity.
            }

            @Override
            public void onResult(ExecuteResult executeResult) {
                //success
            }

            @Override
            public boolean onWarning(ExecuteWarning warning, ExecuteResult executeResult) {
                return false; // App won't handle next step, SDK will finish the Activity.
            }
        });



Updated about 1 month ago

-
[iOS](/w3s/docs/ios)
-
[Android](/w3s/docs/android)
-
[Sample Applications](/w3s/docs/ios-sample-application)

URL: https://developers.circle.com/w3s/docs/user-controlled-send-outbound-transfer
Title: Send an Outbound Transfer
Description: undefined
Keywords: undefined

This guide outlines initiating a currency transfer from a previously created user-controlled wallet. If you have not yet created a user-controlled wallet, go to [this guide](/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart). If you do not have any tokens in your wallet, go to the [inbound transfer](/w3s/docs/user-controlled-receive-inbound-transfer) guide.

Note that if you’re building with Wallet Signing Service, you must manage the transaction broadcasting and indexing with your blockchain infrastructure.

The following steps utilize Circle's sample applications in combination with API requests that can be done via Circle's API references or cURL requests. cURL request will be provided inline, while API references will be linked from the API endpoint code text. You can find instructions on using it in the [testing via the reference pages](/w3s/docs/api-references) guide.

##
1. Run Sample App
[](#1-run-sample-app)
Once you have one of the web, iOS, or Android [sample applications](/w3s/docs/sample-applications) set up locally, you will then:

- Run the sample app and simulator.
- Obtain your App ID. This can be done by one of two options
- Access the developer console and navigate to the [configurator](https://console.circle.com/wallets/user/configurator) within user-controlled wallets. From there, copy the App ID.
- Make an API request to [GET /config/entity](/w3s/reference/getentityconfig) and copy the App ID from the response body.
- Add the App ID to the sample app.
##
2. Acquire a Session Token
[](#2-acquire-a-session-token)
You will start by making a request to [POST /users/token](/w3s/reference/getusertoken) using a previously created [userId](user-controlled-initialization-and-wallet-creation-quickstart). The userToken is a 60-minute session token to initiate requests requiring a user challenge (PIN code entry). After 60 minutes, the session expires, and a new userToken must be generated via the same endpoint.

From this response, you will acquire the encryptionKey and userToken which you should provide in the respective sample app fields. Additionally, you will use the userToken in Step 3.

Node.js
cURL

// Import and configure the user-controlled wallet SDK
const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets');
const circleUserSdk = initiateUserControlledWalletsClient({
  apiKey: '<API_KEY>'
});

const response = await circleUserSdk.createUserToken({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
Response Body

{
  "data": {
    "userToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCC9.eyJkZXZlbG9wZXJFbnRpdHlFbnZpcm9ubWVudCI6IlRFU1QiLCJlbnRpdHlJZCI6IjRlMDdhOGM5LTIxOTAtNDVlNC1hNjc0LWQyMGFkNjg4MWI3YyIsImV4cCI6MTY5MDU1MjcwNywiaWF0IjoxNjkwNTQ5MTA3LCJpbnRlcm5hbFVzZXJJZCI6ImQ2ZjkzODliLWQ5MzUtNWFlYy1iOTVhLWNjNTk1NjA2YWM5NiIsImlzcyI6Imh0dHBzOi8vcHJvZ3JhbW1hYmxlLXdhbGxldC5jaXJjbGUuY29tIiwianRpIjoiMmE0YmJlMzAtZTdkZi00YmM2LThiODMtNTk0NGUyMzE2ODlkIiwic3ViIjoiZXh0X3VzZXJfaWRfOSJ9.dhfByhxZFbJx0XWlzxneadT4RQWdnxLu3FSN9ln65hCDOfavaTL1sc4h-jUR8i4zMmfdURw3FFcQIdSbm-BUg6M7FP_fp-cs9xBbNmRZa31gMd1aKdcajJ9SvlVrfUowYfGXM3VcNF8rtTFtW-gk1-KzU4u10U35XXbbMcW1moxE0Rqx_fKotDgk2VdITuuds5d5TiQzAXECqeCOCtNoDKktMkglltbnLxOaRl2ReZjGt-ctD2V0DbYNO4T_ndPSUDI6qD7dXQRed5uDcezJYoha3Qj3tFGBglEnox2Y6DWTbllqjwmfTGrU8Pr0yz4jQz7suGwmiCzHPxcpYxMzYQ",
    "encryptionKey": "Tlcyxz7Ts9ztRLQq5+pic0MIETblYimOo2d7idV/UFM="
  }
}
##
3. Check the Wallet Balance and Acquire the Token ID
[](#3-check-the-wallet-balance-and-acquire-the-token-id)
Before making an outbound transfer, you must gather the token's ID and ensure you are holding a token balance. To do this, make a request to [GET /wallets](/w3s/reference/listwallets) passing in the wallets userToken to get the walletId.

Node.js
cURL

const response = await circleUserSdk.listWallets({
  userToken: '<USER_TOKEN>',
  pageSize: 10
});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "01899cf2-d415-7052-a207-f9862157e546",
        "state": "LIVE",
        "walletSetId": "01899cf2-d407-7f89-b4d9-84d63573f138",
        "custodyType": "ENDUSER",
        "userId": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
        "address": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
        "addressIndex": 0,
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-07-28T14:41:47Z",
        "createDate": "2023-07-28T14:41:47Z"
      }
    ]
  }
}
Next, you will make a request to [GET /wallet/{id}/balances](/w3s/reference/listwalletballance) to check the balance of tokens and acquire the tokenId you intend to transfer. The token ID will be used in the following steps.

Node.js
cURL

const response = await circleUserSdk.getWalletTokenBalance({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "tokenBalances": [
      {
        "token": {
          "id": "36b6931a-873a-56a8-8a27-b706b17104ee",
          "blockchain": "MATIC-AMOY",
          "tokenAddress": "0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582",
          "standard": "ERC20",
          "name": "USDC",
          "symbol": "USDC",
          "decimals": 6,
          "isNative": false,
          "updateDate": "2024-03-27T17:55:12Z",
          "createDate": "2024-03-27T17:55:12Z"
        },
        "amount": "10",
        "updateDate": "2024-04-16T15:52:23Z"
      }
    ]
  }
}
##
4. Estimate the Cost of Transferring the token (Optional)
[](#4-estimate-the-cost-of-transferring-the-token-optional)
To estimate the fees for the transaction to transfer tokens, make a request to [POST transactions/transfer/estimateFee](/w3s/reference/createtransferestimatefee) .

Node.js
cURL

const response = await circleUserSdk.estimateTransferFee({
  userToken: '<USER_TOKEN>',
  amount: ['.01'],
  destinationAddress: '0xEb9614D6d001391e22dDbbEA7571e9823A469c1f',
  tokenId: '36b6931a-873a-56a8-8a27-b706b17104ee',
  walletId: '01899cf2-d415-7052-a207-f9862157e546'
});
Response Body

{
  "data": {
    "low": {
      "gasLimit": "21000",
      "baseFee": "2.456220277",
      "priorityFee": "1.022783914",
      "maxFee": "5.935224468"
    },
    "medium": {
      "gasLimit": "21000",
      "baseFee": "2.456220277",
      "priorityFee": "2.655282857",
      "maxFee": "7.567723411"
    },
    "high": {
      "gasLimit": "21000",
      "baseFee": "2.456220277",
      "priorityFee": "15.986229693",
      "maxFee": "20.898670247"
    }
  }
}
##
5. Initiate a Blockchain Transfer
[](#5-initiate-a-blockchain-transfer)
Make a request to [POST /user/transactions/transfer](/w3s/reference/createusertransactiontransferchallenge) to initiate a blockchain transfer from a specifiedwalletId to a blockchain addressdestinationAddress. This call returns a challengeId, used within the sample app, that prompts users to enter their PIN code to authorize the transfer.

##📘
If you do not have a wallet to use as a destination for the transfer, you can create another User-Controlled Wallet by stepping through [create your first wallet](/w3s/docs/user-controlled-create-your-first-wallet) or send funds to any other blockchain wallet such as [Metamask](https://metamask.io/).

Node.js
cURL

const response = await circleUserSdk.createTransaction({
  userToken: '<USER_TOKEN>',
  amounts: ['.01'],
  destinationAddress: '0x6E5eAf34c73D1CD0be4e24f923b97CF38e10d1f3',
  tokenId: '36b6931a-873a-56a8-8a27-b706b17104ee',
  walletId: '01899cf2-d415-7052-a207-f9862157e546',
  fee: {
    type: 'level',
    config: {
      feeLevel: 'MEDIUM'
    }
  }
});
Response Body

{
  "data": {
    "challengeId": "0d1b5f41-1381-50af-983b-f54691415158"
  }
}
##
6. Authorize transfer from the sample app
[](#6-authorize-transfer-from-the-sample-app)
Using the sample application, enter the userToken and secretKey returned from Step 2. Also, enter the challengeId returned from Step 5. 

At this point, you should be ready to execute your first transfer through the sample app. Click Execute in the sample app to continue. 


The sample application takes you through the authentication process, which includes the user entering their PIN code to authorize the transfer. 


###
7. Check the Transfer Status
[](#7-check-the-transfer-status)
As the transfer state changes and ultimately completes, Circle sends notifications to a [subscribed endpoint](/w3s/docs/web3-services-notifications-quickstart). You can find a list of all possible states in the [Asynchronous States and Statuses guide](/w3s/docs/asynchronous-states-and-statuses). The Webhook notification will be similar to the one below.

Webhook Request Body

{
  "subscriptionId": "d4c07d5f-f05f-4fe4-853d-4dd434806dfb",
  "notificationId": "acab8c14-92ae-481a-8335-6eb5271da014",
  "notificationType": "transactions.outbound",
  "notification": {
    "id": "ad3f40ae-9c0e-52cf-816f-91838850572a",
    "blockchain": "MATIC-AMOY",
    "tokenId": "36b6931a-873a-56a8-8a27-b706b17104ee",
    "walletId": "01899cf2-d415-7052-a207-f9862157e546",
    "sourceAddress": "0x7b777eb80e82f73f118378b15509cb48cd2c2ac3",
    "destinationAddress": "0x6e5eaf34c73d1cd0be4e24f923b97cf38e10d1f3",
    "transactionType": "OUTBOUND",
    "custodyType": "ENDUSER",
    "state": "COMPLETE",
    "amounts": [
      "0.01"
    ],
    "nfts": null,
    "txHash": "0x535ff240984f54e755d67cdc9c79c88768fe5997955f09f3a66b4d1126810900",
    "blockHash": "0xa4c5c79500240f3ae3f4e5c5f641198b7c698d83b7539ac4e8cf2d3f5f49bdfd",
    "blockHeight": 41100000,
    "networkFee": "0.07037500047405219",
    "firstConfirmDate": "2023-10-11T21:08:28Z",
    "operation": "TRANSFER",
    "userId": "c266945c-f440-4537-85cf-a16b6e33b0cc",
    "abiParameters": null,
    "createDate": "2023-10-11T21:08:13Z",
    "updateDate": "2023-10-11T21:08:37Z"
  },
  "timestamp": "2023-10-11T21:08:13Z",
  "version": 2
}
Alternatively, you can poll [GET /transactions](/w3s/reference/listtransactions) using the userId or userToken associated with your user. 

Node.js
cURL

const response = await circleUserSdk.listTransactions({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "transactions": [
      {
        "id": "ad3f40ae-9c0e-52cf-816f-91838850572a",
        "blockchain": "MATIC-AMOY",
        "tokenId": "36b6931a-873a-56a8-8a27-b706b17104ee",
        "walletId": "01899cf2-d415-7052-a207-f9862157e546",
        "sourceAddress": "0x7b777eb80e82f73f118378b15509cb48cd2c2ac3",
        "destinationAddress": "0x6e5eaf34c73d1cd0be4e24f923b97cf38e10d1f3",
        "transactionType": "OUTBOUND",
        "custodyType": "ENDUSER",
        "state": "COMPLETE",
        "amounts": [
          "0.01"
        ],
        "nfts": null,
        "txHash": "0x535ff240984f54e755d67cdc9c79c88768fe5997955f09f3a66b4d1126810900",
        "blockHash": "0xa4c5c79500240f3ae3f4e5c5f641198b7c698d83b7539ac4e8cf2d3f5f49bdfd",
        "blockHeight": 41100000,
        "networkFee": "0.07037500047405219",
        "firstConfirmDate": "2023-10-11T21:08:28Z",
        "operation": "TRANSFER",
        "userId": "c266945c-f440-4537-85cf-a16b6e33b0cc",
        "abiParameters": null,
        "createDate": "2023-10-11T21:08:13Z",
        "updateDate": "2023-10-11T21:08:37Z"
      },
      {
        "id": "81cf790a-ed95-5d41-b7bd-c4e15390eef6",
        "blockchain": "MATIC-AMOY",
        "tokenId": "36b6931a-873a-56a8-8a27-b706b17104ee",
        "walletId": "01899cf2-d415-7052-a207-f9862157e546",
        "sourceAddress": "0x48520ff9b32d8b5bf87abf789ea7b3c394c95ebe",
        "destinationAddress": "0x7b777eb80e82f73f118378b15509cb48cd2c2ac3",
        "transactionType": "INBOUND",
        "custodyType": "ENDUSER",
        "state": "COMPLETE",
        "amounts": [
          "10"
        ],
        "nfts": null,
        "txHash": "0x5121f9efec29d4d661ffb0b777727d1f5ba7b5bc286ac4891c82f7b1b80a9485",
        "blockHash": "0xba7984dbe7423827b5fd175a636552ae85401c3f2a0c5cdda934a37d6652ac49",
        "blockHeight": 41098635,
        "networkFee": "0.001911870000955935",
        "firstConfirmDate": "2023-10-11T20:13:33Z",
        "operation": "TRANSFER",
        "userId": "c266945c-f440-4537-85cf-a16b6e33b0cc",
        "abiParameters": null,
        "createDate": "2023-10-11T20:13:33Z",
        "updateDate": "2023-10-11T20:13:45Z"
      }
    ]
  }
}
Updated 1 day ago

Congratulations! You’ve received your first transaction to your user-controlled wallet. To learn how wallet recovery works, go to

-
[Reset Account Pin Code](/w3s/docs/user-controlled-reset-account-pin)

URL: https://developers.circle.com/w3s/docs/sample-applications
Title: Sample Applications
Description: undefined
Keywords: undefined

Explore web sample applications to learn and kickstart your project with Circle's Web3 Services offering.

##
User-Controlled Wallets Sample Apps
[](#user-controlled-wallets-sample-apps)
Use these sample applications to start building with User-controlled wallets.

##
Create and use the pin authorization flow
[](#create-and-use-the-pin-authorization-flow)
Sample application for the [User-controlled wallets client-side SDK](https://www.npmjs.com/package/@circle-fin/w3s-pw-web-sdk) for browser-based testing.

Features of this sample include:

- Transaction authorization using a 6-digit pin
- Wallet backup and recovery
###
Using the sample app
[](#using-the-sample-app)
[Interact with the sample app](http://pw-auth-example.circle.com/).

View the sample app source code on GitHub:

- [iOS](https://github.com/circlefin/w3s-ios-sample-app-wallets)
- [Android](https://github.com/circlefin/w3s-android-sample-app-wallets)
- [React](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/react-example)
- [JavaScript (Vanilla)](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/js-example)
- [Vue.js](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/vue-example)
##
Create a wallet with Social Log-in
[](#create-a-wallet-with-social-log-in)
Sample application for User-Controlled Wallets.

- User account creation with social log-in functionality
- Wallet address generation after sign-up
- Send & receive transactions
- Transaction authorization using a 6-digit pin
- View transaction history
- Wallet backup and recovery
###
Using the sample app
[](#using-the-sample-app-1)
View the sample app source code on GitHub:

- [iOS](https://github.com/circlefin/w3s-digital-wallet-iOS-sample-app)
##
Manage user sessions
[](#manage-user-sessions)
Test Server for User-Controlled wallets to manage sessions and requests.

Features of this sample include:

- Challenge initiation for transaction authorization
- Session management
###
Using the sample app
[](#using-the-sample-app-2)
View the sample app source code on GitHub:

- [JavaScript](https://github.com/circlefin/w3s-programmable-wallets-test-server)
Updated 1 day ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/entity-secret-management
Title: Entity Secret Management
Description: undefined
Keywords: undefined

##
What is an Entity Secret?
[](#what-is-an-entity-secret)
The Entity Secret is a 32-byte private key designed to secure your Developer-Controlled wallets. It acts as your secret password, your personalized cryptographic stamp, known only to you.

Our platform does not store the Entity Secret. This ensures that only you can invoke private keys, maintaining complete control. It is therefore your responsibility to safeguard this secret.

Video Tutorial: Generate and Encrypt an Entity Secret
##
What is an Entity Secret Ciphertext?
[](#what-is-an-entity-secret-ciphertext)
The Entity Secret Ciphertext is a RSA encryption token generated from your Entity Secret and Circle's public key. This asymmetrically encrypted value is sent in API requests like wallet creation or transaction initiation to ensure critical actions are secure. This process enables secure usage of the Entity Secret, to ensure it cannot be easily accessed or misused.

##
How to Create and Register the Entity Secret Ciphertext
[](#how-to-create-and-register-the-entity-secret-ciphertext)
To create and register your Entity Secret Ciphertext, follow these four steps:

- Generate the Entity Secret
- Fetch your Entity's Public Key
- Generate the Entity Secret Ciphertext
- Register the Entity Secret Ciphertext to Circle's Developer Console

###
1. Generate the Entity Secret
[](#1-generate-the-entity-secret)
First, generate the Entity Secret and store it somewhere safe where your server-side app can access it. For testing purposes, you can store it in your environment variables. This will be used in the following steps to create the Entity Secret Ciphertext.

When you follow the provided methods below, you will receive a 32-byte string value similar to 7ae43b03d7e48795cbf39ddad2f58dc8e186eb3d2dab3a5ec5bb3b33946639a4.

Bash
Node.js
JavaScript
Python
Go

openssl rand -hex 32
##🚧
Remember to keep the Entity Secret safe. Securely store it, as you'll need it to create an Entity Secret Ciphertext in the following steps.

###
2. Fetch your Entity's Public Key
[](#2-fetch-your-entitys-public-key)
To proceed with the Entity Secret Ciphertext creation, the next essential element is your entity's public key. This public key plays an important role in generating the Entity Secret Ciphertext in the upcoming step. To obtain the required public key, you need to initiate a request to the [GET /config/entity/publicKey](/w3s/reference/getpublickey) API endpoint. Remember, this API endpoint can be accessed by providing your valid [API key](/w3s/docs/web3-services-api-keys-authentication) for authentication.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>' // Make sure to enter the entity secret from the step above.
});

const response = await circleDeveloperSdk.getPublicKey({});
Response Body

{
  "data": {
    "publicKey": "-----BEGIN RSA PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAsL4dzMMQX8pYbiyj0g5H\nFGwdygAA2xDm2VquY8Sk0xlOC0yKr+rqUrqnZCj09vLuXbg1BreO/BP4F4GEIHgR\nBNT+o5Q8k0OqxLXmcm5sz6CPlsFCom+MiOj6s7RD0SXg91WF8MrN88GyN53xkemA\nOYU1AlIt4dVIrFyGY8aQ57sbWHyIjim+do1kBX+svIA/FLHG/sycoGiPU1E+Kydf\nlEDga4iR2DSbW6Zte9cGDg9Ivw/seNd0TLzJz6oC9XgSK5Et6/ZpOmqJgvISQ6rT\nK15DJ8EzIOzZZuEVOefgy1S7rLdSH7DexuR4W7T+KpP/f8Px0bxd4N6MT5V5kBYa\ngYHHIvqlJvXe5EzwidIWk1rg1X+YJt2M48h3Pr9HeECcmrnEYOgp32m/9lJ8vKp9\nhNh0rEKww/ULd1HqCEm/I0QGuji13XcGxVo5+7KCb/C76CNdW3pdRMn6fwFh4WVu\nu99iRc9OZhlkphysWm44hs1ZPpMCAkKttWjhnLZwIatN27x2JUqoCEUOho19iT+F\nwlPFA7E0Ju9Rqm68AkCXxHsJsAuGT8m6FLQZLHv4JyO/QEVzD7vY08A2I5dz1mVt\ngVam1/05Axju6poRomx/DUxiR0QH1+0Kg15+2A0fRkBggTTn7kvGsgz0cqk9cTm0\nEITpIVGcSGrVNRrmSye2OW0CAwEAAQ==\n-----END RSA PUBLIC KEY-----\n"
  }
}
###
3. Generate the Entity Secret Ciphertext
[](#3-generate-the-entity-secret-ciphertext)
Once you have the public key, you'll use RSA encryption to secure your Entity Secret and generate the ciphertext. Immediately after, you'll transform this encrypted data into the Base64 format. The output Ciphertext will be exactly 684 characters long.

Node.js
Python
Go

const forge = require('node-forge')

const entitySecret = forge.util.hexToBytes('YOUR_ENTITY_SECRET')
const publicKey = forge.pki.publicKeyFromPem('YOUR_PUBLIC_KEY')
const encryptedData = publicKey.encrypt(entitySecret, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
  mgf1: {
    md: forge.md.sha256.create(),
  },
})

console.log(forge.util.encode64(encryptedData))
NOTE: You can also refer to the provided [sample code](https://github.com/circlefin/w3s-entity-secret-sample-code) in Python and Go for encrypting and encoding the Entity Secret.

###
4. Register the Entity Secret Ciphertext
[](#4-register-the-entity-secret-ciphertext)
After generating, encrypting, and encoding the Entity Secret, developers must register the Entity Secret ciphertext within the developer console.

- Access the [Configurator Page](https://console.circle.com/wallets/dev/configurator) in the developer console.
- Enter the Entity Secret Ciphertext generated in the previous step.
- Select "Register" to complete the Entity Secret Ciphertext registration.

Once registered, you are provided with a file to facilitate recovery in cases where the Entity Secret is lost. This file is used in subsequent Entity Secret reset procedures.

##📘
Our platform does not store the Entity Secret, meaning only you can invoke private keys. However, it also means that you must keep the Entity Secret carefully yourself to ensure the security and accessibility of your Developer-Controlled wallets.

##
Re-Encrypt the Entity Secret to create new ciphertext for each API request
[](#re-encrypt-the-entity-secret-to-create-new-ciphertext-for-each-api-request)
Circle's [APIs Requiring Entity Secret Ciphertext](/w3s/docs/entity-secret-management#apis-requiring-entity-secret-ciphertext) enforce a different Entity Secret Ciphertext for each API request. One-time-use Entity Secret Ciphertext tokens ensure that even if an attacker captures the ciphertext from previous communication, they cannot exploit it in subsequent interactions.

To create a different Entity Secret Ciphertext token, repeat [Step 3: Generate the Entity Secret Ciphertext](/w3s/docs/entity-secret-management#3-generate-the-entity-secret-ciphertext). If you generated the Entity Secret Ciphertext using the registered entity secret, it is valid for an API request.

If you use the same ciphertext for more than one request, all requests after the first one fail.

##📘
Use the Web3 Services SDKs to generate ciphertext tokens

If you use the Web3 Services SDKs, after you complete the step to [Register the Entity Secret Ciphertext](#4-register-the-entity-secret-ciphertext) in the console, you need only to configure and register the entity secret with the SDK. The SDK then handles creating a new ciphertext token for each API request.

###
APIs Requiring Entity Secret Ciphertext
[](#apis-requiring-entity-secret-ciphertext)
Summary	API
Create a new wallet set	[POST /developer/walletSets](/w3s/reference/createwalletset)
Create wallets	[POST /developer/wallets](/w3s/reference/createdeveloperwallet)
Create a transfer transaction	[POST /developer/transactions/transfer](/w3s/reference/createdevelopertransactiontransfer)
Accelerate a transaction	[POST /developer/transactions/{id}/accelerate](/w3s/reference/createdevelopertransactionaccelerate)
Cancel a transaction	[POST /transactions/{id}/cancel](/w3s/reference/createdevelopertransactioncancel)
Execute a contract transaction	[POST /developer/transactions/contractExecution](/w3s/reference/createdevelopertransactioncontractexecution)
Deploy a contract	[POST /contracts/deploy](/w3s/reference/deploycontract)
Deploy a contract from a template	[POST /templates/{id}/deploy](/w3s/reference/deploycontracttemplate)
##
How to Rotate the Entity Secret
[](#how-to-rotate-the-entity-secret)
Periodic rotation of the Entity Secret enhances the overall security of Developer-Controlled wallets. Developers can initiate the Entity Secret rotation process when they possess the existing Entity Secret. To perform rotation, you will provide the system with the current Entity's Secret Ciphertext and the newly created one. The system verifies the authenticity of the provided information before updating the Entity Secret. This process ensures that the Entity Secret remains fresh, reducing the risk of potential vulnerabilities associated with long-term use of the same secret.

Additional Notes:

- Currently, Entity Secret rotation takes immediate effect, rendering the old Entity Secret deprecated. As a result, ongoing API requests using the old Entity Secret will fail. Make sure to complete existing API requests before rotation, or reinitialize them following the entity secret rotation.
- The existing Entity Secret Ciphertext does not need to be the same as the one registered as long as it is derived from the existing Entity Secret (encrypted and encoded). The newly created Entity Secret Ciphertext should be derived from a newly generated 32-byte entity secret to ensure security.
- When the newly created Entity Secret is registered the previous recovery file will be deprecated, and a renewed recovery file can be downloaded for resetting the Entity Secret.
##
How to Reset the Entity Secret
[](#how-to-reset-the-entity-secret)
Developers can initiate the Entity Secret reset process when an Entity Secret is compromised or lost. To ensure the security of the reset operation, developers need to upload the recovery file for authentication. After uploading the recovery file and entering the newly created Entity Secret Ciphertext into the system, the Entity Secret is reset, and a renewed recovery file can be downloaded

Additional Notes:

- Entity Secret reset takes immediate effect, rendering the old Entity Secret deprecated. As a result, ongoing API requests using the old Entity Secret will fail. Make sure to complete existing API requests before reset, or reinitializing them following the Entity Secret reset.
##🚧
If both the Entity Secret and the recovery file are lost, you cannot create new Developer-Controlled wallets or initiate transactions from existing ones. Please store both the Entity Secret string and the recovery file safely.

Updated 28 days ago

Jump back into creating your first Developer-Controlled wallet!

-
[Create Your First Wallet](/w3s/docs/developer-controlled-create-your-first-wallet)
Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/deploy-smart-contract-template
Title: Deploy a Smart Contract From a Template
Description: undefined
Keywords: undefined

Building smart contracts is made effortless with templates. Circle’s Smart Contract Templates provide a quick and simple way to integrate smart contracts into your application. You can deploy contracts in just a few minutes without writing Solidity code. Our carefully curated templates, backed by Circle and audited by third parties, cater to popular on-chain use cases. Circle's templates can be deployed conveniently through either the console or APIs.

This guide walks through deploying an NFT on Polygon Amoy Testnet using the NFT smart contract template and uploading an image to NFT.storage to mint as an NFT. This guide will provide you with a Web3 Services Console and API paths. Generally speaking, the Console is a great first option for learning to use SCP and breaking down all the functions a contract provides. 

Many of the steps used here are transferable to other templates like Token and Multi-Tokens.

##
Evaluate Templates 
[](#evaluate-templates)
To learn more about the NFT template or any other template, go to the [Web3 Services Console](https://developers.circle.com/w3s/docs/circle-developer-account) or the [Templates Glossary](/w3s/docs/scp-templates-glossary) guide. 

- Web3 Services Console: Viewing templates from the console provides a simple way to understand the contracts offered, what they are, the primary use cases each supports, the contracts' ABI functions, events, and code.
- Template Glossary: Outlines the templates offered, a summary of each one, and the template parameters provided when deploying via API.


##
Select Your Preferred Approach
[](#select-your-preferred-approach)
- [Console Path:](/w3s/docs/deploy-smart-contract-template#console-ui-path) Use the web3 services console and a console wallet to deploy a smart contract template and mint an NFT. This UI option is the preferred method for those new to using Smart Contracts. It is faster and has much fewer prerequisite steps.
- [API Path:](/w3s/docs/deploy-smart-contract-template#api-path) Use APIs to deploy a smart contract template and mint an NFT. This option is more technical and requires creating an API key and a Developer-Controlled wallet. 
##
Console UI Path
[](#console-ui-path)
###
Prerequisites
[](#prerequisites)
- Create a [Developer Account](/w3s/docs/circle-developer-account).
- Create an [NFT.Storage](https://nft.storage/) account. NFT.storage is a free service for uploading files to IPFS and requires an email or GitHub account. 
###
1. Create a Console Wallet
[](#1-create-a-console-wallet)
To deploy a smart contract, you must provide a wallet to deploy from. If you do not already have a console wallet, follow the steps below to create one.

##📘
Console Wallets

Console Wallets are specifically designed to be used within the Console UI and cannot be utilized with the APIs. They are classified as Smart Contract Account (SCA) wallets within Web3 Services. Notably, W3S SCA Wallets leverage Circle's Gas Station product, eliminating the need to maintain the gas in the wallet to cover transaction fees. To learn more about the Gas Station feature, please refer to the [Gas Station](/w3s/docs/gas-station) overview.

In the [console](https://console.circle.com):

- Navigate to the Console Wallets tab.

- Select Create Wallet in the top right corner.
- Provide a wallet name. In this case, you can use “Console Wallet 1”

- Select Create.
You should now see two wallets, one for Polygon and Ethereum, on the console wallets dashboard. Copy the address of the Polygon wallet. This will be used in the subsequent step.

##🚧
Console Wallet Deploy Cost

Unlike Externally Owned Accounts (EOAs), SCAs cost gas to “deploy”. Note: With our process of lazy deployment, you won't have to pay the gas fee at the time of wallet creation. Instead, the fee will be charged when you initiate your first outbound transaction.


###
2. Deploy the Smart Contract
[](#2-deploy-the-smart-contract)
Now that you have a wallet let’s deploy the smart contract from a template.

In the [console](https://console.circle.com):

-
Navigate to the Templates tab via the left navigation.

-
Select NFT ERC-721.

-
Fill in the contract template deployment parameters:

-
Name: The off-chain name of the contract. This will only be represented on Circle’s systems, such as the APIs and Web3 Services Console. In this case, you can use “My First NFT Contract” or any other name you prefer.

-
Contract Name: The on-chain name for the contract. In this case, you can use “My First NFT Contract” or any other name you prefer.

-
Default Admin: The address of the default admin. This address can execute permissions functions on the contract. This should be an address you control or the address of a user if deploying on their behalf. In this case, use the console wallet’s address from step one.

- NOTE: The following roles will be given to the defaultAdmin:  DEFAULT_ADMIN_ROLE, MINTER_ROLE, TRANSFER_ROLE. The defaultAdmin will also be set as the contract's owner. All of these roles can be updated by the defaultAdmin later. 
-
Primary Sale Recipient: The recipient's address for first-time sales. All first-time sale proceeds will go to this address. In this case, use the console wallet’s address from step one.

-
Royalty Recipient: The recipient address for all royalties. Any proceeds from secondary sales will go to this address. In this case, use the console wallet’s address from step one.

-
Royalty Percent: The percentage of secondary sales that go to the royalty recipient. There should be a value between 0 and 100. In this case, use “0”.

-
Network: The network to deploy onto. In this case, select “Polygon Amoy”.

-
Select Wallet: The wallet to deploy the smart contract from. In this case, select “Console Wallet 1”

-
Deployment Speed: A fee level setting (FAST, AVERAGE, SLOW). This affects how quickly the network will process your deployment transaction. In this case, you will use “AVERAGE”.

-
Select Deploy.

Once deployed, you will be placed back on the Contracts dashboard. Here, you can check the deployment status of the contract. When you land on the page, expect the deployment status to be Pending. Give it several seconds, and then refresh the page. At this point, the deployment status should be set to Complete.


###
3. Upload an NFT to IPFS
[](#3-upload-an-nft-to-ipfs)
You’ll be using [NFT.Storage](https://nft.storage/) to upload your NFT’s metadata to IPFS. NFT.Storage is a free, decentralized storage and bandwidth provider for IPFS and Filecoin. [Filecoin](https://filecoin.io/) provides long-term storage for the data, ensuring that even if NFT.Storage is attacked or taken down, the NFT data will still be available.

##📘
NFT data stored by NFT.Storage can be accessed from the decentralized IPFS network from any peer with the content. This means you can use the CID of your data to find it through any IPFS provider.

- Login to [NFT.Storage](https://nft.storage/) account and navigate to Files. 
- Using the Upload File flow, upload your data, and you will receive the CID of your NFT metadata. You can make a full IPFS URL (ipfs://<cid>). 

- Grab the IPFS URL of your newly uploaded data. You should use IPFS URLs when minting your NFT in the next steps.


###
4. Mint an NFT
[](#4-mint-an-nft)
Now that your NFT has been successfully uploaded let's dive into the Console UI to explore the full range of capabilities it offers and mint an NFT. With the Console, you can seamlessly explore the NFT contract you deployed. Gain insights into its ABI functions, inspect their inputs, and easily execute functions. In this example, we will directly execute the mintTo function from the console - which mints an NFT to a user without a sale. 

In the [console](https://console.circle.com):

-
Navigate to the Contracts tab.

-
Select the My First NFT Contract within the console.

-
Select ABI Functions sub tab. This should be selected by default.

-
Select Write.

-
Select mintTo.

-
On the mintTo ABI function, provide the following parameters:

- _to: The wallet address to which the NFT will be minted. In this case, use the wallet address of the wallet you deployed the contract from. Ultimately, this can be any wallet on-chain.
- _uri: A distinct Uniform Resource Identifier (URI) for a given asset. In this case, provide the IPFS URL copied from NFT.Storage. 
-
Select Execute Function and ensure Console Wallet 1 is selected.

-
Select Execute.


Once executed, select View Transaction History to access the Console Wallet's transaction dashboard. Here, you can easily monitor the progress and status of the transaction. Once the transaction state is marked as Complete, the NFT has been successfully transferred on-chain to the designated address mentioned above.

##📘
In the transaction history view, you will also notice an inbound transfer indicating that the NFT has been minted to the Console Wallet.

Congratulations, you have deployed a smart contract and minted an NFT!

Next Steps

- Learn how to mint NFTs via the [API](/w3s/docs/deploy-smart-contract-template#api-path).
- Deploy a Token or Multi-Token contract.
##
API Path
[](#api-path)
###
Prerequisites
[](#prerequisites-1)
- Create a Developer Account, API key, and set up a Developer-Controlled Wallet. We recommend using our [interactive quickstart guide](https://learn.circle.com/quickstarts/dev-controlled-wallets) to complete these requirements if you haven’t already. 
- Create an [NFT.Storage](https://nft.storage/) account. NFT.storage is a free service that requires an email or GitHub account. 
###
1. Deploy the Smart Contract
[](#1-deploy-the-smart-contract)
To deploy a smart contract, you must provide a wallet to deploy from. If you don’t have the walletId, you can use the [GET /wallets](/w3s/reference/listwallets-1) API to acquire the wallet ID. Ensure the wallet custody type is Developer-Controlled and the blockchain is Polygon Amoy. We recommend the account type be SCA - removing the need for gas in your wallet for deployment. Lastly, note down the wallet’s address. This will be used in the subsequent steps.

Deployment Request Parameters:

- idempotencyKey: A unique value generated by the client that the server uses to recognize subsequent retries of the same request.
- name: The off-chain name of the contract. This will only be represented on Circle’s System such as the APIs and Web3 Services Console. In this case, you can use My First NFT Contract or any other name you prefer.
- walletId: The ID of the wallet to deploy from.
- templateId: The ID of the chosen template. In this case, you will use 76b83278-50e2-4006-8b63-5b1a2a814533 as the ID for the NFT template. This templateId and other template-specific details are listed in the [Templates Glossary](/w3s/docs/scp-templates-glossary).
- blockchain: The network to deploy onto. In this case, you will use MATIC-AMOY.
- entitySecretCiphertext: The encrypted ciphertext of your entity secret. It must be re-encrypted for every new request. Go to [How to Re-Encrypt the Entity Secret](/w3s/docs/entity-secret-management#how-to-re-encrypt-the-entity-secret) to learn more about generating an entity secret ciphertext. 
- feeLevel: A fee level setting (LOW, MEDIUM, HIGH). This affects how quickly the network will process your deployment transaction. In this case, you will use MEDIUM.
- templateParameters: A JSON object representing the template deployment parameters and arguments. This object is explained in more detail below. 
####
Template Parameters
[](#template-parameters)
Template parameters are the inputs for initializing and deploying the template on-chain. Each template will have its own set of parameters. 

For the NFT template, the required properties are:

- name: The on-chain name for the contract. In this case, you can use My First NFT Contract or any other name you prefer.
- defaultAdmin: The address of the default admin. This address can execute permissioned functions on the contract. This should be an address you control or the address of a user if deploying on their behalf. In this case, use the developer-controlled wallet’s address retrieved from the [GET /wallets](/w3s/reference/listwallets-1) API above.
- NOTE: The following roles will be given to the defaultAdmin:  DEFAULT_ADMIN_ROLE, MINTER_ROLE, TRANSFER_ROLE. The defaultAdmin will also be set as the contract's owner. All of these roles can be updated by the defaultAdmin later.
- primarySaleRecipient: The recipient address for first-time sales. All first-time sale proceeds will go to this address. In this case, use the developer-controlled wallet’s address retrieved from the [GET /wallets](/w3s/reference/listwallets-1) API above.
- royaltyRecipient: The recipient address for all royalties. Any proceeds from secondary sales will go to this address. In this case, use 0.
- royaltyPercent: The percentage of secondary sales that go to the royalty recipient. It should be a value between 0 and 100. In this case, use the developer-controlled wallet’s address retrieved from the [GET /wallets](/w3s/reference/listwallets-1) API above.
There are additional properties and configurations available for more advanced use cases. Please see the [Templates Glossary](/w3s/docs/scp-templates-glossary) for more information on configuring the NFT Template and for extended use cases of all template types. 

Now that you have all your request parameters in order, you can deploy the smart contract by making a request to the [POST /templates/{id}/deploy](/w3s/reference/deploycontracttemplate) API. You must also pass your entity secret ciphertext in the request body. The full request is as follows:

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

// Import and configure the smart contract SDK
const { initiateSmartContractPlatformClient } = require('@circle-fin/smart-contract-platform');
const circleContractSdk = initiateSmartContractPlatformClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

const response = await circleContractSdk.deployContractTemplate({
  id: 'aea21da6-0aa2-4971-9a1a-5098842b1248',
  blockchain: 'MATIC-AMOY', 
  name: 'My First NFT Contract',
  walletId: '<WALLET_ID>',
  templateParameters: {
    name: 'My First NFT Contract',
    defaultAdmin: '<ADDRESS>',
    primarySaleRecipient: '<ADDRESS>',
    royaltyRecipient: '<ADDRESS>',
    royaltyPercent: 0
  },
  fee: {
    type: 'level',
    config: {
      feeLevel: 'MEDIUM'
    }
  }
});
JSON

{
  "data": {
    "contractIds": ["b7c35372-ce69-4ccd-bfaa-504c14634f0d"],
    "transactionId": "601a0815-f749-41d8-b193-22cadd2a8977"
  }
}
##📘
A successful API response indicates that the deployment process has been initiated but does not guarantee that the deployment will be successful. The returned transactionId can be used to check the status of the deployment transaction using [get a transaction](/w3s/reference/gettransaction-1).

To validate that your contract has deployed successfully, make a request to [GET /transactions/{id}](/w3s/reference/gettransaction-1), providing the ID of the transaction received in the response above.

Node.js
cURL

const response = await circleDeveloperSdk.listTransactions({});
Response

{
  "data": {
    "transaction": {
      "id": "601a0815-f749-41d8-b193-22cadd2a8977",
      "blockchain": "MATIC-AMOY",
      "state": "COMPLETE",
      ... 
      [other fields truncated]
    }
  }
}
The returned contractId can be used to perform further management and interaction with the deployed smart contract, such as minting an NFT.

###
2. Upload an NFT to IPFS
[](#2-upload-an-nft-to-ipfs)
You’ll be using [NFT.Storage](https://nft.storage/) to upload your NFT’s metadata to IPFS. NFT.Storage is a free, decentralized storage and bandwidth provider for IPFS and Filecoin. [Filecoin](https://filecoin.io/) provides long-term storage for the data, ensuring that even if NFT.Storage is attacked or taken down, the NFT data will still be available.

##📘
NFT data stored by NFT.Storage can be accessed from the decentralized IPFS network from any peer with the content. This means you can use the CID of your data to find it through any IPFS provider.

- Login to [NFT.Storage](https://nft.storage/) account and navigate to Files. 
- Using the Upload File flow, upload your data, and you will receive the CID of your NFT metadata. You can make a full IPFS URL (ipfs://<cid>). 


- Grab the IPFS URL of your newly uploaded data. You should use IPFS URLs when minting your NFT in the next steps. 

###
3. Mint an NFT
[](#3-mint-an-nft)
Now that your NFT has been successfully uploaded let's dive into the Web3 Services Console to explore its full range of capabilities. With the Console, you can seamlessly explore the NFT  contract you deployed. Gain insights into its ABI functions, inspect their inputs, and easily execute functions through an API call by conveniently copying the required code.

Alternatively, you can retrieve detailed information about the contract using the[Get contract by ID](/w3s/reference/getwallet-1) response, which includes all the available functions and events. 

In this example, we will use the code snippet generator to build the contract execution request for the mintTo function - which mints an NFT to a user without a sale. This can be done by selecting the contract within the Console, then in the ABI functions tab, selecting write, and selecting mintTo.

As you fill in the parameters for the mintTo function, the code to make a contract execution API request and mint the NFT is automatically generated. Make sure to select your preferred wallet type and code generated, in this case, select a Developer Controlled wallet and Shell to create a cURL request example.

mintTo function parameters:

- _to: The wallet address that the NFT will be minted to. In this case, use the wallet address of the wallet you deployed the contract from. Ultimately, this can be any wallet on-chain.
- _uri: A distinct Uniform Resource Identifier (URI) for a given asset. In this case, provide the IPFS URL copied from NFT.Storage.
As a result of plugging these parameters in, you will be provided with a code block similar to the one below. Once copied to your preferred API client, add your walletId and entitySecretCiphertext to the API request.

Node.js
cURL

const response = await circleDeveloperSdk.createContractExecutionTransaction({
  walletId: '<WALLET_ID>',
  abiFunctionSignature: 'mintTo(address,string)',
  abiParameters: [
    '0x6E5eAf34c73D1CD0be4e24f923b97CF38e10d1f3',
    'ipfs://bafkreibdi6623n3xpf7ymk62ckb4bo75o3qemwkpfvp5i25j66itxvsoei'
  ],
  contractAddress: '0xf7761519cf2d84c31f634e249790b4757c10a103',
  fee: {
    type: 'level',
    config: {
      feeLevel: 'MEDIUM'
    }
  }
});
Note that the address of the walletId must have the MINTER_ROLE on the contract to mint an NFT. Make sure you use the appropriate walletId. 

JSON

{
  "data": {
    "id": "601a0815-f749-41d8-b193-22cadd2a8977",
    "state": "INITIATED"
  }
}
##📘
A successful API response indicates that the `mintTo` transaction has been initiated but does not guarantee that the deployment will be successful.

To validate that your contract has deployed successfully, make a request to [GET /transactions/{id}](/w3s/reference/gettransaction-1) providing the ID of the transaction received in the response above.

Node.js
cURL

const response = await circleDeveloperSdk.getTransaction({
  id: '601a0815-f749-41d8-b193-22cadd2a8977'
});
Response

{
  "data": {
    "transaction": {
      "id": "601a0815-f749-41d8-b193-22cadd2a8977",
      "blockchain": "MATIC-AMOY",
      "state": "COMPLETE",
      ... 
      [other fields truncated]
    }
  }
}
Once the transaction is complete, the receiver - the _to address that you previously specified - will now be able to use and interact with the minted NFT. 

Congratulations, you have deployed a smart contract and minted an NFT!

Next Steps

- Learn how to mint NFTs via the [Console](/w3s/docs/deploy-smart-contract-template#console-ui-path).
- Deploy a Token or Multi-Token contract.
Updated 24 days ago


URL: https://developers.circle.com/w3s/docs/erc-20-token
Title: Token (ERC-20)
Description: undefined
Keywords: undefined

The Token template is an audited, ready-to-deploy smart contract for an ERC-20 token. The ERC-20 standard is the most popular standard for fungible tokens. The token's fungibility allows it to be used for a variety of use cases, including:

-
Stablecoins: ERC-20 tokens serve as the foundation for stablecoins like USDC, backed by US dollars. To learn more about the USDC contract, see [0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) on Etherscan.

-
Loyalty Points: ERC-20 tokens can represent on-chain loyalty points to incentivize and reward users for their activities within a platform or ecosystem.

-
Governance: ERC-20 tokens can represent governance rights, allowing holders to participate in protocol decisions. Notable examples include tokens utilized by platforms like Uniswap.

-
Ownership: ERC-20 tokens can also represent fractional ownership in real-world assets, such as houses, ounces of gold, or company shares.

In this comprehensive guide, you explore the Token template, which provides all the necessary information to deploy and understand the contract's common functions.

##
Deployment Parameters
[](#deployment-parameters)
The Token template creates a customized, fully compliant ERC-20 smart contract. To create a contract using this template, provide the following parameter values when deploying a smart contract template using the [POST: /templates/{id}/deploy](/w3s/reference/deploycontracttemplate) API.

Template ID: a1b74add-23e0-4712-88d1-6b3009e85a86

Template Deployment Parameters

Parameter	Type	Required	Description
name	String	X	Name of the contract - stored as a property of the contract on-chain. 
symbol	String		Symbol of the token - stored on-chain. The symbol is usually 3 or 4 characters in length.
defaultAdmin	String	X	The address of the default admin. This address can execute permissioned functions on the contract.
Important: You will lose administrative access to the contract if this is not set to an address you control.
primarySaleRecipient	String	X	The recipient address for first-time sales.  
platformFeeRecipient	String		The recipient address for all sales fees. If you deploy a template on someone else’s behalf, you can set this to your own address.
platformFeePercent	Float		The percentage of sales that go to the platform fee recipient. For example, set it as 0.1 if you want 10% of sales fees to go to the  platformFeeRecipient. 
contractUri	String		The URL for the marketplace metadata of your contract. This is used on marketplaces like OpenSea. See [Contract-level Metadata](https://docs.opensea.io/docs/contract-level-metadata) for more information. 
trustedForwarders	Strings[]		A list of addresses that can forward ERC2771 meta-transactions to this contract. See [ethereum.org](https://eips.ethereum.org/EIPS/eip-2771) for more information. 
Here is an example of the templateParameters JSON object within the request body to [deploy a contract from a template](https://developers.circle.com/w3s/reference/deploycontracttemplate) for the ERC-20 Token template. 

##📘
In this example, the defaultAdmin and primarySaleRecipient parameters are the same address but can be set distinctly based on your use case.

JSON

"templateParameters": {
  "name": "My Token Contract",
  "defaultAdmin": "0x4F77E56dfA40990349e1078e97AC3Eb479e0dAc6",
  "primarySaleRecipient": "0x4F77E56dfA40990349e1078e97AC3Eb479e0dAc6"
}
##
Common Functions
[](#common-functions)
This section lists the most commonly used functions on the Token template, their respective parameters and potential failure scenarios. These functions include:

-
[approve [write]](#approve-address-spender-uint256-amount-write)

-
[transfer [write]](#transfer-address-to-uint256-amount-write)

-
[mintTo [write]](#mintto-address-to-uint256-amount-write)

-
[burn [write]](#burn-uint256-amount-write)

-
[grantRole [write]](#grantrole-bytes32-role-address-account-write)

-
[revokeRole [write]](#revokerole-bytes32-role-address-account-write)

-
[balanceOf [read]](#balanceof-address-account-read)

-
[allowance [read]](#allowance-address-owner-address-spender-read)

##📘
At this time, failure scenarios and error messages received from the blockchain are not passed through Circle's APIs. Instead, you will receive a generic [ESTIMATION_ERROR](https://developers.circle.com/w3s/docs/asynchronous-states-and-statuses#transaction-errors) error.

###
approve [write]
[](#approve-write)
The approve function lets token owners specify a limit on the number of tokens another account address can spend on their behalf.

Parameters:

Parameter	Type	Description
spender	address	The owner approves the account address to spend tokens. It could be another smart contract address or an externally owned account address.
amount	unit256	The number of tokens the owner approves for spending by the specified spender. The amount should be set in the smallest denomination of the ERC-20 token.
Failure Scenarios:

- The approve function fails if the spender is the zero address.
"ERC20: approve to the zero address"
Notes:

-
If amount is the maximum uint256 value the allowance is not updated when the transferFrom function is called. This is semantically equivalent to an infinite approval.

-
There is no balance check on the approve function so token owners may approve allowances greater than their current balance.  

Solidity

function approve(address spender, uint256 amount) public virtual override returns (bool) {
  address owner = _msgSender();
  _approve(owner, spender, amount);
  return true;
}
###
transfer [write]
[](#transfer-write)
Allows the token owner to transfer a specified number of tokens to another account address.

Parameters:

Parameter	Type	Description
to	address	The address to which the token will be transferred. This can be another user's address or a smart contract address.
amount	unit256	The number of tokens to transfer.
Failure Scenarios:

- The to address parameter is checked to ensure it is not the zero address address(0).
"ERC20: transfer to the zero address"
- The beforeTokenTransfer hook is called, which checks TOKEN_TRANSFER permissions.
"restricted to TRANSFER_ROLE holders”
- The token owner doesn’t have a sufficient balance to transfer the specified token amount.
"ERC20: transfer amount exceeds balance"
Solidity

function transfer(address to, uint256 amount) public virtual override returns (bool) {
  address owner = _msgSender();
  _transfer(owner, to, amount);
  return true;
}
###
mintTo [write]
[](#mintto-write)
A designated minter can create a specified number of tokens and assign them to a specified account address.

Parameters: 

Parameter	Type	Description
to	address	The address to which the newly minted tokens will be assigned.
amount	unit256	The number of tokens to be minted and assigned to the specified address.
Failure Scenarios: 

- The function checks if the caller has the MINTER_ROLE. The function will fail if the caller does not have the minter role. Expect to see the following error:
"not minter."
- The function ensures that the account address is not the zero address address(0), as this is an invalid address to mint tokens to.
"ERC20: mint to the zero address"
- The beforeTokenTransfer hook is called, which checks TOKEN_TRANSFER permissions.
"restricted to TRANSFER_ROLE holders”
Solidity

function mintTo(address to, uint256 amount) public virtual {
  require(hasRole(MINTER_ROLE, _msgSender()), "not minter.");
  _mintTo(to, amount);
}
###
burn [write]
[](#burn-write)
Allows a token holder to burn (destroy) a specified number of the token total supply.

Parameters:

Parameter	Type	Description
amount	unit256	The number of tokens to be burned.
Failure Scenarios:

- The _beforeTokenTransfer  hook is called, which checks TOKEN_TRANSFER permissions.
"restricted to TRANSFER_ROLE holders”
- The token owner has no sufficient balance to burn the specified token amount.
"ERC20: burn amount exceeds balance"
Solidity

function burn(uint256 amount) public virtual {
  _burn(_msgSender(), amount);
}
Permissions and Roles

Roles are referred to by their bytes32 identifier. For example: 

Solidity

bytes32 public constant MY_ROLE = keccak256("MY_ROLE");
 Roles can also represent a set of permissions. To restrict access to a function call, use hasRole:

Solidity

function foo() public {
  require(hasRole(MY_ROLE, msg.sender));
  ...
}
Roles can be granted and revoked dynamically via the grantRole and revokeRole functions. Each role has an associated admin role, and only accounts that have a role's admin role can call grantRole and revokeRole. By default, the admin role for all roles is DEFAULT_ADMIN_ROLE. Only accounts with the DEFAULT_ADMIN_ROLE can grant or revoke other roles. You can use the _setRoleAdmin function to create more complex role relationships.

###
grantRole [write]
[](#grantrole-write)
Grants a specified role to an account. Only an account address that has the admin role assigned can call this function.

Parameters

Parameter	Type	Description
role	bytes32	The bytes32 identifier of the role to be granted.
account	address	The address to which the role will be granted.
Failure Scenarios:

- The onlyRole(getRoleAdmin(role)) modifier ensures that the function caller has the admin role for the specified role. Only addresses with the admin role can grant roles to other accounts.
"AccessControl: account ", StringsUpgradeable.toHexString(account), " is missing role ", StringsUpgradeable.toHexString(uint256(role), 32)
- The function checks if the account already has the specified role using the hasRole function. If the account does not have the role, the function continues. There is no error message. 
Solidity

function grantRole(bytes32 role, address account) public virtual override onlyRole(getRoleAdmin(role)) {
  _grantRole(role, account);
}
###
revokeRole [write]
[](#revokerole-write)
Revoke the specified role from an account address. Only account addresses with the admin role assigned can call this function.

Parameters:

Parameter	Type	Description
role	bytes32	The role to be revoked.
account	address	The address from which the role will be revoked.
Failure Scenarios:

- The onlyRole(getRoleAdmin(role)) modifier ensures that the function caller has the admin role for the specified role. Only addresses with the admin role can revoke roles from other accounts.
"AccessControl: account ", StringsUpgradeable.toHexString(account), " is missing role ", StringsUpgradeable.toHexString(uint256(role), 32)
Solidity

function revokeRole(bytes32 role, address account) public virtual override onlyRole(getRoleAdmin(role)) {
  _revokeRole(role, account);
}
###
balanceOf [read]
[](#balanceof-read)
Retrieves the balance of tokens owned by a specific account address.

Parameter:

Parameter	Type	Description
account	address	The address for which the token balance is being fetched.
Solidity

function balanceOf(address account) public view virtual override returns (uint256) {
  return _balances[account];
}
###
allowance [read]
[](#allowance-read)
Returns the maximum amount the spender is approved to withdraw from the owner’s account. This function retrieves the allowance granted by the owner account address to the spender account address.

Parameters:

Parameter	Type	Description
owner	address	The address that granted the allowance.
spender	address	The address for which the allowance is being fetched.
Solidity

function allowance(address owner, address spender) public view virtual override returns (uint256) {
  return _allowances[owner][spender];
}

Updated 2 months ago


URL: https://developers.circle.com/w3s/reference/getuserchallenge
Title: Get a challenge
Description: undefined
Keywords: undefined

TIME	STATUS	USER AGENT	
Make a request to see history.
id
string
required
ExtChallenge id to query


X-User-Token
string
required
Unique system generated JWT session token for specific user.


#
200
Success response

#
Default
Error response

Updated 6 months ago

Did this page help you?

Yes

No

Shell

Node

Ruby

PHP

Python


1
curl --request GET \
2
     --url https://api.circle.com/v1/w3s/user/challenges/id \
3
     --header 'accept: application/json'
Click Try It! to start a request and see the response here! Or choose an example:
application/json

200

Default

URL: https://developers.circle.com/w3s/docs/developer-controlled-receive-an-inbound-transfer
Title: Receive an Inbound Transfer
Description: undefined
Keywords: undefined

Circle Programmable Wallets provide a comprehensive developer solution to storing, sending, and spending Web3 digital currencies and NFTs. You or your users can manage asset infrastructure. Circle provides a one-stop-shop experience with all the tools and services to handle the complex parts, including security, transaction monitoring, account recovery flows, and more.

This guide outlines initiating a currency transfer into a previously created developer-controlled wallet. You’ll learn to make API requests via Circle's API references or cURL requests. In this guide, you’ll find cURL requests presented inline, while API references are linked from the API endpoint text. You can find instructions on using it in the [testing via the reference pages](/w3s/docs/api-references) guide.

- As with most of our quickstarts, all API calls and transactions in this guide occur within the Testnet environment; no real-world funds will be transferred.
- If you have not yet created a developer-controlled wallet, see [this guide](/w3s/docs/developer-controlled-create-your-first-wallet).
###
1. Acquire the Wallet Address
[](#1-acquire-the-wallet-address)
To receive funds on-chain, you will need the wallet's address. The address is the on-chain identifier provided to the sending wallet or faucet in the following step. To retrieve the address, make a request to [GET /wallets](/w3s/reference/listwallets) as shown below. You should also note down the wallets.id this will be used in step 3.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

const response = await circleDeveloperSdk.getWallet({});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "ce714f5b-0d8e-4062-9454-61aa1154869b",
        "state": "LIVE",
        "walletSetId": "0189bc61-7fe4-70f3-8a1b-0d14426397cb",
        "custodyType": "DEVELOPER",
        "address": "0xf5c83e5fede8456929d0f90e8c541dcac3d63835",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-08-03T19:33:14Z",
        "createDate": "2023-08-03T19:33:14Z"
      }
    ]
  }
}
###
2. Transfer Testnet Currency
[](#2-transfer-testnet-currency)
Send Testnet currency from an external wallet outside the Programmable Wallet infrastructure into your applicable wallet address. The best way to achieve this is through the use of a faucet. In our case, we will use the [USDC Faucet](https://usdcfaucet.com/) to transfer USDC on Polygon Amoy to the wallet.

Here is a list of reputable faucets for each blockchain:

- Polygon: [USDC on MATIC Faucet](https://faucet.circle.com/) | [Amoy MATIC Faucet](https://www.alchemy.com/faucets/polygon-amoy)
- Ethereum: [USDC on ETH Faucet](https://faucet.circle.com/) | [Sepolia ETH Faucet](https://sepoliafaucet.com/)
- Avalanche: [USDC on AVAX Faucet](https://faucet.circle.com/) | [Fuji AVAX Faucet](https://core.app/en/tools/testnet-faucet/?token=C)
###
3. Check the Transfer State
[](#3-check-the-transfer-state)
Once an inbound transfer is made to the address and completed, Circle sends a notification to a [subscribed endpoint](/w3s/docs/web3-services-notifications-quickstart). The Webhook notification will be similar to the one below.

Webhook Request Body

{
  "subscriptionId": "d4c07d5f-f05f-4fe4-853d-4dd434806dfb",
  "notificationId": "05b3f4e5-ec27-44b8-aa40-3698577f6d92",
  "notificationType": "transactions.inbound",
  "notification": {
    "id": "2f4b6bcd-a752-5d8b-996b-92e3e04bd33b",
    "blockchain": "MATIC-AMOY",
    "walletId": "ce714f5b-0d8e-4062-9454-61aa1154869b",
    "tokenId": "38f2ad29-a77b-5a44-be05-8d03923878a2",
    "destinationAddress": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
    "amounts": [
      "10"
    ],
    "nftTokenIds": [],
    "state": "COMPLETED",
    "transactionType": "INBOUND",
    "createDate": "2023-07-28T16:03:08Z",
    "updateDate": "2023-07-28T16:06:40Z"
  },
  "timestamp": "2023-07-28T16:06:40.907831464Z",
  "version": 2
}
Alternatively, you can poll [GET /transactions](/w3s/reference/listtransactions) using the walletId as query parameter.

Node.js
cURL

const response = await circleDeveloperSdk.listTransactions({
  walletIds: ['ce714f5b-0d8e-4062-9454-61aa1154869b']
});
Response Body

{
  "data": {
    "transactions": [
      {
        "id": "97d22a88-6d25-5947-a7b6-61b3dc668057",
        "blockchain": "MATIC-AMOY",
        "tokenId": "38f2ad29-a77b-5a44-be05-8d03923878a2",
        "walletId": "ce714f5b-0d8e-4062-9454-61aa1154869b",
        "sourceAddress": "0x6e5eaf34c73d1cd0be4e24f923b97cf38e10d1f3",
        "destinationAddress": "0xf5c83e5fede8456929d0f90e8c541dcac3d63835",
        "transactionType": "INBOUND",
        "custodyType": "DEVELOPER",
        "state": "CONFIRMED",
        "amounts": [
          "10"
        ],
        "nfts": null,
        "txHash": "0xdd2f81a78605dcad759265c703fb2b4c507c5ea100319338422714bfcde77225",
        "blockHash": "0x4df6092fdb868331614771ff11944b43051cf6ed1067f8cfa55e9d40ef61426b",
        "blockHeight": 9423950,
        "networkFee": "",
        "firstConfirmDate": "2023-07-28T19:07:24Z",
        "operation": "TRANSFER",
        "abiParameters": null,
        "createDate": "2023-07-28T19:07:34Z",
        "updateDate": "2023-07-28T19:07:37Z"
      }
    ]
  }
}
You have successfully transferred 10 USDC into your developer-controlled wallet! Now you can move on to [transferring tokens from wallet to wallet](developer-controlled-transfer-tokens-across-wallets).

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/gas-station-paymaster
Title: Paymaster
Description: undefined
Keywords: undefined

Circle’s Paymasters are smart contracts that sponsor network fees for users of your SCA wallets on your behalf. Circle deploys these paymasters on every chain, so you don’t have to. If a transaction meets the policies you set up, Circle's off-chain service lets the paymaster sponsor that transaction.

The following are the on-chain addresses of Circle's Paymasters:

Blockchain	Network	Blockchain Address
Ethereum	Mainnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://etherscan.io/address/0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25)
Ethereum	Sepolia Testnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://sepolia.etherscan.io/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Polygon	Mainnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://polygonscan.com/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Polygon	Amoy Testnet	[0x7ceA357B5AC0639F89F9e378a1f03Aa5005C0a25](https://www.oklink.com/amoy/address/0x7cea357b5ac0639f89f9e378a1f03aa5005c0a25)
Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/user-controlled-create-your-first-wallet
Title: Create Your First User-Controlled Wallet
Description: undefined
Keywords: undefined

This guide outlines initializing and creating a user-controlled smart contract account (SCA) wallet by setting their PIN code and security questions. It utilizes Circle’s sample application in combination with API requests that can be done via Circle's API references or cURL requests. cURL request will be provided inline, while API references are linked from the API endpoint text. Instructions on using the API references are in this [guide](/w3s/docs/api-references). 

If you prefer to create an externally owned account (EOA) wallet, take note of the callout where an API parameter must be altered. To learn more about the two account types, please see the [account types guide](/w3s/docs/programmable-wallets-account-types).

##
Prerequisites
[](#prerequisites)
- Create a [developer account and acquire an API key in the console](/w3s/docs/circle-developer-account).
- Install the [Web3 Services SDKs](/w3s/docs/web3-services-sdks), which is currently only available for Node.js. (optional)
- Set up one of the web, iOS, or Android [sample applications](/w3s/docs/sample-applications) locally.
##
1. Configure and Run the Sample App
[](#1-configure-and-run-the-sample-app)
Once you have one of the web, iOS, or Android [sample applications](/w3s/docs/sample-applications) set up locally, you will then:

- Run the sample app and simulator.
- Obtain your App ID. This can be done by one of two options
- Access the developer console and navigate to the [configurator](https://console.circle.com/wallets/user/configurator) within user-controlled wallets. From there, copy the App ID.
- Make an API request to [GET /config/entity](/w3s/reference/getentityconfig) and copy the App ID from the response body.
- Add the App ID to the sample app.
##📘
App ID

AKA Application ID is a unique identifier assigned to your application. It serves as a key that allows you to configure and manage various settings specific to your User-Controlled Wallet integration. The App ID is essential for identifying your application and enabling communication with the Circle Platform APIs.


View of the sample app on launch

##
2. Create a User
[](#2-create-a-user)
Make a request to [POST /users](/w3s/reference/createuser) to create a userId. This represents the user’s account and all associated wallets, assets, and transactions. The userId is recommended to be in the UUID format.

##📘
We recommend that you maintain a mapping to associate the end-user profile usernames with the userId provided to our service/end-point. You can use a local database to maintain this mapping.

Node.js
cURL

// Import and configure the user-controlled wallet SDK
const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets');
const circleUserSdk = initiateUserControlledWalletsClient({
  apiKey: '<API_KEY>'
});

const response = await circleUserSdk.createUser({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
If the request is successful, you will receive an empty response body.

Response Body

{}
##
3. Acquire a Session Token
[](#3-acquire-a-session-token)
Next, you will need to acquire a session token. To do this, you will make a request to the [POST /users/token](/w3s/reference/getusertoken) using the previously created userId in Step 2. The userToken is a 60-minute session token, which is used to initiate requests that require a user challenge (PIN code entry). After 60 minutes, the session expires, and a new userToken must be generated via the same endpoint.

From this response, you will acquire the encryptionKey and userToken which you should provide in the respective sample app fields. Additionally, you will use the userToken in Step 4.

Node.js
cURL

const response = await circleUserSdk.createUserToken({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
Response body

{
  "data": {
    "userToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCC9.eyJkZXZlbG9wZXJFbnRpdHlFbnZpcm9ubWVudCI6IlRFU1QiLCJlbnRpdHlJZCI6IjRlMDdhOGM5LTIxOTAtNDVlNC1hNjc0LWQyMGFkNjg4MWI3YyIsImV4cCI6MTY5MDU1MjcwNywiaWF0IjoxNjkwNTQ5MTA3LCJpbnRlcm5hbFVzZXJJZCI6ImQ2ZjkzODliLWQ5MzUtNWFlYy1iOTVhLWNjNTk1NjA2YWM5NiIsImlzcyI6Imh0dHBzOi8vcHJvZ3JhbW1hYmxlLXdhbGxldC5jaXJjbGUuY29tIiwianRpIjoiMmE0YmJlMzAtZTdkZi00YmM2LThiODMtNTk0NGUyMzE2ODlkIiwic3ViIjoiZXh0X3VzZXJfaWRfOSJ9.dhfByhxZFbJx0XWlzxneadT4RQWdnxLu3FSN9ln65hCDOfavaTL1sc4h-jUR8i4zMmfdURw3FFcQIdSbm-BUg6M7FP_fp-cs9xBbNmRZa31gMd1aKdcajJ9SvlVrfUowYfGXM3VcNF8rtTFtW-gk1-KzU4u10U35XXbbMcW1moxE0Rqx_fKotDgk2VdITuuds5d5TiQzAXECqeCOCtNoDKktMkglltbnLxOaRl2ReZjGt-ctD2V0DbYNO4T_ndPSUDI6qD7dXQRed5uDcezJYoha3Qj3tFGBglEnox2Y6DWTbllqjwmfTGrU8Pr0yz4jQz7suGwmiCzHPxcpYxMzYQ",
    "encryptionKey": "Tlcyxz7Ts9ztRLQq5+pic0MIETblYimOo2d7idV/UFM="
  }
}
##
4. Initialize the User's Account and Acquire the Challenge ID
[](#4-initialize-the-users-account-and-acquire-the-challenge-id)
You have two options to initialize your user’s account:

For this guide, we will use option one to create a user and a wallet simultaneously.

1	[POST /user/initialize](createuserwithpinchallenge): Initialize a user account and create a wallet	This call generates wallets for the specified blockchains at the time of account creation. Use this method if you know which blockchain the wallet will be created on.
2	[POST /user/pin](/w3s/reference/createuserpinchallenge): Initialize the user account	This call generates an account without creating a wallet. Use this method if you are unsure when creating an account on which blockchain the wallet will be created on.
Make a request to [POST /user/initialize](/w3s/reference/createuserwithpinchallenge) using the userToken returned from Step 3. This call returns a Challenge ID, which is used with the Circle Programmable Wallet SDK to have the user set their PIN code and security questions.

Make sure to provide a Testnet blockchain such as ETH-SEPOLIA, MATIC-AMOY, and AVAX-FUJI.

##📘
Externally Owned Account (EOA)

If you prefer to create an EOA, change the account type parameter to "accountType": "EOA". If an account type is not provided an EOA wallet will be created.

Node.js
cURL

const response = await circleUserSdk.createUserPinWithWallets({
  userToken: '<USER_TOKEN>',
  accountType: 'SCA',
  blockchains: ['MATIC-AMOY']
});
Response Body

{
  "data": {
    "challengeId": "0d1b5f41-1381-50af-983b-f54691415158"
  }
}
##
5. Create a Wallet in the Sample App
[](#5-create-a-wallet-in-the-sample-app)
At this point, you should be ready to execute your first request through the sample app. Once you’ve entered the required fields indicated in Step 4, click Execute to continue. 

The sample application takes you through the end user initialization process, which includes setting up the user’s PIN code and security questions and having the user confirm their configuration.


##
6. Check User Status
[](#6-check-user-status)
Once you have completed all the steps in the sample app, you can then check the user status by making a request to [GET /user](/w3s/reference/getuserbytoken) providing the userToken to retrieve the status of the user’s account.

To understand the current state of the user, inspect the following values:

-
PIN Status: This parameter indicates whether the end-user has successfully set a 6-digit PIN. If the user has set the PIN successfully, the pinStatus value will be enabled.

-
Security Question Status: This parameter provides information about the user's recovery method status, specifically related to the defined security questions. If the end-user has successfully established a recovery method by defining their security questions, the securityQuestionStatus will be set to enabled.

##📘
Additional information provided will include the number of failed attempts for both the pinStatus and the security questions. If the end-user enters an incorrect PIN or security answers more than three times, the pin entry or recovery method will be locked, and they will need to wait 30 minutes for it to be unlocked.

Node.js
cURL

const response = await circleUserSdk.getUserStatus({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "id": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
    "status": "ENABLED",
    "createDate": "2023-07-26T15:27:32Z",
    "pinStatus": "ENABLED",
    "pinDetails": {
      "failedAttempts": 0
    },
    "securityQuestionStatus": "ENABLED",
    "securityQuestionDetails": {
      "failedAttempts": 0
    }
  }
}
##
7. Check Wallet Status
[](#7-check-wallet-status)
Additionally, you can make an API request to [GET /wallets](/w3s/reference/listwallets) using the userToken to see the user’s newly created wallets.

Node.js
cURL

const response = await circleUserSdk.listWallets({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "01899cf2-d415-7052-a207-f9862157e546",
        "state": "LIVE",
        "walletSetId": "01899cf2-d407-7f89-b4d9-84d63573f138",
        "custodyType": "ENDUSER",
        "userId": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
        "address": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
        "addressIndex": 0,
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-07-28T14:41:47Z",
        "createDate": "2023-07-28T14:41:47Z"
      }
    ]
  }
}
Updated about 1 month ago

Congratulations! You’ve successfully set up your first user-controlled wallet. To learn how to send tokens to the wallet see the next guide.

-
[Receive an Inbound Transfer](/w3s/docs/user-controlled-receive-inbound-transfer)
Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/web3-services-api-keys-authentication
Title: API Keys & Authentication
Description: undefined
Keywords: undefined

Get the API keys you need to authenticate client requests and learn how to keep them safe.

###
What is an API Key?
[](#what-is-an-api-key)
API keys are unique data strings that we use to authenticate users and enable access to privileged operations on Circle’s APIs. All Circle APIs use API keys as the mechanism to authenticate client requests. Without it, requests will fail.

###
Keep Your API Keys Safe
[](#keep-your-api-keys-safe)
Because our API keys allow access to privileged operations on Circle APIs, you should always keep them secure.
You should never share or record your API key in a publicly accessible medium (client-side code, public repositories, etc.).

##🚧
Caution

If you lose secure control of your API keys, you may suffer financial loss.

###
API Key Authentication
[](#api-key-authentication)
Testnet (Test) authorization header example:


 authorization: Bearer TEST_API_KEY:ebb3ad72232624921abc4b162148bb84:019ef3358ef9cd6d08fc32csfe89a68d
Mainnet (Live) authorization header example:


 authorization: Bearer LIVE_API_KEY:ebb3ad72232624921abc4b162148bb84:019ef3358ef9cd6d08fc32csfe89a68d
###
Testing Authentication
[](#testing-authentication)
To verify that your API key is correctly set up, run the command below, which causes your code to reach one of our read endpoints (in this case, the endpoint to retrieve all wallets).

cURL

curl --request GET \
     --url https://api.circle.com/v1/w3s/wallets \
     --header 'accept: application/json' \
     --header 'authorization: Bearer <API_KEY>'
Successful Response:

JSON

{
  "data": {
    "wallets": [ ]
  }
}
Error Response:

JSON

{
  "code": 401,
  "message": "Malformed authorization. Are the credentials properly encoded?"
}
###
Managing Your API keys
[](#managing-your-api-keys)
You can use your Circle Developer account to get access to and manage your API keys. When generating an API key, copy it exactly as displayed to avoid errors.

Updated 6 months ago

Ready to sign up for a Circle Developer Account? Continue the User-Controlled Wallets Quickstart:

-
[Circle Developer Account](/w3s/docs/circle-developer-account)
Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/user-controlled-recover-account
Title: Recover an Account
Description: undefined
Keywords: undefined

Circle Programmable Wallets provide a comprehensive developer solution to storing, sending, and spending Web3 digital currencies and NFTs. You or your users can manage asset infrastructure. Circle provides a one-stop-shop experience with all the tools and services to handle the complex parts, including security, transaction monitoring, account recovery flows, and more.

This guide outlines how users can regain access to their account using their pre-set security questions in the event that they forget their original PIN code. 

Users should be aware that the answers to their security questions are their responsibility to remember. No additional parties can help users regain access to a user-controlled wallet if their PIN code is lost and they cannot remember the answers to their security questions. 

##🚧
Caution: If a user loses both their PIN code and the answers to their Security Questions, they will be permanently locked out of their account, losing access to all of their wallets and assets.

##
1. Run Sample App
[](#1-run-sample-app)
Once you have one of the web, iOS, or Android [sample applications](/w3s/docs/sample-applications) set up locally, you will then:

- Run the sample app and simulator.
- Obtain your App ID. This can be done by one of two options
- Access the developer console and navigate to the [configurator](https://console.circle.com/wallets/user/configurator) within user-controlled wallets. From there, copy the App ID.
- Make an API request to [GET /config/entity](/w3s/reference/getentityconfig) and copy the App ID from the response body.
- Add the App ID to the sample app.
##
2. Acquire Session Token
[](#2-acquire-session-token)
Next, you will need to acquire a session token. Make a request to the[POST /users/token](/w3s/reference/getusertoken) using the previously created userId in Step 1. The userToken is a 60-minute session token used to initiate requests requiring a user challenge (PIN code entry). After 60 minutes, the session expires, and a new userToken must be generated via the same endpoint.

From this response, you will acquire the encryptionKey and userToken which you should provide in the respective fields in the sample app. Additionally, you will use the userToken in Step 2.

Node.js
cURL

// Import and configure the user-controlled wallet SDK
const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets');
const circleUserSdk = initiateUserControlledWalletsClient({
  apiKey: '<API_KEY>'
});

const response = await circleUserSdk.createUserToken({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
Response body

{
  "data": {
    "userToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCC9.eyJkZXZlbG9wZXJFbnRpdHlFbnZpcm9ubWVudCI6IlRFU1QiLCJlbnRpdHlJZCI6IjRlMDdhOGM5LTIxOTAtNDVlNC1hNjc0LWQyMGFkNjg4MWI3YyIsImV4cCI6MTY5MDU1MjcwNywiaWF0IjoxNjkwNTQ5MTA3LCJpbnRlcm5hbFVzZXJJZCI6ImQ2ZjkzODliLWQ5MzUtNWFlYy1iOTVhLWNjNTk1NjA2YWM5NiIsImlzcyI6Imh0dHBzOi8vcHJvZ3JhbW1hYmxlLXdhbGxldC5jaXJjbGUuY29tIiwianRpIjoiMmE0YmJlMzAtZTdkZi00YmM2LThiODMtNTk0NGUyMzE2ODlkIiwic3ViIjoiZXh0X3VzZXJfaWRfOSJ9.dhfByhxZFbJx0XWlzxneadT4RQWdnxLu3FSN9ln65hCDOfavaTL1sc4h-jUR8i4zMmfdURw3FFcQIdSbm-BUg6M7FP_fp-cs9xBbNmRZa31gMd1aKdcajJ9SvlVrfUowYfGXM3VcNF8rtTFtW-gk1-KzU4u10U35XXbbMcW1moxE0Rqx_fKotDgk2VdITuuds5d5TiQzAXECqeCOCtNoDKktMkglltbnLxOaRl2ReZjGt-ctD2V0DbYNO4T_ndPSUDI6qD7dXQRed5uDcezJYoha3Qj3tFGBglEnox2Y6DWTbllqjwmfTGrU8Pr0yz4jQz7suGwmiCzHPxcpYxMzYQ",
    "encryptionKey": "Tlcyxz7Ts9ztRLQq5+pic0MIETblYimOo2d7idV/UFM="
  }
}
##
3. Initialize Account Recovery and Acquire a Challenge ID
[](#3-initialize-account-recovery-and-acquire-a-challenge-id)
Make a request to [POST /user/pin/restore](/w3s/reference/createuserpinrestorechallenge) using the userToken returned from Step 1. This call returns a challengeId, which is used with the Circle Programmable Wallet SDK to have the user reset their PIN code.

Node.js
cURL

const response = await circleUserSdk.restoreUserPin({
  userToken: '<USER_TOKEN>'
});
JSON

{
  "data": {
    "challengeId": "c4d1da72-111e-4d52-bdbf-2e74a2d803d5"
  }
}
##
4. Recover Account in the Sample App
[](#4-recover-account-in-the-sample-app)
Using the sample application, enter the userToken and secretKey returned from Step 1. Enter the challengeId returned from Step 2. 

At this point, you should be ready to execute the account recovery workflow through the Circle Programmable Wallet SDK. Once you’ve entered the required fields indicated in Step 3, click Execute to continue. 


The sample application takes you through the account recovery process by answering your Security Questions. If answered correctly, the sample application prompts you to enter a new PIN code.  


##
5. Check the Challenge Status
[](#5-check-the-challenge-status)
Make a request to [GET /user/challenges/{id}](getuserchallenge) using the challengeId received from Step 2 to retrieve the status of the challenge. Additionally, Circle sends a notification to a [subscribed endpoint](/w3s/docs/web3-services-notifications-quickstart) once the account recovery process is complete. For a full list of possible statuses, see the [Asynchronous States and Statuses guide](/w3s/docs/asynchronous-states-and-statuses).

Node.js
cURL

const response = await circleUserSdk.getUserChallenge({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "challenge": {
      "id": "c4d1da72-111e-4d52-bdbf-2e74a2d803d5",
      "correlationIds": [
        "54399e5a-1bf6-4921-9559-10c1115678cd"
      ],
      "status": "COMPLETED",
      "type": "RESTORE_PIN"
    }
  }
}
Updated 2 months ago

The user has now successfully recovered their account and set a new PIN code! That's it for the User Controlled Wallet Quickstarts! May we suggest the following resources:

-
[Supported Blockchains and Currencies](/w3s/docs/supported-blockchains-and-currencies)
-
[Blockchain Confirmations](/w3s/docs/blockchain-confirmations)
-
[Wallet Security](/w3s/docs/wallet-security)
Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/compliance-requirements
Title: Compliance Requirements
Description: undefined
Keywords: undefined

This topic describes the compliance requirements you should be familiar with as you use the Circle platform.

##
Addressing OFAC Sanctions in Wallet Management
[](#addressing-ofac-sanctions-in-wallet-management)
You must adhere to the Circle terms of service regarding [OFAC sanctions](https://www.investopedia.com/terms/o/ofac.asp) when managing wallets. Developers must maintain compliance with these regulations and understand the potential consequences of violating these terms.

###
OFAC Sanctions Compliance in Terms of Service
[](#ofac-sanctions-compliance-in-terms-of-service)
- Developers must ensure that the wallets they create do not transact with OFAC-sanctioned addresses.
- Violations may result in reporting to OFAC or banning the developer’s account.
###
Actions Taken for Detected Non-Compliant Transactions
[](#actions-taken-for-detected-non-compliant-transactions)
Circle takes the following actions for wallets that send or receive transactions to or from, respectively, any OFAC-sanctioned address:

- The wallet will be temporarily restricted from making outbound transactions pending a review.
- The developer should contact Circle Customer Support to move these funds out to a quarantine wallet properly.
For more information, refer to the following:

- US Treasury's Office of Foreign Assets Control [SDN List](https://www.treasury.gov/ofac/downloads/sdnlist.txt)
- Circle [Legal & Privacy](https://www.circle.com/en/legal)
Updated 24 days ago


URL: https://developers.circle.com/w3s/docs/developer-controlled-create-your-first-wallet
Title: Create Your First Developer-Controlled Wallet
Description: undefined
Keywords: undefined

Circle Programmable Wallets provide a comprehensive developer solution to storing, sending, and spending Web3 digital currencies and NFTs. You or your users can manage asset infrastructure. Circle provides a one-stop-shop experience with all the tools and services to handle the complex parts, including security, transaction monitoring, account recovery flows, and more. 

This guide will assist you in creating a developer-controlled Smart Contract Account (SCA) wallet. You will learn how to register an Entity Secret Ciphertext, create a wallet set, and ultimately establish a wallet. Throughout this comprehensive guide, you will utilize both command line and API requests. These can be achieved by referring to Circle's API references or utilizing cURL requests. For instructions on navigating the API references, please consult this [guide](/w3s/docs/api-references).

If you prefer to create an externally owned account (EOA) wallet, take note of the callout where an API parameter must be altered. To learn more about the two account types, please see the [account types guide](/w3s/docs/programmable-wallets-account-types).

Note: The following guide uses the Polygon Amoy [Testnet](https://en.wikipedia.org/wiki/Testnet) for illustration purposes; no real-world funds will be transferred. In production, you can create and use programmable wallets that support crypto tokens with the following blockchains and standards:

- Ethereum (ETH), Polygon (MATIC), and Avalanche (AVAX), both Testnet and Mainnet
- ERC-20 tokens
- ERC-721 and ERC-1155 NFTs (non-fungible tokens)
##📘
This guide features a Developer-Controlled wallet. Programmable Wallets also support a user-controlled wallet [quickstart](/w3s/docs/user-controlled-create-your-first-wallet).

###
Prerequisites
[](#prerequisites)
- Create a [Developer Account and acquire an API key in the Console](/w3s/docs/circle-developer-account).
- Install the [Web3 Services SDKs](/w3s/docs/web3-services-sdks), which is currently only available for Node.js. (optional)
###
[](#section-)
View sequence diagram
###
[](#-1)
###
1. Register an Entity Secret Ciphertext
[](#1-register-an-entity-secret-ciphertext)
The Entity Secret is a randomly generated 32-byte key designed to enhance security in developer-controlled wallets. After being created, the hex-encoded Entity Secret key is encrypted as a ciphertext for even greater data safety. To create a wallet or perform a transaction, you must append the ciphertext to the API request parameters. The ciphertext must be re-encrypted (rotated) whenever an API requires it.

You should generate the Entity Secret, encrypt it, and then register the ciphertext in the developer dashboard.

####
a. Generate the Entity Secret
[](#a-generate-the-entity-secret)
First, generate the Entity Secret and store it somewhere safe where your server-side app can access it. For testing purposes, you can store it in your environment variables. This will be used in the following steps to create the Entity Secret Ciphertext.

When you follow the provided methods below, you will receive a 32-byte string value similar to 7ae43b03d7e48795cbf39ddad2f58dc8e186eb3d2dab3a5ec5bb3b33946639a4.

Bash
Node.js
JavaScript
Python
Go

openssl rand -hex 32
##🚧
Remember to keep the Entity Secret safe. Securely store it, as you'll need it to create an Entity Secret Ciphertext in the following steps.

####
b. Fetch your Entity's Public Key
[](#b-fetch-your-entitys-public-key)
To proceed with the Entity Secret Ciphertext creation, the next essential element is your entity's public key. This public key plays an important role in generating the Entity Secret Ciphertext in the upcoming step. To obtain the required public key, initiate a request to the [GET /config/entity/publicKey](/w3s/reference/getpublickey) API endpoint. Remember, this API endpoint can be accessed by providing your valid [API key](/w3s/docs/web3-services-api-keys-authentication) for authentication.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>' // Make sure to enter the entity secret from the step above.
});

const response = await circleDeveloperSdk.getPublicKey({});
Response Body

{
  "data": {
    "publicKey": "-----BEGIN RSA PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAsL4dzMMQX8pYbiyj0g5H\nFGwdygAA2xDm2VquY8Sk0xlOC0yKr+rqUrqnZCj09vLuXbg1BreO/BP4F4GEIHgR\nBNT+o5Q8k0OqxLXmcm5sz6CPlsFCom+MiOj6s7RD0SXg91WF8MrN88GyN53xkemA\nOYU1AlIt4dVIrFyGY8aQ57sbWHyIjim+do1kBX+svIA/FLHG/sycoGiPU1E+Kydf\nlEDga4iR2DSbW6Zte9cGDg9Ivw/seNd0TLzJz6oC9XgSK5Et6/ZpOmqJgvISQ6rT\nK15DJ8EzIOzZZuEVOefgy1S7rLdSH7DexuR4W7T+KpP/f8Px0bxd4N6MT5V5kBYa\ngYHHIvqlJvXe5EzwidIWk1rg1X+YJt2M48h3Pr9HeECcmrnEYOgp32m/9lJ8vKp9\nhNh0rEKww/ULd1HqCEm/I0QGuji13XcGxVo5+7KCb/C76CNdW3pdRMn6fwFh4WVu\nu99iRc9OZhlkphysWm44hs1ZPpMCAkKttWjhnLZwIatN27x2JUqoCEUOho19iT+F\nwlPFA7E0Ju9Rqm68AkCXxHsJsAuGT8m6FLQZLHv4JyO/QEVzD7vY08A2I5dz1mVt\ngVam1/05Axju6poRomx/DUxiR0QH1+0Kg15+2A0fRkBggTTn7kvGsgz0cqk9cTm0\nEITpIVGcSGrVNRrmSye2OW0CAwEAAQ==\n-----END RSA PUBLIC KEY-----\n"
  }
}
####
c. Generate the Entity Secret Ciphertext
[](#c-generate-the-entity-secret-ciphertext)
Once you have the public key, you'll use RSA encryption to secure your Entity Secret and generate the Ciphertext. Immediately after, you'll transform this encrypted data into the Base64 format. The output Ciphertext will be exactly 684 characters long.

Node.js
Python
Go

const forge = require('node-forge')

const entitySecret = forge.util.hexToBytes('YOUR_ENTITY_SECRET')
const publicKey = forge.pki.publicKeyFromPem('YOUR_PUBLIC_KEY')
const encryptedData = publicKey.encrypt(entitySecret, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
  mgf1: {
    md: forge.md.sha256.create(),
  },
})

console.log(forge.util.encode64(encryptedData))
NOTE: You can also refer to the provided [sample code](https://github.com/circlefin/w3s-entity-secret-sample-code) in Python and Go for encrypting and encoding the Entity Secret.

####
d. Register the Entity Secret Ciphertext
[](#d-register-the-entity-secret-ciphertext)
After generating, encrypting, and encoding the Entity Secret, register the Entity Secret Ciphertext within the developer console. You must register ciphertext in the console only once.

- Access the [Configurator Page](https://console.circle.com/wallets/dev/configurator) in the developer console.
- Enter the Entity Secret Ciphertext generated in the previous step.
- Click Register to complete the Entity Secret Ciphertext registration.

Once registered, you are provided with a file to facilitate recovery in cases where the Entity Secret is lost. This file is used in subsequent Entity Secret reset procedures.

##📘
Our platform does not store the Entity Secret, meaning only you can invoke private keys. However, it also means that you must keep the Entity Secret carefully yourself to ensure the security and accessibility of your Developer-Controlled wallets.

####
Re-Encrypt the Entity Secret to create a new ciphertext for each API request
[](#re-encrypt-the-entity-secret-to-create-a-new-ciphertext-for-each-api-request)
Circle's [APIs Requiring Entity Secret Ciphertext](/w3s/docs/entity-secret-management#apis-requiring-entity-secret-ciphertext) enforce a different Entity Secret Ciphertext for each API request. One-time-use Entity Secret Ciphertext tokens ensure that even if an attacker captures the ciphertext from previous communication, they cannot exploit it in subsequent interactions.

To create a different Entity Secret Ciphertext token, repeat [Step 1.c: Generate the Entity Secret Ciphertext](developer-controlled-create-your-first-wallet#c-generate-the-entity-secret-ciphertext). As long as the Entity Secret Ciphertext comes from the same registered entity secret, it is valid for an API request.

Using the same Ciphertext for multiple requests will lead to rejection.

##📘
Use the Web3 Services SDKs to generate ciphertext tokens

If you use the Web3 Services SDKs, after you complete the step to [Register the Entity Secret Ciphertext](#d-register-the-entity-secret-ciphertext) in the console, you need only to configure and register the entity secret with the SDK. The SDK then handles creating a new ciphertext token for each API request.

###
2. Create a Wallet Set
[](#2-create-a-wallet-set)
A wallet set refers to a unified set of wallets, all managed by a single cryptographic private key. This makes it possible to have wallets from different blockchains sharing the same address.

To create a wallet set, make a request to [POST /developer/walletSets](/w3s/reference/createwalletset) and create a wallet set providing a unique Entity Secret Ciphertext as described in [How to Re-Encrypt the Entity Secret](/w3s/docs/developer-controlled-create-your-first-wallet#how-to-re-encrypt-the-entity-secret).

Node.js
cURL

const response = await circleDeveloperSdk.createWalletSet({
  name: 'Entity WalletSet A'
});
Response Body

{
  "data": {
    "walletSet": {
      "id": "0189bc61-7fe4-70f3-8a1b-0d14426397cb",
      "custodyType": "DEVELOPER",
      "updateDate": "2023-08-03T17:10:51Z",
      "createDate": "2023-08-03T17:10:51Z"
    }
  }
}
###
3. Create a Wallet
[](#3-create-a-wallet)
In Web3, a wallet isn't just a storage mechanism for digital tokens or NFTs; it’s the core structure of all user interactions on the blockchain. A wallet is comprised of a unique address and accompanying metadata stored on the blockchain.

To create a wallet, make a request to [POST /developer/wallets](/w3s/reference/createdeveloperwallet) using the walletSet.id from step 2 and a count of 2 as request parameters. We'll use the second wallet in the following quickstart to transfer tokens from wallet to wallet. NOTE: Don't forget to generate a new Entity Secret Ciphertext.

##📘
Externally Owned Account (EOA)

If you prefer to create an EOA, change the account type parameter to "accountType": "EOA". If an account type is not provided an EOA wallet will be created.

Node.js
cURL

const response = await circleDeveloperSdk.createWallets({
  accountType: 'SCA',
  blockchains: ['MATIC-AMOY'],
  count: 2,
  walletSetId: '71f2a6b4-ffa7-417a-ad5b-fb928753edc8'
});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "ce714f5b-0d8e-4062-9454-61aa1154869b",
        "state": "LIVE",
        "walletSetId": "0189bc61-7fe4-70f3-8a1b-0d14426397cb",
        "custodyType": "DEVELOPER",
        "address": "0xf5c83e5fede8456929d0f90e8c541dcac3d63835",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-08-03T19:33:14Z",
        "createDate": "2023-08-03T19:33:14Z"
      },
      {
        "id": "703a83de-4851-47b8-ad08-94aa2271bfa6",
        "state": "LIVE",
        "walletSetId": "0189bc61-7fe4-70f3-8a1b-0d14426397cb",
        "custodyType": "DEVELOPER",
        "address": "0x7b777eb80e82f73f118378b15509cb48cd2c2ac3",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-08-03T19:33:14Z",
        "createDate": "2023-08-03T19:33:14Z"
      }
    ]
  }
}
##
Next Steps
[](#next-steps)
You have successfully created two deveopler-controlled wallets! Jump into the next guide, where you will learn how to acquire Testnet tokens and transfer them from wallet to wallet.

- [Transfer Tokens from Wallet to Wallet](/w3s/docs/developer-controlled-transfer-tokens-across-wallets): Try out your first transfer from two on-chain wallets!
- [Deploy a Smart Contract](/w3s/docs/scp-deploy-smart-contract): Use your newly created wallet to deploy your first Smart Contract on-chain!
- [Infrastructure Models](/w3s/docs/programmable-wallets-infrastructure-models): Learn more about the difference between User-Controlled and Developer-Controlled wallets!
Updated 24 days ago


URL: https://developers.circle.com/w3s/docs/web-sdk-ui-customizations
Title: Web SDK UI Customizations
Description: undefined
Keywords: undefined

This document describes the different customizable sections of the Programmable Wallet SDK customization API. To get the most flexibility from the SDK, use this reference with the [SDK API](/w3s/docs/web).

###
Colors
[](#colors)
####
setThemeColor(themeColor: ThemeColor)
[](#setthemecolorthemecolor-themecolor)
#	ThemeColor property	Note
A1	backdrop	Modal backdrop color, e.g. '#000000'.
A2	backdropOpacity	Modal backdrop opacity, e.g. 0.5.
A3	divider	Divider border color, e.g. '#808080' or 'grey'.
A4	bg	Modal background color, e.g. '#FFFFFF' or 'white'.
A5	success	Success text color, e.g. '#008000' or 'green'.
A6	error	Error text color, e.g. '#FF0000' or 'red'.
A7	textMain	Primary color, e.g. '#000000' or 'black'.
A8	textMain2	Secondary color, e.g. '#000000' or 'black'.
A9	textAuxiliary	Primary auxiliary color, e.g. '#000000' or 'black'.
A10	textAuxiliary2	Secondary auxiliary color, e.g. '#000000' or 'black'.
A11	textSummary	Summary text color, e.g. '#000000' or 'black'.
A12	textSummaryHighlight	Summary highlight text color, e.g. '#000000' or 'black'.
A13	textPlaceholder	Text input placeholder color, e.g. '#808080' or 'grey'.
A14	pinDotBase	Fill color for pincode input dot, e.g. '#FFFFFF' or 'white'.
A15	pinDotBaseBorder	Stroke color for pincode input dot, e.g. '#000000' or 'black'.
A16	pinDotActivated	Fill color for inputted pincode input dot, e.g. '#0000ff' or 'blue'.
A17	inputBorderFocused	Outline color for text input on focused, e.g. '#0000ff' or 'blue'.
A18	inputBorderFocusedError	Outline color for text input when error, e.g. '#FF0000' or 'red'.
A19	inputBg	Background color for text input, e.g. '#FFFFFF' or 'white'.
A20	inputBgDisabled	Background color for disabled text input, e.g. '#FFFFFF' or 'white'.
A21	dropdownBg	Background color for dropdown, e.g. '#FFFFFF' or 'white'.
A22	dropdownBorderIsOpen	Outline color for dropdown is open, e.g. '#0000ff' or 'blue'.
A23	dropdownBorderError	Outline color for dropdown when error, e.g. '#FF0000' or 'red'.
A24	mainBtnText	Text color for primary button, e.g. '#FFFFFF' or 'white'.
A25	mainBtnTextDisabled	Text color for disabled primary button, e.g. '#FFFFFF' or 'white'.
A26	mainBtnTextOnHover	Text color for primary button on hover, e.g. '#FFFFFF' or 'white'.
A27	mainBtnBg	Background color for primary button, e.g. '#000000' or 'black'.
A28	mainBtnBgOnHover	Background color for primary button on hover, e.g. '#000000' or 'black'.
A29	mainBtnBgDisabled	Background color for disabled primary button, e.g. '#000000' or 'black'.
A30	secondBtnText	Text color for secondary button, e.g. '#000000' or 'black'.
A31	secondBtnTextDisabled	Text color for disabled secondary button, e.g. '#000000' or 'black'.
A32	secondBtnTextOnHover	Text color for secondary button on hover, e.g. '#000000' or 'black'.
A33	secondBtnBorder	Outline color for secondary button, e.g. '#000000' or 'black'.
A34	secondBtnBorderOnHover	Outline color for secondary button on hover, e.g. '#000000' or 'black'.
A35	secondBtnBgOnHover	Background color for secondary button on hover, e.g. '#FFFFFF' or 'white'.
A36	secondBtnBorderDisabled	Outline color for disabled secondary button, e.g. '#FFFFFF' or 'white'.
A37	plainBtnText	Text color for plain text button, e.g. '#000000' or 'black'.
A38	plainBtnTextDisabled	Text color for disabled plain text button, e.g. '#000000' or 'black'.
A39	plainBtnTextOnHover	Text color for plain text button on hover, e.g. '#000000' or 'black'.
A40	plainBtnBg	Background color for plain text button, e.g. '#FFFFFF' or 'white'.
A41	plainBtnBgOnHover	Background color for plain text button on hover, e.g. '#FFFFFF' or 'white'.
A42	recoverPinHintTitle	Text color for recover pincode hint title, e.g. '#FFFFFF' or 'white'.
A43	recoverPinHintTitleBg	Background color for recover pincode hint title, e.g. '#FFFFFF' or 'white'.
A44	recoverPinHint	ext color for recover pincode hint, e.g. '#FFFFFF' or 'white'.
A45	titleGradients	Background color for linear-gradient text, e.g. ['#B090F5', '#1AA3FF'].
####
Create your PIN
[](#create-your-pin)


####
Re-enter your PIN to Confirm
[](#re-enter-your-pin-to-confirm)


####
Set up your Recovery Method
[](#set-up-your-recovery-method)

####
Recovery Method: Security Questions Customization
[](#recovery-method-security-questions-customization)
Please reference the Security Question section in the [SDK](/w3s/docs/web#securityquestion) for how to customize the question by using setSecurityQuestions method.




####
Security Summary
[](#security-summary)

####
Security Confirmation
[](#security-confirmation)

####
Enter your PIN
[](#enter-your-pin)


####
Recover your PIN
[](#recover-your-pin)


###
Custom security questions
[](#custom-security-questions)
#	Function	Note
B1	setCustomSecurityQuestions(  questions?: SecurityQuestion[] | null,  requiredCount = 2,  securityConfirmItems?: string[])	Set the array of security questions
B2	setCustomSecurityQuestions(  questions?: SecurityQuestion[] | null,  requiredCount = 2,  securityConfirmItems?: string[])	Set the required count of security questions. The value is 2 by default.
B3	setCustomSecurityQuestions(  questions?: SecurityQuestion[] | null,  requiredCount = 2,  securityConfirmItems?: string[])	Set the array of customized disclaimers on the SecurityConfirm screen. The value is undefined by default.
B4	setOnForgotPin(onForgotPin: () => void)	Set the callback of onForgotPin
###
Resources
[](#resources)
####
setResource(resources: Resources)
[](#setresourceresources-resources)
#	Resources Property	Note
C1	naviClose	Close icon url for the navigation bar.
C2	naviBack	Back icon URL for the navigation bar.
C3	securityIntroMain	Security intros image URL for the security intros page.
C4	dropdownArrow	Arrow icon URL for the dropdown.
C5	selectCheckMark	Selected item icon URL for the dropdown.
C6	securityConfirmMain	Security confirm image URL for the security confirm page.
C7	errorInfo	Error icon for form validation.
C8	fontFamily	Font-family for customization.E.g. { name: 'Edu TAS Beginner', URL: 'https\://fonts.googleapis.com/css2?family=Edu+TAS+Beginner:wght\@400;500;600;700\&display=swap' }.
####
Create your PIN
[](#create-your-pin-1)





###
CustomLinks
[](#customlinks)
####
setCustomLinks(customLinks: CustomLinks)
[](#setcustomlinkscustomlinks-customlinks)
#	Custom Links Property	Note
D1	learnMoreUrl	External link for the learn more button link.
###
Localizations
[](#localizations)
####
setLocalizations(localizations: Localizations)
[](#setlocalizationslocalizations-localizations)
#	Localizations Property	Note
E1~E3	confirmPincode	Holds localization settings for ConfirmPincode screen.
F1~F4	enterPincode	Holds localization settings for EnterPincode screen.
G1~G3	newPincode	Holds localization settings for NewPincode screen.
H1~H5	recoverPincode	Holds localization settings for RecoverPincode screen.
I1~I5	securityConfirm	Holds localization settings for SecurityConfirm screen.
J1~J4	securityIntros	Holds localization settings for SecurityIntros screen.
K1~K8	securityQuestions	Holds localization settings for SecurityQuestions screen.
L1~L2	securitySummary	Holds localization settings for SecuritySummary screen.
####
confirmPincode
[](#confirmpincode)
#	Property Name	Note
E1	headline	Headline textKeep the placeholder like this:E.g. `Re-enter your {{pin}} to confirm`
E2	headline2	Headline text 2The text will replace the {{pin}} placeholder in the headline text
E3	subhead	Subhead text

####
enterPincode
[](#enterpincode)
#	Property Name	Note
F1	headline	Headline textKeep the placeholder like this:E.g. `Enter your {{pin}}` or `輸入你的 {{pin}}`
F2	headline2	Headline text 2The text will replace the {{pin}} placeholder in the headline text
F3	subhead	Subhead text
F4	forgotPin	Forgot pin text

####
newPincode
[](#newpincode)
#	Property Name	Note
G1	headline	Headline textKeep the placeholder like this:E.g. `Enter your {{pin}}` or `輸入你的 {{pin}}`
G2	headline2	Headline text 2The text will replace the {{pin}} placeholder in the headline text
G3	subhead	Subhead text

####
recoverPincode
[](#recoverpincode)
#	Property Name	Note
H1	headline	Headline text
H2	headline2	Headline text 2
H3	subhead	Subhead text
H4	answerInputHeader	Answer input header text
H5	answerInputPlaceholder	Answer input placeholder text

####
securityConfirm
[](#securityconfirm)
#	Property Name	Note
I1	title	Title text
I2	headline	Headline text
I3	inputHeadline	Input headline text
I4	inputPlaceholder	Input placeholder text
I5	inputMatch	Input match text


####
securityIntros
[](#securityintros)
#	Property Name	Note
J1	headline	Headline text
J2	headline2	Headline text 2
J3	description	Description text
J4	link	Link text

####
securityQuestions
[](#securityquestions)
#	Property name	Note
K1	title	Title text
K2	questionHeader	Question header text
K3	questionPlaceholder	Question placeholder text
K4	requiredMark	Required mark text
K5	answerHeader	Answer header text
K6	answerPlaceholder	Answer placeholder text
K7	answerHintHeader	Answer hint header text
K8	answerHintPlaceholder	Answer hint placeholder text

####
securitySummary
[](#securitysummary)
#	Property Name	Note
L1	title	Title text
L2	question	The question text keeps the placeholder like this: E.g. {{ordinal}}. You are not required to place an ordinal value into the placeholder. The web SDK will do it for you.
Updated 5 months ago


URL: https://developers.circle.com/w3s/docs/blockchain-compatibility-for-circle-apis
Title: Blockchain Compatibility for Web3 Services APIs
Description: undefined
Keywords: undefined

This topic lists the support for Circle API endpoints on the various blockchains you can use with Circle Web3 Services. The tables in each section list the Web3 Services APIs and endpoints for a product, and indicate whether they are supported on the specified blockchains. For each blockchain included, support applies to both the Testnet and Mainnet networks for that chain. The Wallet Signing Service column indicates whether the service supports the APIs.

##
User-controlled wallets
[](#user-controlled-wallets)
API Category	Endpoint	Polygon	Ethereum	Avalanche	Solana	Wallet Signing Service
Users and PINs	POST /user/initialize	Yes	Yes	Yes	Coming soon	Yes
Wallets	POST /user/wallets	Yes	Yes	Yes	Coming soon	Yes
GET /wallets	Yes	Yes	Yes	Coming soon	Yes
GET /wallets/{id}	Yes	Yes	Yes	Coming soon	Yes
PUT /wallets/{id}	Yes	Yes	Yes	Coming soon	Yes
GET /wallets/{id}/balances	Yes	Yes	Yes	Coming soon	No
GET /wallets/{id}/nfts	Yes	Yes	Yes	Coming soon	No
Transactions	POST /user/transactions/transfer	Yes	Yes	Yes	Coming soon	No
POST /user/transactions/{id}/accelerate	Yes	Yes	Yes	Coming soon	No
POST /user/transactions/{id}/cancel	Yes	Yes	Yes	Coming soon	No
POST /user/transactions/contractExecution	Yes	Yes	Yes	Coming soon	No
GET /transactions	Yes	Yes	Yes	Coming soon	No
GET /transactions/{id}	Yes	Yes	Yes	Coming soon	No
POST /transactions/transfer/estimateFee	Yes	Yes	Yes	Coming soon	No
POST /transactions/contractExecution/estimateFee	Yes	Yes	Yes	Coming soon	No
POST /transactions/validateAddress	Yes	Yes	Yes	Coming soon	Yes
Token Lookup	GET /tokens/{id}	Yes	Yes	Yes	Coming soon	No
Signing	POST /user/sign/message	Yes	Yes	Yes	Coming soon	Yes
POST /user/sign/typedData	Yes	Yes	Yes	Coming soon	Yes
POST /user/sign/transaction	Coming soon	Coming soon	Coming soon	Coming soon	Yes
##
Developer-controlled wallets
[](#developer-controlled-wallets)
API Category	Endpoint	Polygon	Ethereum	Avalanche	Solana	Wallet Signing Service
WalletSets	POST /
developer/walletSets	Yes	Yes	Yes	Coming soon	Coming soon
PUT /developer/walletSets/{id}	Yes	Yes	Yes	Coming soon	Coming soon
GET /walletSets	Yes	Yes	Yes	Coming soon	Coming soon
GET /walletSets/{id}	Yes	Yes	Yes	Coming soon	Coming soon
Wallets	POST /developer/wallets	Yes	Yes	Yes	Coming soon	Coming soon
GET /wallets	Yes	Yes	Yes	Coming soon	Coming soon
GET /wallets/{id}	Yes	Yes	Yes	Coming soon	Coming soon
PUT /wallets/{id}	Yes	Yes	Yes	Coming soon	Coming soon
GET /wallets/{id}/balances	Yes	Yes	Yes	Coming soon	No
GET /wallets/{id}/nfts	Yes	Yes	Yes	Coming soon	No
Transactions	POST /developer/transactions/transfer	Yes	Yes	Yes	Coming soon	No
POST /developer/transactions/{id}/accelerate	Yes	Yes	Yes	Coming soon	No
POST /developer/transactions/{id}/cancel	Yes	Yes	Yes	Coming soon	No
POST /developer/transactions/contractExecution	Yes	Yes	Yes	Coming soon	No
GET /transactions	Yes	Yes	Yes	Coming soon	No
GET /transactions/{id}	Yes	Yes	Yes	Coming soon	No
POST /transactions/transfer/estimateFee	Yes	Yes	Yes	Coming soon	No
POST /transactions/contractExecution/estimateFee	Yes	Yes	Yes	Coming soon	No
POST /transactions/validateAddress	Yes	Yes	Yes	Coming soon	Yes
Token Lookup	GET /tokens/{id}	Yes	Yes	Yes	Coming soon	No
Signing	POST /developer/sign/message	Yes	Yes	Coming soon	Coming soon	No
POST /developer/sign/typedData	Yes	Yes	Coming soon	Coming soon	Coming soon
POST /developer/sign/transaction	Coming soon	Coming soon	Coming soon	Coming soon	Coming soon
##
Smart Contract Platform
[](#smart-contract-platform)
API Category	Endpoint	Polygon	Ethereum	Avalanche	Solana	Wallet Signing Service
Deploy / Import	POST /contracts/import	Yes	Yes	No	Coming soon	No
POST /contracts/deploy	Yes	Yes	No	Coming soon	No
POST /contracts/deploy/estimateFee	Yes	Yes	No	Coming soon	No
Interact	POST /contracts/{id}/read	Yes	Yes	No	Coming soon	No
View / Update	GET /contracts	Yes	Yes	No	Coming soon	No
GET /contracts/{id}	Yes	Yes	No	Coming soon	No
PATCH /contracts/{id}	Yes	Yes	No	Coming soon	No
Templates	POST /templates/{id}/deploy	Yes	Yes	No	Coming soon	No
POST /templates/{id}/deploy/estimateFee	Yes	Yes	No	Coming soon	No
Updated 1 day ago


URL: https://developers.circle.com/w3s/docs/scp-quickstarts
Title: Quickstarts
Description: undefined
Keywords: undefined

Whether you're a beginner or an experienced developer, our quickstarts provide step-by-step guidance to introduce you to the world of Web3 Services and empower you to leverage the potential of smart contracts. Select your preferred quickstart example and style to start your journey in unlocking the full potential of decentralized applications and smart contract development.

[

##Interactive Quickstart
Learn how to use the Smart Contract platform with the CCTP platform and its smart contracts to bridge USDC across chains.

GET STARTED

](https://learn.circle.com/quickstarts/cctp) [

##Quickstart Guides
Use our step-by-step guide in our docs to set up your local environment and deploy an ERC-721 contract on Testnet.

GET STARTED

](https://developers.circle.com/w3s/docs/scp-deploy-smart-contract)
Please note the interactive quickstart will redirect you to a different location. You can always return to this page to access our comprehensive developer documentation.

Updated 5 months ago


URL: https://developers.circle.com/w3s/docs/gas-fees
Title: Gas Fees
Description: undefined
Keywords: undefined

Gas fees are the costs associated with initiating a transaction on a given blockchain, allowing network validators to perform the requested function. 

Gas fees can fluctuate due to network congestion, the type of transaction you request, and the priority placed on your request. You can accelerate a transaction by paying additional gas fees.  

Each blockchain has its own native asset required to pay these fees. When interacting with the blockchain, you’ll need to account for the amount of this native asset required for the requested transaction.

You can estimate transaction fees and contract execution with Circle’s Programmable Wallets APIs. 

Video Tutorial: Estimating Gas Fees for Transactions
###
Programmable Wallets Gas Fee Configurations
[](#programmable-wallets-gas-fee-configurations)
When initiating a request via Circle’s Programmable Wallets APIs that require gas fees, you have two options for configuring your fee tolerance: fee levels and gas limits. 

Fee levels provide a simple way to configure your gas tolerance. Depending on your choice, the speed in which your transaction completes compared to the expected [blockchain confirmation](https://developers.circle.com/developer/docs/blockchain-confirmations) timing may vary. 

You can choose between three tolerance levels:

- low - a low-priority transaction, expected to take longer than the average amount of time to complete, with lower fees
- medium - a medium-priority transaction, expected to take the average amount of time to complete, with average fees
- high - a high-priority transaction, expected to take less than the average amount of time to complete, with higher fees
Gas limits allow for a more advanced configuration of your gas tolerance. Setting gas limit requires the following parameters, which are all designated in wei: 

- gasLimit - the base amount of gas required to process the requested transaction
- priorityFee - an additional “tip” that can be added to the validator to accelerate the requested transaction
- maxFee - maximum fee that can be used for the requested transaction, if the fee is greater, the transaction will be failed.
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/gas-station-policy-management
Title: Policy Management
Description: undefined
Keywords: undefined

A policy is your configuration on a blockchain network for using Circle’s managed Paymaster i.e. Gas Station. You must set up a Paymaster policy to use the Gas Station on Mainnet. Here, you will configure what you are willing to sponsor on behalf of your users. Circle’s Paymaster will pay the fees if a transaction meets your configuration.

##
Policy Setup for Gas Station on Mainnets
[](#policy-setup-for-gas-station-on-mainnets)
To activate a Mainnet Policy, follow these steps:

- Access the Paymaster policy page: Using your credentials, log in to the developer console. Toggle to Mainnet [top left corner]. From there, navigate to the Gas Station Paymaster section, which allows you to manage your gas fee sponsorship policies efficiently.
- Choose to create or select a policy: If you do not have a Mainnet Policy yet, select the option to create a new policy. Alternatively, if you already have an existing policy, choose the desired policy from the available list.
- Configure policy settings: Set the desired configurations for your policy based on your specific needs. Refer to the list of configurations provided below for clarity on available options.
- Update or create the policy: At the bottom of the page, after applying the necessary configurations, choose either the "Update" or "Create" button. "Update" should be selected if you modify an existing policy, whereas "Create" is appropriate for a new policy.
- Activate the policy: After confirming your chosen policy configurations, proceed to the top right corner of the page and select the "Activate" button. This final step confirms your intention to enable the selected policy and initiate gas fee sponsorship according to the specified settings.
###
Policy Configurations
[](#policy-configurations)
A policy consists of the following configurations:

-
Policy Name: Provide a descriptive name for the policy to identify it in the policy list. The policy name displays only in the console.

-
Network: Refers to the specific blockchain network where the Paymaster will cover gas fees. Each policy supports only one blockchain network, ensuring clarity and ease of management.

-
Default Policy: The policy sponsors gas fees for any transaction on a specific network, including wallet creation and contract execution. You can set only one default policy per network. We recommend designating this as the first policy for the specified network.

Note: Transactions use only default policies. If you want to use a non-default policy, update it to default and activate it.

-
Maximum spend per day* (optional): Specify the maximum allowance for gas fees in USD that you are willing to sponsor within a 24-hour period. This restriction provides control over the allocated budget for gas fee sponsorship.

-
Maximum spend per transaction* (optional): The maximum allowance for gas fees in USD that you are willing to sponsor for a single transaction.

Note: Maximum spend per transaction limit does not apply to the first transaction made from any Smart Contract Account (SCA) wallet. This includes SCA-type wallets as well as Console Wallets.

- Maximum operations per day* (optional): The maximum number of supported operations (such as transactions) per day.
- Blocked addresses (optional): A configurable list of blockchain addresses that will not receive sponsored gas fees. This feature allows you to exclude specific addresses from benefiting from gas fee coverage.
*Daily limits reset once every 24 hours at 0:00 UTC.

By leveraging these configurations within a policy, you can fine-tune the gas fee sponsorship parameters to your specific requirements and financial considerations. Note that any limits left blank will not be applied.


##
Deactivating a Gas Station Policy on Mainnet
[](#deactivating-a-gas-station-policy-on-mainnet)
If you need to deactivate the Mainnet policy at any given time, it can be done conveniently from the developer console. To deactivate the Mainnet policy, follow these simple steps:

- Select the Mainnet policy: Access the console and locate the intended Mainnet policy that you wish to deactivate. This can be found in the Paymaster policy management section.
- Click on the deactivate option: Once you have identified the Mainnet policy, click on the "deactivate" button in the console interface's top right corner. This action triggers the deactivation process.
- Confirm the deactivation: A pop-up modal will appear after clicking the deactivate option. Select the deactivate button within the modal to confirm your intention to deactivate the Mainnet policy.
##🚧
CAUTION: Deactivating a policy will stop all Mainnet transaction sponsorships and end-users wallet transactions will be rejected effectively unusable.

By following these steps, you can easily deactivate the Mainnet policy, granting you control and flexibility over the gas fee sponsorship within your application.


##
Policy Setup for Gas Station on Testnets
[](#policy-setup-for-gas-station-on-testnets)
When it comes to using Gas Station on Testnets, the setup process is hassle-free. Circle takes care of the initial configuration by automatically generating a default policy upon signing up for an account. This default policy is specially designed for developers leveraging Programmable Wallets during the testing phase. With this default policy in place, you can seamlessly sponsor Testnet tokens while building your applications.

It's important to note the gas fee sponsorship limits applicable for different Testnet networks:

- Polygon Amoy: The daily limit for sponsoring Testnet tokens is 0.5 MATIC.
- Ethereum Sepolia: The daily limit for sponsoring Testnet tokens is 0.02 ETH.
###
View Gas Sponsored Transactions
[](#view-gas-sponsored-transactions)
To monitor and analyze these gas sponsorship transactions, we provide a dashboard and APIs.

####
Using the Developer Dashboard
[](#using-the-developer-dashboard)
To view gas-sponsored transactions in Testnet or Mainnet:

- Access the Paymaster policy page: Using your credentials, log in to the developer console. From there, navigate to the Gas Station Paymaster section, which allows you to manage your gas fee sponsorship policies efficiently.
- Select a policy: Choose the specific policy you want to review from the available list on the Paymaster policy page.
View sponsored transactions table: On the policy page, you will find a table view listing the transactions that have been sponsored. You also have the option to apply filters to refine the view, enabling you to focus on the relevant data that meets your specific requirements.

Updated about 1 month ago

-
[Billing for Sponsored Gas Fees](/w3s/docs/gas-station-billing)

URL: https://developers.circle.com/w3s/docs/ios-sdk-ui-customization-api
Title: iOS SDK UI Customization API
Description: undefined
Keywords: undefined

This article describes two different ways to customize your iOS application using the [iOS SDK](/w3s/docs/ios):

- Static Resources customize the UI layout during build time.
- Programmatic Customization modifies the layout at run time.
##🔖
Note:

You can combine static and programmatic approaches for customization.

To use the SDK in the most flexible way, combine the reference in this article with the [iOS SDK](/w3s/docs/ios).

Sample Application: Refer to the [iOS sample application](https://github.com/circlefin/w3s-ios-sample-app-wallets) for example code.

##
Layouts
[](#layouts)
###
How to Use the Layout Images and Index Tables
[](#how-to-use-the-layout-images-and-index-tables)
This section includes screenshots of layouts for the SDK functionality. As you review the layouts, you can cross-reference the alphanumeric tags in the images with the values in the index tables that follow this section:

- [Index Table A](https://developers.circle.com/w3s/docs/ios-sdk-ui-customization-api#index-table-a)
- [Index Table B](https://developers.circle.com/w3s/docs/ios-sdk-ui-customization-api#index-table-b)
- [Index Table C](https://developers.circle.com/w3s/docs/ios-sdk-ui-customization-api#index-table-c)
###
New PIN Code
[](#new-pin-code)


###
Confirm PIN Code
[](#confirm-pin-code)


###
Enter PIN Code
[](#enter-pin-code)


###
Security Intros
[](#security-intros)

###
Security Questions
[](#security-questions)



###
Select Question
[](#select-question)

###
Security Summary
[](#security-summary)

###
Security Confirm
[](#security-confirm)


###
Recover PIN Code
[](#recover-pin-code)


###
Index Table A
[](#index-table-a)
Based on the layouts, table A provides the UI item named in the controller and the keys currently in use. You can get the UIViewController from WalletSdkDelegate and customize the UI items in the run time. Also, you can provide the values in static resources CirclePWLocalizable.strings and CirclePWTheme.json for customization layouts in build time.

 	Programmatic Customization			Static Resources		
 	 	 	 	CirclePWLocalizable.strings	CirclePWTheme.json	
#	Controller	Item	Note	 	font	color
A1	NewPINCodeViewController	titleLabel1	-	circlepw_new_pincode_headline	semibold	text_main
A2		titleLabel2	-	circlepw_new_pincode_headline_2	bold	title_gradients
A3		subtitleLabel	-	circlepw_new_pincode_subhead	regular	text_auxiliary
A4	BasePINInputViewController	-	dots and texts will be generated dynamically	-	light	pin_dot_base / pin_dot_base_border / pin_dot_activated / success / error
A5		-	shows ApiError.displayString	-	regular	error
A6		showPINButton	show pin	circlepw_show_pin	semibold	text_action / text_action_pressed
A7			hide pin	circlepw_hide_pin		
A8	ConfirmPINCodeViewController	titleLabel1	-	circlepw_confirm_pincode_headline	semibold	text_main
A9		titleLabel2	-	circlepw_confirm_pincode_headline_2	bold	title_gradients
A10		subtitleLabel	-	circlepw_confirm_pincode_subhead	regular	text_auxiliary
A11	EnterPINCodeViewController	titleLabel1	-	circlepw_enter_pincode_headline	semibold	text_main
A12		titleLabel2	-	circlepw_enter_pincode_headline_2	bold	title_gradients
A13		subtitleLabel	-	circlepw_enter_pincode_subhead	regular	text_auxiliary
A14		forgotPINButton	-	circlepw_enter_pincode_forgot_pin	semibold	text_action / text_action_pressed
A15	SecurityIntrosViewController	titleLabel1	-	circlepw_security_intros_headline	semibold	text_main
A16		titleLabel2	-	circlepw_security_intros_headline_2	bold	title_gradients
A17		introDescLabel	-	circlepw_security_intros_description	regular	text_auxiliary2
A18		introLinkButton	-	circlepw_security_intros_link	regular	text_action / text_action_pressed
A18-1		introLink	custom your url string	-	-	-
A19		continueButton	text	circlepw_continue	semibold	main_bt_text / main_bt_text_pressed / main_bt_text_disabled
background			-	-	main_bt_background / main_bt_background_pressed / main_bt_background_disabled
A20	SecurityQuestionsViewController	baseNaviTitleLabel	-	circlepw_security_questions_title	medium	text_main
A21		nextButton	text	circlepw_next	semibold	main_bt_text / main_bt_text_pressed / main_bt_text_disabled
background			-	-	main_bt_background / main_bt_background_pressed / main_bt_background_disabled
A22	SecurityQuestionTableViewCell	questionTitleLabel	-	circlepw_security_questions_question_header	regular	text_auxiliary
A23		questionMarkLabel	-	circlepw_security_questions_required_mark	regular	error
A24		-	placeholder	circlepw_security_questions_question_placeholder	regular	text_placeholder
selected question			-	regular	text_main
A25		answerTitleLabel	-	circlepw_security_questions_answer_header	regular	text_auxiliary
A26		-	placeholder	circlepw_security_questions_answer_placeholder	regular	text_placeholder
input text			-	regular	text_main
view			-	-	input_background_disabled / input_border / input_border_focused
A27		hintTitleLabel	-	circlepw_security_questions_answer_hint_header	regular	text_auxiliary
A28		-	placeholder	circlepw_security_questions_answer_hint_placeholder	regular	text_placeholder
input text			-	regular	text_main
view			-	-	input_background_disabled / input_border / input_border_focused
A29		hintWarningLabel	ApiError(.hintsMatchAnswers)	-	regular	error
A30	SelectQuestionViewController	baseNaviTitleLabel	-	circlepw_select_question_title	medium	text_main
A31	SelectQuestionTableViewCell	titleLabel	question list	-	regular	text_main
A32	SecuritySummaryViewController	baseNaviTitleLabel	-	circlepw_security_summary_title	medium	text_main
A33		continueButton	text	circlepw_continue	semibold	main_bt_text / main_bt_text_pressed / main_bt_text_disabled
background			-	-	main_bt_background / main_bt_background_pressed / main_bt_background_disabled
A34	SecuritySummaryTableViewCell	titleLabel	-	{ordinal} + circlepw_question	semibold	text_main2
A35		questionTitleLabel	-	circlepw_question + “:”	regular	text_auxiliary2
A36		-	-	-	regular	text_summary
A37		answerTitleLabel	-	circlepw_answer + “:“	regular	text_auxiliary2
A38		-	-	-	semibold	text_summary_highlight
A39		hintTitleLabel	-	circlepw_hint + “:“	regular	text_auxiliary2
A40		-	-	circlepw_empty_placeholder	regular	text_summary
A41	SecurityConfirmViewController	baseNaviTitleLabel	-	circlepw_security_confirm_title	medium	text_main
A42		imageBgView	-	-	-	security_confirm_main_bg
A43		tipsTitleLabel	-	circlepw_security_confirm_headline	medium	text_main
A44		agreeTitleLabel	-	circlepw_security_confirm_input_headline	semibold	text_main2
A45		agreeTextField	placeholder	circlepw_security_confirm_input_placeholder	regular	text_placeholder
input text			-	regular	text_main2
view			-	-	input_background_disabled / input_border / input_border_focused
A46		continueButton	text	circlepw_continue	semibold	main_bt_text / main_bt_text_pressed / main_bt_text_disabled
background			-	-	main_bt_background / main_bt_background_pressed / main_bt_background_disabled
A47	RecoverPINCodeViewController	titleLabel1	-	circlepw_recover_pincode_headline	semibold	text_main
A48		titleLabel2	-	circlepw_recover_pincode_headline_2	bold	title_gradients
A49		-	shows ApiError.displayString	-	regular	text_main
A50		errorMessageView	background	-	-	error_background
A51		confirmButton	text	circlepw_confirm	semibold	main_bt_text / main_bt_text_pressed / main_bt_text_disabled
background			-	-	main_bt_background / main_bt_background_pressed / main_bt_background_disabled
A52	RecoverPINCodeTableViewCell	-	-	-	regular	text_main2
A53		hintTitleLabel	text	circlepw_hint	regular	recover_pin_hint_title
background			-	-	recover_pin_hint_title_bg
A54		-	placeholder	circlepw_empty_placeholder	regular	recover_pin_hint
A55		answerTitleLabel	-	circlepw_recover_pincode_answer_input_header	regular	text_auxiliary
A56		answerMarkLabel	-	circlepw_security_questions_required_mark	regular	error
A57		-	placeholder	circlepw_recover_pincode_answer_input_placeholder	regular	text_placeholder
input text			-	regular	text_main
view			-	-	input_background_disabled / input_border / input_border_focused
###
Index Table B
[](#index-table-b)
By confirming the WalletSdkLayoutProvider, you can custom layout from Table-B dynamically. See the [Sample Code](https://circlepay.atlassian.net/wiki/spaces/~62e7eea5da8620d53392126b/pages/767852579/PW+SDK+UI+Customization+-+iOS#WalletSdkLayoutProvider).

#	WalletSdkLayoutProvider	Note
B1	func securityQuestions() -> [SecurityQuestion]	Set security question list
B2	func securityQuestionsRequiredCount() -> Int	Set security question required count (default is 2)
B3	func securityConfirmItems() -> [SecurityConfirmItem]	Set security confirm item list
B4	func displayDateFormat() -> String	Set the date format for display date strings. (Default is "yyyy-MM-dd")
-	func imageStore() -> ImageStore	Set local and remote images (more details in Table-C)
-	func themeFont() -> ThemeConfig.ThemeFont?	Set theme font programmatically- Provide the ThemeFont structure by code. - This method will override the font setups in CirclePWTheme.json file. -
###
Index Table C
[](#index-table-c)
 	Used in WalletSdkLayoutProvider	Used in WalletSdkDelegate	
#	ImageStore.Img (Enum)	Controller	Item
C1	naviClose	-	-
C2	naviBack	-	-
C3	securityIntroMain	SecurityIntrosViewController	introImageView
C4	dropdownArrow	SecurityQuestionTableViewCell	questionTrailingButton
C5	selectCheckMark	SelectQuestionTableViewCell	checkmarkImage
C6	securityConfirmMain	SecurityConfirmViewController	imageView
C7	errorInfo	RecoverPINCodeViewController	errorImageView
- Use the ImageStore.Img (Enum) column to customize with the WalletSdkLayoutProvider:
Swift

func imageStore() -> ImageStore {
    let local: [ImageStore.Img: UIImage] = [
        .naviBack: UIImage(named: "ic_navi_back")!,
        .naviClose: UIImage(named: "ic_navi_close")!,
        .selectCheckMark: UIImage(named: "ic_checkmark")!,
        .dropdownArrow: UIImage(named: "ic_trailing_down")!,
        .errorInfo: UIImage(named: "ic_warning_alt")!,
        .securityIntroMain: UIImage(named: "img_security_intro")!,
        .securityConfirmMain: UIImage(named: "img_driver_blog")!
    ]

    let remote: [ImageStore.Img: URL] = [
        .securityIntroMain: URL(string: "https://www.circle.com/hs-fs/hubfs/Sundaes/810/global-payments-810x810.png")!,
        .securityConfirmMain: URL(string: "https://www.circle.com/hs-fs/hubfs/Sundaes/810/Trust-810x810.png")!,
    ]

    return ImageStore(local: local, remote: remote)
}
- If you set both local and remote images, the remote image will present after the image loads, and the local image will be set as the placeholder image.
- The remote image formats supported include those from the Apple system (JPEG, PNG, TIFF, BMP, etc.), GIF, and APNG animated images.
- The remote image formats unsupported for new image formats include HEIC, BPG, AVIF, and vector formats such as PDF and SVG.
- The Controller and Item column shows where the images are used.
Refer to the UI items If you have special customizations with WalletSdkDelegate (not recommended)
##
Static Resources
[](#static-resources)
This section describes how to customize the UI for iOS statically.

##🔖
Note:

Circle recommends customizing the UI using static files.

You can copy the icon images from the [Sample Project](https://github.com/circlefin/w3s-ios-sample-app-wallets).

###
Assets
[](#assets)
Because the CircleProgrammableWalletSDK does not contain any image resources for the SDK; you must provide the icon images from either local assets or remote URLs.

##🚧
Important:

Setting the WalletSdkLayoutProvider.imageStore in WalletSdkLayoutProvider is required for you to see icons in the layouts.

###
CirclePWLocalizable.strings
[](#circlepwlocalizablestrings)
####
Setup
[](#setup)
- Create a file CirclePWLocalizable.strings in your main bundle.
- Provide your strings. Partial override is supported.
##🔖
Note:

You can provide specific keys and values you want to override and others keys will still refer to the default values.


- Localization is supported. For more information, see [Localization | Apple Developer Documentation](https://developer.apple.com/documentation/Xcode/localization/).

###
Example CirclePWLocalizable.strings
[](#example-circlepwlocalizablestrings)
Swift

/*
 * Loco ios export: iOS Localizable.strings
 * Project: strings.xml conversion
 * Release: Working copy
 * Locale: en, English
 * Exported by: Circle
 * Exported at: Tue, 27 Jun 2023 23:35:22 +0800
 */

// [General]
"circlepw_show_pin" = "Show PIN";
"circlepw_hide_pin" = "Hide PIN";
"circlepw_continue" = "Continue";
"circlepw_next" = "Next";
"circlepw_confirm" = "Confirm";

"circlepw_question" = "Question";
"circlepw_answer" = "Answer";
"circlepw_hint" = "Hint";
"circlepw_empty_placeholder" = "-";

// [Page] EnterPINCode
"circlepw_enter_pincode_headline" = "Enter your";
"circlepw_enter_pincode_headline_2" = "Web3 PIN";
"circlepw_enter_pincode_subhead" = "Let us know if it’s really you.";
"circlepw_enter_pincode_forgot_pin" = "Forgot PIN?";

// [Page] NewPINCode
"circlepw_new_pincode_headline" = "Enter your new";
"circlepw_new_pincode_headline_2" = "Web3 PIN";
"circlepw_new_pincode_subhead" = "Your PIN can’t have repeating (e.g. 000000) or consecutive (e.g. 123456) numbers.";

// [Page] ConfirmPINCode
"circlepw_confirm_pincode_headline" = "Re-enter your PIN to confirm";
"circlepw_confirm_pincode_headline_2" = "";
"circlepw_confirm_pincode_subhead" = "";

// [Page] SecurityIntros
"circlepw_security_intros_headline" = "Set up your";
"circlepw_security_intros_headline_2" = "Recovery Method";
"circlepw_security_intros_description" = "This is the only way to recover wallet access if you forget your PIN. Pick 2 security questions and provide answers for access recovery.";
"circlepw_security_intros_link" = "Learn more";

// [Page] SecurityQuestions
"circlepw_security_questions_title" = "Recovery method";
"circlepw_security_questions_question_header" = "Choose your %@ question";
"circlepw_security_questions_question_placeholder" = "Select one question";
"circlepw_security_questions_required_mark" = "*";
"circlepw_security_questions_answer_header" = "Provide an answer";
"circlepw_security_questions_answer_placeholder" = "Type your answer here";
"circlepw_security_questions_answer_hint_header" = "Provide an answer hint (optional)";
"circlepw_security_questions_answer_hint_placeholder" = "Type your hint here";

// [Page] SelectQuestion
"circlepw_select_question_title" = "Select one question";

// [Page] SecuritySummary
"circlepw_security_summary_title" = "Summary";

// [Page] SecurityConfirm
"circlepw_security_confirm_title" = "Confirmation";
"circlepw_security_confirm_headline" = "Keep your questions safe";
"circlepw_security_confirm_input_headline" = "Type “I agree” to proceed:";
"circlepw_security_confirm_input_placeholder" = "Type “I agree” here";
"circlepw_security_confirm_input_match" = "I agree";

// [Page] RecoverPINCode
"circlepw_recover_pincode_headline" = "Recover your";
"circlepw_recover_pincode_headline_2" = "Web3 PIN";
"circlepw_recover_pincode_subhead" = "Please enter the answer of the security questions provided below.";
"circlepw_recover_pincode_answer_input_header" = "Enter your answer here";
"circlepw_recover_pincode_answer_input_placeholder" = "Type your answer here";

###
CirclePWTheme.json
[](#circlepwthemejson)
####
Setup
[](#setup-1)
- Copy the file CirclePWTheme.json into your main bundle. You can use Cmd + drag and drop.

- Make sure you have selected the Target Membership.

-
Provide values to set the font: To use a custom font, refer to this document: [Adding a Custom Font to Your App | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/text_display_and_fonts/adding_a_custom_font_to_your_app).

-
Set the color with a hex string:

####
Example: CirclePWTheme.json
[](#example-circlepwthemejson)
JSON

{
  "font": {
    "ultraLight": "",
    "thin": "",
    "light": "",
    "regular": "CustomFont-Regular",
    "medium": "CustomFont-Medium",
    "semibold": "",
    "bold": "",
    "heavy": "",
    "black": ""
  },
  "color": {
    "background": "#FFFFFF",
    "divider": "#F0EFEF",
    "success": "#00B14F",
    "error": "#F55538",
    "error_background": "#FDF2F2",

    "text_main": "#1A1A1A",
    "text_main2": "#1C1C1C",
    "text_auxiliary": "#3D3D3D",
    "text_auxiliary2": "#707070",
    "text_summary": "#005339",
    "text_summary_highlight": "#005339",
    "text_placeholder": "#A3A3A3",
    "text_action": "#136FD8",
    "text_action_pressed": "#B3136FD8",

    "pin_dot_base": "#FFFFFF",
    "pin_dot_base_border": "#707070",
    "pin_dot_activated": "#3D3D3D",

    "input_border": "#E8E8E8",
    "input_border_focused": "#00B14F",
    "input_background_disabled": "#F5F5F5",

    "main_bt_text": "#FFFFFF",
    "main_bt_text_pressed": "#FFFFFF",
    "main_bt_text_disabled": "#BFBFBF",
    "main_bt_background": "#00B14F",
    "main_bt_background_pressed": "#005339",
    "main_bt_background_disabled": "#F5F5F5",

    "recover_pin_hint_title": "#005339",
    "recover_pin_hint_title_bg": "#EEF9F9",
    "recover_pin_hint": "#005339",

    "security_confirm_main_bg": "#3AB5EE",

    "title_gradients": [
      "#00B14F",
      "#23B64B",
      "#35BA47",
      "#43BE43",
      "#4FC23F",
      "#59C53C",
      "#62C838",
      "#6ACA34",
      "#71CD31",
      "#77CF2D",
      "#7DD02A",
      "#82D228",
      "#85D325",
      "#88D424",
      "#8AD522",
      "#8CD521"
    ]
  }
}
##
Programmatic Customization
[](#programmatic-customization)
To programmatically customize the UI refers to the following documents:

- The corresponding rendered layout and the notes on [Index Table B](#index-table-b).
- The [Programmable Wallet iOS SDK](/w3s/docs/ios) .
###
Example Code
[](#example-code)
Swift

extension WalletSdkAdapter: WalletSdkLayoutProvider {

    func securityQuestions() -> [SecurityQuestion] {
        return [
            SecurityQuestion(title: "What is your childhood nickname?", inputType: .text),
            SecurityQuestion(title: "What is the middle name of your oldest child?", inputType: .text),
            SecurityQuestion(title: "What is your favorite team?", inputType: .text),
            SecurityQuestion(title: "When was your birthday?", inputType: .datePicker),
            SecurityQuestion(title: "When is your marriage anniversary?", inputType: .datePicker),
        ]
    }

    func securityQuestionsRequiredCount() -> Int {
        return 2
    }

    func securityConfirmItems() -> [SecurityConfirmItem] {
        return [
            SecurityConfirmItem(image: UIImage(named: "img_info"),
                                text: "This is the only way to recover my account access."),
            SecurityConfirmItem(image: UIImage(named: "img_claim_success"),
                                text: "Circle won’t store my answers so it’s my responsibility to remember them."),
            SecurityConfirmItem(image: UIImage(named: "img_claim_success"),
                                text: "I will lose access to my wallet and my digital assets if I forget my answers."),
        ]
    }
    
    func displayDateFormat() -> String {
        return "yyyy/MM/dd"
    }
    
    func imageStore() -> ImageStore {
        let local: [ImageStore.Img: UIImage] = [
            .naviBack: UIImage(named: "ic_navi_back")!,
            .naviClose: UIImage(named: "ic_navi_close")!,
            .selectCheckMark: UIImage(named: "ic_checkmark")!,
            .dropdownArrow: UIImage(named: "ic_trailing_down")!,
            .errorInfo: UIImage(named: "ic_warning_alt")!,
            .securityIntroMain: UIImage(named: "img_security_intro")!,
            .securityConfirmMain: UIImage(named: "img_driver_blog")!
        ]

        let remote: [ImageStore.Img: URL] = [
            .securityIntroMain: URL(string: "https://www.circle.com/hs-fs/hubfs/Sundaes/810/global-payments-810x810.png")!,
            .securityConfirmMain: URL(string: "https://www.circle.com/hs-fs/hubfs/Sundaes/810/Trust-810x810.png")!,
        ]

        return ImageStore(local: local, remote: remote)
    }
    
    func themeFont() -> ThemeConfig.ThemeFont? {
        return ThemeConfig.ThemeFont(
            ultraLight: nil,
            thin: nil,
            light: "CustomFont-Light",
            regular: "CustomFont-Regular",
            medium: "CustomFont-Medium",
            semibold: "CustomFont-SemiBold",
            bold: "CustomFont-Bold",
            heavy: nil,
            black: nil
        )
    }
}
###
WalletSdkDelegate
[](#walletsdkdelegate)
By modifying the WalletSdkDelegate, you can get the current view controller and control the UI parameters in the controller at run time.

Swift

public protocol WalletSdkDelegate: AnyObject {

    /// Tells the delegate that the SDK is about to be presented in the controller.
    /// You can customize the layout as you wish dynamically.
    ///
    /// - Parameter controller: The UIViewController to be presented
    func walletSdk(willPresentController controller: UIViewController)

    ...
}
####
Example Code
[](#example-code-1)
Swift

extension WalletSdkAdapter: WalletSdkDelegate {

    func walletSdk(willPresentController controller: UIViewController) {
        print("willPresentController: \(controller)")

        if let controller = controller as? NewPINCodeViewController {
            controller.titleLabel1.text = "Hello World"
            controller.titleLabel1.textColor = .blue
            controller.titleLabel1.font = .systemFont(ofSize: 28, weight: .black)

        } 
        
        if let controller = controller as? SecurityConfirmViewController {
            controller.imageBgView.backgroundColor = .blue
            controller.imageView.contentMode = .scaleAspectFill
        }
    }
    
    ...
}
####
View Public Interface
[](#view-public-interface)
Use Jump to Definition (Cmd + left click) to see the public interface of the view controller. Change the UI items as you need.

#####
Jump to Definition
[](#jump-to-definition)

#####
Public Interface
[](#public-interface)

##
ErrorMessenger
[](#errormessenger)
###
 ApiError Interface
[](#apierror-interface)
Swift

public struct ApiError: Error {

    public let errorCode: ErrorCode

    /// Error string from the SDK
    public let errorString: String

    /// Error string for UI display
    public var displayString: String
}
By modifying the ErrorMessenger, you can customize the ApiError messages. Your customization replaces the default ApiError.displayString for UI usage. You can also manage the localized error message.

Swift

public protocol ErrorMessenger {

    func getErrorString(_ code: ApiError.ErrorCode) -> String?
}
   
#####
Example Code
[](#example-code-2)
Swift

import CircleProgrammableWalletSDK

class MyErrorMessenger: ErrorMessenger {

    func getErrorString(_ code: ApiError.ErrorCode) -> String? {
        switch code {
        case .hintsMatchAnswers:
            return "Your custom error message."

        case .networkError:
            return "Your custom error message."

        default:
            return nil
        }
    }
}

// ============ setErrorMessenger ============

WalletSdk.shared.setErrorMessenger(MyErrorMessenger())
##
ErrorCode and Messages Table
[](#errorcode-and-messages-table)
The following table shows the ApiError.ErrorCode key names and their default values:

ApiError.ErrorCode	Default Value
unknown(-1)	Unknown error
success(0)	Success
apiParameterMissing(1)	API parameter missing
apiParameterInvalid(2)	API parameter invalid
forbidden(3)	Forbidden
unauthorized(4)	Unauthorized
retry(9)	Retry
customerSuspended(10)	Customer suspended
pending(11)	Pending
invalidSession(12)	Invalid session
invalidPartnerId(13)	Invalid partner ID
invalidMessage(14)	Invalid Message published to a SQS queue.
invalidPhone(15)	Invalid phone number %@
walletIdNotFound(156001)	
tokenIdNotFound(156002)	
transactionIdNotFound(156003)	
entityCredentialNotFound(156004)	
walletSetIdNotFound(156005)	
userAlreadyExisted(155101)	user already existed
userNotFound(155102)	user not found
userTokenNotFound(155103)	user token not found
userTokenExpired(155104)	user token expired
invalidUserToken(155105)	invalid user token
userWasInitialized(155106)	user was initialized
userHasSetPin(155107)	user has set pin
userHasSetSecurityQuestion(155108)	user has set security question
userWasDisabled(155109)	user was disabled
userDoesNotSetPinYet(155110)	user does not set pin yet
userDoesNotSetSecurityQuestionYet(155111)	user does not set security questions yet
incorrectUserPin(155112)	The PIN you entered is incorrect. You have %@ attempts left.
incorrectDeviceId(155113)	incorrect device id
incorrectAppId(155114)	app id not found
incorrectSecurityAnswers(155115)	The answers you entered are incorrect. You have %@ attempts left.
invalidChallengeId(155116)	invalid challenge id
invalidApproveContent(155117)	invalid approve content
invalidEncryptionKey(155118)	invalid encryption key
userPinLocked(155119)	You’ve used up all PIN attempts. Please wait for %@ mins to retry later.
securityAnswersLocked(155120)	The answers you entered are incorrect. Please wait for %@ mins to retry again.
walletIsFrozen(155501)	Wallet is Frozen
maxWalletLimitReached(155502)	Max wallet limit reached 
walletSetIdMutuallyExclusive(155503)	WalletSetId can not be used together with blockchain and address filter
metadataUnmatched(155504)	metadata array length does not match wallet count
userCanceled(155701)	User canceled
launchUiFailed(155702)	
pinCodeNotMatched(155703)	The PIN you entered is not the same as the first one.
insecurePinCode(155704)	Your PIN can’t have repeating or consecutive numbers.
hintsMatchAnswers(155705)	Your hint can’t be the same as the answer.
networkError(155706)	Network error
Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/blockchain-confirmations
Title: Blockchain Confirmations
Description: undefined
Keywords: undefined

This guide provides an overview of blockchain confirmations and their significance in ensuring the security of transactions. It discusses the concept of blockchain reorganizations and their potential associated risks. By waiting for confirmations, developers can protect against reorg attacks and ensure the validity of on-chain transactions.

##
Blockchain Transactions and Reorganizations
[](#blockchain-transactions-and-reorganizations)
Transactions in a blockchain are grouped into blocks, each containing a set of transactions. Different blockchains have distinct rules for selecting which transactions are included in a block. Occasionally, recently generated blocks may be invalidated, resulting in a rewrite of the transaction history known as a "reorganization" or "reorg" for short. Such reorganizations invalidate any transactions in the invalidated blocks, essentially treating them as if they "never happened".

Reorganizations pose a security risk as attackers could exploit them by initiating a cryptocurrency transaction as payment for a good or service and triggering a reorg. This action would invalidate the transaction while allowing the attacker to retain the goods or services received.

##
The Importance of Confirmations
[](#the-importance-of-confirmations)
To mitigate the risk of reorg attacks, it is recommended to wait for several confirmed blocks before considering a transaction as valid. With each additional block added to the blockchain, the difficulty of invalidating previous blocks increases. By choosing the number of blocks to wait for, developers can balance transaction processing speed with the security of avoiding reorg-related losses.

The number of blocks to wait before deeming a transaction valid is called the "confirmation number". It is important to note that confirmation numbers may differ across different blockchain networks. Web3 Services and its APIs utilize specific confirmation numbers for each supported chain, considering factors such as historical performance, potential reorganizations, and overall network architecture.

##
Confirmation Numbers for Supported Chains
[](#confirmation-numbers-for-supported-chains)
The following table showcases the confirmation numbers used by Web3 Services and its APIs for each supported chain. Please note that these numbers are based on Circle's considerations and may not reflect the inherent requirements or limits of the blockchain networks themselves.

Chain	Confirmations/Blocks	Approximate Time
Avalanche ([USDC](https://snowtrace.io/token/0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E), [EURC](https://snowtrace.io/token/0xc891eb4cbdeff6e073e859e987815ed1505c2acd))	1	~2 seconds
Ethereum ([USDC](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48), [EURC](https://etherscan.io/token/0x1aBaEA1f7C830bD89Acc67eC4af516284b1bC33c))	12	~3 minutes
Polygon PoS([USDC](https://polygonscan.com/token/0x3c499c542cef5e3811e1192ce70d8cc03d5c3359))	50	~2 minutes
###
Polygon PoS Confirmation Number
[](#polygon-pos-confirmation-number)
For transactions on Polygon PoS, Circle's platform requires 50 confirmations before considering them as complete. This process generally takes around 2 minutes. The chosen confirmation number represents three times the number of confirmations needed for sufficient transaction finality, based on the largest reorg observed thus far.

##
Confirmations and Transaction Status
[](#confirmations-and-transaction-status)
As soon as an incoming transaction is included in a block, Web3 Services API enables access to the transaction details by polling the "[get transaction endpoint](/w3s/reference/gettransaction)" or subscribing to notifications. The transaction will start with a confirmed status and update the wallet's balance. If the confirmed balance is utilized for a transaction, be aware that there is a possibility that the transaction might be reversed as a result of blockchain reorganization.

Once the required confirmations have been obtained, the transaction status will change to completed. Developers who have subscribed to notifications will receive a message indicating this status change.

It is essential to note that considering an incoming transaction as completed before the status change can be risky, as the funds may not be received if a reorg occurs before the required number of confirmations is reached.

By following these guidelines and understanding the importance of blockchain confirmations, you can enhance the security and reliability of your applications when dealing with blockchain transactions.

Updated about 2 months ago


URL: https://developers.circle.com/w3s/docs/programmable-wallets
Title: Programmable Wallets
Description: undefined
Keywords: undefined

Circle built Programmable Wallets on top of multi-party computation (MPC) technology to provide a comprehensive developer solution to store and interact with cryptocurrencies. That includes stablecoins, NFTs, and other ERC20, ERC721, and ERC1155 tokens.

As a developer, you can decide whether you or your users manage the wallet’s private keys. Circle provides all of the tools and services you need to handle the complexity of security, transaction monitoring, account recovery flows, and more.

If you want to use a blockchain where Programmable Wallets are not yet supported, Circle offers the Wallet Signing Service, which is built on the same MPC technology as Programmable Wallets. You can get in touch with us with your project and chain requirements to see if Wallet Signing Service can support your needs. To learn more, see [Wallet Signing Service](https://developers.circle.com/w3s/docs/wallet-signing-service).

##
What is a wallet?
[](#what-is-a-wallet)
Within the crypto world, a “wallet” refers to any solution that allows users to store, send, receive, and spend digital assets, whether the wallet exists in software (a program or service) or hardware (a device or physical medium). A wallet doesn’t store digital assets. It stores the private keys to access those coins, which exist on public blockchain networks.

##
Problems with wallets
[](#problems-with-wallets)
Currently, the broad range of wallet solutions over various blockchain networks make it harder for developers to deliver a seamless, consistent wallet experience. Other solutions have each have their own latency, error rate, and native tokens for gas.

Wallet solutions have faced uphill battles with complex and difficult onboarding issues for mass adoption and scalability of Web3 apps:

- Unfamiliar authentication methods for handling private keys.
- Cumbersome approaches to gas fee management for transactions.
- Opaque debugging for failed transactions.
- High complexity for integrating apps with smart contracts.
- Developers must maintain the security of a user’s private keys amidst multiple vendors and offerings.
- No single end-to-end solution available to help them build in Web3.
##
What is MPC technology?
[](#what-is-mpc-technology)
Multi-party computation (MPC) is the next-generation cryptographic solution to multi-signature. MPC manages private keys by distributing key shards across multiple parties, securing against accidental or intentional acts to misuse private keys.

##
Circle’s solution
[](#circles-solution)
Circle Programmable Wallets is a Wallet as a Service that simplifies creating and managing secure web3 wallets and their private keys. They extend wallet functionality with approachable user flows, provide optionality for developer and user infrastructure solutions and enable seamless smart contract integration.

Developers can interact with Programmable Wallets using RESTful APIs.Circle offers Web SDK and Mobile-ready SDKs for Android and iOS for user-controlled wallets, ensuring users have full control over their wallets.

Updated about 21 hours ago

-
[Infrastructure Models](/w3s/docs/programmable-wallets-infrastructure-models)

URL: https://developers.circle.com/w3s/reference/createmonitoredtokens
Title: Set monitored tokens
Description: undefined
Keywords: undefined

TIME	STATUS	USER AGENT	
Make a request to see history.
Request body

tokenIds
array of strings
required
The list of tokens to add to the monitored tokens list. When fetching wallet balances, only these tokens will be shown by default.


STRING



ADD STRING
#
200
Success and no update happened

#
201
Success and updated

#
Default
Error response

Updated 6 months ago

Did this page help you?

Yes

No

Shell

Node

Ruby

PHP

Python


1
curl --request POST \
2
     --url https://api.circle.com/v1/w3s/config/entity/monitoredTokens \
3
     --header 'accept: application/json' \
4
     --header 'content-type: application/json'
Click Try It! to start a request and see the response here! Or choose an example:
application/json

200

201

Default

URL: https://developers.circle.com/w3s/docs/gas-station-billing
Title: Billing for Sponsored Gas Fees
Description: undefined
Keywords: undefined

To ensure a smooth and transparent billing reimbursement process, please note the following details regarding bill payments for gas fee sponsorships made through Gas Station:

- Reimbursement and Invoicing: As the policyholder, you are responsible for reimbursing Circle for all the gas fee sponsorships made on your behalf. Invoices will be generated and sent to you, including applicable local taxes and a 5% processing fee.
- Period and Frequency: Bills are charged at the earliest occurrence of two events:
The 1st of each calendar month: On this date, the gas fee sponsorships are made on your behalf will be consolidated into a bill for that specific billing period.
- Accumulation of $100 worth of gas fees: If the cumulative value of your gas fee sponsorships reaches or exceeds $100 before the 1st of the month, a bill will be generated at that point.
- Payment Terms: Invoices must be settled within seven days of being issued or up to $20 in additional Gas Station charges. It is essential to adhere to these payment terms to ensure uninterrupted gas fee sponsorship and the continued functionality of Gas Station.
- Consequences of Unpaid Bills: Failure to pay a bill within seven days after it is invoiced or upon accumulating an additional $20 in Gas Station charges will result in the pausing of all gas station policies. When the policy is paused, Gas Station will cease to sponsor any Mainnet transactions. Therefore, it is crucial to keep a valid credit card on file and promptly settle any outstanding bills to avoid service disruptions.
##📘
If these limits do not meet your needs, please contact our sales team or Account Executive you are working with.

##🚧
Please be vigilant in managing your bill payments and maintaining a valid credit card account to avoid any interruptions in gas fee sponsorship and the smooth operation of your Gas Station policy.

Updated 6 months ago

-
[Paymaster](/w3s/docs/paymaster)

URL: https://developers.circle.com/w3s/reference/createuserwithpinchallenge
Title: Create a challenge for PIN setup and create wallet(s)
Description: undefined
Keywords: undefined

TIME	STATUS	USER AGENT	
Make a request to see history.
Request body

idempotencyKey
string
required
Universally unique identifier (UUID v4) idempotency key. This key is utilized to ensure exactly-once execution of mutating requests. To create a UUIDv4 go to [uuidgenerator.net](https://www.uuidgenerator.net). If the same key is reused, it will be treated as the same request and the original response will be returned.


accountType
string
An Account can be a Smart Contract Account (SCA) or an Externally Owned Account (EOA). To learn more about account types and the differences between the two, see the [account types guide](https://developers.circle.com/w3s/docs/programmable-wallets-account-types).

If an account type is not specified during the creation of a wallet, it will default to EOA (Externally Owned Account).


blockchains
array of strings
Blockchain(s) the requested wallets will be created on.


STRING



ADD STRING
metadata
array of objects
List of metadata fields to associate with the corresponding wallet.


ADD OBJECT
X-User-Token
string
required
Unique system generated JWT session token for specific user.


#
200
Success response

#
Default
Error response

Updated 6 months ago

Did this page help you?

Yes

No

Shell

Node

Ruby

PHP

Python


1
curl --request POST \
2
     --url https://api.circle.com/v1/w3s/user/initialize \
3
     --header 'accept: application/json' \
4
     --header 'content-type: application/json' \
5
     --data '
6
{
7
  "blockchains": [
8
    "ETH"
9
  ]
10
}
11
'
Click Try It! to start a request and see the response here! Or choose an example:
application/json

200

Default

URL: https://developers.circle.com/w3s/docs/web
Title: Web SDK
Description: undefined
Keywords: undefined

The Circle web SDK enables your web application to provide user-controlled wallets. By integrating this SDK, your users can securely input sensitive data like PINs or security answers through an iFrame element. The SDK encrypts the request body using an encryption key, protecting your users' information.

📘 The Web and Mobile SDKs preserve the user keyshare with the individual, giving them complete control. You must use the SDKs with the user-controlled wallet product. Additionally, the Web and Mobile SDKs support only the user-controlled wallet product.

At Circle, we understand the importance of end-to-end security for your application and the need to create a tailored and seamless user experience for your end-users. Hence, Circle’s SDK also exposes functionality for you to customize the description and layout: 

-
UI Title and Subtitle Customization: Modify the title and subtitle to reflect your brand identity or provide specific instructions.

-
Custom PIN Code Input Layout: Adjust the layout and styling of the PIN code input field to align with your application's design guidelines.

-
Question List Configuration: Set the list of security questions displayed in the User Wallet UI, allowing users to choose from a predefined set.

-
SDK Initialization: Initialize the Web SDK by setting the endpoint server, ensuring seamless communication between your application and Circle’s services.

-
Predefined Error Messages: Customize the error messages displayed to users, providing a more personalized experience and guidance.

-
ChallengeID Acceptance and Operation Retrieval: Easily accept the challengeID and retrieve any relevant operations within the SDK.

💡Tip: See the [Web SDK UI Customization API](/w3s/docs/web-sdk-ui-customizations) article to customize the SDK.

##
Install the SDKs
[](#install-the-sdks)
Install using [npm](https://docs.npmjs.com/about-npm):

Shell

npm install @circle-fin/w3s-pw-web-sdk
##
Sample code
[](#sample-code)
###
React.js
[](#reactjs)
JavaScript

import React, { useCallback, useEffect, useState } from 'react'
import { ToastContainer, toast } from 'react-toastify'
import TextField from '@mui/material/TextField'
import Button from '@mui/material/Button'
import { W3SSdk } from '@circle-fin/w3s-pw-web-sdk'

let sdk: W3SSdk

function App() {
  useEffect(() => {
    sdk = new W3SSdk()
  }, [])

  const [appId, setAppId] = useState(
    localStorage.getItem('appId') || 'someAppId'
  )
  const [userToken, setUserToken] = useState(
    localStorage.getItem('userToken') || 'someUserToken'
  )
  const [encryptionKey, setEncryptionKey] = useState(
    localStorage.getItem('encryptionKey') || 'someEncryptionKey'
  )
  const [challengeId, setChallengeId] = useState(
    localStorage.getItem('challengeId') || 'someChallengeId'
  )

  const onChangeHandler = useCallback(
    (setState, key) => (e) => {
      const value = e.target.value
      setState(value)
      localStorage.setItem(key, value)
    },
    []
  )

  const onSubmit = useCallback(() => {
    sdk.setAppSettings({ appId })
    sdk.setAuthentication({ userToken, encryptionKey })

    sdk.execute(challengeId, (error, result) => {
      if (error) {
        toast.error(`Error: ${error?.message ?? 'Error!'}`)
        return
      }
      toast.success(`Challenge: ${result?.type}, Status: ${result?.status}`)
    })
  }, [appId, userToken, encryptionKey, challengeId])

  return (
    <div className="p-4">
      <TextField
        label="App Id"
        onChange={onChangeHandler(setAppId, 'appId')}
        value={appId}
      />
      <TextField
        label="User Token"
        onChange={onChangeHandler(setUserToken, 'userToken')}
        value={userToken}
      />
      <TextField
        label="Encryption Key"
        onChange={onChangeHandler(setEncryptionKey, 'encryptionKey')}
        value={encryptionKey}
      />
      <TextField
        label="Challenge Id"
        onChange={onChangeHandler(setChallengeId, 'challengeId')}
        value={challengeId}
      />
      <Button variant="contained" color="primary" onClick={onSubmit}>
        Verify Challenge
      </Button>
      <ToastContainer />
    </div>
  )
}
###
Vanilla Javascript
[](#vanilla-javascript)
JavaScript

// Assume W3SSdk is globally available
let sdk = new W3SSdk()

// Initialize settings
const initSettings = () => {
  sdk.setAppSettings({
    appId: 'someAppId',
  })
  sdk.setAuthentication({
    userToken: 'someUserToken',
    encryptionKey: 'someEncryptionKey',
  })
}

// Handle form submission
const handleSubmit = () => {
  const challengeId = document.getElementById('challengeId').value

  sdk.execute(challengeId, (error, result) => {
    if (error) {
      console.error(`Error: ${error?.message ?? 'Error!'}`)
      return
    }
    console.log(`Challenge: ${result?.type}, Status: ${result?.status}`)
  })
}

// Initialization
initSettings()

// Listen for button click event
document.getElementById('submitBtn').addEventListener('click', handleSubmit)
##
SDK architecture
[](#sdk-architecture)
You must use Web, iOS, or Android SDKs to access the user-controlled Programmable Wallet product. The SDK secures, manages, and communicates with your server to ensure your user’s keyshare, always stays with them and is not exposed to your servers.

To learn more, see [SDK Architecture for User-Controlled Wallets](/w3s/docs/sdk-architecture-for-user-controlled-wallets).


##
SDK API references: enums
[](#sdk-api-references-enums)
The Web SDK supports the API reference enums listed in the following sections.

###
ChallengeType
[](#challengetype)
Enumerates the types of challenges supported.

JavaScript

enum ChallengeType {
  RESTORE_PIN,
  SET_SECURITY_QUESTIONS,
  CREATE_WALLET,
  CREATE_TRANSACTION,
  ACCELERATE_TRANSACTION,
  CANCEL_TRANSACTION,
  CONTRACT_EXECUTION,
  SIGN_MESSAGE,
  SIGN_TYPEDDATA,
  UNKNOWN,
}
###
ChallengeStatus
[](#challengestatus)
Enumerates the possible statuses for a challenge.

JavaScript

enum ChallengeStatus {
  COMPLETE,
  EXPIRED,
  FAILED,
  IN_PROGRESS,
  PENDING,
}
###
QuestionType
[](#questiontype)
Enumerates the types of security questions.

JavaScript

enum QuestionType {
  DATE,
  TEXT,
}
###
ErrorCode
[](#errorcode)
Enumerates the types of error code.

JavaScript

enum ErrorCode {
  unknown = -1,
  success = 0,
  apiParameterMissing = 1,
  apiParameterInvalid = 2,
  forbidden = 3,
  unauthorized = 4,
  retry = 9,
  customerSuspended = 10,
  pending = 11,
  invalidSession = 12,
  invalidPartnerId = 13,
  invalidMessage = 14,
  invalidPhone = 15,
  userAlreadyExisted = 155101,
  userNotFound = 155102,
  userTokenNotFound = 155103,
  userTokenExpired = 155104,
  invalidUserToken = 155105,
  userWasInitialized = 155106,
  userHasSetPin = 155107,
  userHasSetSecurityQuestion = 155108,
  userWasDisabled = 155109,
  userDoesNotSetPinYet = 155110,
  userDoesNotSetSecurityQuestionYet = 155111,
  incorrectUserPin = 155112,
  incorrectDeviceId = 155113,
  incorrectAppId = 155114,
  incorrectSecurityAnswers = 155115,
  invalidChallengeId = 155116,
  invalidApproveContent = 155117,
  invalidEncryptionKey = 155118,
  userPinLocked = 155119,
  securityAnswersLocked = 155120,
  notEnoughFunds = 155201,
  notEnoughBalance = 155202,
  exceedWithdrawLimit = 155203,
  minimumFundsRequired = 155204,
  invalidTransactionFee = 155205,
  rejectedOnAmlScreening = 155206,
  tagRequired = 155207,
  gasLimitTooLow = 155208,
  transactionDataNotEncodedProperly = 155209,
  fullNodeReturnedError = 155210,
  walletSetupRequired = 155211,
  lowerThenMinimumAccountBalance = 155212,
  rejectedByBlockchain = 155213,
  droppedAsPartOfReorg = 155214,
  operationNotSupport = 155215,
  amountBelowMinimum = 155216,
  wrongNftTokenIdNumber = 155217,
  invalidDestinationAddress = 155218,
  tokenWalletChainMismatch = 155219,
  wrongAmountsNumber = 155220,
  walletIsFrozen = 155501,
  maxWalletLimitReached = 155502,
  walletSetIdMutuallyExclusive = 155503,
  metadataUnmatched = 155504,
  userCanceled = 155701,
  launchUiFailed = 155702,
  pinCodeNotMatched = 155703,
  insecurePinCode = 155704,
  hintsMatchAnswers = 155705,
  networkError = 155706,
  biometricsSettingNotEnabled = 155708,
  deviceNotSupportBiometrics = 155709,
  biometricsKeyPermanentlyInvalidated = 155710,
  biometricsUserSkip = 155711,
  biometricsUserDisableForPin = 155712,
  biometricsUserLockout = 155713,
  biometricsUserLockoutPermanent = 155714,
  biometricsUserNotAllowPermission = 155715,
  biometricsInternalError = 155716,
  walletIdNotFound = 156001,
  tokenIdNotFound = 156002,
  transactionIdNotFound = 156003,
  entityCredentialNotFound = 156004,
  walletSetIdNotFound = 156005,
}
##
SDK API references: interfaces
[](#sdk-api-references-interfaces)
The Web SDK supports the interfaces listed in the following sections.

###
AppSettings
[](#appsettings)
Holds application settings.

- appId [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Application ID, retrieved from Circle Web3 Services Console
###
Authentication
[](#authentication)
Holds authentication information.

- userToken [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) User token
- encryptionKey [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Encryption key
###
Challenge
[](#challenge)
Holds information about a challenge.

- challengeId [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Challenge Id
###
SignMessageResult
[](#signmessageresult)
Holds the result of a sign message or sign typed-data challenge.

- signature [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Signature result after signing
###
ChallengeResult
[](#challengeresult)
Holds the result of a challenge.

- type [<ChallengeType>](/w3s/docs/web#challengetype) Challenge type
- status [<ChallengeStatus>](/w3s/docs/web#challengestatus) Challenge status
- data [<SignMessageResult | undefined>](/w3s/docs/web#signmessageresult) Sign message response
###
SecurityQuestion
[](#securityquestion)
Holds information about a custom security question.

- question [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Custom security question
- type [<QuestionType>](/w3s/docs/web#questiontype) Type of the question
###
Error
[](#error)
Holds error information.

- code [<integer | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#number_type) Internal error code
- message [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Error message
###
Localizations
[](#localizations)
Holds localization settings.

- common [<Common | undefined>](/w3s/docs/web#common) The settings for common texts.
- confirmInitPincode [<ConfirmPincode | undefined>](/w3s/docs/web#confirmpincode) The settings for ConfirmInitPincode screen.
- confirmNewPincode [<ConfirmPincode | undefined>](/w3s/docs/web#confirmpincode) The settings for ConfirmNewPincode screen.
- enterPincode [<EnterPincode | undefined>](/w3s/docs/web#enterpincode) The settings for EnterPincode screen.
- initPincode [<NewPincode | undefined>](/w3s/docs/web#newpincode) The settings for InitPincode screen.
- newPincode [<NewPincode | undefined>](/w3s/docs/web#newpincode) The settings for NewPincode screen.
- recoverPincode [<RecoverPincode | undefined>](/w3s/docs/web#recoverpincode) The settings for RecoverPincode screen.
- securityConfirm [<SecurityConfirm | undefined>](/w3s/docs/web#securityconfirm) The settings for SecurityConfirm screen.
- securityIntros [<SecurityIntros | undefined>](/w3s/docs/web#securityintros) The settings for SecurityIntros screen.
- securityQuestions [<SecurityQuestions | undefined>](/w3s/docs/web#securityquestions) The settings for SecurityQuestions screen.
- securitySummary [<SecuritySummary | undefined>](/w3s/docs/web#securitysummary) The settings for SecuritySummary screen.
###
Common
[](#common)
Holds localization settings for common texts.

- continue [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Continue text
- showPin [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Show pin text
- hidePin [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Hide pin text
###
ConfirmPincode
[](#confirmpincode)
Holds localization settings for ConfirmInitPincode or ConfirmNewPincode screen.

- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- headline2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text 2
- subhead [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Subhead text
###
EnterPincode
[](#enterpincode)
Holds localization settings for EnterPincode screen.

- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- headline2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text 2
- subhead [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Subhead text
- forgotPin [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Forgot pin text
###
NewPincode
[](#newpincode)
Holds localization settings for InitPincode or NewPincode screen.

- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- headline2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text 2
- subhead [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Subhead text
###
RecoverPincode
[](#recoverpincode)
Holds localization settings for RecoverPincode screen.

- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- headline2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text 2
- subhead [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Subhead text
- answerInputHeader [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer input header text
- answerInputPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer input placeholder text
###
SecurityConfirm
[](#securityconfirm)
Holds localization settings for SecurityConfirm screen.

- title [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Title text
- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- inputHeadline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Input headline text
- inputPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Input placeholder text
- inputMatch [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Input match text
###
SecurityIntros
[](#securityintros)
Holds localization settings for SecurityIntros screen.

- headline [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text
- headline2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Headline text 2
- description [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Description text
- link [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Link text
###
SecurityQuestions
[](#securityquestions)
Holds localization settings for SecurityQuestions screen.

- title [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Title text
- questionHeader [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Question header text
- questionPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Question placeholder text
- requiredMark [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Required mark text
- answerHeader [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer header text
- answerPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer placeholder text
- answerHintHeader [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer hint header text
- answerHintPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Answer hint placeholder text
###
SecuritySummary
[](#securitysummary)
Holds localization settings for SecuritySummary screen.

- title [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Title text
- question [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Question text
###
ThemeColor
[](#themecolor)
Holds customization color settings.

- backdrop [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Backdrop color, e.g. '#000000'.
- backdropOpacity [<number | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#snumber_type) Backdrop opacity, e.g. 0.5.
- divider [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Divider color, e.g. '#808080' or 'grey'.
- bg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color e.g. '#FFFFFF' or 'white'.
- success [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Success color, e.g. '#008000' or 'green'.
- error [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Error color, e.g. '#FF0000' or 'red'.
- textMain [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Main text color, e.g. '#000000' or 'black'.
- textMain2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Main text color 2, e.g. '#000000' or 'black'.
- textAuxiliary [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Auxiliary text color, e.g. '#000000' or 'black'.
- textAuxiliary2 [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Auxiliary text color 2, e.g. '#000000' or 'black'.
- textSummary[<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Summary text color, e.g. '#000000' or 'black'.
- textSummaryHighlight [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Summary highlight text color, e.g. '#000000' or 'black'.
- textPlaceholder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Placeholder text color, e.g. '#808080' or 'grey'.
- pinDotBase [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Fill color for pincode input dot, e.g. '#FFFFFF' or 'white'.
- pinDotBaseBorder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Stroke color for pincode input dot, e.g. '#000000' or 'black'.
- pinDotActivated [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Fill color for inputted pincode input dot, e.g. '#0000ff' or 'blue'.
- inputBorderFocused [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for text input on focused, e.g. '#0000ff' or 'blue'.
- inputBorderFocusedError [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for text input when error, e.g. '#FF0000' or 'red'.
- inputBg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for text input, e.g. '#FFFFFF' or 'white'.
- inputBgDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for disabled text input, e.g. '#FFFFFF' or 'white'.
- dropdownBg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for dropdown, e.g. '#FFFFFF' or 'white'.
- dropdownBorderIsOpen [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for dropdown is open, e.g. '#0000ff' or 'blue'.
- dropdownBorderError [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for dropdown when error, e.g. '#FF0000' or 'red'.
- mainBtnText [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for primary button, e.g. '#FFFFFF' or 'white'.
- mainBtnTextDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for disabled primary button, e.g. '#FFFFFF' or 'white'.
- mainBtnTextOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for primary button on hover, e.g. '#FFFFFF' or 'white'.
- mainBtnBg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for primary button, e.g. '#000000' or 'black'.
- mainBtnBgOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for primary button on hover, e.g. '#000000' or 'black'.
- mainBtnBgDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for disabled primary button, e.g. '#000000' or 'black'.
- secondBtnText [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for secondary button, e.g. '#000000' or 'black'.
- secondBtnTextDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for disabled secondary button, e.g. '#000000' or 'black'.
- secondBtnTextOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for secondary button on hover, e.g. '#000000' or 'black'.
- secondBtnBorder [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for secondary button, e.g. '#000000' or 'black'.
- secondBtnBorderOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for secondary button on hover, e.g. '#000000' or 'black'.
- secondBtnBgOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for secondary button on hover, e.g. '#FFFFFF' or 'white'.
- secondBtnBorderDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Outline color for disabled secondary button, e.g. '#FFFFFF' or 'white'.
- plainBtnText [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for plain text button, e.g. '#000000' or 'black'.
- plainBtnTextDisabled [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for disabled plain text button, e.g. '#000000' or 'black'.
- plainBtnTextOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for plain text button on hover, e.g. '#000000' or 'black'.
- plainBtnBg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for plain text button, e.g. '#FFFFFF' or 'white'.
- plainBtnBgOnHover [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for plain text button on hover, e.g. '#FFFFFF' or 'white'.
- recoverPinHintTitle [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for recover pincode hint title, e.g. '#FFFFFF' or 'white'.
- recoverPinHintTitleBg [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for recover pincode hint title, e.g. '#FFFFFF' or 'white'.
- recoverPinHint [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Text color for recover pincode hint, e.g. '#FFFFFF' or 'white'.
- titleGradients [<string[] | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Background color for linear-gradient text, e.g. ['#B090F5', '#1AA3FF'].
###
Resources
[](#resources)
Holds resource urls and properties, such as images or font-family settings. These resource properties should be hosted on a public server. SDK will load these resources from the urls provided.

- naviClose [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Close icon
- naviBack [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Back icon
- securityIntroMain [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Security intro main image
- dropdownArrow [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Dropdown arrow icon
- selectCheckMark [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Select check mark icon
- securityConfirmMain [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Security confirm main image
- errorInfo [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Error info icon
- fontFamily [<{ name?: string, url?: string } | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#object_type) Font family settings
###
CustomLinks
[](#customlinks)
Holds custom links.

- learnMoreUrl [<string | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Learn more link
##
Class Definition
[](#class-definition)
The WEB SDK supports the class definitions listed in the following sections.

###
W3SSdk
[](#w3ssdk)
####
Constructor
[](#constructor)
Initializes a new instance of W3SSdk.

####
Methods
[](#methods)
#####
execute
[](#execute)
Executes a challenge.

-
challengeId [<string>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) Challenge ID

-
onCompleted [<Function>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback function

- error [<Error>](/w3s/docs/web#error) Presents if error occurs
- result[<ChallengeResult>](/w3s/docs/web#challengeresult) Presents when success
#####
setAppSettings
[](#setappsettings)
Sets the application settings.

- appSettings [<AppSettings>](/w3s/docs/web#appsettings) Application settings object
#####
setAuthentication
[](#setauthentication)
Sets the authentication information.

- auth [<Authentication>](/w3s/docs/web#authentication) Authentication object
#####
setCustomSecurityQuestions
[](#setcustomsecurityquestions)
Sets custom security questions.

- questions [<SecurityQuestion[] | undefined>](/w3s/docs/web#securityquestion) Array of SecurityQuestion
- requiredCount [<integer | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#number_type) Required count of SecurityQuestion. The value is 2 by default.
- securityConfirmItems [<string[] | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#array_type) Array of customized disclaimers on SecurityConfirm screen. The value is undefined by default.
#####
setLocalizations
[](#setlocalizations)
Sets custom localizations

- localizations [<Localizations | undefined>](/w3s/docs/web#localizations) Settings of Localizations
#####
setResources
[](#setresources)
Sets custom resources

- resources [<Resources | undefined>](/w3s/docs/web#resources) Settings of Resources
#####
setThemeColor
[](#setthemecolor)
Sets custom theme colors

- themeColor [<ThemeColor | undefined>](/w3s/docs/web#themecolor) Settings of ThemeColor
#####
setCustomLinks
[](#setcustomlinks)
Sets custom links

- customLinks [<CustomLinks | undefined>](/w3s/docs/web#customlinks) Settings of CustomLinks
#####
setOnForgotPin
[](#setonforgotpin)
Sets callback function for ForgotPin button click event

- onForgotPin [<Function | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback function
- shouldCloseModalOnForgotPin [<boolean | undefined>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#boolean_type) Should close modal on forgot pin click event
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/idempotent-requests
Title: Idempotent Requests
Description: undefined
Keywords: undefined

Circle APIs support [idempotent requests](https://en.wikipedia.org/wiki/Idempotence), a type of request method in which multiple executions of the same request have no effect. You can safely retry API calls when things go wrong during a transaction, such as a network connection issue.

An idempotency key is a unique value generated by the client, which the server uses to recognize subsequent retries of the same request. To ensure a request is idempotent, you must generate and provide an idempotency key formatted as a [UUID version 4 format](https://en.wikipedia.org/wiki/Universally_unique_identifier). Subsequent requests with the same idempotencyKey parameter value will produce the same result as the initial request.

To quickly generate a UUID version 4 you can go to [uuidgenerator.net](https://www.uuidgenerator.net/).

Updated 6 months ago

Let's test the API! Continue the User-Controlled Wallets Quickstart:

-
[Using API References](/w3s/docs/api-references)

URL: https://developers.circle.com/w3s/docs/user-controlled-reset-account-pin
Title: Reset Account Pin Code
Description: undefined
Keywords: undefined

This guide outlines how to enable a user to reset their PIN code. Note users must know their original PIN code to reset it. Otherwise, they must follow the Account Recovery flow outlined in this [guide](/w3s/docs/user-controlled-recover-account).

##🚧
Caution: If a user loses both their PIN code and the answers to their Security Questions, they will be permanently locked out of their account, losing access to all of their wallets and assets.

##
1. Run the Sample App
[](#1-run-the-sample-app)
After you set up one of the web, iOS, or Android [sample applications](/w3s/docs/sample-applications), you can:

- Run the sample app and simulator.
- Obtain your App ID. To get the App ID, do one of the following:
- Access the developer console and navigate to the [configurator](https://console.circle.com/wallets/user/configurator) within user-controlled wallets. From there, copy the App ID.
- Make an API request to [GET /config/entity](/w3s/reference/getentityconfig) and copy the App ID from the response body.
- Add the App ID to the sample app.
##
2. Acquire a Session Token
[](#2-acquire-a-session-token)
Next, you need to acquire a session token. To do this, you make a request to the[POST /users/token](/w3s/reference/getusertoken) using the previously created userId in Step 1. The userToken is a 60-minute session token used to initiate requests requiring a user challenge (PIN code entry). After 60 minutes, the session expires, and a you must generate a new userToken using the same endpoint.

From this response, you can acquire the encryptionKey and userToken which you provide in the respective sample app fields. You also use the userToken in the next step.

Node.js
cURL

// Import and configure the user-controlled wallet SDK
const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets');
const circleUserSdk = initiateUserControlledWalletsClient({
  apiKey: '<API_KEY>'
});

const response = await circleUserSdk.createUserToken({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
Response body

{
  "data": {
    "userToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCC9.eyJkZXZlbG9wZXJFbnRpdHlFbnZpcm9ubWVudCI6IlRFU1QiLCJlbnRpdHlJZCI6IjRlMDdhOGM5LTIxOTAtNDVlNC1hNjc0LWQyMGFkNjg4MWI3YyIsImV4cCI6MTY5MDU1MjcwNywiaWF0IjoxNjkwNTQ5MTA3LCJpbnRlcm5hbFVzZXJJZCI6ImQ2ZjkzODliLWQ5MzUtNWFlYy1iOTVhLWNjNTk1NjA2YWM5NiIsImlzcyI6Imh0dHBzOi8vcHJvZ3JhbW1hYmxlLXdhbGxldC5jaXJjbGUuY29tIiwianRpIjoiMmE0YmJlMzAtZTdkZi00YmM2LThiODMtNTk0NGUyMzE2ODlkIiwic3ViIjoiZXh0X3VzZXJfaWRfOSJ9.dhfByhxZFbJx0XWlzxneadT4RQWdnxLu3FSN9ln65hCDOfavaTL1sc4h-jUR8i4zMmfdURw3FFcQIdSbm-BUg6M7FP_fp-cs9xBbNmRZa31gMd1aKdcajJ9SvlVrfUowYfGXM3VcNF8rtTFtW-gk1-KzU4u10U35XXbbMcW1moxE0Rqx_fKotDgk2VdITuuds5d5TiQzAXECqeCOCtNoDKktMkglltbnLxOaRl2ReZjGt-ctD2V0DbYNO4T_ndPSUDI6qD7dXQRed5uDcezJYoha3Qj3tFGBglEnox2Y6DWTbllqjwmfTGrU8Pr0yz4jQz7suGwmiCzHPxcpYxMzYQ",
    "encryptionKey": "Tlcyxz7Ts9ztRLQq5+pic0MIETblYimOo2d7idV/UFM="
  }
}
##
3. Initialize PIN reset and Acquire Challenge ID
[](#3-initialize-pin-reset-and-acquire-challenge-id)
Make a request to [PUT /user/pin](/w3s/reference/updateuserpinchallenge) using the userToken returned from Step 1. This call returns a challengeId, which is used with the Circle Programmable Wallet SDK to have the user reset their PIN code.

Node.js
cURL

const response = await circleUserSdk.updateUserPin({
  userToken: '<USER_TOKEN>'
});
JSON

{
  "data": {
    "challengeId": "c4d1da72-111e-4d52-bdbf-2e74a2d803d5"
  }
}
##
4. Reset the PIN in the Sample App
[](#4-reset-the-pin-in-the-sample-app)
Using the sample application, enter the userToken and secretKey returned from Step 1. Enter the challengeId returned from Step 2. 

You should be ready to execute the reset PIN code workflow via the Circle Programmable Wallet SDK. Once you’ve entered the required fields indicated in Step 3, click Execute to continue. 


The sample application takes you through the authentication and PIN code reset process, which includes the user entering their PIN code to authorize the reset and entering a new PIN code.




##
5. Check the Challenge Status
[](#5-check-the-challenge-status)
Make a request [GET /user/challenges/{id}](getuserchallenge) using the challengeId received from Step 2 to retrieve the status of the challenge. Additionally, Circle sends a notification to a [subscribed endpoint](/w3s/docs/web3-services-notifications-quickstart) once the PIN code reset is complete. For a full list of possible statuses, see the [Asynchronous States and Statuses guide](/w3s/docs/asynchronous-states-and-statuses).

Node.js
cURL

const response = await circleUserSdk.getUserChallenge({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "challenge": {
      "id": "c4d1da72-111e-4d52-bdbf-2e74a2d803d5",
      "correlationIds": [
        "54399e5a-1bf6-4921-9559-10c1115678cd"
      ],
      "status": "COMPLETED",
      "type": "CHANGE_PIN"
    }
  }
}
Updated 2 months ago

The user has successfully reset their PIN code! Move on to recover an account guide to complete:

-
[Recover an Account](/w3s/docs/user-controlled-recover-account)

URL: https://developers.circle.com/w3s/docs/wallet-signing-service
Title: Wallet Signing Service
Description: undefined
Keywords: undefined

Wallet Signing Service lets you build Web3 wallets using Circle's advanced MPC technology while leveraging the blockchain infrastructure that you’re using for node and data management. Circle offers SDKs and APIs to manage user authentication, wallet creation, and signing capabilities that seamlessly integrate with your node infrastructure.





##
Choosing between Programmable Wallet and Wallet Signing Service
[](#choosing-between-programmable-wallet-and-wallet-signing-service)
###
Feature comparison
[](#feature-comparison)
A brief overview highlighting the key feature differences.

Feature Support	Programmable Wallets	Wallet Signing Service
Wallet Features	- User authentication
- Wallet creation

Full transaction lifecycle management:
- Signing tx or messages
- Broadcast
- Monitor deposit
- Index tx and balances	- User authentication
- Wallet creation
- Signing tx or messages
Wallet Infrastructure	- User-controlled wallets
- Developer-controlled wallets	- User-controlled wallets
- [Coming soon] Developer-controlled wallets
Smart Contract Platform Support	SCP APIs support for most of the chains that PW APIs support (given that the specified chains support smart contracts)	Not supported
Gas Station Support	- Access to Gas Station to enable gas sponsorship for all smart contract account wallets
- Self-serve ability to set up Paymaster policies
- Available for chains supported by PW that supports smart contract accounts or native gas station for EOA	N/A.
Currently, Wallet Signing Service supports only EOA wallets. As Gas Station and paymaster policies require the use of SCA Wallets, Gas Station is not supported with Wallet Signing Service.

###
Solution support
[](#solution-support)
Below table helps you determine which product best fits your business needs.

I need a solution that..	Programmable Wallets	Wallet Signing Service
Fully manages blockchain operations, including node and data management, to accelerate time-to-market.		
Provide a dashboard where I can see my user’s transaction history and wallet balances to inform business decisions.		
Allows me to sponsor network fees on behalf of my users in order to build an optimal transaction experience.		
Build for a specific app chain to enjoy customizability, reduced gas fees, and faster transaction settlement.		
Support a blockchain not currently supported by Programmable Wallets.		

##
Get in touch
[](#get-in-touch)
Submit your project and chain requirements over [here](https://www.circle.com/en/wallet-signing-service) to see if we can support your needs.

Updated about 20 hours ago


URL: https://developers.circle.com/w3s/docs/circle-programmable-wallets-an-overview
Title: Circle Web3 Services
Description: undefined
Keywords: undefined

Circle's Web3 Services offer a range of SDKs and APIs that simplify the development process and decrease the required effort to build decentralized applications. By providing pre-built tools and abstractions, Circle's Web3 Services eliminate the need for extensive learning curves, enabling developers to quickly and efficiently create applications that leverage blockchain technology.

###
Programmable Wallets
[](#programmable-wallets)
Programmable Wallets enable developers to create and embed secure wallets in their apps. With these APIs, you can specify parameters to generate purpose-built wallets tailored to the use case you're building. To learn about the product and infrastructure models, go [here](/w3s/docs/programmable-wallets).

If you're building on the blockchain where Programmable Wallets have not yet supported, Wallet Signing Service lets you implement functionality similar to Programmable Wallets. To learn more, see [Wallet Signing Service](https://developers.circle.com/w3s/docs/wallet-signing-service).


[

##User-Controlled Wallets
Embed user-controlled wallets in your web & mobile apps.

GET STARTED

](https://developers.circle.com/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart) [

##Developer-Controlled Wallets
Embed developer-controlled wallets into web and mobile apps for your users.

GET STARTED

](https://developers.circle.com/w3s/docs/developer-controlled-wallet-quickstart)
###
Smart Contract Platform
[](#smart-contract-platform)
Smart Contract Platform (SCP) enables developers to integrate smart contract functionality into their apps. Using the SCP, developers can deploy a new smart contract on-chain or interact with any smart contract using Circle's developer console and APIs. The SCP can be accessed through the web3 services console UI. To learn more about the product, go [here](/w3s/docs/smart-contract-platform).

[

##Smart Contract Platform
Deploy, import, and interact with smart contracts.

GET STARTED

](https://developers.circle.com/w3s/docs/scp-quickstarts)
###
Gas Station
[](#gas-station)
Gas Station enables developers to sponsor gas fees on behalf of their end users, eliminating the need for users to hold native tokens. By removing the burden of gas fees, Gas Station ensures a seamless and familiar application experience for users. Setting up Gas Station is simple – create a policy within the developer console, and you're good to go. Circle's Paymaster will sponsor transactions that adhere to your policy and originate from a Smart Contract Account (SCA) Programmable wallet. To learn more about the benefits of Gas Station, see [here](/w3s/docs/gas-station).

[

##Gas Station
Sponsor gas fees for your users and pay gas fees using a card on file.

GET STARTED

](https://developers.circle.com/w3s/docs/gas-station-quickstarts)
###
Circle Access Network (Beta) Coming Soon
[](#circle-access-network-beta-coming-soon)
Circle Access Network offers a seamless solution for USDC integration in your application. With easy on/off ramps and frictionless USDC access for your users, you can simplify their experience while keeping them in your application. Say goodbye to the complexities of licenses and internal know-your-customer processes while providing low-cost access to USDC.

[

##Circle Access Network (Beta)
Be one of the first to give your users direct access to USDC through embedded on/off ramps.

LEARN MORE

](https://developers.circle.com/w3s/docs/circle-access-network-beta)
Updated about 21 hours ago


URL: https://developers.circle.com/w3s/docs/web3-services-sdks
Title: Web3 Services SDKs
Description: undefined
Keywords: undefined

Circle offers SDKs for client-side and server-side integration to the Web3 Services APIs. These SDKs reduce the amount of boilerplate code you write and abstract complexities to simplify the development process. You must utilize the Web, iOS, or Android SDKs to integrate user-controlled wallets into your app.


##
Server-side SDKs
[](#server-side-sdks)
Circle’s Web3 Services server-side SDKs reduce the effort required to use Circle’s REST APIs.

Use the following command to install the Node.js SDKs:

Shell

npm install @circle-fin/smart-contract-platform @circle-fin/user-controlled-wallets @circle-fin/developer-controlled-wallets --save
To learn more, see [Node.js SDKs](/w3s/docs/nodejs-sdk).

##
Web SDK
[](#web-sdk)
Circle provides the following client-side web SDK to enable integrations with Circle’s prebuilt UI components to create user wallets securely, send transactions and more. The Web SDK supports only user-controlled wallets. 

Use the following command to install the web SDK:

Shell

npm install @circle-fin/w3s-pw-web-sdk --save
To learn more, see [Web SDK](/w3s/docs/web).

##
Mobile SDKs
[](#mobile-sdks)
Circle provides the following client-side mobile device SDKs to help you create native applications for iOS and Android devices and platforms. Mobile SDKs support only user-controlled wallets.

[

##iOS SDK
Set up the client-side iOS SDK for user-controlled wallets in your mobile application.

GET STARTED

](https://developers.circle.com/w3s/docs/ios) [

##Android SDK
Set up the client-side Android SDK for user-controlled wallets in your mobile application.

GET STARTED

](https://developers.circle.com/w3s/docs/android)
##
Postman API Suite
[](#postman-api-suite)
Circle’s Postman workspace lets you quickly send requests and learn to use Web3 Services APIs.

[

##Postman API Suite
Set up the Postman suite to try out Web3 Services APIs.

GET STARTED

](https://developers.circle.com/w3s/docs/postman)
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/smart-contract-references
Title: Smart Contract Template References
Description: undefined
Keywords: undefined

[
##Token (ERC-20)
A template for an ERC-20 token: the most popular standard for fungible tokens.

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-20-token) [
##NFT (ERC-721)
A template for minting ERC-721 non-fungible tokens (NFTs).

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-721-nft) [
##Multi-Token (ERC-1155)
A template for the versatile ERC-1155 token standard. Multi-Token allows you to create and manage multiple token types within one contract.

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-1155-multi-token)
Updated 2 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/nodejs-sdk
Title: Node.js SDK
Description: undefined
Keywords: undefined

The Node.js SDKs provide simple, programmatic access to Circle Web3 Services APIs. You can use the SDKs to simplify application development for apps that interact with Circle Web3 Services, such as embedding secure wallets in your applications and interacting with smart contracts on the Web3 Services platform.

##
Prerequisites
[](#prerequisites)
To successfully use the Node.js SDK to interact with Circle Web3 Services APIs, you must:

-
Have an active Circle Web3 Services account. You can [create a new account](/w3s/docs/circle-developer-account) using the [Developer Console](https://console.circle.com/api-keys). 

-
After you create an account, [generate an API Key](/w3s/docs/web3-services-api-keys-authentication) to use in your requests to Circle APIs. 

-
Create an entity secret to use in requests with the Smart Contract Platform SDK and Developer-Controlled Programmable Wallets SDK. See the steps in [Generate an entity secret](/w3s/docs/entity-secret-management) to generate your entity secret.

For instructions about using your API key and entity secret in requests, see the section for the specific programming language you use.

##
Available SDKs
[](#available-sdks)
The Node.js SDKs include the following application-specific SDKs:

-
User-Controlled Wallets SDK

-
Developer-Controlled Wallets SDK

-
Smart Contract Platform SDK

See [Programmable Wallets](/w3s/docs/circle-programmable-wallets-an-overview) and [Smart Contract Platform](/w3s/docs/smart-contract-platform) to learn about these features and concepts. 

##
Install the SDKs
[](#install-the-sdks)
The three available SDKs are independent, separate installations, but you can use them together for application development. You can install all the SDKs using a single command or run an install command for each SDK separately.

Currently, the SDK supports only Node.js. We are working on adding support for more programming languages. To indicate your language preference, please complete the [Web3 Services server-side SDK programming languages survey](https://console.circle.com/home?ff=b8d3997a) accessible in the developer console.

Use the commands in the following sections to install the Node.js SDKs.

Install all available SDKs

Install using [npm](https://docs.npmjs.com/about-npm):

Shell

npm install @circle-fin/smart-contract-platform @circle-fin/user-controlled-wallets @circle-fin/developer-controlled-wallets --save
Install using [yarn](https://classic.yarnpkg.com/lang/en/docs/install/#mac-stable):

Shell

yarn add @circle-fin/smart-contract-platform @circle-fin/user-controlled-wallets @circle-fin/developer-controlled-wallets
Install the User-Controlled Programmable Wallets SDK

Use the following commands to install the SDK. View the [package information on the npm site](https://www.npmjs.com/package/@circle-fin/user-controlled-wallets).

Install using [npm](https://docs.npmjs.com/about-npm):

Shell

npm install @circle-fin/user-controlled-wallets --save
Install using [yarn](https://classic.yarnpkg.com/lang/en/docs/install/#mac-stable):

Shell

yarn add @circle-fin/user-controlled-wallets
Install the Developer-Controlled Programmable Wallets SDK

Use the following commands to install the SDK. View the [package information on the npm site](https://www.npmjs.com/package/@circle-fin/developer-controlled-wallets).

Install using [npm](https://docs.npmjs.com/about-npm):

Shell

npm install @circle-fin/developer-controlled-wallets --save
Install using [yarn](https://classic.yarnpkg.com/lang/en/docs/install/#mac-stable):

Shell

yarn add @circle-fin/developer-controlled-wallets
Install the Smart Contract Platform SDK

Use the following commands to install the SDKs. View the [package information on the npm site](https://www.npmjs.com/package/@circle-fin/smart-contract-platform).

Install using [npm](https://docs.npmjs.com/about-npm):

Shell

npm install @circle-fin/smart-contract-platform --save
Install using [yarn](https://classic.yarnpkg.com/lang/en/docs/install/#mac-stable):

Shell

yarn add @circle-fin/smart-contract-platform
##
Use the Node.js SDKs
[](#use-the-nodejs-sdks)
To start using the SDKs, you first need to configure a client.

###
User-Controlled Programmable Wallets Client
[](#user-controlled-programmable-wallets-client)
To interact with the User-Controlled Wallets, import the factory initiateUserControlledWalletsClient from the SDK, and then initialize the client using your API key.

The following code examples demonstrate how to import the client and configure it to use your API key:

JavaScript

const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets')
const client = initiateUserControlledWalletsClient({
  apiKey: '<your-api-key>'
})
Alternatively:

JavaScript

import { initiateUserControlledWalletsClient } from '@circle-fin/user-controlled-wallets'
const client = initiateUserControlledWalletsClient({
  apiKey: '<your-api-key>',
})
Interact with the client

The following code sample demonstrates how to create a transaction using the client.

JavaScript

const response = await client.createTransaction({
  userToken: 'dummy-user-token',
  amount: ['0.01'],
  destinationAddress: '0xa51c9c604b79a0fadbfed35dd576ca1bce71da0a',
  tokenId: '738c8a6d-8896-46d1-b2cb-083600c1c69b',
  walletId: 'a635d679-4207-4e37-b12e-766afb9b3892',
  fee: {
    type: 'level',
    config: {
      feeLevel: 'HIGH',
    },
  },
})
console.log(response.data?.challengeId)
###
Developer-Controlled Programmable Wallets Client
[](#developer-controlled-programmable-wallets-client)
To interact with the Developer-Controlled Wallets, import the factory initiateDeveloperControlledWalletsClient from the SDK, and then initialize the client using your API key and entity secret.

The following code examples demonstrate how to import the client and configure it to use your API key and entity secret:

JavaScript

const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets')
const client = initiateDeveloperControlledWalletsClient({
  apiKey: '<your-api-key>',
  entitySecret: '<your-entity-secret>',
})
Alternatively:

JavaScript

import { initiateDeveloperControlledWalletsClient } from '@circle-fin/developer-controlled-wallets'
const client = initiateDeveloperControlledWalletsClient({
  apiKey: '<your-api-key>',
  entitySecret: '<your-entity-secret>',
})
Interact with the client

The following code sample demonstrates how to create a transaction using the client.

JavaScript

const walletSetResponse = await client.createWalletSet({
  name: 'WalletSet 1',
})
console.log('Created WalletSet', walletSetResponse.data?.walletSet)
const walletsResponse = await client.createWallets({
  blockchains: ['MATIC-AMOY'],
  count: 2,
  walletSetId: walletSetResponse.data?.walletSet?.id ?? '',
})
console.log('Created Wallets', walletsResponse.data?.wallets)
###
Smart Contract Platform Client
[](#smart-contract-platform-client)
To interact with the Smart Contract Platform, import the factory initiateSmartContractPlatformClient from the SDK, and then initialize the client using your API key and entity secret.

The following code examples demonstrate how to import the client and configure it to use your API key and entity secret:

JavaScript

const { initiateSmartContractPlatformClient } = require('@circle-fin/smart-contract-platform')
const client = initiateSmartContractPlatformClient({
  apiKey: '<your-api-key>',
  entitySecret: '<your-entity-secret>',
})
Alternatively:

JavaScript

import { initiateSmartContractPlatformClient } from '@circle-fin/smart-contract-platform'
const client = initiateSmartContractPlatformClient({
  apiKey: '<your-api-key>',
  entitySecret: '<your-entity-secret>',
})
Interact with the client

The following code sample demonstrates how to create a simple, smart contract using the client.

JavaScript

const response = await client.deployContract({
  name: 'First Contract',
  description: 'My first hello world contract',
  walletId: '004735f6-d9fc-44f8-933c-672cdf3d240d',
  abiJson: "[\n\t{\n\t\t'inputs': [],\n\t\t'stateMutability': 'nonpayable',\n\t\t'type': 'constructor'\n\t},\n\t...",
  bytecode: '0x60806040523480156200001157600080fd5b50604051806040...',
  constructorParameters: ['TICK', 10000],
  feeLevel: 'MEDIUM',
})console.log(response.data)
###
Client Configuration options
[](#client-configuration-options)
The client for each SDK accepts the following configuration parameters:

Option	Required	Description
apiKey	[x]	The API Key used to authenticate against Circle APIs.
entitySecret	[x]	Your configured entity secret. Required for Developer-Controlled Programmable Wallets and Smart Contract Platform SDKs only.
storage	[ ]	Optional custom storage solution for persisting data. We will fall back to InMemoryStorage if none was provided.
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/programmable-wallet-api-rate-limits
Title: Programmable Wallet API Rate Limits
Description: undefined
Keywords: undefined

Web3 Services handle a high volume of API requests. To ensure optimum performance for the many developers using Circle Web3 Services, we implemented rate limits for API requests per second (RPS) per developer entity. These limits ensure the availability, stability, and reliability of Web3 Services.

Rate limits apply to all Programmable Wallets APIs. The default limit per endpoint is 3 requests per second. The following table lists the endpoints with custom limits.

Endpoint	RPS Limit
POST /v1/w3s/developer/wallets	5
POST v1/w3s/transactions/contractExecution/estimateFee	10
POST /v1/w3s/users/token	10
POST /v1/w3s/user/transactions/transfer	20
Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/monitored-tokens
Title: Monitored Tokens
Description: undefined
Keywords: undefined

The Monitored Tokens feature in the Programmable Wallet aims to empower developers with enhanced control over viewing token balances and transactions. This feature enables developers to easily manage and filter out tokens unrelated to their use case from our API responses, thereby selectively screening which tokens end-users can see, providing a more streamlined and personalized experience.

By utilizing the API endpoint [POST config/entity/monitoredTokens](/w3s/reference/createmonitoredtokens), developers can easily add tokens via their unique token IDs. This guide will walk you through the steps to interact with the Monitored Tokens feature, including retrieving token IDs, adding tokens, and using API endpoints to get transaction and wallet balances for specific tokens.

##📘
By default, there are no Monitored Tokens. This means that the system will monitor all tokens as no filter exists. Calling [GET /entity/config/monitoredTokens](/w3s/reference/listmonitoredtokens) before configuring, it will return 204, a no-content response.

###
Update Monitored Token List
[](#update-monitored-token-list)
To add tokens to the monitored token list, follow these steps:

-
Retrieve the Token ID: Programmable wallet currently supports native coins, ERC-20, ERC-721, and ERC-1155.

- Each token deposited into the programmable wallet generates a specific token ID, which developers can obtain through the following methods:
- Deposit Callback: The callback response will include the corresponding token ID when a token is deposited.
- Querying Wallet Balance: [GET /wallets/{id}/balances](/w3s/reference/listwalletballance) or [GET /wallets/{id}/nfts](/w3s/reference/listwalletnfts) will retrieve the tokens in the wallet and their related token IDs.
- Querying Transaction: [GET /transactions](/w3s/reference/listtransactions) also retrieves the token IDs for inbound or outbound transactions.
-
Add tokens into the monitored token list: Once you have the token ID, make [POST config/entity/monitoredTokens](createmonitoredtokens) request with the token IDs specified in the required field.

###
Remove Monitored Tokens
[](#remove-monitored-tokens)
To remove tokens from Monitored Tokens, use [POST /config/entity/monitoredTokens/delete](/w3s/reference/deletemonitoredtokens) to remove specific tokens. Specify the token IDs you want to delete, and they will be excluded from the list.

###
Update Monitor
[](#update-monitor)
To replace tokens with Monitored Tokens, use [PUT /config/entity/monitoredTokens](/w3s/reference/updatemonitoredtokens) entirely replace the existing monitored token list by specifying the tokens you want to monitor. The previous list will be overwritten with the new tokens provided.

###
Change scope for Monitored Tokens
[](#change-scope-for-monitored-tokens)
By default, all ERC-20, ERC-721, and ERC-1155 tokens deposited into your wallets are monitored and will be returned in [GET /wallets/{id}/balances](/w3s/reference/listwalletballance), [GET /wallets/{id}/nfts](/w3s/reference/listwalletnfts), [GET /transactions](/w3s/reference/listtransactions) APIs, and webhook callbacks.

However, after adding tokens to Monitored Tokens, the Monitored Tokens scope is set to SELECTED by default, which means only the provided tokens will be returned in the above APIs and callbacks. To revert to monitoring all tokens, call [PUT /config/entity/monitoredTokens/scope](/w3s/reference/updatemonitoredtokensscope) and set the scope to MONITOR_ALL.

It’s important to note that even when the monitored token scope is set with SELECTED, developers can view all tokens in the wallet balance and transactions. This can be achieved by setting the includAll field to True (default value is False) in the [GET /wallets/{id}/balances](/w3s/reference/listwalletballance), [GET /wallets/{id}/nfts](/w3s/reference/listwalletnfts), [GET /transactions](/w3s/reference/listtransactions) APIs. By doing so, the API responses will include details for all tokens in the wallet, irrespective of whether they are part of the monitored token list.

Updated 6 months ago


URL: https://developers.circle.com/w3s/docs/user-controlled-receive-inbound-transfer
Title: Receive an Inbound Transfer
Description: undefined
Keywords: undefined

Circle Programmable Wallets provide a comprehensive developer solution to storing, sending, and spending Web3 digital currencies and NFTs. You or your users can manage asset infrastructure. Circle provides a one-stop-shop experience with all the tools and services to handle the complex parts, including security, transaction monitoring, account recovery flows, and more.

Note that if you’re building with Wallet Signing Service, you will be responsible for deposit monitoring and balance retrieval.

This guide outlines initiating a currency transfer into a previously created user-controlled wallet. You’ll learn to use Circle’s sample application and how to make API requests via Circle's API references or cURL requests. In this guide, you’ll find cURL requests presented inline, while API references are linked from the API endpoint text. You can find instructions on using it in the [testing via the reference pages](/w3s/docs/api-references) guide.

- As with most of our quickstarts, all API calls and transactions in this guide occur within the Testnet environment; no real-world funds will be transferred.
- If you have not yet created a user-controlled wallet, see [this guide](/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart).
##
1. Acquire a Session Token
[](#1-acquire-a-session-token)
Make a request to [POST /users/token](/w3s/reference/getusertoken) using a previously created [userId](user-controlled-initialization-and-wallet-creation-quickstart). The userToken is a 60-minute session token to initiate requests requiring a user challenge (PIN code entry). After 60 minutes, the session expires, and a new userToken must be generated via the same endpoint. 

Node.js
cURL

// Import and configure the user-controlled wallet SDK
const { initiateUserControlledWalletsClient } = require('@circle-fin/user-controlled-wallets');
const circleUserSdk = initiateUserControlledWalletsClient({
  apiKey: '<API_KEY>'
});

const response = await circleUserSdk.createUserToken({
  userId: '2f1dcb5e-312a-4b15-8240-abeffc0e3463'
});
Response Body

{
  "data": {
    "userToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCC9.eyJkZXZlbG9wZXJFbnRpdHlFbnZpcm9ubWVudCI6IlRFU1QiLCJlbnRpdHlJZCI6IjRlMDdhOGM5LTIxOTAtNDVlNC1hNjc0LWQyMGFkNjg4MWI3YyIsImV4cCI6MTY5MDU1MjcwNywiaWF0IjoxNjkwNTQ5MTA3LCJpbnRlcm5hbFVzZXJJZCI6ImQ2ZjkzODliLWQ5MzUtNWFlYy1iOTVhLWNjNTk1NjA2YWM5NiIsImlzcyI6Imh0dHBzOi8vcHJvZ3JhbW1hYmxlLXdhbGxldC5jaXJjbGUuY29tIiwianRpIjoiMmE0YmJlMzAtZTdkZi00YmM2LThiODMtNTk0NGUyMzE2ODlkIiwic3ViIjoiZXh0X3VzZXJfaWRfOSJ9.dhfByhxZFbJx0XWlzxneadT4RQWdnxLu3FSN9ln65hCDOfavaTL1sc4h-jUR8i4zMmfdURw3FFcQIdSbm-BUg6M7FP_fp-cs9xBbNmRZa31gMd1aKdcajJ9SvlVrfUowYfGXM3VcNF8rtTFtW-gk1-KzU4u10U35XXbbMcW1moxE0Rqx_fKotDgk2VdITuuds5d5TiQzAXECqeCOCtNoDKktMkglltbnLxOaRl2ReZjGt-ctD2V0DbYNO4T_ndPSUDI6qD7dXQRed5uDcezJYoha3Qj3tFGBglEnox2Y6DWTbllqjwmfTGrU8Pr0yz4jQz7suGwmiCzHPxcpYxMzYQ",
    "encryptionKey": "Tlcyxz7Ts9ztRLQq5+pic0MIETblYimOo2d7idV/UFM="
  }
}
##
2. Acquire the Wallet ID
[](#2-acquire-the-wallet-id)
Make a request to [GET /wallets](/w3s/reference/listwallets) using the userToken returned in Step 1 to retrieve the wallet information for a given user.

Node.js
cURL

const response = await circleUserSdk.listWallets({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "01899cf2-d415-7052-a207-f9862157e546",
        "state": "LIVE",
        "walletSetId": "01899cf2-d407-7f89-b4d9-84d63573f138",
        "custodyType": "ENDUSER",
        "userId": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
        "address": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
        "addressIndex": 0,
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2023-07-28T14:41:47Z",
        "createDate": "2023-07-28T14:41:47Z"
      }
    ]
  }
}
##
3. Transfer Testnet Currency
[](#3-transfer-testnet-currency)
Transfer Testnet currency from an external wallet outside the Programmable Wallet infrastructure into your applicable wallet address. The best way to achieve this is through the use of a faucet. In our case, we will use the [USDC Faucet](https://faucet.circle.com/) to transfer USDC on Polygon Amoy to user wallet.

Here is a list of reputable faucets for each blockchain:

- Polygon: [USDC on MATIC Faucet](https://faucet.circle.com/) | [Amoy MATIC Faucet](https://www.alchemy.com/faucets/polygon-amoy)
- Ethereum: [USDC on ETH Faucet](https://faucet.circle.com/) | [Sepolia ETH Faucet](https://sepoliafaucet.com/)
- Avalanche: [USDC on AVAX Faucet](https://faucet.circle.com/) | [Fuji AVAX Faucet](https://core.app/en/tools/testnet-faucet/?token=C)
Once an inbound transfer is made to the address and completed, Circle sends a notification to a [subscribed endpoint](/w3s/docs/web3-services-notifications-quickstart). The Webhook notification will be similar to the one below.

Webhook Request Body

{
  "subscriptionId": "d4c07d5f-f05f-4fe4-853d-4dd434806dfb",
  "notificationId": "05b3f4e5-ec27-44b8-aa40-3698577f6d92",
  "notificationType": "transactions.inbound",
  "notification": {
    "id": "2f4b6bcd-a752-5d8b-996b-92e3e04bd33b",
    "blockchain": "MATIC-AMOY",
    "walletId": "01899cf2-d415-7052-a207-f9862157e546",
    "tokenId": "38f2ad29-a77b-5a44-be05-8d03923878a2",
    "userId": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
    "destinationAddress": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
    "amounts": [
      "10"
    ],
    "nftTokenIds": [],
    "state": "COMPLETED",
    "transactionType": "INBOUND",
    "createDate": "2023-07-28T16:03:08Z",
    "updateDate": "2023-07-28T16:06:40Z"
  },
  "timestamp": "2023-07-28T16:06:40.907831464Z",
  "version": 2
}
Alternatively, you can poll [GET /transactions](/w3s/reference/listtransactions) using the userId or userToken associated with your user. 

Node.js
cURL

const response = await circleUserSdk.listTransactions({
  userToken: '<USER_TOKEN>'
});
Response Body

{
  "data": {
    "transactions": [
      {
        "id": "97d22a88-6d25-5947-a7b6-61b3dc668057",
        "blockchain": "MATIC-AMOY",
        "tokenId": "38f2ad29-a77b-5a44-be05-8d03923878a2",
        "walletId": "01899cf2-d415-7052-a207-f9862157e546",
        "sourceAddress": "0x6e5eaf34c73d1cd0be4e24f923b97cf38e10d1f3",
        "destinationAddress": "0x075e62c80e55d024cfd8fd4e3d1184834461db57",
        "transactionType": "INBOUND",
        "custodyType": "ENDUSER",
        "state": "CONFIRMED",
        "amounts": [
          "10"
        ],
        "nfts": null,
        "txHash": "0xdd2f81a78605dcad759265c703fb2b4c507c5ea100319338422714bfcde77225",
        "blockHash": "0x4df6092fdb868331614771ff11944b43051cf6ed1067f8cfa55e9d40ef61426b",
        "blockHeight": 9423950,
        "networkFee": "",
        "firstConfirmDate": "2023-07-28T19:07:24Z",
        "operation": "TRANSFER",
        "userId": "2f1dcb5e-312a-4b15-8240-abeffc0e3463",
        "abiParameters": null,
        "createDate": "2023-07-28T19:07:34Z",
        "updateDate": "2023-07-28T19:07:37Z"
      }
    ]
  }
}
Updated 1 day ago

Congratulations! You’ve received your first transaction to your user-controlled wallet. To learn how to make a transfer out of your user-controlled wallet, check out the next guide.

-
[Send an Outbound Transfer](/w3s/docs/user-controlled-send-outbound-transfer)

URL: https://developers.circle.com/w3s/docs/supported-blockchains-and-currencies
Title: Supported Blockchains and Currencies
Description: undefined
Keywords: undefined

###
Introduction to Blockchains and Currencies
[](#introduction-to-blockchains-and-currencies)
If you are unfamiliar with blockchain concepts, a blockchain can be described as a distributed ledger that records a sequence of actions. It is maintained and synchronized across multiple computers connected through the Internet. Visualize it as a decentralized database where participants in the network record their transactions according to predefined rules to prevent fraudulent or dishonest actions. Notable examples of blockchains include [Bitcoin](https://en.wikipedia.org/wiki/Bitcoin) and [Ethereum](https://en.wikipedia.org/wiki/Ethereum).

Typically, a blockchain supports one or more cryptocurrencies, often known as tokens. These tokens can represent value and are sometimes interchangeably referred to as currencies. In most cases, a blockchain hosts a native currency and may include additional tokens or currencies on the same chain. For instance, the Ethereum blockchain supports its native currency, Ethereum (ETH), alongside various tokens such as USDC and EURC. To streamline compatibility and interaction between different tokens, many adhere to a technical standard known as ERC-20.

It is worth noting that occasionally, a single currency can be available on multiple blockchains, expanding its reach and versatility.

###
Supported Blockchains and Currencies
[](#supported-blockchains-and-currencies)
Below is a list of blockchain networks supported by each product. Whichever Programmable Wallet [infrastructure model](/w3s/docs/programmable-wallets-infrastructure-models) you select, user-controlled or developer-controlled, the same blockchains are supported. The selected [account type](/w3s/docs/programmable-wallets-account-types), externally owned account (EOA) or smart contract account (SCA), does affect which blockchains are supported.

Blockchain Network	Programmable Wallets EOA	Programmable Wallets SCA	Smart Contract Platform	Gas Station
Avalanche Fuji Testnet				
Avalanche Mainnet				
Ethereum Sepolia Testnet				
Ethereum Mainnet				
Polygon Amoy Testnet				
Polygon Mainnet				
*For all blockchains, the following tokens and standards are supported: ETH, ERC-20, ERC-721, ERC-1155

###
Testnet Faucets
[](#testnet-faucets)
Blockchain Network	Native Token	USDC
Avalanche Fuji Testnet	[Fuji AVAX Faucet](https://core.app/en/tools/testnet-faucet/?token=C)	[USDC on AVAX Faucet](https://faucet.circle.com/)
Ethereum Sepolia Testnet	[Sepolia ETH Faucet](https://sepoliafaucet.com/)	[USDC on ETH Faucet](https://faucet.circle.com/)
Polygon Amoy Testnet	[Amoy MATIC Faucet](https://www.alchemy.com/faucets/polygon-amoy)	[USDC on MATIC Faucet](https://faucet.circle.com/)
###
Supporting additional EVM blockchains
[](#supporting-additional-evm-blockchains)
If you have a requirement where you need a consistent wallet address across multiple EVM chains, ensuring a direct mapping between the addresses on different chains, our wallet creation APIs can support this functionality. This means that when you create a wallet address on one EVM chain, you can replicate and map the same address to another EVM chain seamlessly. This 1-to-1 mapping ensures that the wallet address remains consistent and easily identifiable across the different chains you are utilizing.

- For user-controlled wallets, you can achieve this by sending an API request using the [POST /user/wallets](/w3s/reference/createuserwallet). Make sure to include the same X-User-Token in the header for authentication. In the request body, specify the new blockchain(s) you want to associate with the wallet(s) by including them in the blockchains field. The existing EVM wallet address will be used by default to create the wallet(s) on the newly specified blockchain(s), ensuring consistency across chains.
- For developer-controlled wallets, you can achieve this by sending an API request using the [POST /developer/wallets](/w3s/reference/createdeveloperwallet) endpoint. Include the same walletSetId that was used for previously created wallets on another EVM chain. To ensure consistency, provide the same count value as the number of wallets currently associated with the walletSetId. If you need to obtain the count for a specific walletSetId, you can send an API request to [GET /wallets?walletSetId=<ID>](listwallets-1) and include the walletSetId as a query parameter.
Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/web3-services-api-keys-authentication
Title: API Keys & Authentication
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/w3s/docs/smart-contract-references
Title: Smart Contract Template References
Description: undefined
Keywords: undefined

[
##Token (ERC-20)
A template for an ERC-20 token: the most popular standard for fungible tokens.

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-20-token) [
##NFT (ERC-721)
A template for minting ERC-721 non-fungible tokens (NFTs).

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-721-nft) [
##Multi-Token (ERC-1155)
A template for the versatile ERC-1155 token standard. Multi-Token allows you to create and manage multiple token types within one contract.

LEARN MORE

](https://developers.circle.com/w3s/docs/erc-1155-multi-token)
Updated 2 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/react-native-sdk
Title: React Native SDK
Description: undefined
Keywords: undefined

The Circle React Native SDK enables your mobile application to provide user-controlled programmable wallets. By integrating this SDK, your users can securely input sensitive data like PINs or security answers. The SDK encrypts the request body using a secret key, protecting your users' information.

📘 The Web and Mobile SDKs preserve the user keyshare with the individual, giving them complete control. You must use the SDKs with the user-controlled wallet product. Additionally, the Web and Mobile SDKs support only the user-controlled wallet product.

At Circle, we understand the importance of end-to-end security for your application and the need to create a tailored and seamless user experience for your end-users. Hence, Circle’s SDK also exposes functionality for you to customize the description and layout of UI components we serve, including: 

-
UI Title and Subtitle Customization: Modify the title and subtitle to reflect your brand identity or provide specific instructions.

-
Custom PIN Code Input Layout: Adjust the layout and styling of the PIN code input field to align with your application's design guidelines.

-
Question List Configuration: Set the list of security questions displayed in the User Wallet UI, allowing users to choose from a predefined set.

-
SDK Initialization: Initialize the Web SDK by setting the endpoint server, ensuring seamless communication between your application and Circle’s services.

-
Predefined Error Messages: Customize the error messages displayed to users, providing a more personalized experience and guidance.

-
ChallengeID Acceptance and Operation Retrieval: Easily accept the challengeId and retrieve any relevant operations within the SDK.

💡Tip: See the [React Native SDK UI Customization API]() article to customize the SDK.

##
Install the SDK
[](#install-the-sdk)
You can set up the React Native SDK by downloading it from [GitHub](https://github.com/circlefin/w3s-react-native-sdk).

##
SDK architecture
[](#sdk-architecture)
You must use Web, iOS, Android or React Native SDKs to access the user-controlled Programmable Wallet product. The SDK secures, manages, and communicates with your server to ensure your user’s keyshare, always stays with them and is not exposed to your servers.

To learn more, see [SDK Architecture for User-Controlled Wallets](/w3s/docs/sdk-architecture-for-user-controlled-wallets).


##
SDK API references
[](#sdk-api-references)
###
WalletSdk
[](#walletsdk)
implement IWallet

###
IWalletSdk
[](#iwalletsdk)
interface IWalletSdk

Properties
sdkVersion: SdkVersion
SDK version.
Methods
init: (configuration: Configuration) => Promise<void>
Configure the Circle endpoint for SDK to connect, reject Promise if the endpoint and appId’s format are invalid.
setSecurityQuestions: (securityQuestions: SecurityQuestion\[]) => void
Set the security question list, throw Throwable when the SecurityQuestion array is empty or contains any question whose title length is not 2~512.
addListener: (listener: EventListener) => void
Register an EventListener for the app to handle events, e.g. forgot PIN.
removeAllListeners: () => void
Unregister all EventListener.
execute: ( userToken: string, encryptionKey: string, challengeIds: string\[], successCallback: SuccessCallback, errorCallback: ErrorCallback ) => void
Execute the operations by challengeId and trigger the SuccessCallback with SuccessResult after sending the request to Circle endpoint successfully.
ErrorCallback will be triggered when parameters are null or invalid.
executeWithKeyShare: ( userToken: string, encryptionKey: string, pinCodeDeviceShare: string, challengeIds: string\[], successCallback: SuccessCallback, errorCallback: ErrorCallback ) => void
Execute the operations by challengeId and PIN code device share and trigger the SuccessCallback with SuccessResult after sending the request to Circle endpoint successfully.
ErrorCallback will be triggered when parameters are null or invalid.
getDeviceId: () => string?
setBiometricsPin: ( userToken: string, encryptionKey: string, successCallback: SuccessCallback, errorCallback: ErrorCallback ) => void
Setup BiometricsForPin and trigger the SuccessCallback with SuccessResult after finishing the operation successfully.
ErrorCallback will be triggered when parameters are null or invalid.
setDismissOnCallbackMap: (map: Map\<ErrorCode, boolean>) => void
The SDK UI can be dismissed if there's an error or warning during execute() and setBiometricsPin(). App can specify ErrorCode as true in the map to dismiss.
moveTaskToFront: () => void
Bring the SDK UI to foreground. After calling moveRnTaskToFront() and finishing the flow on React Native UI, e.g. forgot PIN flow, apps can back to SDK UI by calling moveTaskToFront() and continue the SDK task.
moveRnTaskToFront: () => void
Bring the React Native Activity / ViewController to front. In some cases, apps may need to go back to React Native UI during execute() or setBiometricsPin, e.g., receive an error, warning or event like forgotPin.
setTextConfigsMap: (map: Map\<TextsKey, TextConfig\[]>) => void
Define strings with specific configurations for special text customization.
All keys are listed in A Index Table.
setIconTextConfigsMap: ( map: Map\<IconTextsKey, Array<IconTextConfig>> ) => void
Define icon and string sets with specific configurations for icon text list item customization.
All keys are listed in B Index Table.
setTextConfigMap: (map: Map\<TextKey, TextConfig>) => void
Define strings with specific configurations for general string customization. It will replace values.
All keys are listed in C Index Table.
setImageMap: (map: Map\<ImageKey, ImageSourcePropType>) => void
Define image resource for image customization.
All keys are listed in D Index Table.
setDateFormat: (format: DateFormat) => void
Set display date format, e.g. the answer of a security question in which inputType is datePicker.
All supported formats are listed in DateFormat.
The default format is “YYYY-MM-DD”.
setDebugging: (debugging: boolean) => void
Android only.
true: default value, check customization map value and print warn level log.
false: skip checking and turn off the log.
setCustomUserAgent: (userAgent: string) => void
Set custom user agent value.
setErrorStringMap: (map: Map\<ErrorCode, string) => void
Define error message for customization.
###
SdkVersion
[](#sdkversion)
interface SdkVersion

Properties
rn: string
Programmable Wallet React Native SDK version.
native: string
Programmable Wallet Native SDK version.
###
Configuration
[](#configuration)
interface Configuration

Properties
endpoint: string
The endpoint SDK connects.
appId: string
Application ID, retrieved from Circle Web3 Services Console.
settingsManagement?: SettingsManagement
Extra settings for SDK
###
SettingsManagement
[](#settingsmanagement)
interface SettingsManagement

Properties
enableBiometricsPin: boolean
Enable biometrics to protect PIN code.
###
SecurityQuestion
[](#securityquestion)
class SecurityQuestion

Properties
title: string
The question string.
inputType: InputType
The input type of the question. Support text input and timePicker.
Constructor
constructor(title: string, inputType?: InputType)
Initialize a SecurityQuestion, use InputType.text as default value.
###
InputType
[](#inputtype)
Enumerates the types of security questions.

JavaScript

enum InputType {
  text = 'text',
  datePicker = 'datePicker',
}
###
SuccessResult
[](#successresult)
interface SuccessResult

Properties
result: ExecuteResult
Execute result.
warning?: ExecuteWarning
Execute warning.
###
Error
[](#error)
interface Error

Properties
code: string
Error code.
message: ExecstringuteWarning
Error message.
###
SuccessCallback
[](#successcallback)
type SuccessCallback

Type declaration
(result: SuccessResult) => void
Callback when the operation is executed successfully.
###
ErrorCallback
[](#errorcallback)
type ErrorCallback

Type declaration
(error: Error) => void
The callback is triggered when a failure occurs in operation or is canceled by the user.
###
ExecuteResult
[](#executeresult)
interface ExecuteResult

Properties
resultType: ExecuteResultType
The type of the operation that the challenge represents.
status: ExecuteResultStatus
The status of the execution.
data?: ExecuteResultData
The data of the execution.
###
ExecuteResultData
[](#executeresultdata)
interface ExecuteResultData

Properties
signature?: string
The signature for SIGN_MESSAGE and SIGN_TYPEDDATA.
###
ExecuteResultType
[](#executeresulttype)
Enumerates the types of challenges supported

JavaScript

enum ExecuteResultType {
  UNKNOWN = 'UNKNOWN',
  SET_PIN = 'SET_PIN',
  RESTORE_PIN = 'RESTORE_PIN',
  SET_SECURITY_QUESTIONS = 'SET_SECURITY_QUESTIONS',
  CREATE_WALLET = 'CREATE_WALLET',
  CREATE_TRANSACTION = 'CREATE_TRANSACTION',
  ACCELERATE_TRANSACTION = 'ACCELERATE_TRANSACTION',
  CANCEL_TRANSACTION = 'CANCEL_TRANSACTION',
  CONTRACT_EXECUTION = 'CONTRACT_EXECUTION',
  SIGN_MESSAGE = 'SIGN_MESSAGE',
  SIGN_TYPEDDATA = 'SIGN_TYPEDDATA',
  SET_BIOMETRICS_PIN = 'SET_BIOMETRICS_PIN',
}
###
ExecuteResultStatus
[](#executeresultstatus)
Enumerates the possible statuses for a challenge

JavaScript

enum ExecuteResultStatus {
  UNKNOWN = 'UNKNOWN',
  PENDING = 'PENDING',
  IN_PROGRESS = 'IN_PROGRESS',
  COMPLETE = 'COMPLETE',
  FAILED = 'FAILED',
  EXPIRED = 'EXPIRED',
}
###
EventListener
[](#eventlistener)
type EventListener

Type declaration
(event: ExecuteEvent) => void
Event listener for receiving ExecuteEvent.
###
ExecuteEvent
[](#executeevent)
Enumerates the possible event

JavaScript

enum ExecuteEvent {
  forgotPin = 'forgotPin',
}
###
ErrorCode
[](#errorcode)
Enumerates the types of error code

JavaScript

enum ErrorCode {
  unknown = '-1',
  success = '0',
  apiParameterMissing = '1',
  apiParameterInvalid = '2',
  forbidden = '3',
  unauthorized = '4',
  retry = '9',
  customerSuspended = '10',
  pending = '11',
  invalidSession = '12',
  invalidPartnerId = '13',
  invalidMessage = '14',
  invalidPhone = '15',
  walletIdNotFound = '156001',
  tokenIdNotFound = '156002',
  transactionIdNotFound = '156003',
  walletSetIdNotFound = '156004',
  notEnoughFounds = '155201',
  notEnoughBalance = '155202',
  exceedWithdrawLimit = '155203',
  minimumFundsRequired = '155204',
  invalidTransactionFee = '155205',
  rejectedOnAmlScreening = '155206',
  tagRequired = '155207',
  gasLimitTooLow = '155208',
  transactionDataNotEncodedProperly = '155209',
  fullNodeReturnedError = '155210',
  walletSetupRequired = '155211',
  lowerThenMinimumAccountBalance = '155212',
  rejectedByBlockchain = '155213',
  droppedAsPartOfReorg = '155214',
  operationNotSupport = '155215',
  amountBelowMinimum = '155216',
  wrongNftTokenIdNumber = '155217',
  invalidDestinationAddress = '155218',
  tokenWalletChainMismatch = '155219',
  wrongAmountsNumber = '155220',
  userAlreadyExisted = '155101',
  userNotFound = '155102',
  userTokenNotFound = '155103',
  userTokenExpired = '155104',
  invalidUserToken = '155105',
  userWasInitialized = '155106',
  userHasSetPin = '155107',
  userHasSetSecurityQuestion = '155108',
  userWasDisabled = '155109',
  userDoesNotSetPinYet = '155110',
  userDoesNotSetSecurityQuestionYet = '155111',
  incorrectUserPin = '155112',
  incorrectDeviceId = '155113',
  incorrectAppId = '155114',
  incorrectSecurityAnswers = '155115',
  invalidChallengeId = '155116',
  invalidApproveContent = '155117',
  invalidEncryptionKey = '155118',
  userPinLocked = '155119',
  securityAnswersLocked = '155120',
  walletIsFrozen = '155501',
  maxWalletLimitReached = '155502',
  walletSetIdMutuallyExclusive = '155503',
  metadataUnmatched = '155504',
  userCanceled = '155701',
  launchUiFailed = '155702',
  pinCodeNotMatched = '155703',
  insecurePinCode = '155704',
  hintsMatchAnswers = '155705',
  networkError = '155706',
  biometricsSettingNotEnabled = '155708',
  deviceNotSupportBiometrics = '155709',
  biometricsKeyPermanentlyInvalidated = '155710',
  biometricsUserSkip = '155711',
  biometricsUserDisableForPin = '155712',
  biometricsUserLockout = '155713',
  biometricsUserLockoutPermanent = '155714',
  biometricsUserNotAllowPermission = '155715',
  biometricsInternalError = '155716',
}
###
TextConfig
[](#textconfig)
Data-only class for text customization.
class TextConfig

Properties
text?: string
Text to display.
gradientColors?: string\[]
Array of Gradient text color. Only used by
TextsKey.enterPinCodeHeadline,
TextsKey.securityIntroHeadline,
TextsKey.newPinCodeHeadline
textColor?: string
Text color.
font?: string
Font.
Constructor
constructor( text?: string, gradientColorsOrTextColor: string\[] | string, font?: string )
###
TextConfig
[](#textconfig-1)
Data-only class for icon text list item customization.
class IconTextConfig

Properties
image: [ImageSourcePropType](https://reactnative.dev/docs/image#source)
The image source for customization.
textConfig: TextConfig
Text config for text customization.
Constructor
constructor(image: ImageSourcePropType, textConfig: TextConfig)
###
TextsKey
[](#textskey)
Enum for setTextConfigsMap().
See [A Index Table](https://developers.circle.com/w3s/docs/react-native-sdk-ui-customization-api#a-index-table--textskey).

###
IconTextsKey
[](#icontextskey)
Enum for setIconTextConfigsMap().
See [B Index Table](https://developers.circle.com/w3s/docs/react-native-sdk-ui-customization-api#b-index-table---icontextskey).

###
TextKey
[](#textkey)
Enum for setTextConfigMap().
See [C Index Table](https://developers.circle.com/w3s/docs/react-native-sdk-ui-customization-api#c-index-table---resourcekey).

###
ImageKey
[](#imagekey)
Enum for setImageMap().
See [D Index Table](https://developers.circle.com/w3s/docs/react-native-sdk-ui-customization-api#d-index-table).

###
DateFormet
[](#dateformet)
Enum for setDateFormat().

JavaScript

enum DateFormat {
  YYYYMMDD_HYPHEN = 'yyyy-MM-dd',
  DDMMYYYY_SLASH = 'dd/MM/yyyy',
  MMDDYYYY_SLASH = 'MM/dd/yyyy',
}

###
SampleCode
[](#samplecode)
JavaScript

import * as React from 'react';
import { WalletSdk } from '@circle-fin/w3s-pw-react-native-sdk';

export default function App() {
    const [endpoint, setEndpoint] = React.useState(
    'https://api.circle.com/v1/w3s/'
  );
    const [appId, setAppId] = React.useState('');
    const [enableBiometricsPin, setEnableBiometricsPin] = React.useState(false);
    const [userToken, setUserToken] = React.useState('');
    const [encryptionKey, setEncryptionKey] = React.useState('');
    const [challengeId, setChallengeId] = React.useState('');

    React.useEffect(() => {
        // Regester EventListener
        WalletSdk.addListener((event: any) => {
	  if(event == ExecuteEvent.forgotPin){
              WalletSdk.moveRnTaskToFront();
              // forgot PIN flow in React Native UI
           }
        });
	_initSdk();
        _setSecurityQuestions();
        _setDismissOnCallbackMap();
        _setTextConfigsMap();
        _setIconTextConfigsMap();
        _setTextConfigMap();
        _setErrorStringMap();
        _setImageMap();
        _setDateFormat();
        _setDebugging();
        return () => {
           // Removes listeners once unmounted
           WalletSdk.removeAllListeners();
        };
   }, []);
   ...
   return (
       <View>
        <TextInput
          accessibilityLabel={'endpointInput'}
          onChangeText={setEndpoint}
          value={endpoint}
        />
        <TextInput
          accessibilityLabel={'appIdInput'}
          onChangeText={setAppId}
          value={appId}
        />
        <TextInput
          accessibilityLabel={'userTokenInput'}
          onChangeText={setUserToken}
          value={userToken}
        />
        <TextInput
          accessibilityLabel={'encryptionKeyInput'}
          onChangeText={setEncryptionKey}
          value={encryptionKey}
        />
        <TextInput
          onChangeText={setChallengeId}
          value={challengeId}
        />
        <TouchableOpacity
          accessibilityLabel={'executeSdkButton'}
          onPress={() => {
            _executeSdk();
          }}
        >
          <Text>Execute</Text>
        </TouchableOpacity>
        <TouchableOpacity
          accessibilityLabel={'setBiometricsPinButton'}
          onPress={() => {
            _setBiometricsPin();
          }}
        >
          <Text>Set Biometrics Pin</Text>
        </TouchableOpacity>
      </View>
   )
}
JavaScript

 ...
   const _initSdk = async () => {
     try {
            // Set endpoint, app ID and extra settings
   await WalletSdk.init({
     endpoint,
     appId,
     settingsManagement: { enableBiometricsPin: enableBiometricsPin }
    });
     } catch (e: any) {
     toast(e.message);
     return;
     }
   };
   
  const _setSecurityQuestions = () => {
    WalletSdk.setSecurityQuestions([
      new SecurityQuestion('What was your childhood nickname?', InputType.text),
      new SecurityQuestion('When is your favorite date?', InputType.datePicker),
      new SecurityQuestion('What is the name of your first pet?'),
      new SecurityQuestion('What is your favorite country?'),
    ]);
  };

  const _setDismissOnCallbackMap = () => {
      const map = new Map();
      map.set(ErrorCode.userCanceled, true);
      map.set(ErrorCode.networkError, true);
      WalletSdk.setDismissOnCallbackMap(map);
  };

  const _setTextConfigsMap = () => {
      let gradientColors = ['#05184b', '#21bad5'] as Array<string>;
      const map = new Map();
      map.set(TextsKey.newPinCodeHeadline, [
        new TextConfig('ENTER your new ', '#a6183f'),
        new TextConfig(
          'W3s PIN',
          undefined,
          gradientColors,
          'Roboto'
        ),
      ]);
      map.set(TextsKey.securityIntroLink, [
        new TextConfig('==Learn more=='),
        new TextConfig('https://www.circle.com/en/legal/privacy-policy'),
      ]);
      WalletSdk.setTextConfigsMap(map);
  };

 const _setImageMap = () => {
     const imageMap = new Map();
     imageMap.set(ImageKey.naviBack, require('../../assets/image/ic_back.png'));
     imageMap.set(ImageKey.naviClose, require('../../assets/image/ic_close.png'));
     imageMap.set(
       ImageKey.securityIntroMain,
       require('../../assets/image/grab_confirm_main_icon.png')
     );
     imageMap.set(ImageKey.selectCheckMark, {
       uri: 'https://drive.google.com/uc?id=1UTX1tFnECuj1k3U1bggH0g5YvH_Ens5M',
     });
     imageMap.set(ImageKey.dropdownArrow, {
       uri: 'https://drive.google.com/uc?id=1PR3yYpk4AmsCAlUM8nw6C3y-RAXNjGMv',
     });
     imageMap.set(ImageKey.errorInfo, {
       uri: 'https://drive.google.com/uc?id=1UJjinISU6ZHO0fAoKaZofBMgItaAn9kS',
     });
     imageMap.set(ImageKey.securityConfirmMain, {
       uri: 'https://drive.google.com/uc?id=16OkP3VzEjLICOifNKRQi3FMJntV9F-n-',
     });
     WalletSdk.setImageMap(imageMap);
 };
  const _setIconTextConfigsMap = () => {
      const map = new Map<IconTextsKey, Array<IconTextConfig>>();
      map.set(IconTextsKey.securityConfirmationItems, [
        new IconTextConfig(
          require('../../assets/image/ic_intro_item0_icon.png'),
          new TextConfig('This is the only way to recover my account access. '       )
      ),
        new IconTextConfig(
          require('../../assets/image/ic_intro_item1_icon.png'),
          new TextConfig(
            'Circle won’t store my answers so it’s my responsibility to remember them.'
          )
      ),
       new IconTextConfig(
         require('../../assets/image/ic_intro_item2_icon.png'),
         new TextConfig(
           'I will lose access to my wallet and my digital assets if I forget my answers. '
         )
       ),
     ]);
     WalletSdk.setIconTextConfigsMap(map);
 };
 const _setTextConfigMap = () => {};
 const _setErrorStringMap = () => {};
 const _setDateFormat = () => {};
 const _setDebugging = () => {};

 // execute
 const _executeSdk = async () => {
     try {
         let { result } = await WalletSdk.execute(userToken, encryptionKey, [
           challengeId,
         ]);
         console.log(`${result.resultType}, ${result.status}, ${result.data?.signature}`);
     } catch (e: any) {
         console.log(e.message);
     }
  };
  // setBiometricsPin
 const _setBiometricsPin = async () => {
    try {
      let { result } = await WalletSdk.setBiometricsPin(
        userToken,
        encryptionKey
      );
      console.log(`${result.resultType}, ${result.status}`);
    } catch (e: any) {
      console.log(e.message);
    }
  };


Updated 15 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/sample-applications
Title: Sample Applications
Description: undefined
Keywords: undefined

Explore web sample applications to learn and kickstart your project with Circle's Web3 Services offering.

##
User-Controlled Wallets Sample Apps
[](#user-controlled-wallets-sample-apps)
Use these sample applications to start building with User-controlled wallets.

##
Create and use the pin authorization flow
[](#create-and-use-the-pin-authorization-flow)
Sample application for the [User-controlled wallets client-side SDK](https://www.npmjs.com/package/@circle-fin/w3s-pw-web-sdk) for browser-based testing.

Features of this sample include:

- Transaction authorization using a 6-digit pin
- Wallet backup and recovery
###
Using the sample app
[](#using-the-sample-app)
[Interact with the sample app](http://pw-auth-example.circle.com/).

View the sample app source code on GitHub:

- [iOS](https://github.com/circlefin/w3s-ios-sample-app-wallets)
- [Android](https://github.com/circlefin/w3s-android-sample-app-wallets)
- [React](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/react-example)
- [JavaScript (Vanilla)](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/js-example)
- [Vue.js](https://github.com/circlefin/w3s-pw-web-sdk/tree/master/examples/vue-example)
##
Create a wallet with Social Log-in
[](#create-a-wallet-with-social-log-in)
Sample application for User-Controlled Wallets.

- User account creation with social log-in functionality
- Wallet address generation after sign-up
- Send & receive transactions
- Transaction authorization using a 6-digit pin
- View transaction history
- Wallet backup and recovery
###
Using the sample app
[](#using-the-sample-app-1)
View the sample app source code on GitHub:

- [iOS](https://github.com/circlefin/w3s-digital-wallet-iOS-sample-app)
##
Manage user sessions
[](#manage-user-sessions)
Test Server for User-Controlled wallets to manage sessions and requests.

Features of this sample include:

- Challenge initiation for transaction authorization
- Session management
###
Using the sample app
[](#using-the-sample-app-2)
View the sample app source code on GitHub:

- [JavaScript](https://github.com/circlefin/w3s-programmable-wallets-test-server)
Updated 1 day ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/sandbox-vs-production
Title: Testnet (Sandbox) vs. Mainnet (Production)
Description: undefined
Keywords: undefined

###
Testnet Overview
[](#testnet-overview)
The Circle Testnet environment, similar to a sandbox, allows you to test the functionality of Circle’s Web3 Services. The Circle Testnet is connected to applicable blockchain Testnets (the sandboxed versions of a given blockchain), allowing you to build your application without the risk of testing in a Production environment.

After signing up for a [Circle Developer account](/w3s/docs/circle-developer-account), you can create Sandbox API keys for deploying on Testnet. Sandbox API keys will have the prefix TEST_API_KEY:, and can only be used for Testnet.

###
Upgrading from Testnet to Mainnet
[](#upgrading-from-testnet-to-mainnet)
Congratulations on completing your Testnet implementation! Now that you’re ready to bring your application to life in Circle’s Production environment, you’ll need to go through a few more steps.

-
Within your Circle Developer Account, click UPGRADE TO PROD.

-
You’ll be directed to select whether you want to upgrade as an individual or a business.

-
You’ll now be presented with the following screens: 

- Confirm personal information
- Pricing and payment
- Account security
- Accept Terms!
-
Once your information is submitted, you’ll receive access to Circle’s Production environment within minutes! 

Have questions? Reach out to one of our team members at [customer-support@circle.com](mailto:customer-support@circle.com). 

Continue reading for more information on transitioning your application from Testnet to Mainnet. 

###
Migrating your code from Testnet to Mainnet
[](#migrating-your-code-from-testnet-to-mainnet)
To move your application into Circle’s Production environment, you must obtain a Production API Key through the Circle Developer Account. Before being able to access a Production API Key, you will need to follow the steps outlined earlier in [this guide](/w3s/docs/sandbox-vs-production#upgrading-from-testnet-to-mainnet). Production API keys will have the prefix "LIVE_API_KEY:", and can only be used for mainnet.

Once you’ve received your Production API key, you can replace your Sandbox API Key with your Production API Key in your codebase. 

###
Differences between Testnet and Mainnet
[](#differences-between-testnet-and-mainnet)
When migrating from the Testnet environment to the Mainnet environment, you should be aware of a couple of key differences:

[Blockchain confirmation](/w3s/docs/blockchain-confirmations) times may vary between a blockchain’s Testnet and Mainnet. You may experience longer transaction times in Production to ensure stability as you handle your users’ real-world assets. Make sure you are accounting for Production transaction times when building your application. 

[Gas Fees](/w3s/docs/gas-fees) will differ on a blockchain’s Testnet and Mainnet. The Mainnet is more congested, as it handles real user transactions and interactions. Ensure that the settings you’ve configured around gas fees in your Sandbox environment are updated to your preferred setting in the Production environment. 

Testing: Circle recommends you test your application in both the Testnet and Mainnet environments before it goes live to ensure that you account for all possible variances.

To review which Testnets are connected to which different blockchains, see [Supported Blockchains and Currencies](/w3s/docs/supported-blockchains-and-currencies).

Updated 6 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart
Title: Quickstarts
Description: undefined
Keywords: undefined

Whether you're a beginner or an experienced developer, our quickstarts provide step-by-step guidance to introduce you to the world of Web3 Services and empower you to leverage the potential of on-chain wallets. Select your preferred quickstart example and style to start your journey in unlocking the full potential of decentralized applications and wallet management.

[

##Interactive Quickstart (recommended)
Build your first user-controlled wallet(s) without requiring you to enter (nearly) any code.

GET STARTED

](https://learn.circle.com/quickstarts/user-controlled-wallets) [

##Quickstart Guides
Use our step-by-step guide in our docs to set up your local environment and start with user-controlled wallet(s).

GET STARTED

](https://developers.circle.com/w3s/docs/user-controlled-create-your-first-wallet)
Please note the interactive quickstart will redirect you to a different location. You can always return to this page to access our comprehensive developer documentation.

##🚀
Want to test it out right away?

View our hosted sample app here or clone this Github repo and follow the README.

Updated 16 days ago


URL: https://developers.circle.com/w3s/docs/wallet-security
Title: Wallet Security
Description: undefined
Keywords: undefined

##
Security setup and best practices
[](#security-setup-and-best-practices)
Programmable Wallets leverage Shamir's secret sharing and secure multi-party computation (MPC) technology to ensure your wallet has the highest level of security. Shamir's secret sharing lets you, the developer, your end users, and Circle each have a key shard that can reconstruct a golden key, which can be used to access the MPC access tokens and sign through three separate MPC nodes. Circle's security model manages the complexity of hosting MPC nodes and enables developers to go to market quickly. The following diagram shows the architecture for user-controlled wallets and how signing works:


We also offer the option for self-hosting MPC nodes for enterprises with higher security needs and the requirement and ability to host and maintain their own MPC nodes.

###
Security settings for Web3
[](#security-settings-for-web3)
To further enhance your security settings, we recommend establishing an API call IP allowlist as it:

- Restricts accepted API calls to those originating from designated IPs, blocking potential unauthorized usage.
- Reduces the potential for loss due to API key exposure.
- Block bad IP addresses.
##🚧
Note: Neglecting to establish an IP allowlist can expose APIs to exploit from unauthorized IP addresses.

Updated 6 months ago


URL: https://developers.circle.com/w3s/docs/programmable-wallets-account-types
Title: Account Types for Wallets
Description: undefined
Keywords: undefined

In addition to the wallet type (infrastructure model), the account type you choose also determines the capabilities supported in the wallets you create. Circle offer two account types:

- Externally Owned Accounts (EOA)
- Smart Contract Accounts (SCA)
Refer to the table below to view the supported account types for the products.

Account Type	Programmable Wallets	Wallet Signing Service
Externally Owned Accounts (EOA)		
Smart Contract Accounts (SCA)		

##
Externally Owned Accounts
[](#externally-owned-accounts)
EOA are the most commonly used account type in Ethereum. They consists of a private and public key, with the public key representing the account address. EOAs require secure management of the private key and always require gas tokens for initiating transactions. While EOAs offer the flexibility to initiate any transaction, they have certain limitations, such as the need for a private key and the inability to control them programmatically.

Benefits:

- Zero Cost Creation: Creating an Externally Owned Account (EOA) does not incur any fees, making it an accessible option for users. This is especially important for Networks like Ethereum Mainnet, where gas costs are higher.
- Versatile Transaction Initiation: EOAs can initiate any transaction on the Ethereum network, allowing users to interact with various decentralized applications and perform a wide range of operations.
Limitations:

- Private Key Dependency: EOAs require the secure management of a private key. Users must protect their private key to ensure the security and control of their EOA, as it grants access to account functions and funds.
- Native Token Dependency for Gas: To perform transactions or execute operations on the Ethereum network, EOAs must hold native tokens such as Ether (ETH) to cover gas fees. These tokens must pay for the computational resources utilized during transaction validation and execution.
##
Smart Contract Accounts (SCA)
[](#smart-contract-accounts-sca)
SCA ae another type of Ethereum accounts that replace an EOA with a smart contract. SCAs do not have private keys, but are often controlled by an EOA that interacts with the deployed smart contract. SCAs allow developers to write customized logic within the smart contract, enabling advanced functionalities.

SCAs have been in the Ethereum ecosystem for many years but recently gained popularity thanks to a new standard called ERC-4337 (account abstraction). ERC 4337 went live on Mainnet in March 2023, enabling a trustless standard the community could build SCA around. ERC-4337 also introduced the concept of paymaster - which enables simplified gas abstraction, enabling anyone to sponsor gas fees for the end user or letting the end user pay for gas in any ERC-20 token.

When considering wallet infrastructure models, factoring in the infrastructure model (user-controlled or developer-controlled) and the account type (EOA or SCA) is essential. User-controlled wallets offer autonomy and security while providing a seamless user experience, while developer-controlled wallets simplify user interactions with the blockchain. Choosing the most suitable combination of infrastructure model and account type empowers developers to create powerful and user-friendly applications tailored to their needs.

Benefits:

- Custom Logic and Functionality: Smart Contract Accounts (SCAs) allow developers to build custom logic and functionality directly into the account. By deploying smart contracts to control SCAs, developers can create innovative and customized features, enabling advanced capabilities and automated operations within the wallet.
- Gas Abstraction and Fee Sponsorship: SCAs offer gas abstraction, simplifying gas fee management for end-users. Developers can implement mechanisms, such as the paymaster pattern, to sponsor gas fees on behalf of users. This reduces the burden on users to hold and manage native tokens for gas and enhances the overall user experience by providing a seamless gas fee payment process.
Limitations:

- Gas Tokens Required for Creation: Gas tokens are required when creating SCA wallets. However, emerging solutions like the one implemented in Programmable Wallets address this limitation by introducing a lazy deployment mechanism. This process postpones the gas fee payment until the first outbound transaction, easing the initial cost burden of SCA wallet creation.
##
Choosing between EOAs and SCAs 
[](#choosing-between-eoas-and-scas)
An EOA or an SCA can be chosen by specifying the account type parameter during wallet creation, as shown below.

Applicable APIs:

- Create a user-controlled challenge for PIN setup and create wallet(s): [POST /user/initialize](/w3s/reference/createuserwithpinchallenge).
- Create a user-controlled wallet(s): [POST /user/wallets](/w3s/reference/createuserwallet).
- Create a developer-controlled wallet(s) [POST /developer/wallets](/w3s/reference/createdeveloperwallet).
You can click the linked API references to see them in action.

Account Type Parameter

// EXAMPLE REQUEST BODY IN JSON
// For deveveloper-controlled wallets 
// entityentitySecretCiphertext and walletSetId are also required in the request.

// SCA
{
  "idempotencyKey": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
  "accountType": "SCA", // HERE
  "blockchains": [
    "MATIC-AMOY"
  ]
}

// EOA
{
  "idempotencyKey": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
  "accountType": "EOA", // HERE
  "blockchains": [
    "MATIC-AMOY"
  ]
}
###
Selection guidance
[](#selection-guidance)
Based on our experience, SCAs generally provide a more straightforward user experience for most use cases. They allow developers to focus on creating innovative functionalities within the smart contract. However, the specific needs of each project should be carefully considered when choosing between EOA and SCA account types.

##🚧
If you are building on Ethereum - creating an SCA wallet might become expensive, and we recommend using EOAs. If you are using any Polygon or any L2s, we strongly recommend using SCAs.

###
Key Considerations
[](#key-considerations)
Externally Owned Account (EOA)	Smart Contract Account (SCA)
Fees for account creation	No, it is free	Yes, creating a wallet incurs gas fees. Note: With our process of lazy deployment, you won't have to pay the gas fee at the time of wallet creation. Instead, the fee will be charged when you initiate your first outbound transaction.
Gas abstraction	No, this isn't feasible	Yes, it is supported via Paymasters.
Modular plugins	No	Yes, modules/plugins are coming soon that extend the functionality of the base wallet.
Where it makes sense	If you are building on Ethereum (since gas costs on Ethereum can be expensive)	If you want a web2-type user experience that makes blockchain invisible or wants advanced control of your wallets e.g. embedded wallets, payments wallets, developer wallets, and vault type wallet.
###
Supported Blockchains
[](#supported-blockchains)
Blockchain Network	Externally Owned Account (EOA)	Smart Contract Account (SCA)
Avalanche Fuji Testnet		
Avalanche Mainnet		
Ethereum Sepolia Testnet		
Ethereum Mainnet		 (Coming Soon)
Polygon Amoy Testnet		
Polygon Mainnet		
Updated 1 day ago


URL: https://developers.circle.com/w3s/docs/manage-team-members
Title: Manage Team Members
Description: undefined
Keywords: undefined

The Circle developer console lets you create and add additional users to your developer account to seamlessly collaborate with your teammates. As an Admin, you can easily assign only the necessary permissions each user needs by assigning them a user role. Each role has distinct permissions and access levels, letting you set the least permissive access policies for your team members in a streamlined developer process.

##
User Roles
[](#user-roles)
The console displays two roles. You can choose one of the following supported roles:

-
Owner (Admin): When you create a developer account, you are automatically assigned an Admin role. As an owner, you will have all the same permissions as an Admin, except that this user cannot be removed, and their role cannot be modified. It is highly recommended that a person of significant control has this role. The person of significant control can be modified by contacting customer support.

-
Admin: Administrators have full control and can perform all actions within the console, including adding and managing users, updating admin settings, managing API keys, and using the smart contract platform in the developer console.

-
View-only: These users have limited permissions and may only edit their personal account information. They can view almost everything in the console (see the permissions table below for more details), but they cannot make any changes or perform any actions within the console. This role is ideal for team members who need access to the account for observation or reporting purposes but do not need editing capabilities.

###
Roles and Feature Permissions
[](#roles-and-feature-permissions)
Feature	Admin	View-only
Upgrade to Mainnet	Edit	Not visible
Toggle Between Testnet & Mainnet	Edit	Edit
API Keys	Edit	View
API Logs	View*	View
Webhooks	Edit	View
Webhook Logs	View*	View
Programmable Wallets	—	—
Overview	View*	View
User-Controlled Wallets	—	—
Dashboard	View*	View
Configurator	View*	Not visible
Users	View*	View
Transactions	View*	View
Developer-Controlled Wallets	—	—
Dashboard	View*	View
Configurator	View*	Not visible
Wallets	View*	View
Transactions	View*	View
Smart Contracts	—	—
Contracts	Edit	View
Templates	Edit	View
Console Wallets	Edit	View
Gas Station	—	—
Paymasters	Edit	View
Personal Settings	—	—
My Account	Edit	Edit
Account Settings	—	—
Team (User Management)	Edit	Not visible
Billing	Edit	Not visible
Payment Methods	Edit	Not visible
*For some capabilities, the console supports only View access. You can't edit settings for these features.

##
Manage users on the Developer Console
[](#manage-users-on-the-developer-console)
As an admin user, you can add and remove users, and update user roles. This allows you to maintain control over access and permissions for your team members. Use the steps in the following sections to manage users:

-
[Add a user](#add-a-user): Learn how to add a new user to your account in the developer console.

-
[Remove a user](#remove-a-user): Find out how to remove a user from your account via the developer console.

-
[Update user roles](#update-user-roles): Discover how to modify the roles and permissions assigned to a user.

###
Add a user
[](#add-a-user)
####
1. Navigate to admin settings
[](#1-navigate-to-admin-settings)
Log in using an Admin account. To add more users, click your profile icon in the top-right corner of the page and select Admin Settings from the dropdown menu. 


####
2. Open add member modal
[](#2-open-add-member-modal)
Select Add Member to begin the invitation process.


####
3. Add member details
[](#3-add-member-details)
Enter the new user information, including email address, first name, and last name. Select View-only or Admin to set the new user’s permissions. Select Send Invite.

##📘
Email addresses must be unique across accounts

An email address that is invited must be unique across Circle accounts. If you invite an email used on another developer or Circle Mint account the user will not be able to successfully join your team.A common workaround to this issue is resolved by using plus addressing. To do this, append a plus ("+") sign and any combination of words or numbers after your email address  For example, if my email were [testuser@email.com](mailto:testuser@email.com) I would use the email address [testuser+1@email.com](mailto:testuser+1@email.com), and I would still receive emails to my original email account. Google Workspace and Microsoft 365 support plus addressing. If you do not use one of these providers, please check with your email hosting provider before using it.


####
4. User accepts invitation
[](#4-user-accepts-invitation)
The user will receive an email invitation to join your team. Email invitations will expire 7 days after they are issued. An Admin can resend an invitation from the Team page as needed.

They should follow the steps below to join:

-
Open the email invitation and locate the Join Our Team button. Click on it to proceed.

-
They will be directed to the sign-up form and will be required to review their legal first name, legal last name, and provide a password.

-
After filling out the form, click on the Create Account button to create your account.

-
Once the user successfully creates their account, they will be granted access to the developer console in your shared account.


###
Remove a user
[](#remove-a-user)
####
1. Navigate to admin settings
[](#1-navigate-to-admin-settings-1)
Navigate to Admin Settings from the profile dropdown menu. 

####
2. Select remove member
[](#2-select-remove-member)
Select the three dots to view the different actions. Select Remove member to remove this user from the account.

 

####
3. Confirm removal of user
[](#3-confirm-removal-of-user)
Confirm that you want to remove this user by selecting Remove user. This action will disable the user information and permissions in the developer console. 


###
Update user roles
[](#update-user-roles)
####
1. Select change role
[](#1-select-change-role)
From the Admin Settings screen, select the three dots next to the user whose permissions you want to change and select Change role. 


####
2. Select new role
[](#2-select-new-role)
Select the new role from the dropdown menu. 


####
3. Confirm the new role of the user
[](#3-confirm-the-new-role-of-the-user)
Confirm the new role for the user by selecting Update Role. 


Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/ios
Title: iOS SDK
Description: undefined
Keywords: undefined

The Circle iOS SDK enables your mobile application to provide user-controlled programmable wallets. By integrating this SDK, your users can securely input sensitive data like PINs or security answers. The SDK encrypts the request body using a secret key, protecting your users' information.

📘 The Web and Mobile SDKs preserve the user keyshare with the individual, giving them complete control. You must use the SDKs with the user-controlled wallet product. Additionally, the Web and Mobile SDKs support only the user-controlled wallet product.

At Circle, we understand the importance of end-to-end security for your application and the need to create a tailored and seamless user experience for your end-users. Hence, Circle’s SDK also exposes functionality for you to customize the description and layout: 

-
UI Title and Subtitle Customization: Modify the title and subtitle to reflect your brand identity or provide specific instructions.

-
Custom PIN Code Input Layout: Adjust the layout and styling of the PIN code input field to align with your application's design guidelines.

-
Question List Configuration: Set the list of security questions displayed in the User Wallet UI, allowing users to choose from a predefined set.

-
SDK Initialization: Initialize the Web SDK by setting the endpoint server, ensuring seamless communication between your application and Circle’s services.

-
Predefined Error Messages: Customize the error messages displayed to users, providing a more personalized experience and guidance.

-
ChallengeID Acceptance and Operation Retrieval: Easily accept the challengeID and retrieve any relevant operations within the SDK.

💡Tip: See the [iOS SDK UI Customization API](/w3s/docs/ios-sdk-ui-customization-api) article about customizing the SDK.

##
Install the SDKs
[](#install-the-sdks)
The iOS SDK supports:

-
iOS 13.0+

-
macOS 12.5+

-
Xcode 14.1+

*Earlier versions are not supported.

###
CocoaPods (recommended)
[](#cocoapods-recommended)
CocoaPods is a dependency manager for Cocoa projects. You can install it with the following command:

Terminal

$ brew install cocoapods
How to install Homebrew in MacOS: [Link](https://mac.install.guide/homebrew/3.html)

To integrate into your Xcode project using CocoaPods, specify it in your Podfile:

Podfile

source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/circlefin/w3s-ios-sdk-podspecs.git'

target '<Your Target Name>' do  
  pod 'CircleProgrammableWalletSDK'
end
Then, run the following command:

Terminal

$ pod install
###
Manual
[](#manual)
You can also manually set up the iOS SDK by downloading it from [GitHub: circlefin/w3s-ios-sdk](https://github.com/circlefin/w3s-ios-sdk).

##
SDK architecture
[](#sdk-architecture)
You must use Web, iOS, or Android SDKs to access the user-controlled Programmable Wallet product. The SDK secures, manages, and communicates with your server to ensure your user’s keyshare, always stays with them and is not exposed to your servers.

To learn more, see [SDK Architecture for User-Controlled Wallets](/w3s/docs/sdk-architecture-for-user-controlled-wallets).


##
SDK API references
[](#sdk-api-references)
###
WalletSdk
[](#walletsdk)
Public Methods
/// Singleton
public static let shared = WalletSdk()
/// Set SDK configuration
/// - Parameter configuration: Configure the Circle endpoint for SDK to connect and other settings.
/// - Throws: An error is thrown while the configuration is invalid.

public func setConfiguration(_ configuration: Configuration)
/// Execute the operations by challengeId and call the completion after sending the request to the Circle endpoint.

public func execute(userToken: String, secretKey: String, challengeIds: [String], completion: ExecuteCompletion? = nil)
/// Set SDK LayoutProvider
/// - Parameter provider: WalletSdkLayoutProvider

public func setLayoutProvider(_ provider: WalletSdkLayoutProvider)
/// Set messenger for custom error messages
/// - Parameter messenger: ErrorMessager

public func setErrorMessenger(_ messenger: ErrorMessenger)
/// Set SDK delegate
/// - Parameter delegate: WalletSdkDelegate

public func setDelegate(_ delegate: WalletSdkDelegate)
/// Set up biometrics to protect PIN code
/// - Parameters:
/// - userToken: User token
/// - encryptionKey: Encryption key
/// - completion: The completion handler that will be called when biometrics setup is complete.
public func setBiometricsPin(userToken: String, encryptionKey: String, completion: ExecuteCompletion? = nil)
####
WalletSdk.Configuration
[](#walletsdkconfiguration)
public struct Configuration

Fields	
String	endPoint
Init with Circle endpoint. SDK will connect to this endpoint.
String	appId
AppID from Circle Web3 Services Console
SettingsManagement	settings

Configure other settings and features
####
SettingsManagement
[](#settingsmanagement)
Swift

public struct SettingsManagement {
        
    /// Enable biometrics to protect PIN code
    let enableBiometricsPin: Bool
        
    public init(enableBiometricsPin: Bool = false)
}

##💡
Notice:

Important: In any app that uses biometrics, include the [NSFaceIDUsageDescription](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW75) key in the app's Info.plist file. Without this key, the system won’t allow your app to use biometrics (Face ID).

####
ExecuteCompletion
[](#executecompletion)
public typealias ExecuteCompletion = ((ExecuteCompletionStruct) -> Void)

####
ExecuteCompletionStruct
[](#executecompletionstruct)
Swift

public struct ExecuteCompletionStruct {
    public let challenges: [String]
    public let result: Result<ExecuteResult, ApiError>
    public let onErrorController: UINavigationController?
    public let onWarning: ExecuteWarning?
}
- When receiving an error or warning, we will not close the current page by default.
- You will get the onErrorController in the callback, and then you can decide to dismiss/push the page by your error-handling strategy.
####
ExecuteResult
[](#executeresult)
Swift

public struct ExecuteResult {
  
  /// The type of the operation that the challenge represents.
  public let resultType: ChallengeType
  
  /// The execute result
  public let status: ChallengeStatus
  
  /// The data of the execution. (optional)
  public private(set) var data: ExecuteResultData? = nil
}
####
ChallengeType
[](#challengetype)
Swift

public enum ChallengeType: String, Decodable {
  case SET_PIN
  case CHANGE_PIN
  case RESTORE_PIN
  case SET_SECURITY_QUESTIONS
  case SET_BIOMETRICS_PIN
  case CREATE_WALLET
  case CREATE_TRANSACTION
  case ACCELERATE_TRANSACTION
  case CANCEL_TRANSACTION
  case CONTRACT_EXECUTION
  case SIGN_MESSAGE
  case SIGN_TYPEDDATA
  case UNKNOWN
}
####
ChallengeStatus
[](#challengestatus)
Swift

public enum ChallengeStatus: String, Decodable {
  case PENDING
  case IN_PROGRESS
  case COMPLETE
  case FAILED
  case EXPIRED
}
####
ExecuteResultData
[](#executeresultdata)

public struct ExecuteResultData: Decodable {
  
    /// The signature for SIGN_MESSAGE and SIGN_TYPEDDATA (optional)
    public let signature: String?
}
####
ExecuteWarning
[](#executewarning)
Swift

public struct ExecuteWarning {

    /// The warning type of the operation (the challenge execution is success)
    public let warningType: WarningType

    /// Description of the warning type
    public var warningString: String
}
####
WarningType
[](#warningtype)
Swift

public enum WarningType: Int {

    // Biometrics

    case deviceNotSupportBiometrics = 155709
    case biometricsKeyPermanentlyInvalidated = 155710   // For Android
    case biometricsUserSkip = 155711
    case biometricsUserDisableForPin = 155712
    case biometricsUserLockout = 155713                 // For Android
    case biometricsUserLockoutPermanent = 155714
    case biometricsUserNotAllowPermission = 155715
    case biometricsInternalError = 155716
}
####
ApiError
[](#apierror)
public struct ApiError

Fields	
ApiError.ErrorCode	errorCode
String	errorString /// Error string from the SDK
String	displayString /// Error string for UI display
ApiError.ErrorCode

public enum

Enum Cases

case unknown = -1  
case success = 0  
case apiParameterMissing = 1  
case apiParameterInvalid = 2  
case forbidden = 3  
case unauthorized = 4  
case retry = 9  
  
case walletIdNotFound = 156001  
case tokenIdNotFound = 156002  
case transactionIdNotFound = 156003  
case walletSetIdNotFound = 156004  
  
case notEnoughFounds = 155201  
case notEnoughBalance = 155202  
case exceedWithdrawLimit = 155203  
case minimumFundsRequired = 155204  
case invalidTransactionFee = 155205  
case rejectedOnAmlScreening = 155206  
case tagRequired = 155207  
...
WalletSdkLayoutProvider

Set WalletSdkLayoutProvider for layout customization.

Swift

public protocol WalletSdkLayoutProvider: AnyObject {
  /// Set security question list
  ///
  /// - Returns: [SecurityQuestion]
  func securityQuestions() -> [SecurityQuestion]

  /// Set security question required count (default is 2), used in SecurityQuestionsViewController
  ///
  /// - Returns: Int
  func securityQuestionsRequiredCount() -> Int

  /// Set security confirm item list
  ///
  /// - Returns: [SecurityConfirmItem]
  func securityConfirmItems() -> [SecurityConfirmItem]

  /// Set local and remote images
  ///
  /// - Returns: ImageStore
  func imageStore() -> ImageStore

  /// Set theme font programmatically
  ///
  /// - Returns: ThemeFont
  func themeFont() -> ThemeConfig.ThemeFont?

  /// Set the date format for display date strings. (Default is "yyyy-MM-dd")
  /// Reference: https://stackoverflow.com/a/52297497
  ///
  /// - Returns: Date format string
  func displayDateFormat() -> String
}
SecurityQuestion

public struct SecurityQuestion

Fields	
String	title

The question string
SecurityQuestion.InputType	inputType

The input type of the question.
SecurityQuestion.InputType
public enum InputType

Enum Cases

text  
datePicker
...
SecurityConfirmItem
public struct SecurityConfirmItem

Fields	
UIImage?	Image
The drawable resource ID for the introduction item.
URL?	url

The remote image url
String	Text

The Text for the introduction item.
ImageStore
public struct ImageStore

Fields	
[Img: UIImage]	local
The images from local
[Img: URL]	remote

The images from remote
ImageStore.Img
public enum Img

Enum Cases


naviClose
naviBack

securityIntroMain
selectCheckMark
dropdownArrow
errorInfo
securityConfirmMain

biometricsAllowMain

showPin
hidePin
####
WalletSdkDelegate
[](#walletsdkdelegate)
> Methods for managing CircleProgrammableWalletSDK process and customizing controllers.

Swift

public protocol WalletSdkDelegate: AnyObject {
  /// Tells the delegate that the SDK is about to be present the controller.
  /// You can customize the layout as you wish dynamically.
  ///
  /// - Parameter controller: The UIViewController to be presented
  func walletSdk(willPresentController controller: UIViewController)

  /// Tells the delegate that the forget PIN button was selected in the controller
  ///
  /// - Parameter controller: Current controller
  /// - Parameter onSelect: Void
  func walletSdk(controller: UIViewController, onForgetPINButtonSelected onSelect: Void)
}

####
ErrorMessenger
[](#errormessenger)
> Define the customized error description. All error codes are listed in ApiError.ErrorCode.

Swift

public protocol ErrorMessenger {
  func getErrorString(_ code: ApiError.ErrorCode) -> String?
}
###
Sample Code
[](#sample-code)
Set endpoint

Swift

let configuration = WalletSdk.Configuration(endPoint: endPoint, appId: appId)

do {
    try WalletSdk.shared.setConfiguration(configuration)
} catch {
    // error handling
}

Set endpoint and enable biometrics

Swift

let settings: WalletSdk.SettingsManagement = .init(enableBiometricsPin: true)
let configuration = WalletSdk.Configuration(endPoint: endPoint, appId: _appId, settingsManagement: settings)

do {
    try WalletSdk.shared.setConfiguration(configuration)
} catch {
    // error handling
}

Execute

Swift

Wallets.shared.execute(userToken: "USERTOKEN",
                       encryptionKey: "ENCRYPTIONKEY",
                       challengeIds: ["challengeId_1", "challengeId_2"])

Set Biometrics Pin

Swift

WalletSdk.shared.setBiometricsPin(userToken: "USERTOKEN",
                                  encryptionKey: "ENCRYPTIONKEY")
WalletSdkLayoutProvider

Swift

class SomeClass: WalletSdkLayoutProvider {

    func securityQuestions() -> [SecurityQuestion] {
        return [...]
    }

    func securityConfirmItems() -> [SecurityConfirmItem] {
        return [...]
    }

    func imageStore() -> ImageStore {
        let local: [ImageStore.Img: UIImage] =  [...]
        let remote: [ImageStore.Img : URL] = [...]
        return ImageStore(local: local, remote: remote)
    }

    func displayDateFormat() -> String {
        return "dd/MM/yyyy"
    }
}

WalletSdk.shared.setLayoutProvider(SomeClass())

Set Error Messages

Swift

class CustomErrorMessenger: ErrorMessenger {

    func getErrorString(_ code: ApiError.ErrorCode) -> String? {
        switch code {
        case .pinCodeNotMatched:
            return "The code you entered is not the same as the first one."

        case .insecurePinCode:
            return "Your PIN can’t have repeating or consecutive numbers."

        case .hintsMatchAnswers:
            return "Your hint can’t be the same as the answer."

        default:
            return nil
        }
    }
}

WalletSdk.shared.setErrorMessenger(CustomErrorMessenger())

Confirm the delegate for customizing the UI

Swift

class SomeClass: WalletSdkDelegate {
func walletSdk(willPresentController controller: UIViewController) {
        
// You can customize the controller's UI here

        if let vc = controller as? SecurityIntrosViewController {
            vc.introImageView.sd_setImage(with: URL(string: "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png"), placeholderImage: UIImage(named: "imangeName"))
            vc.introLink = "https://www.google.com"
            vc.introDescLabel.text = "Test Desc here"
        }
    }
}

See our sample app guides [here](/w3s/docs/sample-applications).

Updated about 1 month ago

-
[Android](/w3s/docs/android)
-
[Overview](/w3s/docs/programmable-wallets-sdk-overview)
-
[Sample Applications](/w3s/docs/sample-applications)
Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/web3-services-notifications-quickstart
Title: Notifications Quickstart
Description: undefined
Keywords: undefined

This quickstart guide helps you quickly set up notifications for Programmable Wallets. Follow this to configure a subscriber endpoint that sends a notification every time the status of resource changes. 

This guide focuses on the challenge resource but applies to any resources available in Programmable Wallet Notifications.

###
1. Expose a Subscriber Endpoint
[](#1-expose-a-subscriber-endpoint)
To receive notifications on changes in resource status, you must expose a publicly accessible subscriber endpoint on your side. The endpoint must handle both HEAD and POST requests over HTTPS.

To quickly expose an endpoint for testing, we will use [webhook.site](https://webhook.site/) in this guide, which allows you to easily inspect, test and automate any incoming HTTPS request or e-mail directly in a web browser.

When visiting [webhook.site](https://webhook.site/) for the first time, you should see a status message that looks like the following:

##📘
Your unique URL (Please copy it from here, not from the address bar!) https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7



##📘
Record the value of the unique URL.

In the example above, the unique URL is:https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7. Use the public-facing URL you receive as you progress throughout this guide.

###
2. Subscribe to Status Notifications
[](#2-subscribe-to-status-notifications)
Now that you have a publicly accessible endpoint, you need to register your endpoint as a subscriber to webhook notifications by doing the following:

- Click Webhooks in your Circle Developer account. 
- Click Add a Webhook in the upper right corner.
- Enter your endpoint URL from above. It will be similar to the earlier example:https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7.
- Click Add Webhook.
- At this stage, you should receive a Hello World test notification indicating you have subscribed to your endpoint successfully:
JSON

{  
  "subscriptionId": "00000000-0000-0000-0000-000000000000",  
  "notificationId": "00000000-0000-0000-0000-000000000000",  
  "notificationType": "webhooks.test",  
  "notification": {  
    "hello": "world"  
  },  
  "timestamp": "2023-07-20T16:18:40.816010685Z",  
  "version": 2  
}
Now you have a sample local environment ready to receive transaction and challenge status change notifications!

###
3. Initiate a Challenge Flow to Receive a Notification
[](#3-initiate-a-challenge-flow-to-receive-a-notification)
To observe a notification message on the status of a challenge, initiate the creation of a wallet (for example, by following the [Programmable Wallets User-Controlled Wallets Tutorial](/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart) for creating a user-controlled wallet).

Once you successfully initiate a challenge payment, you should see a notification message on your local server shell that looks like the following:

Sample Challenge Notification:

JSON

{
  "subscriptionId": "a68cd974-d209-46cd-8dbf-b7a081fbd627",
  "notificationId": "eaa4a4fe-24b8-4329-a4c6-6dfd557dbcb2",
  "notificationType": "challenges.initialize",
  "notification": {
    "id": "9c0a1991-735a-5140-8996-7b95720c5e55",
    "userId": "2a054cd1-3124-4aa7-b1f0-86c4a4df995c",
    "type": "INITIALIZE",
    "status": "COMPLETE",
    "correlationIds": [
      "01890792-a199-77bc-b005-b229f81824fa"
    ],
    "errorCode": 0,
    "errorMessage": ""
  },
  "timestamp": "2023-06-29T14:33:17.785131449Z",
  "version": 2
}
###
4. Digital Signature Verification
[](#4-digital-signature-verification)
- Every webhook notification is digitally signed by an asymmetric key. In the headers of each webhook, you can find:
- X-Circle-Signature: a header containing the digital signature generated by Circle
- X-Circle-Key-Id: a header containing the UUID you need to retrieve the relevant public key

# Headers
`X-Circle-Key-Id: “879dc113-5ca4-4ff7-a6b7-54652083fcf8”`  
`X-Circle-Signature: “MEYCIQCA9EvPbdEJiy7Cw0eY+KQZA/oFi5ZEInPs8CYpyaJexgIhAKtRNnDz9QRQmFKx8QFrvawp+8b9Bs2dQ03xD+XaWVDE”`
- Using the X-Circle-Key-Id value, query the following endpoint to retrieve the public key and algorithm used to sign the message: [GET /v2/notifications/publicKey/{keyId)](/w3s/reference/getnotificationsignature)
cURL

# Replace ${YOUR_API_KEY} with your API key
# Replace ${PUBLIC_KEY_ID} with your public key id
curl --request GET \
     --url 'https://api.circle.com/v2/notifications/publicKey/${PUBLIC_KEY_ID)' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer $ENV_API_KEY:ID:SECRET$' \
If successful, you will receive a response with the following shape

Response Body

{  
  "data": {  
    "id": "879dc113-5ca4-4ff7-a6b7-54652083fcf8",  
    "algorithm": "ECDSA_SHA_256",  
    "publicKey": "MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAESl76SZPBJemW0mJNN4KTvYkLT8bOT4UGhFhzNk3fJqf6iuPlLQLq533FelXwczJbjg2U1PHTvQTK7qOQnDL2Tg==",  
    "createDate": "2023-06-28T21:47:35.107250Z"  
  }
}
- Use the public key and algorithm to verify the integrity of the webhook’s payload against the signature found in X-Circle-Signature.
Note: Please make sure the webhook notification is properly formatted JSON string prior to verifying it

Response Body

{
  "subscriptionId": "890a8cae-46bd-40ad-bd7f-2e49b8dea9da",
  "notificationId": "0bdd3e4b-5070-4530-8ed4-2e2e4c9fd2f0",
  "notificationType": "webhooks.test",
  "notification": {
    "hello": "world"
  },
  "timestamp": "2023-07-12T04: 02: 28.555562821Z",
  "version": 2
}
The following code sample demonstrates how to verify the X-Circle-Signature:

Python
Node.js
Java

import base64
import json

from cryptography.exceptions import InvalidSignature
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import ec

# Load the public key from the base64 encoded string
public_key_base64 = "MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAESl76SZPBJemW0mJNN4KTvYkLT8bOT4UGhFhzNk3fJqf6iuPlLQLq533FelXwczJbjg2U1PHTvQTK7qOQnDL2Tg=="
public_key_bytes = base64.b64decode(public_key_base64)
public_key = serialization.load_der_public_key(public_key_bytes)

# Load the signature you want to verify
signature_base64 = "MEQCIBlJPX7t0FDOcozsRK6qIQwik5Fq6mhAtCSSgIB/yQO7AiB9U5lVpdufKvPhk3cz4TH2f5MP7ArnmPRBmhPztpsIFQ=="
signature_bytes = base64.b64decode(signature_base64)

# Load and format the message you want to verify
message = "{\n\"subscriptionId\":\"00000000-0000-0000-0000-000000000000\",\"notificationId\":\"00000000-0000-0000-0000-000000000000\",\"notificationType\":\"webhooks.test\",\"notification\":{\"hello\":\"world\"},\"timestamp\":\"2024-01-26T18:22:19.779834211Z\",\"version\":2}"
json_data = json.loads(message)
formatted_json = json.dumps(json_data, separators=(',', ':'))

# Verify the signature
try:
    public_key.verify(
        signature_bytes,
        formatted_json.encode(),
        ec.ECDSA(hashes.SHA256())
    )
    print("Signature is valid.")
except InvalidSignature:
    print("Signature is invalid.")
###
5. IP Accesslist Check
[](#5-ip-accesslist-check)
To ensure the security of your integration, only trust Circle webhook notifications from the following IP addresses.


54.243.112.156  
100.24.191.35  
54.165.52.248  
54.87.106.46
Updated about 2 months ago

Congratulations, you have received your first notification for a Programmable Wallets challenge!

-
[Notification Flows](/w3s/docs/notification-flows)

URL: https://developers.circle.com/w3s/docs/circle-developer-account
Title: Circle Developer Account
Description: undefined
Keywords: undefined

The Circle Developer account allows you to manage your use of Circle’s Web3 Services, such as your Programmable Wallets activity.

Video Tutorial: Walkthrough of the Developer Dashboard
##
Sign Up for a Developer Account
[](#sign-up-for-a-developer-account)
Sign up for the Circle Developer dashboard here: [console.circle.com/signup](https://console.circle.com/signup). 

Within the Circle Developer account, you can create and update [API Keys](/w3s/docs/web3-services-api-keys-authentication), [subscribe for activity notifications](/w3s/docs/web3-services-notifications-quickstart), view your API logs, and more! Anyone can sign up for a Circle Developer account within minutes. All you need is your name, email, and password! 


##
Creating a Web3 Services API Key
[](#creating-a-web3-services-api-key)
To learn more about Circle’s API keys and authentication, refer to the guide on [API Keys & Authentication](/w3s/docs/web3-services-api-keys-authentication).

Using the steps below, you can create and register an API key from the Circle Developer account. 

-
Sign up with your email address at: [console.circle.com/signup](https://console.circle.com/signup)

-
Navigate to API Keys from the left sidebar and click CREATE A KEY. 


-
Enter the name of your API Key and choose the type of API Key you want to create:

-
Standard: Permits the API key read/write to all APIs. This will also include access to newly introduced APIs.

-
Restricted Access: The API keys can be restricted to control their usage and access privileges based on specific Products/Services. When opting for restricted API keys, you can customize the permissions associated with each key as follows:

-
Select the Product/Service for which the key will have permission:

- Webhooks: Refers to the endpoints related to [Webhook Subscription APIs](/w3s/reference/createsubscription).
- Programmable Wallets: Includes all endpoints for [User-Controlled wallets](/w3s/reference/createuser) and Developer-Controlled wallets.
- Smart Contract Platform: Encompasses endpoints related to the [Smart Contract Platform](/w3s/reference/importcontract).
-
Furthermore, you can define the specific permissions for each product/service assigned to the API key:

- No permission: The API key will not have the ability to invoke any APIs for the specified Product/Service.
- Read: The API key will be granted access to read-only GET endpoints for the specified Product/Service.
- Read/Write: The API key will have full access to all endpoints, enabling both reading and writing operations for the specified Product/Service.


##📘
Tip: You may also want to add your IP address(es) or range(s) to the IP ALLOW LIST to increase security.

- You will receive confirmation that your API key has been generated.
- You can click SHOW to see the API key details.
You’re ready to start using your Web3 Services API key!

Updated about 2 months ago

Let's keep moving with idempotency. Continue the User-Controlled Wallets Quickstart:

-
[Idempotent Requests](/w3s/docs/idempotent-requests)

URL: https://developers.circle.com/w3s/docs/sdk-architecture-for-user-controlled-wallets
Title: SDK Architecture for User-Controlled Wallets
Description: undefined
Keywords: undefined

Circle designed the Programmable Wallets SDK to guarantee that end-users have complete control over the private keys of their [user-controlled wallets](/w3s/docs/programmable-wallets-infrastructure-models). Users create their own PIN code and security questions upon account creation to ensure their access, control, and recovery processes are in place to use their wallets. 

Go to one of the following guides to set up your local environment and review the SDK API references:

-
[Web SDK](/w3s/docs/web)

-
[iOS SDK](/w3s/docs/ios)

-
[Android SDK](/w3s/docs/android)

-
[React Native SDK](https://developers.circle.com/w3s/docs/react-native-sdk)

##
What is a PIN?
[](#what-is-a-pin)
The user’s PIN is a 6-digit code chosen by the user during the user initialization process. It is required to authorize interactions with their wallet. The PIN is encrypted during input on the user’s device, ensuring developers never have access to user PINs. 

Users provide their PIN to access and authorize the movement of assets. It is vital that a user remember their PIN code and never share it with another party. 

If the user inputs the incorrect PIN more than three times, the PIN locks, and they will need to wait 30 minutes to unlock.

##
What are Security Questions?
[](#what-are-security-questions)
The user’s security questions are set during the initialization process, allowing the user to input answers to their chosen questions, which can be used in the wallet recovery process. If users lose their PIN code, they can unfreeze their wallets by going through the recovery process, which requires them to answer the previously chosen security questions correctly. 

If the user inputs incorrect security answers more than three times, the recovery locks, and they must wait 30 minutes to unlock.

##
SDK Architecture
[](#sdk-architecture)

The SDK infrastructure uses the following design flow:

-
A user performs a request within their user-controlled wallet on the developer’s platform. For example, a user initiates a USDC transfer to an external wallet.

-
The developer requests a user’s transaction through Circle’s Programmable Wallets RESTful APIs.

-
Circle’s RESTful APIs return a response containing a challenge ID. The challenge must be completed to execute the requested transaction.

-
The developer passes the challenge ID to the Programmable Wallets SDK, prompting users to input their PIN code to complete the challenge.

-
The user completes the challenge by entering their PIN code, and the encrypted input is delivered to Circle’s SDK APIs.

-
Circle delivers a callback notification to the developer (or the developer queries the corresponding GET endpoint for the given transaction) to receive status updates on the requested transaction.

-
Upon completion of the request, the developer notifies the end-user within the developer’s application.

Updated 23 days ago


URL: https://developers.circle.com/w3s/docs/developer-controlled-transfer-tokens-across-wallets
Title: Transfer Tokens from Wallet to Wallet
Description: undefined
Keywords: undefined

This guide outlines transferring tokens across two developer-controlled wallets. If you do not already have two wallets, go to the [create your first wallets](/w3s/docs/developer-controlled-wallet-quickstart) tutorial, where we guide you through it step by step. Also, if you do not have any tokens in your wallet, go to the [inbound transfer](/w3s/docs/developer-controlled-receive-an-inbound-transfer) guide.

Throughout this guide, you will utilize API requests. These can be made using cURL requests inline or Circle's API references. For instructions on navigating the API references, please consult this [guide](/w3s/docs/api-references).

Note: The following tutorial uses the Polygon Amoy [Testnet](https://en.wikipedia.org/wiki/Testnet) for illustration purposes. In production, you can create and use programmable wallets that support crypto tokens with the following blockchains and standards:

- Ethereum (ETH), Polygon (MATIC), and Avalanche (AVAX), both Testnet and Mainnet
- ERC-20 tokens
- ERC-721 and ERC-1155 NFTs (non-fungible tokens)
###
1. Check the Wallet's Balance
[](#1-check-the-wallets-balance)
Before sending funds across wallets, you will need to do two more things:

- Ensure the wallet has received the USDC or MATIC tokens.
- Obtain the token ID for the received USDC or MATIC. The token ID is an assigned identifier that will be used for the token transfer in the upcoming step.
This can be done by making a request to [GET /wallets/{id}/balances](/w3s/reference/listwalletballance-1) providing the ID of Wallet One from the previous step.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

const response = await circleDeveloperSdk.getWalletTokenBalance({
  id: 'ce714f5b-0d8e-4062-9454-61aa1154869b'
});
Response Body

{
  "data": {
    "tokenBalances": [
      {
        "token": {
          "id": "e4f549f9-a910-59b1-b5cd-8f972871f5db", // HERE
          "blockchain": "MATIC-AMOY",
          "name": "Polygon-Amoy",
          "symbol": "MATIC-AMOY",
          "decimals": 18,
          "isNative": true,
          "updateDate": "2023-06-29T02:37:14Z",
          "createDate": "2023-06-29T02:37:14Z"
        },
        "amount": "0",
        "updateDate": "2023-11-17T16:54:55Z"
      },
      {
        "token": {
          "id": "7adb2b7d-c9cd-5164-b2d4-b73b088274dc", // HERE
          "blockchain": "MATIC-AMOY",
          "tokenAddress": "0x9999f7fea5938fd3b1e26a12c3f2fb024e194f97",
          "standard": "ERC20",
          "name": "USD Coin",
          "symbol": "USDC",
          "decimals": 6,
          "isNative": false,
          "updateDate": "2023-10-18T14:29:44Z",
          "createDate": "2023-10-18T14:29:44Z"
        },
        "amount": "10",
        "updateDate": "2023-11-17T16:54:55Z"
      }
    ]
  }
}
###
2. Gas Fees (EOA Wallets Only)
[](#2-gas-fees-eoa-wallets-only)
###
3. Transfer Tokens
[](#3-transfer-tokens)
Now, you can make on-chain transfers from wallet to wallet. This is done by making a request to [POST /developer/transactions/transfer](/w3s/reference/createdevelopertransactiontransfer) with the following request body parameters.

- idempotencyKey: Universally unique identifier (UUID v4) idempotency key. This key is utilized to ensure exactly-one execution of a mutating requests.
- tokenId: Token ID for the token that will be transferred. In this case, you will provide the MATIC token ID from the previous step.
- walletId: Wallet ID of the source wallet where the token amount will be transferred from. In this case, you will provide Wallet One's ID wallets[0].id from step one.
- destinationAddress: Wallet address that will receive the tokens. In this case, use Wallet Two's addresswallets[1].id from step one.
- amounts: The amount of tokens to be sent. In this case, we will use a minimal amount.
- feeLevel: A dynamic blockchain fee level setting (LOW, MEDIUM, or HIGH) that will be used to pay gas for the transaction. Calculated based on network traffic, supply of validators, and demand for transaction verification. Estimates for each fee level can be obtained through the estimate fee API call.
- entitySecretCiphertext: The Entity Secret Ciphertext is an RSA encryption value generated from your Entity Secret and Circle's public key. This asymmetrically encrypted value is sent in API requests like wallet creation or transaction initiation to ensure secure critical actions. This process enables secure usage of the Entity Secret to ensure it cannot be easily accessed or misused.
Node.js
cURL

const response = await circleDeveloperSdk.createTransaction({
  walletId: 'ce714f5b-0d8e-4062-9454-61aa1154869b',
  tokenId: 'e4f549f9-a910-59b1-b5cd-8f972871f5db',
  destinationAddress: '0xc90e058234d4b2db799d787a855ec68d801a53a3',
  amounts: ['.01'],
  fee: {
    type: 'level',
    config: {
      feeLevel: 'MEDIUM'
    }
  }
});
Response Body

{
  "data": {
    "id": "1af639ce-c8b2-54a6-af49-7aebc95aaac1",
    "state": "INITIATED"
  }
}
###
4. Check the Transfer State
[](#4-check-the-transfer-state)
Transfers are a type of blockchain and, therefore, are asynchronous. To ensure the transfer has successfully reached its destination, make a request to [GET /transactions/{id}](/w3s/reference/gettransaction-1) using the ID from the transfer in the previous step.

Node.js
cURL

  const response = await circleDeveloperSdk.getTransaction({ 
    id: '1af639ce-c8b2-54a6-af49-7aebc95aaac1'
  });
Response Body

{
  "data": {
    "transaction": {
      "id": "1af639ce-c8b2-54a6-af49-7aebc95aaac1",
      "blockchain": "MATIC-AMOY",
      "tokenId": "e4f549f9-a910-59b1-b5cd-8f972871f5db",
      "walletId": "ce714f5b-0d8e-4062-9454-61aa1154869b",
      "sourceAddress": "0xf5c83e5fede8456929d0f90e8c541dcac3d63835",
      "destinationAddress": "0xc90e058234d4b2db799d787a855ec68d801a53a3",
      "transactionType": "OUTBOUND",
      "custodyType": "DEVELOPER",
      "state": "COMPLETE",
      "amounts": [
        "0.01"
      ],
      "nfts": null,
      "txHash": "0xc0337cad6164f22eeb37af3e21488f77e7bc5ba4a92a2293e0a8bb8beaa61c88",
      "blockHash": "0xbcaf5d0dc5d50250a137808ca81eb4abebf13a639398c44660b35e387e5ad2d3",
      "blockHeight": 38738377,
      "networkFee": "0.000035910000189",
      "firstConfirmDate": "2023-08-07T14:41:02Z",
      "operation": "TRANSFER",
      "feeLevel": "MEDIUM",
      "estimatedFee": {
        "gasLimit": "21000",
        "baseFee": "0.000000016",
        "priorityFee": "1.709999993",
        "maxFee": "1.710000025"
      },
      "refId": "",
      "abiParameters": null,
      "createDate": "2023-08-07T14:40:54Z",
      "updateDate": "2023-08-07T14:42:04Z"
    }
  }
}
Once the transaction.state is COMPLETE you can rest assured that the token has moved from wallet one to wallet two.

You can also check the transaction using the txHash on [Polygon Amoy explorer](https://www.oklink.com/amoy) and inspect the balance of each wallet using [GET /wallets/{id}/balances](/w3s/reference/listwalletballance-1).

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/reference/listwallets-1
Title: List wallets
Description: undefined
Keywords: undefined

TIME	STATUS	USER AGENT	
Make a request to see history.
address
string
Filter by the Address of the wallet.


blockchain
string
Filter by blockchain.


walletSetId
string
Filter by the wallet set the wallet belongs to.


refId
string
Filter by the reference identifier set on the wallet.


from
date-time
Queries items created since the specified date-time (inclusive) in ISO 8601 format.


to
date-time
Queries items created before the specified date-time (inclusive) in ISO 8601 format.


pageBefore
string
A collection ID value used for pagination.

It marks the exclusive end of a page. When provided, the collection resource will return the next n items before
the id, with n being specified by pageSize.

The items will be returned in the natural order of the collection.

The resource will return the first page if neither pageAfter nor pageBefore are specified.

SHOULD NOT be used in conjuction with pageAfter.


pageAfter
string
A collection ID value used for pagination.

It marks the exclusive begin of a page. When provided, the collection resource will return the next n items after
the id, with n being specified by pageSize.

The items will be returned in the natural order of the collection.

The resource will return the first page if neither pageAfter nor pageBefore are specified.

SHOULD NOT be used in conjuction with pageBefore.


pageSize
integer
Limits the number of items to be returned.

Some collections have a strict upper bound that will disregard this value. In case the specified value is higher
than the allowed limit, the collection limit will be used.

If avoided, the collection will determine the page size itself.


#
200
Wallets found

#
Default
Error response

Updated 6 months ago

Did this page help you?

Yes

No

Shell

Node

Ruby

PHP

Python


1
curl --request GET \
2
     --url 'https://api.circle.com/v1/w3s/wallets?pageSize=10' \
3
     --header 'accept: application/json'
Click Try It! to start a request and see the response here! Or choose an example:
application/json

200

Default

URL: https://developers.circle.com/w3s/docs/smart-contract-platform
Title: Smart Contract Platform
Description: undefined
Keywords: undefined

The Smart Contract Platform (SCP) enables developers to utilize on-chain smart contracts in their apps intuitively. It allows them to facilitate the creation, deployment, and execution of smart contracts via the Developer Console or APIs, ensuring flexibility and ease of integration. The goal of SCP is to make it easy for developers to use smart contracts to get real-world utility into their apps.

Some examples of what developers can do using the SCP:

- Deploying Custom Contracts: Bring your custom contracts to life by efficiently deploying them onto the blockchain network, expanding the functionality and capabilities of your application.
- Deploying NFT Contracts: Easily deploy NFT contracts and programmatically mint unique tokens for end-users, enabling the creation of digital assets and collectibles.
- Creating On-Chain Loyalty Programs: Establish on-chain loyalty programs within your applications, allowing users to earn and redeem rewards seamlessly.
- Interacting with DeFi Projects: Effortlessly integrate with popular DeFi projects like Uniswap, enabling users to interact with decentralized exchanges and other financial services with just a few clicks.
- Integrating Circle Contracts: Seamlessly incorporate Circle contracts, such as CCTP (Circle Contract Transfer Protocol), into your application, providing secure and efficient transfer functionalities.
##
Explore smart contracts
[](#explore-smart-contracts)
The Web3 Services console allows developers to view the details of any smart contract. Developers can import a smart contract by adding the contract's address and chain. Once imported, developers can explore the contract, view the ABI functions, read the source code, and see all transactions associated with the contract.

##📘
Coming soon

We are working on introducing the Magic Decoder, which will decode common interfaces of a contract for you to use via the console or in your app. We are also developing contract event features and exporting code in your preferred integrated development environment (IDE).

##
Deploy smart contracts
[](#deploy-smart-contracts)
Developers can deploy smart contracts using SCP by writing their contracts or using pre-vetted templates provided by Circle. To deploy a contract, developers need to create a Developer-controlled wallet using Circle's Prog wallets and use it to deploy the contract across any supported chains. Deployment can be done through the console in a no-code way or programmatically using APIs.

###
Deploy a custom contract
[](#deploy-a-custom-contract)
If you have already written a smart contract on an IDE, you can deploy it by providing the compiled bytecode and ABI. For console deployment, create a console wallet (a smart contract wallet) and use it to deploy the contract on the desired chain. Include the source code, ABI, and bytecode for API deployment in the request parameters.

###
Deploy contracts with templates
[](#deploy-contracts-with-templates)
For developers unfamiliar with smart contract engineering, Templates provide code snippets to deploy contracts without writing any solidity code. These templates, curated by the Circle team and audited by third-party auditors, cover popular on-chain use cases. Fill in the required properties and deploy the contract. Templates can be deployed through the console or APIs.

Some templates that we support include:

- Token (ERC-20) contract, by Thirdweb
- NFT (ERC-721) contract, by Thirdweb
- NFT (ERC-1155) contract, by Thirdweb
What's next: We are continuously improving the deployment experience and will soon support using SCP via your favorite IDE, deploying proxy contracts, and deploying gasless contracts. We will also be launching new templates

##
Manage smart contracts
[](#manage-smart-contracts)
Once deployed, you can use the console to manage your smart contracts. This includes analytics, calling contract functions, and changing ownership. The console provides an easy way to update and manage contracts post-deployment. Developers can access analytics such as transactions and events for contracts deployed using SCP via APIs.

##
Interact with smart contracts
[](#interact-with-smart-contracts)
Interacting with smart contracts allows you to integrate existing on-chain contracts into your applications. Import the contract and explore its various functions. You can add parameters and generate API resources to interact with the contract in a straightforward manner.

Updated 24 days ago

Head over to our quickstart and deploy your first smart contract!

-
[Quickstarts](/w3s/docs/scp-quickstarts)

URL: https://developers.circle.com/w3s/docs/web3-services-sdks
Title: Web3 Services SDKs
Description: undefined
Keywords: undefined

Circle offers SDKs for client-side and server-side integration to the Web3 Services APIs. These SDKs reduce the amount of boilerplate code you write and abstract complexities to simplify the development process. You must utilize the Web, iOS, or Android SDKs to integrate user-controlled wallets into your app.


##
Server-side SDKs
[](#server-side-sdks)
Circle’s Web3 Services server-side SDKs reduce the effort required to use Circle’s REST APIs.

Use the following command to install the Node.js SDKs:

Shell

npm install @circle-fin/smart-contract-platform @circle-fin/user-controlled-wallets @circle-fin/developer-controlled-wallets --save
To learn more, see [Node.js SDKs](/w3s/docs/nodejs-sdk).

##
Web SDK
[](#web-sdk)
Circle provides the following client-side web SDK to enable integrations with Circle’s prebuilt UI components to create user wallets securely, send transactions and more. The Web SDK supports only user-controlled wallets. 

Use the following command to install the web SDK:

Shell

npm install @circle-fin/w3s-pw-web-sdk --save
To learn more, see [Web SDK](/w3s/docs/web).

##
Mobile SDKs
[](#mobile-sdks)
Circle provides the following client-side mobile device SDKs to help you create native applications for iOS and Android devices and platforms. Mobile SDKs support only user-controlled wallets.

[

##iOS SDK
Set up the client-side iOS SDK for user-controlled wallets in your mobile application.

GET STARTED

](https://developers.circle.com/w3s/docs/ios) [

##Android SDK
Set up the client-side Android SDK for user-controlled wallets in your mobile application.

GET STARTED

](https://developers.circle.com/w3s/docs/android)
##
Postman API Suite
[](#postman-api-suite)
Circle’s Postman workspace lets you quickly send requests and learn to use Web3 Services APIs.

[

##Postman API Suite
Set up the Postman suite to try out Web3 Services APIs.

GET STARTED

](https://developers.circle.com/w3s/docs/postman)
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/asynchronous-states-and-statuses
Title: Transaction States and Errors
Description: undefined
Keywords: undefined

API entities, such as transactions and challenges, are processed asynchronously after the initial POST request. 

As the request is processed, the transaction's state, or the challenge's status, will update accordingly. This update can be retrieved either by polling the associated GET endpoint or via [subscribing to notifications](/w3s/docs/web3-services-notifications-quickstart) (recommended).

###
Transaction States
[](#transaction-states)
Transactions go through various states after initiation to indicate if they’ve begun processing, are identifiable on the blockchain, complete, failed, and more. 

Specific actions, like canceling or accelerating a transaction, can only be taken when a transaction is in a specific state.

Canceling a transaction can only occur when a transaction is in the INITIATED, QUEUED, or SENT states. 

Accelerating a transaction can only occur when a transaction is in the SENT state. 

If a transaction cannot be successfully completed, it will enter the FAILED state and have an accompanying errorReason detailing the failure. 

The following state diagram shows the flow for transaction states:



The following table describes the possible states of a transaction:

State	Description
INITIATED	The transaction has been initiated. 
QUEUED	The transaction is in the processing queue.
PENDING_RISK_SCREENING	The transaction is waiting for risk screening.
SENT	The transaction has been identified in the Mempool and assigned a transaction hash. The transaction may be accelerated at this stage.
CONFIRMED	The transaction has been identified on a mined block. The transaction can no longer be canceled. 
COMPLETE	Terminal State. The transaction has successfully completed. 
CANCELED	Terminal State. The transaction has been canceled and must be re-initiated. 
FAILED	Terminal State. The transaction has failed. The transaction must be re-initiated.
DENIED	Terminal State. The platform denies the transaction. For a detailed reason, refer to the errorReason.
ACCELERATED	The transaction has been accelerated. 
###
Transaction Errors
[](#transaction-errors)
If a transaction cannot be completed and enters the FAILED state, the transaction object will have a related errorReason to describe the failure.

Error Reason	Description
ESTIMATION_ERROR	The transaction estimation failed during node execution.
INSUFFICIENT_NATIVE_TOKEN	The wallet does not have a sufficient balance of the blockchain’s native asset to cover the [gas fees](/w3s/docs/gas-fees) needed for all pending transactions.
ABI_SIGNATURE_PARAMS_MISMATCH	The signature and parameter of ABI are mismatched.
FEE_EXCEEDS_MAX_ALLOWANCE	The fee exceeds the max allowance. There is one native token fee limit to prevent any unintended transaction from being sent out.
QUERY_NFT_METADATA_ERROR	The NFT metadata cannot be found.
GAS_LIMIT_TOO_LOW	The provided fee tolerance (gasLimit) was too low to complete the requested transaction.
TRANSACTION_UNDER_PRICE	The transaction's max fee or priority fee is lower than the current lowest max fee or priority fee in mempool when mempool is full.
FAILED_ON_CHAIN	The transaction failed to be processed by the blockchain.
FAILED_REORG	The transaction was dropped because of blockchain re-org.
INTERNAL_ERROR	The transaction failed due to an internal error.
PAYMASTER_POLICY_NOT_ACTIVATED	The paymaster policy hasn't been set or activated.
PAYMASTER_POLICY_EXCEED_MAX_DAILY_TRANSACTIONS	The maximum number of daily transactions allowed by the paymaster policy has been reached. Please try again tomorrow or update the policy limits.
PAYMASTER_POLICY_EXCEED_MAX_SPEND_USD_PER_TX	The transaction exceeds the maximum allowable spend in USD as per the paymaster policy for a single transaction. Please increase the limits in your policy for such transactions to go through.
PAYMASTER_POLICY_EXCEED_MAX_SPEND_USD_DAILY	The maximum daily spending limit in USD set by the paymaster policy has been reached. Please try again tomorrow or update the policy limits.
PAYMASTER_POLICY_EXCEED_MAX_NATIVE_TOKEN_DAILY	The maximum daily spending limit for native tokens set by the paymaster policy has been reached. Please try again tomorrow or update the policy limits.
PAYMASTER_POLICY_SENDER_IN_BLOCKLIST	The sender wallet of the transaction is blocked by the paymaster policy. Update the policy blocklist to allow this user to go through.
PAYMASTER_ENTITY_RESTRICTION	At this time, your account is restricted from using Gas Station. Go to the Web3 Services [Console Paymaster](https://console.circle.com/gas-station/paymasters) page to learn more.
###
Challenge States
[](#challenge-states)
Challenges go through several statuses to indicate if it has been presented to the user, have been completed, and more. 

If a challenge cannot be successfully completed, it will enter the FAILED state, and have an accompanying errorCode and errorMessage detailing the failure.

Status	Description
PENDING	The request has been created but has not yet been approved by the user.
IN_PROGRESS	The user has approved the request, and it is being executed.
COMPLETED	The request is complete.
FAILED	The request has failed to execute. The request must be re-initiated.
EXPIRED	The request has expired. The request must be re-initiated. 
Updated 17 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/circle-programmable-wallets-an-overview
Title: Circle Web3 Services
Description: undefined
Keywords: undefined

Circle's Web3 Services offer a range of SDKs and APIs that simplify the development process and decrease the required effort to build decentralized applications. By providing pre-built tools and abstractions, Circle's Web3 Services eliminate the need for extensive learning curves, enabling developers to quickly and efficiently create applications that leverage blockchain technology.

###
Programmable Wallets
[](#programmable-wallets)
Programmable Wallets enable developers to create and embed secure wallets in their apps. With these APIs, you can specify parameters to generate purpose-built wallets tailored to the use case you're building. To learn about the product and infrastructure models, go [here](/w3s/docs/programmable-wallets).

If you're building on the blockchain where Programmable Wallets have not yet supported, Wallet Signing Service lets you implement functionality similar to Programmable Wallets. To learn more, see [Wallet Signing Service](https://developers.circle.com/w3s/docs/wallet-signing-service).


[

##User-Controlled Wallets
Embed user-controlled wallets in your web & mobile apps.

GET STARTED

](https://developers.circle.com/w3s/docs/user-controlled-initialization-and-wallet-creation-quickstart) [

##Developer-Controlled Wallets
Embed developer-controlled wallets into web and mobile apps for your users.

GET STARTED

](https://developers.circle.com/w3s/docs/developer-controlled-wallet-quickstart)
###
Smart Contract Platform
[](#smart-contract-platform)
Smart Contract Platform (SCP) enables developers to integrate smart contract functionality into their apps. Using the SCP, developers can deploy a new smart contract on-chain or interact with any smart contract using Circle's developer console and APIs. The SCP can be accessed through the web3 services console UI. To learn more about the product, go [here](/w3s/docs/smart-contract-platform).

[

##Smart Contract Platform
Deploy, import, and interact with smart contracts.

GET STARTED

](https://developers.circle.com/w3s/docs/scp-quickstarts)
###
Gas Station
[](#gas-station)
Gas Station enables developers to sponsor gas fees on behalf of their end users, eliminating the need for users to hold native tokens. By removing the burden of gas fees, Gas Station ensures a seamless and familiar application experience for users. Setting up Gas Station is simple – create a policy within the developer console, and you're good to go. Circle's Paymaster will sponsor transactions that adhere to your policy and originate from a Smart Contract Account (SCA) Programmable wallet. To learn more about the benefits of Gas Station, see [here](/w3s/docs/gas-station).

[

##Gas Station
Sponsor gas fees for your users and pay gas fees using a card on file.

GET STARTED

](https://developers.circle.com/w3s/docs/gas-station-quickstarts)
###
Circle Access Network (Beta) Coming Soon
[](#circle-access-network-beta-coming-soon)
Circle Access Network offers a seamless solution for USDC integration in your application. With easy on/off ramps and frictionless USDC access for your users, you can simplify their experience while keeping them in your application. Say goodbye to the complexities of licenses and internal know-your-customer processes while providing low-cost access to USDC.

[

##Circle Access Network (Beta)
Be one of the first to give your users direct access to USDC through embedded on/off ramps.

LEARN MORE

](https://developers.circle.com/w3s/docs/circle-access-network-beta)
Updated about 21 hours ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/synchronous-errors
Title: HTTP Errors
Description: undefined
Keywords: undefined

The Circle platform returns an [HTTP status code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) when it detects an error in an API request.

##
General error format
[](#general-error-format)
[HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) do not always provide sufficient information to determine the cause of the error, but the responses to a request typically contain additional JSON fields that provide additional details. For example, if a request contains an invalid parameter, the response includes the following:

HTTP/1.1 400 Bad RequestContent-Type: application/json

JSON

{
  "code": 2,
  "message": "API parameter invalid"
}
##
Extended error format
[](#extended-error-format)
In some cases, the response includes extended information about the cause of the error. For example, if a request doesn't include a value for a required parameter, the response resembles the following:

HTTP/1.1 400 Bad RequestContent-Type: application/json

JSON

{
  "code": 2,
  "message": "API parameter invalid",
  "errors": [
    {
      "error": "required",
      "message": "fail to bind request to CreateWalletSetRequest: EOF",
      "location": "field1",
      "invalidValue": "null",
      "constraints": { }
    }
  ]
}
##
Common Web3 Services error messages
[](#common-web3-services-error-messages)
This section describes some common extended error messages you might encounter using Web3 Services APIs.

###
General
[](#general)
The following errors result from malformed request.

Error Code	HTTP code	Error Message	Description
-1	400	Something went wrong	An unknown error occurred while processing the API request, likely an invalid parameter or API key.
3	403	Forbidden	The API key used does not have access to the requested endpoint. 
###
400 Bad Request
[](#400-bad-request)
A 400 HTTP status code typically results when the request includes an invalid parameter. The following table includes additional error messages associated with error code 2.

Error Code	HTTP code	Error Message	Description
2	400	Invalid Entity	There is an error in the JSON format passed in the request.
2	400	Fail to bind request to parameter: invalid UUID format	The parameter must be in UUID format.
2	400	Error: Field validation	One of the fields in the request is invalid.
2	400	Error: Field validation for 'blockchain' failed on the 'blockchain' tag	The blockchain does not exist. Try again with a [supported blockchain](/w3s/docs/supported-blockchains-and-currencies).
2	400	Error: Field validation for gasLimit failed on the required_without tag"	If feeLevel is provided, gasLimit should be set to NULL. If feeLevel is NULL, gasLimit must be provided.
2	400	Error: Field validation for parameter failed on the min tag	The parameter is not in the correct format.
2	400	Cannot unmarshal	The parameter is not in the correct format.
2	400	INVALID: parameter empty	The parameter is required for this request. Try the request again with this parameter.
##
Programmable wallet error messages
[](#programmable-wallet-error-messages)
This section describes the errors that can be returned for Programmable Wallet API requests.

###
User error codes (155101 - 155199)
[](#user-error-codes-155101---155199)
Error code	HTTP Code	Error Message	Description
155101	409	Existing user already created with the provided userId.	The user ID already exists. Try the request again with a different user ID.
155102	404	Cannot find the user id in the system.	The user is not found. Try the request again with a different user ID.
155103	401	Cannot find the user token in the system.	The user token is not found. Try the request again with a different user token.
155104	403	The userToken had expired.	The user token has expired. Please generate a new user token and try the request again.
155105	403	The userToken is invalid.	The user token is not invalid. Please generate a new user token and try the request again.
155106	409	The user had already been initialized.	The user has already been initialized. Please query the user status first to check if the user has already set a pin and created a wallet.
155107	409	User has previously set a PIN. Use PUT /user/pin to reset the PIN.	The PIN has already been set successfully. Please query the user status to check if the user has already set a PIN.
155108	409	User has previously set up their security questions, and they can't be reset.	The user has already set the security questions successfully. The questions can only be set once.
155109	409	The specified user has been disabled.	The specified user has been disabled.
155110	400	User has not set up a PIN yet.	The action cannot be performed before the user sets their pin. To set a pin, please call initialize or set a pin first.
155111	400	User hasn't set the security questions for PIN backup yet.	The action cannot be performed before the user sets their security questions.To set security questions, please call initialize or set security questions first.
155112	400	The user has inputted the incorrect pin.	The user's PIN is not correct. Try the request again with a different PIN.
155113	400	Provided device ID is not found in the system.	The device ID is not correct. Try the request again with a different device ID.
155114	400	Provided app ID is not recognized in the system.	The app ID is not found. Check the app ID in the SDK API. If you do not have the app ID or it is incorrect, you can acquire it from the Developer Console or by making an API request to GET /config/entity.
155115	400	The user has inputted the incorrect security answers	The provided security answers are not correct. Try the request again with different answers.
155116	404	The challenge ID doesn't exist in the system.	The challenge ID is invalid. Check the challenge ID in the SDK API.
155117	400	The content provided for approval is not correct.	The provided approval content is unknown. Please integrate with the latest SDK.
155118	400	Encryption key does not match with the user's token. Call POST /users/token to get the correct token encryption key pair.	The encryption key does not match the user's token. Call POST /users/token to get the correct token encryption key pair.
155119	400	The user's PIN input is locked. It will be unlocked after the cooldown period.	The user's PIN input is locked. Please wait for cold time to unlock, and try again.
155120	400	The user's security questions input is locked. It will be unlocked after the cooldown period.	The user's security questions input is locked. Please wait for the locked period to expire to unlock, and try again.
155121	403	The provided challengeId has expired.	The challenge has expired. Try the request again to create a new challenge.
155122	403	The provided challengeId is invalid	The challenge is invalid. Try the request again to create a new challenge.
155123	403	No extra information provided when adding PIN-related requests.	When SDK approves the challenge, the challenge format is wrong. Try the request again to create a new challenge.
155124	403	The extra information provided for PIN-related requests is invalid.	When SDK approves the challenge, the challenge format is wrong. Try the request again to create a new challenge.
###
Transaction error codes (155201 - 155299)
[](#transaction-error-codes-155201---155299)
Error code	HTTP Code	Error Message	Description
155201	400	Not enough funds to fulfill the withdraw request.	There is insufficient native token balance in the wallet to cover all pending transactions on the network. Please add funds to the wallet, and try again.
155202	400	Transaction nonce is inconsistent with sender’s latest nonce.	Transaction nonce mismatches occur when a sender has created one or more transactions on-chain where the initial transaction has not yet reached finality. Try the transaction again.
155203	400	User op nonce can not be larger than 0 when smart contract wallet hasn't been deployed.	User op nonce can not be larger than 0 when the smart contract wallet hasn't been deployed.
155204	400	The total cost of executing transaction is higher than the balance of the user's account when estimating fee.	There is insufficient native token balance in the wallet to cover the total cost of executing the transaction. Please add funds to the wallet, and try again.
155205	400	Failed to execute this request on EVM due to insufficient token when estimating fee.	There is insufficient non-native token balance in the wallet to cover the request execution. Please add funds to the wallet, and try again.
155206	400	The sender address is not token owner or approved when estimating token transfer.	The owner of the tokens may not call approve ABI to allow the caller to access the tokens.
155207	400	Gas required exceeds allowance when estimating fee.	Gas required exceeds allowance when estimating fee.
155208	400	Estimate fee execution reverted.	Estimate fee execution reverted on EVM.
155209	400	ABI function signature can’t pack ABI parameter.	The ABI parameters were invalid. Please correct the ABI parameters and try again.
155210	400	Fails to perform transaction estimation.	Estimate fee execution failed on EVM.
155211	400	MaxFee * GasLimit exceed configurable max transaction fee (default is 1 native token).	Please reduce maxFee or gasLimit, and try again. Note Circle has system-wide limits for maxFee for each blockchain network and native token pair. Ethereum = 1 ETH, Polygon = 10 MATIC, Avalanche = 1 AVAX.
155215	400	Unsupported operation for transaction.	The requested operation for the transaction is not eligible to be executed anymore. Check the status of the transaction.
155218	400	Invalid number of nft in transaction request.	Invalid number of NFT in the transaction request. Only ERC-1155 supports batch NFT token transfer.
155219	400	Invalid destination address.	Please correct the destination address, and try again.
155220	400	Wallet and token's blockchain mismatch.	The wallet and token's blockchain must be the same.
155221	400	Invalid amounts in transfer request.	Other than ERC-1155, all other types of token transfer amount length should be 1.
155222	404	NFT metadata can not be found.	NFT metadata can not be found.
155223	400	Unsupported userId for get transactions.	UserId is not supported as a query parameter for GET transaction requests.
155224	400	Failed to parse the provided amounts in request to decimals.	Failed to parse the provided amounts in request to decimals.
155225	400	Wallet and request's blockchain mismatch.	The wallet and blockchain in the request should be the same.
155226	400	Invalid source address.	Please correct the source address, and try again.
155227	400	Invalid transaction type.	Transaction validation failed because the transaction type is not outbound.
155228	400	Missing token ID.	The token ID is missing from the request. Add the correct token ID, and try again.
155229	400	Transaction is not eligible for operation.	The transaction is not eligible for operation.
155230	400	No call data or abi signature provided.	Please provide ABI signature, and try again.
155231	400	Transaction needs feeLevel or gasLimit provided.	Please provide feeLevel or gasLimit, and try again.
155232	400	SCA transaction needs feeLevel provided.	SCA transaction needs feeLevel provided. Please specify the feeLevel, and try again.
155233	400	Provided gasLimit is too low to complete the requested transaction.	Provided gasLimit smaller than network estimation. Please provide a higher gasLimit, and try again.
155234	400	Transaction can't have both feeLevel and fee parameters provided.	Specify either feeLevel, or provide the detailed fee params for a transaction.
155235	400	EIP1559 chains need maxFee/priorityFee provided.	EIP1559 chains need maxFee/priorityFee provided.
155236	400	Failed to parse the provided fee in request to decimals.	Failed to parse the provided fee in request to decimals.
155237	400	PriorityFee cannot be larger than maxFee in creating transaction request.	PriorityFee cannot be larger than maxFee in creating transaction requests.
155238	400	Non-EIP1559 chains need gasPrice provided.	Non-EIP1559 chains need gasPrice provided.
155239	400	Invalid token address for transfer.	Please correct the token address, and try again.
155240	400	Invalid token standard for transfer.	Please correct the token standard, and try again.
155241	400	Invalid token decimal for transfer.	Please correct the token decimal, and try again.
155242	400	The lengths of amounts and nft tokens don't match.	The lengths of amounts and NFT tokens don't match.
155243	400	Missing bytecode for contract deployment.	Please provide bytecode for contract deployment, and try again.
155244	400	Cannot provide both WalletID and SourceAddress/Blockchain.	Specify WalletID or provide the SourceAddress/Blockchain, and try the request again.
155245	400	Invalid amount in contract execution request.	Invalid amount in contract execution request. Error raised when parsing amount in the request.
155247	400	Cannot provide both CallData and AbiFunctionSignature/AbiParameters.	Specify CallData or provide AbiFunctionSignature/AbiParameters, and try the request again.
###
Wallet error codes (155501 - 155599)
[](#wallet-error-codes-155501---155599)
Error code	HTTP Code	Error Message	Description
155501	409	Frozen wallets can not be updated or interact with, only query.	Frozen wallets cannot be updated or interacted with. You can only query this wallet.
155502	403	Max amount of wallets (tentative 1M) reached under 1 wallet set.	1M maximum amount of wallets has been reached under 1 wallet set.
155503	400	Metadata array length needs to match wallet count in create developer wallets request.	The metadata array length must match the wallet count in the POST /developer/wallets request.
155504	400	Metadata array length needs to match number of blockchains in create user wallets request.	The metadata array length must match the number of blockchains in the create user wallet request.
155505	400	SCA wallet needs to wait for first-time transaction to be queued before processing more transactions.	SCA wallet needs to wait for first-time transactions to be queued before processing more transactions.
155506	400	SCA wallet config is invalid.	SCA wallet config is invalid.
155507	400	SCA account is not supported on the given blockchain.	The SCA account type is not supported on the given chain. Please see the [account types](https://developers.circle.com/w3s/docs/programmable-wallets-account-types) doc for more details on the supported blockchains.
155508	400	Can't create multiple user-controlled SCA wallets across multiple blockchains at a time.	When the account type is SCA, you cannot send more than one value in the blockchains array. Instead, send two separate requests, providing blockchain value one in the first request and value two in the second request.
155509	400	Entity is not eligible for SCA account creation. Please check paymaster policy setup.	The paymaster policies for your account need to be properly configured. Go to the web3 console and ensure you have a policy configured for the blockchain and do not have multiple policies for a given blockchain.
###
Wallet Set error codes (155601 - 155699)
[](#wallet-set-error-codes-155601---155699)
Error code	HTTP Code	Error Message	Description
155601	400	Failed to retrieve wallet set which already exists.	Cannot create a new wallet set because it already exists. Try again with a unique wallet set.
###
SDK UI error codes (155701 - 155799)
[](#sdk-ui-error-codes-155701---155799)
Error code	HTTP Code	Error Message	Description
155701	N/A	User canceled	The user canceled or exited the execute UI.
155702	N/A	<Launch UI failed reason>	Please check the error message to see the exact UI launch failure reason.
155703	N/A	The code you entered is not the same as the first one.	The code you entered is not the same as the first one.
155704	N/A	Your PIN can’t have repeating or consecutive numbers.	Your PIN can’t have repeating or consecutive numbers. Try again with a valid PIN.
155705	N/A	Your hint can’t be the same as the answer.	Your hint can’t be the same as the answer. Try again with a valid hint.
155706	N/A	Network error	Network error. Try again.
155707	N/A	Failed to encrypt data	Failed to encrypt data. Try again.
155708	N/A	"Biometrics instead of PIN" is disabled	Please enable the "Biometrics instead of PIN" SDK API.
155709	N/A	The device system doesn't support biometrics	Cannot enable biometrics in this device. The user must use the PIN code.
155710	N/A	Failed to decrypt biometrics key	Ask the user to reset the biometrics settings.
155711	N/A	The user has not enabled biometrics yet	The user skipped the biometrics setting.
155712	N/A	The user refused to enable biometrics	Must call the SetBiometricsPin SDK API so the user can enable biometrics.
155713	N/A	Too many requests. Try again later	Please wait for 30 minutes, and try again.
155714	N/A	Too many requests. The biometrics sensor on the device is locked.	Please go to the device system settings to unlock.
155715	N/A	The user didn't enable biometrics	The user can be prompted to go to the App Settings to re-allow the app to use biometrics. (Warning) This operation will cause the app to be relaunched.
155716	N/A	Unexpected error for biometrics	Unexpected error. Try again.
###
Common error codes (156001 - 156099)
[](#common-error-codes-156001---156099)
Error Code	HTTP Code	Error Message	Description
156001	404	Cannot find target wallet in the system. Either the specified wallet doesn't exist or it's not accessible to the caller.	The specified wallet does not exist, or is not visible to the caller. Try the request again with a valid wallet ID.
156002	404	Cannot find target token in the system. Either the specified token doesn't exist or it's not accessible to the caller.	The specified token does not exist, or is not visible to the caller. Try the request again with a valid token ID.
156003	404	Cannot find target transaction in the system. Either the specified transaction doesn't exist or it's not accessible to the caller.	The specified transaction does not exist, or is not visible to the caller. Try the request again with a valid transaction ID.
156004	400	Reusing an entity secret ciphertext is not allowed. Please re-encrypt the entity secret to generate new ciphertext.	Reusing an entity secret ciphertext is not allowed. Please re-encrypt the entity secret to generate a new ciphertext and try again.
156005	404	Cannot find target wallet set in the system. Either no such wallet set, or it's not accessible to the caller.	The specified wallet set does not exist, or is not visible to the caller. Try the request again with a valid wallet set.
156006	400	TEST_API key cannot be used with blockchain mainnets, or LIVE_API key cannot be used with blockchain testnets.	A TEST_API key cannot be used with blockchain Mainnets, and a LIVE_API key cannot be used with blockchain Testnets. Try again with a valid API key.
156007	401	TEST_API key or LIVE_API key is not found for the request.	A TEST_API key or LIVE_API key is not found for the request. Try again with a valid API key.
156008	400	Cannot find target entity config in the system. Either no such entity config, or it's not accessible to the caller.	The specified entity config does not exist, or is not visible to the caller. Try the request again with a valid entity config.
156009	400	Fail to parse id as UUID in url.	The specified id is invalid (must be in UUID format). Try again with a valid id.
156010	404	Cannot find the corresponding entity in the system.	The specified entity does not exist, or is not configured. Try again with a valid entity.
156011	404	Cannot find target nftTokenId in the system.	The nftTokenId does not exist. Try again with a valid nftTokenId.
156012	404	Cannot find corresponding pagination cursor in the system.	The pagination parameters are invalid. Please correct your pagination parameters, and try again.
156013	400	The provided entity secret is invalid.	The specified entity secret is not valid. Please create or re-encrypt your entity secret, and try again.
156014	400	Pagination params are invalid. Only UUID format is supported for pageBefore and pageAfter.	Pagination parameters are invalid. Only UUID format is supported for pageBefore and pageAfter.
156015	409	The secret for this entity has already been set.	The secret for this entity has already been set.
156016	403	The entity secret has not been set yet. Please provide encrypted ciphertext in the console.	The entity secret has not been set yet. Please provide encrypted ciphertext in the Developer console.
156017	400	The specified blockchain parameters are incorrect.	The blockchain parameters are invalid. Please correct the blockchain parameters, and try again.
156018	403	The uploaded recovery file is invalid.	The uploaded recovery file is invalid. Try again with a valid recovery file.
156019	403	Current entity secret is invalid. Please rotate the entity secret first.	The current entity secret is invalid. Please rotate the entity secret first in the Developer console, and try again.
156020	403	Please use a new idempotency key.	Please use a new idempotency key, and try again.
156021	409	A new wallet set ID is required.	Please use a new wallet set ID, and try again.
156023	409	EncodedByHex is true in sign request, but the message is not hex encoded.	The encodedByHex parameter is set to true, but the provided message is not in the correct hex-encoded format. Please ensure the message is hex-encoded and try again, or set encodedByHex to false.
156024	400	Data is not a valid JSON string in sign request.	Data is not a valid JSON string in the Sign request. Please provide a valid JSON format typed data string.
156025	400	Invalid message in request.	Sign message is invalid. Try the request again with a valid EIP-191 message.
156026	400	Invalid typed data in request.	Typed data is invalid. Try the request again with a valid EIP-712 typed data.
156027	400	The specified blockchain is either not supported or deprecated.	Please try again with a supported blockchain. Please see the guide [Supported Blockchains and Currencies](https://developers.circle.com/w3s/docs/supported-blockchains-and-currencies#supported-blockchains-and-currencies) for the complete list of supported blockchains.
##
Smart Contract Platform error messages
[](#smart-contract-platform-error-messages)
This section includes the errors that can be returned from Smart Contract Platform API requests.

###
Contract error codes (175001 - 175200)
[](#contract-error-codes-175001---175200)
Error Code	HTTP Code	Error Message	Description
175001	404	Contract not found.	The specified contract does not exist in the system.
175002	400	No ABI JSON for the target contract.	Cannot execute a read function on a contract without the ABI JSON.
175003	400	Constructor parameters length must match constructor signature.	The number of constructor parameters must match the constructor signature.
175004	409	Contract already exists.	The contract already exists in the system.
175005	400	Address is not a contract address.	The given address is not associated with a smart contract.
175006	400	Contract is archived.	Attempted to interact with an archived contract.
175007	400	Invalid ABI JSON.	The inputted ABI JSON is not correctly formatted.
175008	400	Multi-layered proxies are not supported.	Importing a multi-layered proxy contract is not currently supported.
175009	400	Contract deployment pending.	Contract deployment must be completed before function execution is available.
175010	400	ABI function not found.	The ABI function was not found on the contract.
175011	400	Empty update on a contract.	An empty update for a contract is not allowed
###
Template error codes (175201 - 175400)
[](#template-error-codes-175201---175400)
Error Code	HTTP Code	Error Message	Description
175201	404	Template not found.	The specified template does not exist in the system.
175202	400	Deploying this template is temporarily disabled.	Deploying this template is temporarily disabled.
175203	400	Invalid template deployment parameter.	The request contains an invalid field in the template parameters.
175204	400	Missing required template deployment parameter.	The request is missing a required field in the template parameters.
###
Common error codes (175401 - 175600)
[](#common-error-codes-175401---175600)
Code	HTTP Code	Error Message	Description
175401	400	Fail to parse id as UUID in url.	The specified ID is invalid (must be in UUID format). Try again with a valid ID.
175402	400	The specified blockchain is either not supported or deprecated.	The specified blockchain is either not supported or deprecated.
175403	409	Please use a new idempotency key.	Please use a new idempotency key and try again.
175404	400	TEST_API key cannot be used with blockchain mainnets, or LIVE_API key cannot be used with blockchain testnets.	TEST_API key cannot be used with blockchain mainnets, or LIVE_API key cannot be used with blockchain testnets.
175405	401	TEST_API key or LIVE_API key is not found for the request.	TEST_API key or LIVE_API key is not found for the request.
175406	400	This feature is temporarily disabled.	This feature is temporarily disabled.
175407	400	The specified blockchain is currently unavailable.	The specified blockchain is currently unavailable. Please check the [Circle Status page](https://status.circle.com/) for more details.
###
Transaction error codes (177001 - 177607)
[](#transaction-error-codes-177001---177607)
Code	HTTP Code	Error Message	Description
177001	400	transaction nonce is inconsistent with sender's latest nonce	The transaction nonce is inconsistent with the sender's latest nonce.
177002	400	user op nonce can not be larger than 0 when smart contract wallet hasn't been deployed	User op nonce can not be larger than 0 when the smart contract wallet hasn't been deployed.
177003	400	failed to execute this request on EVM due to insufficient token when estimating fee	Failed to execute this request on EVM due to insufficient tokens when estimating the fee.
177004	400	the total cost of executing transaction is higher than the balance of the user's account when estimating fee	When estimating the fee, the total cost of executing the transaction is higher than the balance of the user's account.
177005	400	the sender address is not token owner or approved when estimating token transfer	The sender address is not token owner or approved when estimating token transfer.
177006	400	gas required exceeds allowance when estimating fee	Gas required exceeds allowance when estimating fee.
177007	400	estimate fee execution reverted	Estimate fee execution reverted.
177008	400	ABI function signature can’t pack ABI parameter	ABI function signature can’t pack ABI parameter.
177009	400	fails to perform transaction estimation	Fails to perform transaction estimation.
177010	400	maxFee * gasLimit exceed configurable max transaction fee (default is 1 native token)	The MaxFee * GasLimit exceeds the configurable max transaction fee (default is 1 native token).
177011	400	transaction needs feeLevel or gasLimit provided	The transaction requires feeLevel or gasLimit to be provided in the request.
177012	400	sca transaction needs feeLevel provided	The SCA transaction requires feeLevel to be provided in the request.
177013	400	EIP1559 chains need maxFee/priorityFee provided	EIP1559 chains require maxFee and priorityFee to be provided in the request.
177014	400	priorityFee cannot be larger than maxFee in creating transaction request	Creating transaction requestspriorityFee cannot be larger than maxFee.
177015	400	missing bytecode for contract deployment	Missing bytecode for contract deployment.
177016	400	cannot provide both WalletID and SourceAddress/Blockchain	You cannot provide WalletID and SourceAddress/Blockchain in a request.
177017	400	Invalid amount in contract execution request	The amount in the contract execution request is invalid.
177018	400	policy is not activated and cannot be used	The Gas Station paymaster policy is not activated and cannot be used.
177019	400	exceeded max daily transaction of the policy	The Gas Station paymaster policy maximum daily transaction limit has been reached.
177020	400	exceeded max spend USD per transaction of the policy	The transaction cost exceeds the Gas Station paymaster policy maximum spend per transaction in USD.
177021	400	exceeded max spend USD daily of the policy	The Gas Station paymaster policy for maximum spending daily in USD has been reached.
177022	400	exceeded max native token daily of the policy	The Gas Station paymaster policy for maximum native tokens daily of the policy.
177023	400	sender is in policy blocklist	The sender is on the Gas Station paymaster policy blocklist.
177024	400	wallet and request's blockchain mismatch.	The wallet and blockchain in the request should be the same.
177301	400	wallet is Frozen	Frozen wallets can not be updated or interacted with; they can only be queried.
177302	400	invalid sca wallet config	The SCA wallet configuration is invalid.
177303	400	sca wallet first-time transaction is still in progress	The SCA wallet needs to wait for the first-time transaction to finish deploying the wallet before processing more transactions.
177304	400	SCA account is not supported on the given blockchain	The SCA account is not supported on the given blockchain.
177305	400	Entity is not eligible for SCA account creation. Please check paymaster policy setup	The entity is not eligible for SCA account creation. Please check the Gas Station paymaster policy setup.
177601	400	could be caused by either no such wallet or wallet is not accessible to the caller	The target wallet cannot be found in the system. Either the specified wallet doesn't exist, or it's inaccessible to the caller.
177602	400	reusing an entity secret ciphertext is not allowed. Please re-encrypt the entity secret to generate new ciphertext	Reusing an entity's secret ciphertext is not allowed. Please re-encrypt the entity secret to generate a new ciphertext.
177603	400	entity is likely not properly set up during the onboarding process	The corresponding entity cannot be found in the system.
177604	400	the provided entity secret is invalid	The provided entity secret is invalid.
177605	400	the entity secret has not been set yet. Please provide encrypted ciphertext in the console	The entity secret has not been set up on your account. Please provide encrypted ciphertext in the console.
177606	400	current entity secret is invalid. Please rotate the entity secret first	The provided entity secret is invalid. Please rotate the entity secret first and send another API request.
177607	400	please use a new idempotency key	Please use a new idempotency key.
Updated 24 days ago


URL: https://developers.circle.com/w3s/docs/onboard-users-to-developer-controlled-wallets
Title: Onboard Users to Developer-Controlled Wallets
Description: undefined
Keywords: undefined

Developer-controlled Programmable Wallets simplify user onboarding to your Web3 app by performing blockchain interactions, such as asset transfers and smart contract executions, on behalf of your users. You maintain control of the wallet while removing all of the complexity for your users.

This guide describes the best practices for creating and managing developer-controlled wallets to integrate into your app, including how to create wallets for existing users already using your app and new users who sign up later.

Scenarios:

-
Create wallets for existing users: For onboarding your existing users to developer-controlled wallets, ensuring a smooth transition to the Web3 ecosystem.

-
Create wallets for new users in advance: For the routine onboarding of new users to your application. You can create wallets in advance and assign them to users when they sign up.

##🚧
Cost Consideration for Externally Owned Account (EOA) Wallets

Creating EOA wallets on mainnet incurs a minor expense per wallet. To optimize costs, especially when planning to create a large number of wallets, it's prudent to carefully predict user demand and batch-create wallets accordingly. For instance, forecast and prepare wallets to meet the expected demand in the next 30 days to avoid unnecessary expenses and ensure optimal resource utilization.

##
Prerequisites
[](#prerequisites)
- Ensure you complete the [Create Your First Developer-Controlled Wallet](https://developers.circle.com/w3s/docs/developer-controlled-create-your-first-wallet) guide. It provides a comprehensive walkthrough for setting up your first entity secret, wallet set, and wallet, making it an essential foundation for understanding developer-controlled wallets.
##
Create wallets for existing users
[](#create-wallets-for-existing-users)
- Batch create and link wallets to existing users: Use the count parameter with the  [POST /developer/wallets](/w3s/reference/createdeveloperwallet) API endpoint to specify the number of wallets needed per blockchain. Simultaneously, link these wallets to individual users by utilizing the metadata attribute to designate wallet names and associate them with user IDs (e.g., UUID) using the refId field for each wallet.
##📘
You can create up to 20 wallets per request. If you need more, make additional requests until the required total is reached.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

const response = await circleDeveloperSdk.createWallets({
  walletSetId: '71f2a6b4-ffa7-417a-ad5b-fb928753edc8',
  accountType: 'SCA',
  blockchains: ['MATIC-AMOY'],
  count: 2,
  metadata: [
    {
      name: 'User Wallet1',
      refId: 'UUID1',
    },
    {
      name: 'User Wallet2',
      refId: 'UUID2',
    },
  ],
});
- Inspect the response payload: After receiving a successful response, inspect the response body and map the wallet IDs data.wallets.id with the respective users on your systems. The response body includes information such as the wallet IDs, custody types, names, and reference IDs.
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "66b67097-307e-5b46-a1d5-0b1577d67fd4",
        "state": "LIVE",
        "walletSetId": "018b42d2-cc38-7a1e-a47a-5927d2c97f16",
        "custodyType": "DEVELOPER",
        "refId": "UUID1",
        "name": "User Wallet1",
        "address": "0xe55628c98f5d81daaa79b72899b38a3535d10990",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2024-01-22T22:57:20Z",
        "createDate": "2024-01-22T22:57:20Z"
      },
      {
        "id": "cda61d8d-7d46-5a39-a8a6-6b4a3d6fdac3",
        "state": "LIVE",
        "walletSetId": "018b42d2-cc38-7a1e-a47a-5927d2c97f16",
        "custodyType": "DEVELOPER",
        "refId": "UUID2",
        "name": "User Wallet1",
        "address": "0x29b27e792b8b854e48e85ab4f456cf5a9f1579fb",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2024-01-22T22:57:20Z",
        "createDate": "2024-01-22T22:57:20Z"
      }
    ]
  }
}
By successfully following these steps, you can create wallets for existing users and link them accordingly.

##
Create wallets for users in advance
[](#create-wallets-for-users-in-advance)
Creating wallets in advance streamlines the real-time onboarding process and prevents delays when users require wallets. This approach optimizes the workflow and ensures that wallets are readily available whenever users need them.

The following demonstrates best practices:

- Invoke the developer-controlled wallets creation API: Utilize the [POST: /developer/wallets](/w3s/reference/createdeveloperwallet) endpoint for wallet creation. Specify the number of wallets needed by adjusting the count parameter.
##📘
You can create up to 20 wallets per request. If you need more, make additional requests until the required total is reached.

Node.js
cURL

// Import and configure the developer-controlled wallet SDK
const { initiateDeveloperControlledWalletsClient } = require('@circle-fin/developer-controlled-wallets');
const circleDeveloperSdk = initiateDeveloperControlledWalletsClient({
  apiKey: '<API_KEY>',
  entitySecret: '<ENTITY_SECRET>'
});

const response = await circleDeveloperSdk.createWallets({
  walletSetId: '71f2a6b4-ffa7-417a-ad5b-fb928753edc8',
  accountType: 'SCA',
  blockchains: ['MATIC-AMOY'],
  count: 2
});
Response Body

{
  "data": {
    "wallets": [
      {
        "id": "66b67097-307e-5b46-a1d5-0b1577d67fd4",
        "state": "LIVE",
        "walletSetId": "018b42d2-cc38-7a1e-a47a-5927d2c97f16",
        "custodyType": "DEVELOPER",
        "address": "0xe55628c98f5d81daaa79b72899b38a3535d10990",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2024-01-22T22:57:20Z",
        "createDate": "2024-01-22T22:57:20Z"
      },
      {
        "id": "cda61d8d-7d46-5a39-a8a6-6b4a3d6fdac3",
        "state": "LIVE",
        "walletSetId": "018b42d2-cc38-7a1e-a47a-5927d2c97f16",
        "custodyType": "DEVELOPER",
        "address": "0x29b27e792b8b854e48e85ab4f456cf5a9f1579fb",
        "blockchain": "MATIC-AMOY",
        "accountType": "SCA",
        "updateDate": "2024-01-22T22:57:20Z",
        "createDate": "2024-01-22T22:57:20Z"
      }
    ]
  }
}
-
Store wallet information: Post-creation, ensure that all relevant wallet information, including wallet IDs and associated attributes (e.g., blockchains, custody types), is securely stored in your system for future reference.

-
Assign the wallet to the user: Once a user requests a wallet, assign one of the wallets created in the previous step to them. This assignment process should occur on your system and within Circle's infrastructure. 

-
Identify an available pre-created wallet: Select a wallet from your pool of created wallets that hasn't been assigned yet.

-
Associate the selected wallet with the user: Update your database to link the user's account with the selected wallet. This link should include the wallet's ID, blockchain information, and any other relevant details to ensure that transactions can be accurately processed and attributed to the correct user.

-
Mark the wallet as assigned: To prevent the same wallet from being assigned to multiple users, update its status in your database to indicate that it has now been assigned.

-
Update Circle’s records associating the user and the assigned wallet: Use the [PUT: /developer/wallets/{id}](/w3s/reference/updatewallet-1) API to update Circle’s records.

Node.js
cURL

const response = await circleDeveloperSdk.updateWallet({
  id: '66b67097-307e-5b46-a1d5-0b1577d67fd4',
  name: 'User Wallet1',
  refId: 'UUID1'
});
Response Body

{
  "data": {
    "wallet": {
      "id": "66b67097-307e-5b46-a1d5-0b1577d67fd4",
      "state": "LIVE",
      "walletSetId": "018b42d2-cc38-7a1e-a47a-5927d2c97f16",
      "custodyType": "DEVELOPER",
      "refId": "UUID1",
      "name": "User Wallet1",
      "address": "0xe55628c98f5d81daaa79b72899b38a3535d10990",
      "blockchain": "MATIC-AMOY",
      "accountType": "SCA",
      "updateDate": "2024-01-22T22:57:20Z",
      "createDate": "2024-01-22T22:57:20Z"
    }
  }
}
By adhering to these guidelines, you can streamline the wallet management process, ensuring that wallets are efficiently assigned to users on an as-needed basis.

Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/developer-controlled-wallet-quickstart
Title: Quickstarts
Description: undefined
Keywords: undefined

Select your preferred quickstart style to begin your journey. Our collection of quickstarts will guide you through creating your first developer-controlled wallet and transferring tokens on-chain.

[

##Interactive Quickstart (Recommended)
Build your first developer-controlled wallet(s), without writing (nearly) any code.

GET STARTED

](https://learn.circle.com/quickstarts/dev-controlled-wallets) [

##Quickstart Guides
Use our step-by-step guide to setup your local environment and make API calls.

GET STARTED

](https://developers.circle.com/w3s/docs/developer-controlled-create-your-first-wallet)
Please note the interactive quickstart will redirect you to a different location. You can always return to this page to access our comprehensive developer documentation.

Updated 6 months ago


URL: https://developers.circle.com/w3s/docs/send-a-gasless-transaction
Title: Send a Gasless Transaction
Description: undefined
Keywords: undefined

This step-by-step guide lays out the process for sending gasless transactions from your user-controlled SCA (Smart Contract Accounts) wallet on Testnet. Gasless transactions are no different than any other Programmable Wallet transaction. As long as a Gas Station policy is configured, and you use a supported wallet, gas fees will be sponsored automatically.

##📘
For Testnet, a preconfigured policy is already in place. Therefore, you are not required to create a Gas Station policy for this quickstart guide.

Before you begin, there are a few essential details to keep in mind:

- Supported Wallet Account Types: SCA only. Externally Owned Accounts (EOA) are not supported.
- Supported Blockchains: Ethereum Mainnet, Ethereum Sepolia, Polygon Mainnet, and Polygon Amoy.
To learn more, refer to our Gas Station overview page [here](/w3s/docs/gas-station).

##
1. Create an SCA Wallet
[](#1-create-an-sca-wallet)
To create a wallet, follow our comprehensive quickstart guide on creating your first developer-controlled SCA wallet [here](/w3s/docs/developer-controlled-create-your-first-wallet). Remember, you must create an SCA wallet to perform gasless transactions. Once the wallet is successfully created, note down the wallet's address from step 3.

Alternatively, if you prefer to use user-controlled wallets, follow the [Create Your First Wallet](/w3s/docs/user-controlled-create-your-first-wallet) guide.

##
2. Fund the Wallet
[](#2-fund-the-wallet)
Next, you will fund your wallet with a token to provide an asset for conducting transactions. In this guide, we will transfer USDC into the wallet using the [USDC faucet](https://faucet.circle.com/). While on the faucet, provide your wallet's address to complete the process.

Alternatively, if you prefer to deposit a native token, you can visit the [ETH](https://www.alchemy.com/faucets/ethereum-sepolia) or [MATIC](https://faucet.polygon.technology/) faucets.

##
3. Send a Transfer
[](#3-send-a-transfer)
Follow the instructions in the quick start guide [Transfer Tokens from Wallet to Wallet](/w3s/docs/developer-controlled-transfer-tokens-across-wallets). This guide will walk you through sending a transaction without requiring gas. Once completed, you can head to the [developer dashboard paymaster page](https://console.circle.com/gas-station/paymasters) and view the sponsored transaction within the respective policy.

For those using user-controlled wallets, follow the [Send an Outbound Transfer](/w3s/docs/user-controlled-send-outbound-transfer) instead.

Congratulations on successfully sending a gasless transaction! With Circle taking care of the complexities, you can fully concentrate on providing a delightful user experience without the hassle of intricate setup.

To expand your knowledge and explore further, please visit the following resources:

- [Gas Station Overview:](/w3s/docs/gas-station) Get familiar with Gas Station, its functions, and how it can be used in your app.
- [Policy Management:](/w3s/docs/gas-station-policy-management) Understand how to manage Gas Station policies effectively and view gas-sponsored transactions.
- [Billing for Sponsored Gas Fees:](/w3s/docs/gas-station-billing) Understand how you will be billed for sponsored gas fees.
- [Paymaster:](/w3s/docs/gas-station-paymaster) Dive deeper into the concept of Paymaster and its role in gasless transactions.
By leveraging these resources, you can enhance your understanding and maximize the potential of gasless transactions. Stay tuned for exciting updates and features as we strive to streamline the gasless transaction experience.

Updated about 1 month ago


URL: https://developers.circle.com/w3s/docs/webhook-logs
Title: Notification Logs
Description: undefined
Keywords: undefined

Utilizing Webhook notification logs, you gain visibility into the comprehensive history of Webhook events triggered by Circle's systems. In addition, you are empowered to resend any specific Webhook events, simplifying the process of troubleshooting any potential issues that may arise.

###
Webhook Logs Overview
[](#webhook-logs-overview)
Within the Circle Developer Console, there is a dedicated tab for Webhook Logs. By accessing this tab, you will be presented with an informative overview highlighting the most recent Webhook Events, thereby facilitating efficient monitoring and management of your Webhook integrations.


###
Detailed Webhook View for Deeper Insight
[](#detailed-webhook-view-for-deeper-insight)
In addition, you have the ability to access a detailed page for each individual entry within the Webhook Logs. By clicking on a specific entry, you will be directed to a dedicated detail page that offers comprehensive information. This includes nested event object data and an associated table of events, including their respective Webhook attempts.

This detailed view enables you to gain deeper insights into the specific details of each Webhook event, aiding in further analysis and troubleshooting. Moreover, it provides you with the valuable option to resend a Webhook, allowing for seamless management and control over your Webhook integration flow.


Field Definitions:

Delivery Status	The delivery status of a webhook notification indicates the outcome of the most recent delivery attempt(s). If any of the recent webhook attempts are marked as failed, it will affect the overall delivery status, resulting in a "failed" status. Monitoring the delivery status helps determine the reliability and success of webhook notifications, allowing for effective troubleshooting and resolution of any delivery issues.
Log ID	Unique identifier [UUID] for the event.
Notification ID	Unique identifier [UUID] for the notification.
Event Type	The event type and state of the event e.g. Transaction.Initiated.
Event	The webhook notification payload.
Updated 6 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/w3s/docs/developer-account-logs
Title: Developer Account Logs
Description: undefined
Keywords: undefined

###
View API log entries in the Web3 Services Console
[](#view-api-log-entries-in-the-web3-services-console)
API logs enable you to view your API transaction history and debug API errors with no setup required. When an API request is sent, Circle stores it along with its associated response.

On the API logs page, you can view logs for up to seven days and filter them to find the specific request you are looking for. Go to your [Web3 Services Console](https://console.circle.com/) to access the logs and click Logs.


###
API Log Data Elements
[](#api-log-data-elements)
For each API request and response Circle will store the following information.

Field	Description
HTTP Status	HTTP status code for each request, such as 200 or 400.
Path	The path of the resource (excludes the base URL).
Request ID	The X-Request-Id field in the request header or returned in the response.
User Agent	The User-Agent field in the request header. Commonly, the HTTP library used will provide this field by default.
Idempotency	Idempotency key that was sent in the request body. This is only found in POST requests.
Origin	Includes the protocol (HTTP/HTTPS), the domain or IP address, and the port number if applicable.
Time	Timestamp of when the request was received.
Request Body	The full request body.
Response Body	The full response body.
###
API Log Filtering
[](#api-log-filtering)
To filter your search, use the search fields and popup menus at the top of the Circle Developer account page.


Filter Name	Description
Search	Filter by request ID, resource ID or idempotency key ID.
Date Range	Filter results by date range.
Status	Filter results by successful and/or failed requests. Succeeded includes all 2## codes and failed includes all 4## and 5## codes.
Method	Filter results by HTTP method. Supports POST, PUT, PATCH, DELETE and GET.
Path	Filter results by the URL path, such as /transactions.
Updated 3 months ago

