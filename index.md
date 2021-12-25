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

## EndPoints

**Fetch Secure Code**

This end point is used for generating a Secure Code or Client ID for new Client Application. 
This request is done usually ONCE when configuring HexaEight Serverless and is treated as one request

```
https://hexaeight-sso-platform.p.rapidapi.com/get-new-securetoken
```

**Fetch Auth Token**

This end point is used for authenticating an Encrypted Authenticated Web Token transferred by the User Mobile
in Cookie Based Authentication and consumes one request.  Since the user information is saved in the cookie, 
there is no need to use this request more than once within an hour after user authentication is complete. 

```
https://hexaeight-sso-platform.p.rapidapi.com/fetchauthtoken
```

**Extend Auth Token Expiration**

This end point is used for extending the expiration of an existing Encrypted Authenticated Web Token used in Cooke based Authentication 
by one hour and is treated as one request.

```
https://hexaeight-sso-platform.p.rapidapi.com/extendauthtoken
```


**Fetch Cookie User**

This end point is used after extending the expiration of a JSON Web token to determine if the expiration 
request was successful and to decode the new expiration time in order to update the existing cookie in Cookie based authentication.  
This is the same endpoint that is used by External Services or Non Cookie based applications to decode the authenticated user using JSON Web tokens.

```
https://hexaeight-sso-platform.p.rapidapi.com/get-cookieuser
```

The code snippets on how to use these EndPoints for various languages is available [HERE](https://rapidapi.com/hexaeight-hexaeight-default/api/hexaeight-sso-platform)


* * *


# HexaEight Serverless

Documentation related to HexaEight Serverless is available [HERE](/serverless.md).

### Deploy HexaEight Serverless On Vercel Platform

Documentation on how to integrate our Authentication using Next.js project is available [HERE](/serverless.md#quick-start)

### Deploy HexaEight Serverless On CloudFlare Workers

Documentation on how to integrate our Authentication using CloudFlare Worker is available [HERE](/serverless.md#deploy-on-cloudflare-worker)

* * *  
 


# HexaEight Token Service
    

**Coming Soon**

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
