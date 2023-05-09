
[![NuGet version (ProvisionPay.SoftposDeeplinkSDK)](https://img.shields.io/nuget/v/ProvisionPay.SoftposDeeplinkSDK.svg?style=flat-square)](https://www.nuget.org/packages/ProvisionPay.SoftposDeeplinkSDK/)


# SoftPOS Deeplink Dotnet SDK

It is a class library developed to reduce integration time in Softpos applications.

# Installation


Dotnet Deeplink Sdk is available on [NuGet](https://www.nuget.org/packages/ProvisionPay.SoftposDeeplinkSDK/).
| NuGet            |                                                        |
| :--------------- | :------------------------------------------------------ |
| Package Manager  | `PM>` `Install-Package ProvisionPay.SoftposDeeplinkSDK`                 |
| .NET CLI         |  `>`  `dotnet add package ProvisionPay.SoftposDeeplinkSDK`             |

# 1. Initialize

Initialize method is used for authentication which requires SDK to create credentials as Application User Id and Password. Those credentials needs to be created with your service provider. Mentioned operational process in this documentation is derived from SoftposDeeplinkSDK class. 
```csharp
var credentials = new Credentials("Your Application User Id", "Your Pasword");

ISoftPosDeeplinkConfiguration sdkConfig = new SoftPosDeeplinkConfiguration()
    .WithRegion(SoftposDeeplinkSDKRegion.Region1)
    .WithEnvironment(EnvironmentType.Test)
    .WithCredentials(credentials)
    .Build();

ISoftposDeeplinkSDK sdk = new SoftposDeeplinkSDK(sdkConfig);
```
Throws: HostException, AuthenticationException

# 2. Enroll User
EnrollUser method is used for defining user to the platform with the default terminal parameter registered to system by Payneos Team. To clear any doubts, Payneos Team receives the terminal details to the customer.
```csharp
var enrollUserRequest=new EnrollUserRequest()
{
    PackageId = "com.provisionpay.softpos.yourtenant",
    UserId = "User1",
    WspTenantId = "yourcompanytenant"
};

var enrollUserResponse = sdk.EnrollUser(enrollUserRequest);
```
Throws: ArgumentNullException, HostException
# 3. Create Payment Session Token
CreatePaymentSessionToken method is used for initiation of the transaction process and generates payment session token to trace the initated transaction. Payment session token should be sent to the mobile side.

Please see the method below. 

```csharp
var createPaymentSessionTokenRequest = new CreatePaymentSessionTokenRequest()
{
    UserHash = "User1",
    Amount = 1,
    CallBackURL = "https://yourapplink",
    CurrencyCode= Constants.CurrencyCode.TRY,
    OrderID = "YourOrderId",
    TransactionType = Constants.TransactionType.Sale
};

var createPaymentSessionResponse = sdk.CreatePaymentSessionToken(createPaymentSessionTokenRequest);

string paymentSessionToken =  createPaymentSessionResponse.Body.PaymentSessionToken;
```
Throws: CreatePaymentSessionTokenException, HostException, ArgumentNullException
# 4. Get Transaction By Payment Session Token
GetTransactionbyPaymentSessionToken method is used for listing the transaction detail as receipt with the input of paymentSessionToken. 

Please see the method detail below. 
```csharp
var transactionByPaymentSessionTokenRequest = new GetTransactionByPaymentSessionTokenRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var getTransactionByPaymentSessionToken = sdk.GetTransactionByPaymentSessionToken(transactionByPaymentSessionTokenRequest);
```
Throws: ArgumentNullException, HostException
# 5. Get Payment Session Status
GetPaymentSessionStatus method is used for the final status of the paymentSessionToken. 

Please see the detail below.
```csharp
var paymentSessionStatusRequest = new GetPaymentSessionStatusRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var getPaymentSessionStatus = sdk.GetPaymentSessionStatus(paymentSessionStatusRequest);

```
Throws: ArgumentNullException, HostException
# 6. Enroll User With Detail
EnrollUserWithDetail method is the detailed enrollment format of the enrolluser method. With this method, terminal and merchant registration is applied. 

Please see the sample format below.
```csharp
var enrolluserWithDetailRequest =new EnrollUserWithDetailRequest()
{
    PackageId = "com.provisionpay.softpos.tenant",
    UserId = "User1",
    WspTenantId = "yourcompanytenant",

    TerminalId = "ASDA1234",
    AcquirerId = "000001",
    TerminalSerialNumber = "12312312",
    EmvProfileId = "123",

    MerchantId = "000000000000001",
    MerchantCategoryCode = "1234",
    MerchantNameLocation = "Your Merchant Adress",
    MerchantNameToPrint = "Your Merchant Name"
};

var enrolluserWithDetailResponse = sdk.EnrollUserWithDetail(enrolluserWithDetailRequest);
```
Throws: ArgumentNullException, HostException, EnrollmentException
# 7. Cancel User Enrollment
CancelUserEnrollment method is used for cancelling the enrollment of the user.
```csharp
var cancelUserEnrollmentRequest = new CancelUserEnrollmentRequest()
{
    UserId = "User1",
    WspTenantId = "yourcompanytenant",
};

var cancelUserEnrollmentResponse = sdk.CancelUserEnrollment(cancelUserEnrollmentRequest);
```
Throws: ArgumentNullException, HostException
# 8. Cancel User Activation
CancelUserActivation method is used for cancelling the enrolled user’s activation.
```csharp
var cancelUserActivationRequest = new CancelUserActivationRequest()
{
    UserId = "User1",
    WspTenantId = "yourcompanytenant",
};

var cancelUserActivationResponse = sdk.CancelUserActivation(cancelUserActivationRequest);
```
Throws: ArgumentNullException, HostException
# 9. Handle Webhook
Handle Webhook model identifies the detail of how to handle the webhook requests by converting the encrypted data to transaction detail.
```csharp

APICredentials apiCredentials = new APICredentials("secretkey", "accesskey");

var webHookTransactionDetail = sdk.HandleWebhook(apiCredentials, "Your private key 32 character", Request);
```
Request variable must be HttpRequestMessage type in Net framework or HttpRequest type in Net core.

Throws: PaymentSessionTokenException, ApiKeyNotFoundException, ApiKeyMismatchException, APICredentialException, HostException, DecryptException, PrivateKeyNotFoundException
# 10. Handle Callback Data
It is used to handle data on the server-side when a callback is made from the SoftPOS application to the web application.
```csharp

var privateKey = "Your private key 32 character";

HandleCallBackDataResponse handlCallbackData = _sdk.HandleCallBackData(Request, privateKey);

return Ok(handlCallbackData);
```
Request variable must be HttpRequestMessage type in Net framework or HttpRequest type in Net core.

Throws: DecryptException, HostException, DataNotFoundException, PaymentSessionTokenException, PrivateKeyNotFoundException
# 11. IoC Support
SoftposDeeplinkSDK implementation
```csharp
services.AddTransient<ISoftPosDeeplinkConfiguration, SoftPosDeeplinkDemoConfiguration>();

services.AddTransient<ISoftposDeeplinkSDK, SoftposDeeplinkSDK>();
```

ISoftPosDeeplinkConfiguration implementation
```csharp
public class SoftPosDeeplinkDemoConfiguration : ISoftPosDeeplinkConfiguration
{
    public EnvironmentType Environment => EnvironmentType.Test;

    public Credentials Credentials => new Credentials("Your Application User Id", "Your Password");

    public SoftposDeeplinkSDKRegion Region => SoftposDeeplinkSDKRegion.South_East1;
}
```
# 12. Sample project
| Framework  | Architecture |                                                           |
|------------|--------------|--------------------------------------------------------------|
| NET 4.52   |MVC           | [ dotnet-framework-mvc-demo](https://github.com/ProvisionPay/dotnet-framework-mvc-demo)      |
| NET 4.52   |Webform       | [ dotnet-framework-webform-demo](https://github.com/ProvisionPay/dotnet-framework-webform-demo)  |
| NET Core  5|Webapi        | [ dotnet-core-webapi-demo](https://github.com/ProvisionPay/dotnet-core-webapi-demo)   |


# 13. Supported Framework

NET 4.5 , NET 4.51 , NET 4.52 , NET 4.6 , NET 4.61 , NET 4.62 , NET 4.57 , NET 4.571 , NET 4.572 , NET 4.8 , NET 4.81 , NET Core  5 , NET Core  6 , NET Core 7

# 14 Video Tutorial
[<img src="https://provisionpay.com/wp-content/uploads/2020/05/dotnet_sdk_youtube_cover.png" >](https://www.youtube.com/watch?v=noNt7OJ_ZzU "Softpos Deeplink Dotnet SDK")

