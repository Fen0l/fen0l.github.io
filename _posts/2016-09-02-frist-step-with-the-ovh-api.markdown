---
layout: post
title:  "First step with the OVH API!"
date:   2016-09-03 23:00:00
author: Anthony
categories: API
tags:	OVH API
cover:  "/assets/banner/api_ovh.png"
---

# Hey !

For a personal project, I had to use the [OVH API][ovh_api]. And because it's OVH, they have a giant and amazing RESTful API.

## How it works ?

To be able to use it, you need 3 keys.

* Application key
* Secret app key
* Consumer key

### 1. Create a new App

The two first can be took directly from their page [createApp][ovh_api_register] by filling fields. Its very easy.

### 2. Request a token

Now, to be able to use the API, you have to request a token. Many languages are supported, but this is an example using cURL. Here, I request a total access _(path: /*)_ for the GET method. The redirection field will redirect you after the authentication.

```
curl -XPOST -H"X-Ovh-Application: APP_KEY" -H "Content-type: application/json" \
https://eu.api.ovh.com/1.0/auth/credential  -d '{
    "accessRules": [
        {
            "method": "GET",
            "path": "/*"
        }
    ],
    "redirection":"https://anthonypradal.com/"
}'
```

In the response, you will found a validation url and your Consumer key.

```
{
	"validationUrl":"https://eu.api.ovh.com/auth/?credentialToken=6HM2dEqzqwktRFMJfWd9eQOndwYxRepWtrxxuOea7KaOcwIq7qYSwnsOwKUlL2db",
	"consumerKey":"MMzWGeFHUldmIDAzyfAl2gkZx2grkFCd",
	"state":"pendingValidation"
}
```

The last thing to do is visit this url, choose a validity duration connect with your OVH account.


And voilà, you can now make a lot of requests ! 

More even, OVH provides you several wrappers available on their [github][ovh_github_wrapper].



[ovh_api]: 				https://api.ovh.com
[ovh_api_register]: 	https://eu.api.ovh.com/createApp/
[ovh_github_wrapper]: 	https://github.com/ovh?utf8=✓&query=-ovh




