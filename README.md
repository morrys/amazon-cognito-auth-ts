# Amazon Cognito Auth SDK for JavaScript
You can now use Amazon Cognito Auth to easily add sign-in and sign-out to your mobile and web apps. Your user pool in Amazon Cognito is a fully managed user directory that can scale to hundreds of millions of users, so you don't have to worry about building, securing, and scaling a solution to handle user management and authentication.

[For more information about this new feature, see Amazon Cognito User Pools App Integration and Federation GA Release.](http://docs.aws.amazon.com/cognito/latest/developerguide/getting-started.html)

# Introduction
The Amazon Cognito Auth SDK for JavaScript simplifies adding sign-up, sign-in with user profile functionality to web apps.

Instead of implementing a UI for sign-up and sign-in, this SDK provides the UI via a hosted page.  It supports sign-up, sign-in, confirmation, multifactor authentication, and sign-out.


# Installation

Install amazon-cognito-auth-ts using yarn or npm:

```
yarn add amazon-cognito-auth-ts
```


### Install using separate JavaScript files

TODO

### Configuration

The Amazon Cognito Auth SDK for JavaScript requires three configuration values from your AWS Account in order to access your Cognito User Pool:

* An User Pool App Client Id (required): e.g. `<TODO: add ClientId>` 
    * When creating the App, if the generate client secret box was **checked**, for /oauth2/token endpoint which gets the user's tokens, the client must pass its client_id and client_secret in the authorization header. For more info, please reference [here](http://docs.aws.amazon.com/cognito/latest/developerguide/token-endpoint.html).
* An App Web Domain (required): e.g. `<TODO: add App Web Domain>`
    * When you click the `Domain name` tab, you can create a domain name there and save it for record. 
* Scope Array (required): `['<TODO: your scope array here, try "phone", "email", ...>'],` e.g.`['phone', 'email', 'profile','openid', 'aws.cognito.signin.user.admin']` (to get more info about scope, please reference ["scope" section of our doc](http://docs.aws.amazon.com/cognito/latest/developerguide/authorization-endpoint.html))
    * When you click the `App settings` tab, you can select the identity provider which you want to use on your App. 
    * In the `sign in and sign out URLs` tab, you can set the `Callback URLs` and `Sign out URLs`. (both are required)
    * Under the `OAuth2.0` tab, you can select the OAuth flows and scopes enabled for this app. (both are required)
* IdentityProvider (Optional): Pre-selected identity provider (this allows to automatically trigger social provider authentication flow).e.g. `Facebook`
* UserPoolId (Optional): e.g. `<TODO: add UserPoolId>` 
* AdvancedSecurityDataCollectionFlag (Optional): boolean flag indicating if the data collection is enabled to support cognito advanced security features. By default, this flag is set to true.

The [AWS Console for Cognito User Pools](https://console.aws.amazon.com/cognito/users/) can be used to get or create these values.

Note that the various errors returned by the service are valid JSON so one can access the different exception types (err.code) and status codes (err.statusCode).


### Usage

The usage examples below use the unqualified names for types in the Amazon Cognito Auth SDK for JavaScript. Remember to import or qualify access to any of these types:

```ts
import {CognitoAuth} from 'amazon-cognito-auth-ts';
```

**Use case 1.** Registering an auth with the application. You need to create a CognitoAuth object by providing a App client ID, a App web domain, a scope array, a sign-in redirect URL, and a sign-out redirect URL: (Identity Provider, UserPoolId and AdvancedSecurityDataCollectionFlag are optional values)

```js
/*
  TokenScopesArray
  Valid values are found under:
  AWS Console -> User Pools -> <Your user pool> -> App Integration -> App client settings
  Example values: ['profile', 'email', 'openid', 'aws.cognito.signin.user.admin', 'phone']

  RedirectUriSignOut 
  This value must match the value specified under:
  AWS Console -> User Pools -> <Your user pool> -> App Integration -> App client settings -> Sign out URL(s)
*/
var authData = {
	ClientId : '<TODO: add ClientId>', // Your client id here
	AppWebDomain : '<TODO: add App Web Domain>',
	TokenScopesArray : ['<TODO: add scope array>'], // e.g.['phone', 'email', 'profile','openid', 'aws.cognito.signin.user.admin'],
	RedirectUriSignIn : '<TODO: add redirect url when signed in>',
	RedirectUriSignOut : '<TODO: add redirect url when signed out>',
	IdentityProvider : '<TODO: add identity provider you want to specify>', // e.g. 'Facebook',
	UserPoolId : '<TODO: add UserPoolId>', // Your user pool id here
	AdvancedSecurityDataCollectionFlag : '<TODO: boolean value indicating whether you want to enable advanced security data collection>', // e.g. true
        Storage: '<TODO the storage object>' // OPTIONAL e.g. new CookieStorage(), to use the specified storage provided
};
var auth = new CognitoAuth(authData);
```

All the methods used in the authentication workflow are managed by "promises" unless you want to manage them through callback functions you need to set userhandler:

```js
auth.userhandler = {
	onSuccess: function(result) {
		alert("Sign in success");
		showSignedIn(result);
	},
	onFailure: function(err) {
		alert("Error!");
	}
};
```
You can also set `state` parameter:

```js
auth.setState(<state parameter>);
```

**Use case 2.** Sign-in using `getSession()` API:

```js
auth.getSession();
```

For the cache tokens and scopes, use the `parseCognitoWebResponse(Response)` API, e.g. the response is the current window url:

```js
var curUrl = window.location.href;
auth.parseCognitoWebResponse(curUrl);
```
Typically, you can put this part of logic in the `onLoad()`, e.g.:

```js
function onLoad() {
	var auth = initCognitoSDK();
	var curUrl = window.location.href;
	auth.parseCognitoWebResponse(curUrl);
}
```

**Use case 3.** Sign-out using `signOut()`:

```js
auth.signOut();
```
**Important to know**

By default, the SDK uses implicit flow(token flow), if you want to enable authorization code grant flow, you have two options:

Option 1:
```js
var auth = new CognitoAuth(authData, false);
```
Option 2:
```js
var auth = new CognitoAuth(authData);
auth.useCodeGrantFlow();. 
```
