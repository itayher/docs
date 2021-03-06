In Backand's system, you can create server-side activity called Actions. These actions can be used for the purpose of security, integration, performance, notification and data integrity, among others, providing you with more flexibility in your app's design. There are two types of Actions that can be created. The first are initiated via a direct web request. These are known as "On Demand" actions. Additionally, you can create automated actions that take place based upon a data interaction event. These automated actions can occur whenever you create, update, or delete an item in your system. On Demand actions are associated with a specific object, and can be found on the Object --> {name} page in the Actions tab. The automated Create, Update and Delete actions are associated with a specific object that is compatible with a specific row in a table, while On Demand actions make association with a specific role optional.

There are 3 kinds of actions that can be created for either action type:
- Server side JavaScript code actions
- Transactional database script actions
- Send Email actions
All 3 types of actions use the following common parameters:
* A Where condition - a SQL where clause that determines if the action will be performed.
* Input Parameters, added to the query string of the request that triggers the action, that will serve as variable values that you can supply to your action's code. These parameters will serve as tokens in the action definition and will be replaced with the actual values when the code executes.

# Server-side JavaScript Code

You can run standard JavaScript on the server. It runs on the [V8 engine](http://en.wikipedia.org/wiki/V8_(JavaScript_engine)). To execute the JavaScript action, put your code into the following function:

```
function backandCallback(userInput, dbRow, parameters, userProfile) {
    // write your code here
    return {};
}
```

The function parameters are:
* **userInput**: This parameter is only provided for Create and Update actions, and is the object that was sent to the action. It is null for Delete actions, as well as for On Demand actions.
* **dbRow**: This parameter is populated in After Create, Update, and Delete automated actions, and if you supply an optional ID to an On Demand action. The dbRow parameter will contain the row's entry in the database prior to any changes made.
* **parameters**: This parameter represents the variables sent in the query string for the action.
* **userProfile**: This parameter stores the current username, the user's role, and the access token used by the user to perform the action. It is of the format {"username": "string", "role": "string", "token": "string"}.
In addition to the above parameters, you can also make use of the following global objects:
* $http - a service for AJAX calls
* `$http({method:"GET",url:CONSTS.apiUrl + "/1/objects/objectexample" , headers: {"Authorization":userProfile.token}});`
* CONSTS - CONSTS.apiUrl for Backands API URL
* `console.log(message, object)` and `console.error(message, object)`, to debug your code
Automated actions will have a response that matches the format expected by the triggering call (such as the return value of a CREATE call). On Demand actions, though, will return whatever is returned by the custom server code, which can be any properly-formatted JSON.

##Error Handling

If your code results in an error (for example, if you write the following: `throw new Error("An error occurred!"))`, the request will return HTTP status 417, and the response body will contain the associated error message.

# Transactional Database Scripts

Transactional database scripts are SQL scripts that run within the same transaction context as the triggering action, provided the event occurs in the object event "During the data save before the object is committed". This means that if the Create, Update or Delete request fails then your script will be rolled back like any other transaction.

# Send Emails

Send Email actions, in addition to common parameters, allow you to also supply the usual email fields: To, Cc, Bcc, From, Subject and Message. In addition, you can provide an object ID to obtain a deep object to use in the action.

