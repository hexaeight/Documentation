* TOC
{:toc}

# HexaEight Serverless

## Serverless Authentication Flow

![Authentication Flow](https://www.hexaeight.com/assets/images/hexaeight-quick-authentication-836x441.png)


## Deploy On Vercel Platform (ZEIT)

This is a [Next.js](https://nextjs.org/) cloned project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).


HexaEight Server Side Code base is now integrated in a [Next.js](https://nextjs.org/) cloned project bootstrapped 
with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) that has the capability to authenticate any 
email user.  Since the authentication code base is already baked into this template, there is no need for developers to write a single line
of code to integrate authentication for their Next.js Project.

This integration when deployed successfully on Vercel Platform, automatically displays a [login page](https://vercel.hexaeight.com) when you visit the vercel site instead
of the index.js default page. Only upon successful authentication, you will be able to access the default index.js page.

This template has baked in the authentication code in the following files.

```
/next.config.js - Rewrites all requests ending with /login to /api/loginpage

/pages/api/loginpage.js - Contains HexaEight Serverless Server Side Code Base which authenticates any email user

/pages/_app.js - Implements automatic Silent refresh of user session using setinterval function.

/pages/_middleware.ts - The middleware typescript function which redirects the user to login page if the user session has expired
```
Developers are free to add their own code into the above files to implement any additional logic required for their project.

### Quick Start

This Authentication template is specific to Vercel Deployment and requires the following environment variables for HexaEight Serverless to start authenticating users.

    RapidAPIKey 
    clientappcode


Get an API Key for HexaEight Secure Platform from [RapidAPI](https://rapidapi.com/hexaeight-hexaeight-default/api/hexaeight-sso-platform/pricing)

A Free Plan is available if you want to test the authentication. Once you have subscribed to a plan, your Rapid API key is available 
@
[Rapid API Dashboard](https://rapidapi.com/developer/dashboard) / Choose the default application / Security on the left hand pane / Application Key / Copy your API Key 
 
Run the below command to Generate a New Client App Code (or Client ID) for your Login Application using your RAPID API Key

Before you execute the below commands you should replace "your rapid api key" with the key you copied from the previous step

Also change the application name to desired description of your choice. This application name is used to identify the orign of authentication for your authenticated users.
This output of this is a Client ID (similar to Oauth Client ID which is used to identify Apps) This Client ID is tied to the Rapid API user account, so you can only decode the tokens 
for this Client ID using the same API keys associated with your Rapid API user account.

From Unix Or Mac using Shell
    
    curl --header 'x-rapidapi-key: your rapidapi key' --data 'Default Login Application v 1.0' --request POST --url https://hexaeight-sso-platform.p.rapidapi.com/get-new-securetoken --header 'content-type: text/plain' --header 'x-rapidapi-host: hexaeight-sso-platform.p.rapidapi.com'

OR

From Windows using Powershell

    $h = @{"x-rapidapi-host"="hexaeight-sso-platform.p.rapidapi.com"; "x-rapidapi-key"="your rapid api key";}
    $response = Invoke-WebRequest -Body 'Default Login Application v 1.0' -Uri 'https://hexaeight-sso-platform.p.rapidapi.com/get-new-securetoken' -Method POST -Headers $h -ContentType 'text/plain';$response.Content;


Once you have the RapidAPIKey and Clientcode, you can use the below Button to Deploy our Authentication template on Vercel Platform.

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2FHexaEightTeam%2FHexaeight-Auth-Nextjs-Vercel-template&env=clientappcode,RapidAPIKey&envDescription=Client%20ID%20used%20for%20identifying%20the%20login%20application%20authenticating%20your%20users%20And%20the%20API%20Key%20used%20to%20fetch%20identity%20information%20of%20user&envLink=https%3A%2F%2Fdocs.hexaeight.com%2Fserverless.html)

This Authentication is also available as an Integration On Vercel Platform.

### Detailed Instructions

In this section, we will provide detailed information related to our implementation details inside this next.js project template

#### How Does Our Authentication Work

When you visit your vercel site after deploying our template,

 1. The middleware typescript (pages/_middleware.ts) intercepts all requests served under pages and redirects the user to login page (/login/loginpage) if the user is not authenticated.
 2. This file /next.config.js rewrites all requests ending with /login and followed by any suffix to be redirected to /api/loginpage.js
 3. This file /pages/api/login.js contains the logic to authenticate email users. If the user is not authenticated, a login page with a QR Code is displayed to the user.
 4. The user will need to install [HexaEight Authenticator Mobile App](https://www.hexaeight.com/mobile.html) on their mobile and create a digital token for his/her email id and then use the digital token to scan the above QR Code
 5. Once the user is successfully authenticated, the middleware typescript (pages/_middleware.ts) will redirect the user to the requested page.
 6. When the user is authenticated, three cookies that contain the user information is available on the Server Side which means these cookies are availble to your Server Side Code usually implemented in /api folder 
 7. One of these cookies contain a JSON Web Token that contain the user information,  however the HexaEight Secure Platform issues JSON Web Token by default only for an hour, hence these cookies will also expire the user session in an hour.
 8. In order to keep the user session active after an hour, this JSON Web token will need to be exchanged in our platform with a new token which will extend the user session by another hour. This file /pages/_app.js implements this automatic Silent refresh of user session using setinterval function every 3 minutes 

Developers will only need to copy _middleware.ts into any subfolder under pages directory  in order to protect these pages servered under subfolders and redeploy the project.



#### Environment Variables

Our authentication template by default requires only two environment variables to get started.  However there are other environment variables
that can be configured to suit your environment. By default our template automatically gathers information about your site and sets default values for these variabbles. 

Below we will explain in detail every environment variable used by our authentication template along with the information on how to customize them to suit your environment.

##### RapidAPIKey (Required)

This is your API Key that is required to perform universal authentication using HexaEight Secure Platform by verifying the Authentication token received from the user upon authentication.

##### clientappcode (Required)

This is the client ID provided by our platform that is used to identify if the orign of the authenticated user in external services.

When the user is authenticated successfully a cookie is automatically available to your code which contains the email id of the logged in user.
However if you want to use an internal or external service  that needs to provide data specific to the user, for example a proxy service which can validate the JWT 
and fetch user specific data from a database and send it back, it would be easier for your to send the JSON Web token to the proxy service.

Assume if you have developed the proxy service, then you can send a request to HexaEight Secure Platform to decode this JSON Web Token. A decoded webtoken contains the information as shown below

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

The securetext contains the application name that you used when you created the clientappcode. This allows your proxy service to verify the origin of authentication and allow the
service to proceed to gather user specific information using the user email address and respond back with the request. 

The clientappcode provides greater security when you pass JSON Web Tokens across microservices that provides them the capabiity to verify the origin of the authentication before processing the request.

##### usemacrometakv (Optional)

Allowed Values : YES/NO

Our template by default uses a [Public Datasink](https://cl1p.net) to deploy HexaEight Serverless. This ensures you can get started with our authentication instantly without external dependancies
If you want to continue using the public datasink, we recommend you purchase a customized url from the public datasink provider for a very low fee ($5 per year) and use our [datasink](https://docs.hexaeight.com/serverless.html#datasink-optional) environment variable to customize the location.

If you wish to use the public datasink you should set the value of this variable to NO 

In order to understand Datasinks in detail please visit our [Understanding DataSinks](https://docs.hexaeight.com/#understanding-datasinks) section

Our Authentication template supports [Macrometa](https://www.macrometa.com) which provides the option
to create a low cost Key Value store on the Edge that allows for exchanging tokens during authentication.

If you wish to use Macrometa private datasink you should set the value of this variable to YES

You can sign up for an account from [here](https://auth.paas.macrometa.io/signup)

```
Disclaimer:  We have no affliation with Macrometa. We are actively scanning and will support any private sink provider that provides a low cost Key value store like Macrometa and allow Swagger API to access the key value store
```

##### usemacrometakvapikey (Optional)

Allowed Values : Macrometa API Key

If you have already signed up in Macrometa platform, you can create an API key by navigating to Macrometa Dashboard and accessing

Account (Left Hand Pane ) / Api Keys (Right Hand Top Pane) / New Api Key (Left Hand Second Top Pane)

``` 
Give a name for your key and click the Create button
```

You will be prompted to copy your key since it wont be displayed again.
Copy this key and use it in this environment variable.

##### usemacrometakvsinkname (Optional)

Allowed Values : Macrometa Key Value Collection name

Macrometa allows you to create a key value collection name using their dashboard. To create a Key value collection, navigate to Macrometa Dashboard and choose

Collections (Left Hand Pane ) /  New Collection (Left Hand Second Top Pane) / Key-Value Store 

```
Give a name for your datasink, ensure that the expiration is turned ON, ensure Enable Stream is not clicked and click the Create button
```

Use the above datasink name for this variable.

This completes the configuration required on Macrometa and you can safely logout from Macrometa site.

* * *

In order to understand subsequent environment variables, you must understand the location these environment variables are used.
Our [default login page](https://vercel.hexaeight.com/login/loginpage) uses a script tag. An Exmaple script tag is shown below for reference.

```js
<script id="hexaeightclient" src="https://cdn.jsdelivr.net/gh/hexaeight/jslibrary/hexaeight-token-quickauth.js" servername="myverceldomainname.dom" path="/" redirecturl="/loginsuccess" clientappcode="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=" datasinkprotocol="https" datasinkurl="myverceldomainname.dom/login/sink"></script>
```

The clientappcode which was discussed earlier is also used in the above script tag.

##### servername (Optional)

This environment variable refers to the custom domain name if you have configured custom domains feature. If you have not confirgured custom domain name,
do not set this environment variable which will force the script to use your Vercel github repo slug (system environment variable in Vercel) followed by .vercel.app as the servername

process.env.NEXT_PUBLIC_VERCEL_GIT_REPO_SLUG  + '.vercel.app'

##### datasinkprotocol (Optional)

This environment variable contains the protocol to be used while accessing the datasink by the HexaEight Mobile App that uses the Datasink to transfer the authenticated token

##### datasink (Optional)

This environment variable contains the datasink host that is used to transfer the authentication token. If this environment variable is not configured, it uses 
api.cl1p.net as the datasink host name.

If you are using Macrometa Datasink, then this environment variable should point to the value in Servername environment variable shown above in order to post authentication tokens from the mobile app to your macrometa datasink.

The datasinkurl in the above script tag uses datasink environment variable to automatically build the datasinkurl value.

* * *

The next set of environment variables are used for specific customization in your environment.


##### cookiedomain (Optional)

Custom Cookie Domain Example : .vercel.hexaeight.com

Cookie Domain Example (When this environment variable is not configured) : .hexaeight-serverless-push.vercel.app

Our Authentication template by default uses vercel provided domain name as cookie domain name. Our Authentication template sets same site http cookies in your domain post user authentication.  
If this environment variable is not configured, the default cookie domain used will be your git hub repo slug that was used when you deployed our template. The value used for this
environment variable is derived from the below Vercel system environment variable if its not configured

"." + process.env.NEXT_PUBLIC_VERCEL_GIT_REPO_SLUG  + '.vercel.app';

##### emaildomainsfilter (Optional)

Allowed Values : NO/YES

If this environment variable is not configured or set to NO, then a domain filter is not enforced for your application.

By default our Authentication template has the capability to authenticate any email address from any domain.  However if you want to limit the authenticated users to specific email domains, then
you must set this environment variable to YES and configure the environment variable emaildomainslist which contains the list of allowed email domains.

##### emaildomainslist (Optional)

Allowed Values : List of email domains in single quotes seperated by comma and the entire list enclosed in double quotes as shown below

```
"'gmail.com','facebook.com','yahoo.com','mydomain.com'"
```

This environment variable is used by our authentication template to only allow only email users belonging to the above list of email domains to login to your application.

Note: Our authentication template does not implement the authorization aspect, it the responsibility of the developer to implement authorization logic post our authentication.

##### bottomlogo (Optional)

Allowed Values : URL pointing to a custom image

By default our [default login page](https://vercel.hexaeight.com/login/loginpage) displays a lock at the bottom of the page. 

You can set this environment variable to point to your custom logo, for example your company logo or product logo instead of the lock.

##### auditing (Optional)

Allowed Values : ENABLED/DISABLED

The default setting for this environment variable is enabled.  By defaut our authentication template captures logs and writes them to the console like shown below

```
1640411432678:LoginSuccess:user@domain.com:{"iss":"auth.hexaeight.com","user":"user@domain.com","state":"User Authenticated Successfully","clientcodechallenge":"37HWGJbiZFQ8siYy0HEcV2g0zCYJp7VLwHzH2GEMsto","securetext":"My Serverless Test Login Application v 1.0","aud":"rapidapiuser","iat":27340190,"nbf":"1640411425","exp":27340250}Capturedby:HexaEight Serverless
```

Since vercel platform does not store any logs, you can only view these captured logs at run time by accessing the Function logs from Vercel Dashboard.

If you wish to capture logs for retention, set up [log drains](https://vercel.com/blog/log-drains) as discussed [here](https://vercel.com/support/articles/how-do-i-store-logs-on-vercel)


* * *

## Deploy On CloudFlare Worker

In order to install the Server Side Code base, you need access to your Cloud Flare account and have the ablility 
to authenticate using wrangler using an API Token.  In addition, you also need to have ability to create a worker 
in your Cloud Flare Account.

**Limitations:  CloudFlare current does not support registering only a subdomain 
as this feature is limited to Enterprise customers as such you can deploy HexaEight 
Serverless only for Top level domains and not subdomains. If you need to support
subdomain, you should consider deploying on other providers like vercel which support subdomains.**

How To Deploy 

1. [Subscribe](https://rapidapi.com/hexaeight-hexaeight-default/api/hexaeight-sso-platform/pricing) to one of our Plans and obtain an API Key.

2. Deploy the Cloud Flare Worker Template by following the instructions.

    Follow the Instructions available [HERE](https://github.com/HexaEightTeam/HexaEight-Auth-CFWorker-Template) under the README section to Deploy HexaEight Serverless using a Cloud Flare Worker.

3. Download and install the Mobile Application and create a Login Token. Instructions for Our Mobile Application Are Available [Here](https://www.hexaeight.com/docs/quick-instructions.html)

4. Access your login page which should be available at https://login.yourdomain.dom/loginpage

5. Test the Authentication using the Mobile App.


* * *

### CF Worker Metrics

Cloud Flare Workers allow limited execution of Workers under the Free Plan and unlimited execution in the Paid Plan 
however it is important to understand the number of times a worker is called to complete HexaEight login process.

The Cloudflare worker Script is called when accessing the below EndPoints

1. Accessing the Login Page `/login/loginpage`
2. Mobile application submits an encrypted token to the Private Datasink. `/login/sink`
3. Displaying the Dashboard Page upon successful login `/login/loginsuccess`
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

Alternatively, you could even use the code challenge as an symmetric key to encrpt data between the calling application and the external service,
so that the external service knows that only an application which has access to the code challenge will be able to decrypt the data.

```
hexaeighttoken : JSON Web Token
```

Hexaeight Token contains the JSON Web Token containing the user identity and can only be decoded by calling 
HexaEight `Fetch Cookie User` API Endpoint 

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

You can extend the JWT by invoking the `Extend Auth Token` End Point using your Rapid API key associated with the Client Secure Code (Client ID) by sending the JSON Web Token as a post request to the EndPoint 
and receive an new JWT extended by one hour.  You can then call the `Fetch Cookie User` end point to verify the extended JWT.

Additional Information about EndPoints is availabe in the EndPoints Section on this page.

* * *

### CF Audit Logs

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


