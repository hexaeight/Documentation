* TOC
{:toc}

# HexaEight Authentication

## Introduction

HexaEight Serverless is a Cloud Solution for implementing Domain Wide authentication 
for Websites and Web Applications using a Custom Domain name.

HexaEight Token Service is an On Premisis Solution for protecting 
resources Applications using Access and Refresh tokens similar to OAuth2.

## Components

HexaEight Authentication has four Components

 1.  Server Side Code Base
 2.  Client Side Code Base
 3.  Data Sinks
 4.  Mobile Application

**Server side code base :**

The Server side code base which performs the 
authentication is implemented as a Serverless Function and is currently 
available for CloudFlare Workers and is written in Javascript.

**Client Side Code Base :**

The Client side code base which endorses the client application is also 
written in Javascript and needs to be included on any web page 
that needs to incorporate HexaEight Serverless authentication.
  
**Data Sinks :**

Datasinks are drop points that should be accessible to the Mobile Applicaton
as well as to the Server Side Code Base in order to transfer an 
encrypted token.

**Mobile Application :**

Our Native Mobile Application HexaEight Authenticator is used by the user to Login to your application.

In order to implement HexaEight Serverless in your Environment, you will need to install the Server Side Code Base 
using your Account with the Cloud Provider and allow your users to download our free Mobile Application to scan the QR Code and login
to your application.

HexaEight Serverless is currently available only for installation on a Cloudflare Worker, but should be available 
for Other Cloud Providers like Microsoft Azure, AWS, Google Cloud, Vercel shortly.

* * *

# HexaEight Serverless

## Deploy On CloudFlare Worker

In order to install the Server Side Code base, you need access to your Cloud Flare account and have the ablility 
to authenticate using wrangler using an API Token.  In addition, you also need to have ability to create a worker 
in your Cloud Flare Account.

**Limitations:  CloudFlare current does not support registering only a subdomain 
as this feature is limited to Enterprise customers as such you can deploy HexaEight 
Serverless only for Top level domains and not subdomains. We are aware of this limitation 
and agressively working towards make HexaEight Serverless available on other Cloud Providers.**

How To Deploy 

