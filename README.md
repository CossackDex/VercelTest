# APP POC

Demo of application -> https://web-production-5bdf3.up.railway.app/

The concept of the app I will split into 3 seperate sections:
1. Base application 
2. E-mail whitelist
3. User Count 

Boilerplate for base application can be easily created by Auth0 app generation. For the POC purpose I will be using 
regular web application, but Auth0 offers template for Native, Single page application and Machine to machine apps.

Auth0 dashboard offers extensive view of several important information but at this moment the most crucial right now is Settings view 
and Connections. 

In settings under Basic Information there is Domain/Client ID and Client Secret. These will be set as environmental variable for app, that will allow app to connect with Auth0.
Another important section in this view is Application URIs, where you will set URLs for callback and logout according to your hosting.

To allow users to login via Google and Facebook you need to open Connections subpage, and turn on switch for both of connections. 
In case when you will need more connections, Marketplace offers other solution like Apple/Github etc. 

For E-mail whitelist and User count I will use Auth0 Action Flows, Login to be precise. 

![](.README_images/7170ef33.png)

```javascript
exports.onExecutePostLogin = async (event, api) => {
const whitelistEmailsSet = new Set(['d.matuszczyk98@gmail.com', 'sample@example.com'])

  if (event.user.email && whitelistEmailsSet.has(event.user.email)) {
}
  else {
  api.access.deny(`Access to ${event.client.name} is not allowed.`);
};
}
```

```javascript
exports.onExecutePostLogin = async (event, api) => {
  if (!event.user.user_metadata.userCount){
    api.user.setUserMetadata("userCount", 1)
  }
  else{
    api.user.setUserMetadata("userCount",event.user.user_metadata.userCount+1 )
  }

  const userCount = event.user.user_metadata.userCount;
  const namespace = "https://sampleapp.com"
  if (event.authorization) {
    // Set claims 
    api.idToken.setCustomClaim(`userCount`, userCount);
  }

};
```