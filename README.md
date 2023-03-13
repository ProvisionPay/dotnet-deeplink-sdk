
[![NuGet version (ProvisionPay.SoftposDeeplinkSDK)](https://img.shields.io/nuget/v/ProvisionPay.SoftposDeeplinkSDK.svg?style=flat-square)](https://www.nuget.org/packages/ProvisionPay.SoftposDeeplinkSDK/)


# Dotnet Deeplink Sdk

It is a class library developed to reduce integration time in Softpos applications.

# Installation


Dotnet Deeplink Sdk is available on [NuGet](https://www.nuget.org/packages/ProvisionPay.SoftposDeeplinkSDK/).
| NuGet            |                                                        |
| :--------------- | :------------------------------------------------------ |
| Package Manager  | `PM>` `Install-Package ProvisionPay.SoftposDeeplinkSDK`                 |
| .NET CLI         |  `>`  `dotnet add package ProvisionPay.SoftposDeeplinkSDK`             |

# 1. Initialize

The SDK needs a credential first. You need to create this information with your service provider. Its entire operation is basically derived from the SoftposDeeplinkSDK class. In case the methods you invoke require an Authentication token, it logs in with the credentials.

```csharp
var credentials = new Credentials("Your Application User Id", "Your Pasword");

ISoftPosDeeplinkConfiguration sdkConfig = new SoftPosDeeplinkConfiguration()
    .WithRegion(SoftposDeeplinkSDKRegion.South_East1)
    .WithEnvironment(EnvironmentType.Test)
    .WithCredentials(credentials)
    .Build();

ISoftposDeeplinkSDK sdk = new SoftposDeeplinkSDK(sdkConfig);
```
Throws : HostException,AuthenticationException

# 2. Enroll User
With this method, you can define the user you want to use in your soft post application.
```csharp
EnrollUserRequest enrolUserRequest=new EnrollUserRequest()
{
    PackageId = "com.provisionpay.softpos.yourtenant",
    UserId = "User1",
    WspTenantId = "yourcompanytenant"
};

var enrolUserResponse = sdk.EnrollUser(enrolUserRequest);
```
Throws : ArgumentNullException ,HostException
# 3. Create Payment Session Token
You need a payment session token to your expectations to start the transaction process. You can generate a payment session token for Transaction using this method.
```csharp
var createPaymentSessionTokenRequest = new CreatePaymentSessionTokenRequest()
{
    UserHash = "User1",
    Amount = 1,
    CallBackURL = "https://google.com",
    CurrencyCode= Constants.CurrencyCode.TRY,
    OrderID = "YourOrderId",
    TransactionType = Constants.TransactionType.Sale
};

var createPaymentSession = sdk.CreatePaymentSessionToken(createPaymentSessionTokenRequest);

string paymentSessionToken =  createPaymentSessionResponse.Body.PaymentSessionToken
```
Throws : CreatePaymentSessionTokenException , HostException,ArgumentNullException
# 4. Get Transaction By Payment Session Token
You can see the details of the transaction you have made using this method.
```csharp
var transactionByPaymentSessionTokenRequest = new GetTransactionByPaymentSessionTokenRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var softposDeeplinkSDKResponse = sdk.GetTransactionByPaymentSessionToken(transactionByPaymentSessionTokenRequest);
```
Throws : ArgumentNullException, HostException
# 5. Get Payment Session Status
With this method, you can list the transaction history with payment session token.
```csharp
var paymentSessionStatusRequest = new GetPaymentSessionStatusRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var createPaymentSession = sdk.GetPaymentSessionStatus(paymentSessionStatusRequest);

```
Throw s: ArgumentNullException, HostException
# 6. Enroll User With Detail
You can use this method when you want to make a more detailed registration process. EnrollUser method is for defining default Merchant and Terminal. With this method, you can define Merchant and Terminal with the information you want.
```csharp
var enroluserWithDetailRequest =new EnrolUserWithDetailRequest()
{
    PackageId = "com.provisionpay.softpos.tenant",
    UserId = "User1",
    WspTenantId = "yourcompanytenant",

    TerminalId = "ASDA1234",
    AcquirerId = "000001",
    TerminalSerialNumber = "12312312"
    EmvProfileId = "123",

    MerchantId = "000000000000001",
    MerchantCategoryCode = "1234",
    MerchantNameLocation = "Your Merchant Adress",
    MerchantNameToPrint = "Your Merchant Name"
};

var enroluserWithDetailResponse = sdk.EnrolUserWithDetail(enroluserWithDetailRequest);
```
Throws :  ArgumentNullException , HostException , EnrollmentException
# 7. Cancel User Enrollment
You can cancel user enrollment with this method.
```csharp
var cancelUserEnrollmentRequest = new CancelUserEnrollmentRequest()
{
    UserId = "User1",
    WspTenantId = "yourcompanytenant",
};

var cancelUserEnrollmentResponse = sdk.CancelUserEnrollment(cancelUserEnrollmentRequest);
```
Throws : ArgumentNullException, HostException
# 8. Cancel User Activation
İf you want to cancel active activation of enrollment you can use this method.
```csharp
var cancelUserActivationRequest = new CancelUserActivationRequest()
{
    UserId = "User1",
    WspTenantId = "yourcompanytenant",
};

var cancelUserActivationResponse = sdk.CancelUserActivation(cancelUserActivationRequest);
```
Throws : ArgumentNullException, HostException
# 9. Handle Webhook
When you add a webhook request to your endpoint, with this method you can convert the encrypted query into the TransactionDetail model you can use. The headerKey in APICredentials is X-API-KEY by default. But you can set the HeadKey field as you want.
```csharp

APICredentials apiCredentials = new APICredentials("secretkey", "accesskey");

WebHookTransactionDetail webHookTransactionDetail = sdk.HandleWebhook(apiCredentials, "Your private key 32 character", Request);
```
Request variable must be HttpRequestMessage type in Net framework or HttpRequest type in Net core.

Throws : PaymentSessionTokenException , ApiKeyNotFoundException, ApiKeyMismatchException,APICredentialException ,HostException  ,DecryptException
# 10. IoC Support
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
# 11. Sample project
| Framework  | Architecture |                                                           |
|------------|--------------|--------------------------------------------------------------|
| NET 4.52   |MVC           |Click for [ detail](https://github.com/ProvisionPay/dotnet-demo-mvc)      |
| NET 4.52   |Webform       |Click for [ detail](https://github.com/ProvisionPay/dotnet-demo-webform)  |
| NET Core  5|Webapi        |Click for [ detail](https://github.com/ProvisionPay/dotnet-demo-webapi)   |


# 12. Supported Framework

NET 4.5, NET 4.52, NET 4.57, NET 4.8, NET Core  5,NET Core  6 , NET Core 7