1. [Subscribe](https://rapidapi.com/hexaeight-hexaeight-default/api/hexaeight-sso-platform/pricing) to one of our Plans and obtain an API Key.

2. Deploy the Cloud Flare Worker Template by following the instructions.

    Follow the Instructions available [HERE](https://github.com/HexaEightTeam/HexaEight-Auth-CFWorker-Template) under the README section to Deploy HexaEight Serverless using a Cloud Flare Worker.

3. Download and install the Mobile Application and create a Login Token. Instructions for Our Mobile Application Are Available [Here](https://www.hexaeight.com/docs/quick-instructions.html)

4. Access your login page which should be available at https://login.yourdomain.dom/loginpage

5. Test the Authentication using the Mobile App.


* * *

## CF Worker Metrics

Cloud Flare Workers allow limited execution of Workers under the Free Plan and unlimited execution in the Paid Plan 
however it is important to understand the number of times a worker is called to complete HexaEight login process.

The Cloudflare worker Script is called when accessing the below EndPoints

1. Accessing the Login Page `/login/loginpage`
2. Mobile application submits an encrypted token to the Private Datasink. `/login/sink`
3. Displaying the Dashboard Page upon successful login `/login/success`
4. While using any of the below endpoints 
    - `login/getuseremail` - To get the user email using the cookie if the user is logged in
    - `login/extendSession` - To extend the user cookie session if the user is logged in.
    - `login/getsessionexpiry` - To check the time remaining before the Cookie expires in order to extend the Cookie session.

The `/login/extendSession` endpoint will only try to extend the cookie expiration by an hour if its called within the 
last 15 minutes prior to cookie expiry. Trying to extend the cookie expiry 15 minutes before wont work since it involves a 
cost factor of using HexaEight RAPID API key to extend the session.
So it is advisible if you invoke `/login/getsessionexpiry` first to check if the time remaining is less than 15 minutes 
before attempting to extend the cookie session.

A typical login sequence uses up 3 requests in a Cloud Flare worker.  There is no logout sequence implemented since your 
application has to just delete the cookies to implement a logout sequence if you really want to logout an user or in a 
SSO environment, do not implement any logout sequence, since the user needs to have access to other applications using 
the same logged in cookie. 

From your application perspective, you will only need to only implement extending cookie session by calling the Cloudflare 
worker prior to cookie expiry to keep the user active in your application.

HexaEight Serverless sets the below three cookies after a user has logged into your domain.

```
hexaeightsessionid : codechallenge
```

Example :  
hexaeightsessionid:xqjIYBERvjcwjHcIGJ4ZkQUABHIwmSZm6z_OFtJE9OA

Hexaeight Sessionid cookie contains the code challenge presented by the Client Application to the Server Server side code base. 
You can use this code challenge like in a PKCE authorization to verify the authenticity of the JWT token which is available in the hexaeighttoken. It is very useful to deploy this
kind of check in an external service to verify the authenticity of the JWT token before allowing access to the service. 

```
hexaeighttoken : JSON Web Token
```

Hexaeight Token contains the JSON Web Token containing the user identity and can only be decoded by calling 
HexaEight `Fetch Cookie User` API Endpoint 

>     https://hexaeight-sso-platform.p.rapidapi.com/get-cookieuser

The code snippets to implement Fetch Cookie User for various languages is available [HERE](https://rapidapi.com/hexaeight-hexaeight-default/api/hexaeight-sso-platform)

```js
{ iss : auth.hexaeight.com, 
user: user@domain.dom,
state : User Authenticated Successfully,
clientcodechallenge : xqjIYBERvjcwjHcIGJ4ZkQUABHIwmSZm6z_OFtJE9OA,
securetext: HexaEight Default Login  Application version 1.6.8,
aud : rapidapiuser,
iat : 27301399,
nbf: 1638083923,
exp: 27301459}
```

The above decrypted information is available after a successful call to `Fetch Cookie User` EndPoint

```
hexaeightuserinfo:user@domain.dom:1638087540000 
```

HexaEight User Info cookie contains information about the user logged in along with expiry time in unixtime milliseconds.  
If you do not want to call Worker token endpoint you can use this value to determine the cookie expiry time before which the session needs to be extended. 

JWT Expiry:  The cookie set by HexaEight Serverless expires exactly at the same time when the cookie expires.  At times you might wish to use the JWT directly
in an external or internal service to keep the user logged in to perform some background tasks on behalf of the user. So it may be viable for you to implement a 
background service that extends the cookie expiration instead of calling the worker script since the worker script is dependent on the cookie. 

You can extend the JWT by invoking the `Extend Auth Token` End Point https://hexaeight-sso-platform.p.rapidapi.com/extendauthtoken
using your Rapid API key associated with the Client Secure Code (Client ID) by sending the JSON Web Token as a post request to the EndPoint 
and receive an new JWT extended by one hour.  You can then call the `Fetch Cookie User` end point to verify the extended JWT.

* * *

## CF Audit Logs

HexaEight Serverless logs the email address of any user who attempts to login to your application in a KV namespace on 
CloudFlare called AUDITLogs.  These logs are stoed as Key value pairs have a default ttl expiry of 1 week after which they 
get automatically expired deleted. It is your responsibility to export these entries into a log database for log term retention.

Here is a [Sample GitHub Project](https://github.com/stevenpack/cloudflare-workers-kv-export) that shows how to export KV namespaces to a SQLite 


Logs are categorized into four types and explained below:

```
Key - Stores the Time at which the login was attempted.
Value - Stores the user information who attempted the login and the action outcome.
```

Categories:

```
LoginSuccess : User was successfully authenticated.
LoginBlocked : User was valid but blocked because Email Domain Filter was active.
CookieExtensionSuccess : JWT inside the cookie was extended successfully by one hour.
```

**Security Note: A user who tries to login with a wrong password into HexaEight Serverless 
residing in your domain, will not even be able to generate an encrypted token, since our 
Platform will detect this and block the user, hence you will never be able to see any Log of 
unauthorizied user attempts without email addresses since our platform handles it and doesent even 
let that request reach your Domain.**

* * *

## Customize Login Pages

At times you may want to customize your login page to include alternate login options along with HexaEight Authentication. 
If you wish to implement your own login page design, you can do so by following the below guidelines.

Create the below two div tag elements along with two buttons and place it at an appropriate location.

```js
<div id="display-hexaeight-qrcode"> </div>
<div id="display-hexaeight-qrcodeid"> </div>
<button id="login-hexaeight-button"> Login </button>
<button id="scan-hexaeight-qrcode"> Scan QR Code </button>
```

Include the below Script tag and change the values to the input parameters
Change the servername to your preferred subdomain and the redirecturl to any successpage of your choice

```js
<script id="hexaeightclient" src="https://cdn.jsdelivr.net/gh/hexaeight/jslibrary/hexaeight-token-quickauth.js" servername=subdomain.yourdomain.dom" path="/" redirecturl="/anysuccesspage.html" clientappcode="your client id" datasinkprotocol="https" datasinkurl="login.yourdomain.dom/login/sink"></script>
```
And your custom login page should be ready.

***Important***


**The default loginpage is served under login.yourdomain.dom by the setting in wranger.toml where as the above servername setting is the subdomain where the custom login page is being displayed.**

Remember that Cloudflare Worker is deployed for all subdomains under yourdomain.dom Any url with the path ending in /login followed by anything 
is intercepted by the worker as shown below,

So any subdomain can be protected by deploying a custom login page and invoking our quickauth script which uses the /login to initiate the page protection.

```
*.hexaeight.com/login*
```

We have already designed a [Sample login page](https://demo.hexaeight.com/cf-login.html) to show case this capability.  
You can follow the same and implement your own login page.

This above sample login page is also available at 
[HexaEight-Auth-CFWorker-Template](https://github.com/HexaEightTeam/HexaEight-Auth-CFWorker-Template) on GitHub.

* * *

# Understanding DataSinks

Data Sinks are nothing but a random drop and pickup location points that is used to 
transmit Encrypted tokens between the User Mobile Application and Server Side Code Base.  
One of the Salient features that these Data sinks posses is that these drop and pickup points are for one time usage.  
In addition to this feature the drop points which is used by the Mobile Application to transfer the encrypted
token cannot be read by the Client Side Code base. 

**Data Sinks can be Private or Public, example of a simple Public Data sink that can be 
used for our authentication is [Internet Clipboard](https://cl1p.net)**

While in a Public Data Sink, the encrypted tokens can be accessed by anyone, in the event the 
encrypted token is accessed by a malicious program or user prior to the Server Side Code Base accessing that location, 
the encrypted token will no longer be available for the Server Side Code base. This is because of the Data Sink feature of allowing access to data in 
the location for ONLY one time use. Since the Client Side Code Base deploys [PKCE](https://oauth.net/2/pkce/) verification it is not possible for a malicious 
program or attacker to use the enrypted tokens to perform any impersonation using an encrypted token since the code verifier and code challenges wont match.

Private Data Sinks are implemented directly in the Server Side code base and offer greater protection for encrypted 
token transferred by Mobile Application since it is not possible for any malicious user to access these tokens once 
they are sent to the Server. 

If you are wondering why does HexaEight Serverless support Public Datasinks, it bascially providing a fine 
balance between Security and Cost. Since Private Datasinks are implemented on the Server Side code base, 
accessing the Datasink from the Mobile is treated as a request which adds to the cost and hence we provided 
a low cost solution like the [Internet Clip](https://cl1p.net) board which allows you buy a url location 
which can contain more drop or pickup sub locations for a very low price which can outbeat the cost if 
you use the Private Datasink.

**From a Security perspective, its important to understand our encrypted tokens can NEVER be deciphered or 
used to impersonatate an user, you just need to be aware that in a public datasink the encrypted tokens are 
accessible to anyone if they know the drop point location. We have left the decsion on the choice of DataSinks to the Implementor**

* * *


# HexaEight Token Service

## Coming Soon

* * *

# Mobile Application


## Availability

HexaEight Authenticator can be installed for Andorid from [Google Play Store](https://play.google.com/store/apps/details?id=com.companyname.hexaeightauth) as well as for iOS from [Apple Play Store](https://apps.apple.com/in/app/hexaeight-authenticator/id1584547289)

Additional Instructions for Usage On Mobile are available [HERE](https://www.hexaeight.com/docs/quick-instructions.html)

## Best Practices To Protect Authentication Tokens

We outline the best practices for users to follow when using our Mobile Application so that 
they can safeguard themselves against malicious attacks targeted at them. 

1.  Use a valid Email address to verify your identity.  Temporary Email Addresses are not allowed in our System.

2. Protect your Email Inbox and do not allow anyone to access your Email. HexaEight sends QR Code emails for verification 
to your Inbox, so if anyone gets access to your Inbox, they may able to steal your identity and impersonate you. 

3. If you are opening Inbox like Gmail on your Mobile phone, ensure to implement a App Lock so that no one can access 
your Inbox without your knowledge.

4. Set a strong Password for your Email Authentication token.  Remember every Vault is associated with an email address, 
and the email address is associated with a single Email Login Token and protected using a password which only you know. 
So set a strong Password which you can remember.

5. Remember that the same Email Authentication token can log you into multiple websites or applications. So you need to 
ensure that you just remember this one strong password and NEVER EVER EVER EVER share the password with anyone.

6. If you happen to forget your password, just delete the existing Email Authentication Token from your mobile and 
generate a new one using the Mail button and follow the same verification process to confirm your identity. You can 
then use the new password to login instantly to your favorite apps and websites.

7. There may be times when you need to trust some with access to a particular site or application that displays a 
HexaEight QR Code for authentication.  Do not give them your mobile phone and password and ask them to use it to login. 
Instead scan the QR Code remotely via a Video call if you trust the person and then give them access to your account.

8. Lastly whenever you type the authentication password in HexaEight Mobile App, the password is stored in the Phone 
memory until its closed, ensure to close the HexaEight App if you don't plan to use the app for a longer period of time like 
Swipe up from the bottom on the Phone, hold, then let go. Swipe up on the HexaEight Authenticator app to close it
