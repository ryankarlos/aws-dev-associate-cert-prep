## Cognito User Pool (Authentication)

User pools are for authentication (identity verification). A user pool is a user directory in Amazon Cognito. 
With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign i
n through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers. 
Whether your users sign in directly or through a third party, all members of the user pool have a directory 
profile that you can access through a Software Development Kit (SDK).

User pools provide:
* Sign-up and sign-in services.
* A built-in, customizable web UI to sign in users.
* Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool.
* User directory management and user profiles.
* Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification.
* Customized workflows and user migration through AWS Lambda triggers.

After successfully authenticating a user, Amazon Cognito issues JSON web tokens (JWT) that you can use to secure and authorize 
access to your own APIs, or exchange for AWS credentials.

o enable users in your user pool to access AWS resources, you can configure an identity pool to exchange user pool tokens for 
AWS credentials.(see next section)

## Cognito Identity Pool (Authorization)

Identity pools are for authorization (access control). You can use Amazon Cognito identity pools to create unique 
identities for your users and authenticate them with identity providers. With an identity, you can obtain 
temporary, limited-privilege AWS credentials to access other AWS services. Amazon Cognito identity pools
support public identity providers—Amazon, Apple, Facebook, and Google—and unauthenticated identities. 
It also supports developer authenticated identities, which let you register and authenticate users via your 
own backend authentication process.

While creating an identity pool, you're prompted to update the IAM roles that your users assume. IAM roles work like this: 

Your identity pool can include: 
1) Users in an Amazon Cognito user pool
2) Users who authenticate with external identity providers such as Facebook, Google, Apple, or an OIDC or SAML identity provider.
3) Users authenticated via your own existing authentication process

When a user logs in to your app, Amazon Cognito generates temporary AWS credentials for the user. These temporary credentials are associated with a specific IAM role. With the IAM role, 
you can define a set of permissions to access your AWS resources. You can specify default IAM roles for authenticated a
nd unauthenticated users. In addition, you can define rules to choose the role for each user based on claims in the user's ID token. 

Use an identity pool when you need to:
* Give your users access to AWS resources, such as an Amazon Simple Storage Service (Amazon S3) bucket or an Amazon DynamoDB table.
* Generate temporary AWS credentials for unauthenticated users.


Amazon Cognito supports developer authenticated identities, in addition to web identity federation through 
Facebook (identity pools), Google (identity pools), Login with Amazon (identity pools), and Sign in with 
Apple (identity pools). With developer authenticated identities, you can register and authenticate users 
via your own existing authentication process, while still using Amazon Cognito to synchronize user 
data and access AWS resources. Using developer authenticated identities involves interaction 
between the end user device, your backend for authentication, and Amazon Cognito.

#### Workflow

A user authenticating with Amazon Cognito goes through a multi-step process to bootstrap their credentials. Amazon Cognito has two different flows for
authentication with public providers: enhanced and basic.
Once you complete one of these flows, you can access other AWS services as defined by your role's access policies. 


**Enhanced (simplified) authflow**

When you use the enhanced authflow, your app first presents an ID token from an authorized Amazon Cognito user pool or third-party identity 
provider in a GetID request. The app exchanges the token for an identity ID in your identity pool. The identity ID is then 
used with the same identity provider token in a GetCredentialsForIdentity request. The enhanced workflow simplifies 
credential retrieval by performing GetOpenIdToken and AssumeRoleWithWebIdentity in the background for you. 
GetCredentialsForIdentity returns AWS API credentials.
* GetId
* GetCredentialsForIdentity


**Basic (classic) authflow** 
When you use the basic authflow, your app first presents an ID token from an authorized Amazon Cognito 
user pool or third-party identity provider in a GetID request. The app exchanges the token for an identity ID in your 
identity pool. The identity ID is then used with the same identity provider token in a GetOpenIdToken request. GetOpenIdToken 
returns a new OAuth 2.0 token that is issued by your identity pool. You can then use the new token in an 
AssumeRoleWithWebIdentity request to retrieve AWS API credentials. The basic workflow gives you more granular control 
over the credentials that you distribute to your users. The GetCredentialsForIdentity request of the enhanced authflow 
requests a role based on the contents of an access token. The AssumeRoleWithWebIdentity request in the classic workflow 
grants your app a greater ability to request credentials for any AWS Identity and Access Management role that you 
have configured with a sufficient trust policy.

* GetId
* GetOpenIdToken
* AssumeRoleWithWebIdentity

When using Developer authenticated identities (identity pools), the client uses a different authflow that includes 
code outside of Amazon Cognito to validate the user in your own authentication system. 
Code outside of Amazon Cognito is indicated as such.

**Enhanced authflow**

* Login via Developer Provider (code outside of Amazon Cognito)
* Validate the user login (code outside of Amazon Cognito)
* GetOpenIdTokenForDeveloperIdentity
* GetCredentialsForIdentity

**Basic authflow**

* Login via Developer Provider (code outside of Amazon Cognito)
* Validate the user login (code outside of Amazon Cognito)
* GetOpenIdTokenForDeveloperIdentity
* AssumeRoleWithWebIdentity

For most customers, the Enhanced Flow is the correct choice, as it offers many benefits over the Basic Flow:
1. One fewer network call to get credentials on the device.
2. All calls are made to Amazon Cognito, meaning it is also one less network connection.


## Cognito Sync 

Amazon Cognito Sync is an AWS service and client library that makes it possible to sync application-related user data 
across devices. Amazon Cognito Sync can synchronize user profile data across mobile devices and the web without 
using your own backend. The client libraries cache data locally so that your app can read and write data regardless 
of device connectivity status. When the device is online, you can synchronize data. If you set up push sync, you 
can notify other devices immediately that an update is available.

