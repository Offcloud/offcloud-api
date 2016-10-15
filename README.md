Offcloud.com API
===============

## Introduction to the Offcloud.com API

Offcloud API is a solution that gives developers ability to interact with Offcloud.com service, including:

* Adding an URL to Offcloud for Instant downloading
* Adding an URL to Offcloud cloud.
* Adding an URL to Offcloud for Remote downloading

All requests return JSON, including errors. All parameters should be passed to API scripts as POST request variables.


## Authentificate to the Offcloud.com API

You authenticate to the Offcloud by sending credentials to https://offcloud.com/api/login/classic 

Parameters are:

* username: actual e-mail of the registered user
* password: password of the registered user

If authentication succeeds, the script will return the following data in JSON format:

* email
* userId

For example:

```json
{
  "email": "test@test.com",
  "userId": "53f89e52822c8bf413000008"
}
```

In case of error, the script will return error message. For example:

```json
{
  "error": "Please enter a valid email address."
}
```

To send the further requests you must pass the cookie “connect.sid” returned by auth request.


## Submitting an input to Offcloud.com through the API

### Adding an URL for instant downloading

To add an URL for instant downloading, pass the following variables to the script:

```
https://offcloud.com/api/instant/download
```

* url: URL of downloaded resource
* proxyId: (optional) ID of the preferred proxy server to use. If proxyId is not specified,  the default user proxy server will be used

In the case of success, the script will return the following JSON answer:
* requestId
* fileName: the name of the requested file
* url: url for instant download
* site: website name
* status: status of the requested file. Should be ‘created’
* originalLink: link to the original file
* createdOn: date and time when request was processed

In the case when adding this URL is not available for this user, API will return the following JSON answer:
* not_available

Here are the following values of the ‘not_available’ response and their descriptions:

| Name | Description          |
| ------------- | ----------- |
| premium      | User must purchase a premium downloading addon for this download.|
| links     | User must purchase a Link increase addon for this download.   |
| proxy     | User must purchase a proxy downloading addon for this download. |
| video     | User must purchase a video sharing site support addon for this download.     |

When a request cannot be processed, API will return error message in the JSON answer.


### Adding an URL for cloud downloading.

To add an URL for cloud downloading, pass the following variables to the script:

```
https://offcloud.com/api/cloud/download 
```

* url: URL of downloaded resource

In the case of success, the script will return the following JSON answer:
* requestId
* fileName: the name of the requested file
* url: url for download from the cloud
* site: website name
* status: status of the requested file downloading. Can be ‘created’, ‘downloaded’, ‘error’.
* originalLink: link to the original file
* createdOn: date and time when request was processed

If you want to start a process of downloading from the cloud, please, be convinced that the status of download is ‘downloaded’. You can check the status of download request by sending an API call (see the section “Retrieving a status of user’s cloud download” below).

In the case when adding this URL is not available for this user, API will return the following JSON answer:
* not_available

Here are the following values of the ‘not_available’ response and their descriptions:

| Name | Description          |
| ------------- | ----------- |
| premium      | User must purchase a premium downloading addon for this download.|
| links     | User must purchase a Link increase addon for this download.   |
| proxy     | User must purchase a proxy downloading addon for this download. |
| cloud     | User must purchase a clowd downloading upgrade addon for this download. |
| video     | User must purchase a video sharing site support addon for this download.     |

When a request cannot be processed, API will return error message in the JSON answer.


### Adding an URL for remote downloading.

To add an URL for remote downloading, pass the following variables to the script:

```
https://offcloud.com/api/remote/download 
```

* url: URL of downloaded resource
* remoteOptionId: ID of the remote account where to download

To get a list of all users remote accounts, see the section “Retrieving a list of remote accounts”.

In the case of success, the script will return the following JSON answer:
* requestId
* fileName: the name of the requested file
* site: website name
* status: status of the requested file downloading. Can be ‘created’, ‘downloaded’, ‘error’.
* originalLink: original link to the file
* createdOn: date and time when request was processed

The API call doesn’t return a link for immediate downloading, you can only check the status of download request by sending additional API call (See the section “Retrieving a status of user’s remote download” below).

In the case when adding this URL is not available for this user, API will return the following JSON answer:
* not_available

Here are the following values of the ‘not_available’ response and their descriptions:

| Name | Description          |
| ------------- | ----------- |
| premium      | User must purchase a premium downloading addon for this download.|
| links     | User must purchase a Link increase addon for this download.   |
| proxy     | User must purchase a proxy downloading addon for this download. |
| video     | User must purchase a video sharing site support addon for this download.     |

When a request cannot be processed, API will return error message in the JSON answer.


## Retrieving some data from Offcloud.com through the API

### Retrieving a list of available proxy servers

To get a list of available proxy servers, make a query to the following script:

```
https://offcloud.com/api/proxy/list
```

This script will return a JSON array of the available proxy servers with the following data:
* id
* name: name of the proxy server
* region: location of the proxy server


### Retrieving a status of user’s cloud download

To get a status of user’s cloud download, pass a requestId parameter to the following script:

```
https://offcloud.com/api/cloud/status
```

The server will return status of the download or an error message if the request cannot be processed.


### Exploring zipped files or folder archives from cloud

To explore your zipped files or folder archives in your cloud history, simply pass a requestId parameter to the following script:

```
https://offcloud.com/api/cloud/explore
```

The server will return a JSON array of download links to each file stored in archive.


### Retrieving a list of user’s remote accounts

To get a list of user’s remote accounts, make a query to the following script:

```
https://offcloud.com/api/remote-account/list
```

This script will return a JSON array of the current user’s remote accounts with the following data:
* accountId
* type: type of account
* username: login used for the remote account
* status: status of the account
* host: remote host
* port: remote port
* usage: amount of traffic used by this remote account


### Retrieving a status of user’s remote download

To get a status of user’s remote download, pass a requestId parameter to the following script:

```
https://offcloud.com/api/remote/status
```

The server will return a status of the download or an error message if the request cannot be processed.



## Check that user is logged in to Offcloud.com

To check that user is logged in to Offcloud.com, make a query to the following script:

```
https://offcloud.com/api/login/check
```

This script will return a JSON response with the following data:
* loggedIn: 1 if user is logged in, else 0

## Examples

Because API uses cookies to authenticate requests, the simplest way to use API is to write a browser script. Here are some examples of jQuery API calls:

##### Authorization:

``` js
$.ajax({
    url: 'https://offcloud.com/api/login/classic',
    data: {username: 'test@test.com', password: 'test'},
    type: 'POST',
    crossDomain: true, // enable this
xhrFields: {
    withCredentials: true
},
    success: function(data) { console.log(data); },
    error: function() { console.log('Failed!'); }
});	
```

#####  Adding an URL for Instant downloading:

```js
$.ajax({
    url: 'https://offcloud.com/api/instant/download',
    data: {'url' : 'http://www.cnn.com'},
    type: 'POST',
    crossDomain: true, // enable this
xhrFields: {
    withCredentials: true
},
    success: function(data) { console.log(data); },
    error: function() { console.log('Failed!'); }
});	
```

#####  Retrieving a list of available remote accounts:

```js
$.ajax({
    url: 'https://offcloud.com/api/remote-account/list',
    data: { },
    type: 'POST',
    crossDomain: true, // enable this
xhrFields: {
    withCredentials: true
},
    success: function(data) { console.log(data); },
    error: function() { console.log('Failed!'); }
});	
```
