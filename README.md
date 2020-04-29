# Documentation
SERVER LOCATION Francfurt http://18.156.3.210:8081
## USER AUTHORIZATION

All endpoinds separated on two types public and secured

Secured endpoints contain in path **"/s"** and require token in header

**Example:**

          [GET] /api/s/ka
          
Public endpoints contain in path **"p"** and do not require any headers

**Example:**

          [GET] /api/p/user/{userId}
          
To access secured endpoints client need use headers like ( headers in priority order):

Dis-Oauth-Token **for Oauth 2.0 authorization Disqus**

Ggl-Oauth-Token **for Oauth 2.0 authorization Google**

Fb-Oauth-Token **for Oauth 2.0 authorization Facebook**

Vk-Oauth-Token **for Oauth 2.0 authorization Vkontakte**

Git-Hub-Oauth-Toke **for Oauth 2.0 authorization GITHUB**

X-Auth-Token **for token base authorization**

Authorization **for token base authorization**

**There is several type of authorization:**

## Token base Authorization

- User send request with header **Authorization** which contains login and password in base64 format at any secured url (which starts like /api/s...)
credentials shoul be in format **Basic login:password**

**Example:**

          [GET] /api/s/ka

          **header:** Authorization: Basic ccccGFrOjEyMww==

- Response will contain **X-Auth-Token** which client should save per user and send each request to identify user.
Once user want logout token can be deleted

Every secure endpoint require token to identify user, if no toke will be provided or token be corrupted or expired server will return error with status 401

if user will send 2 authorization headers like **X-Auth-Token** and **Authorization** server first check X-Auth-Token if it sucseed it will grant access Authorization header won't be considered.

## Oauth 2.0 Authentication (!!!ONLY FOR WEB CLIENT NOW!!!)
To access through social networks like facebook there is endpoint wich allow you to get redirection url

**[GET] /api/p/systemSettings

response is set of redirect link which you can use for authentication at social network

{

  "git_userAuthorizationUrl": "https://github.com/login/oauth/authorize?client_id=1234",
  
  "vk_userAuthorizationUrl": "https://oauth.vk.com/authorize?client_id=1234",
  
  "fb_userAuthorizationUrl": "https://www.facebook.com/v6.0/dialog/oauth?client_id=1234",
  
  "ggl_userAuthorizationUrl": "https://accounts.google.com/o/oauth2/v2/auth?client_id=1234",
  
  "dis_userAuthorizationUrl": "https://disqus.com/api/oauth/2.0/authorize/?client_id=1234"
  
}

When user will enter one of this link he will be redirected to proper social network(vk.com for example) were he need be registered beforehand.After login there he will be redirected to web site with specific **code** in url parameter example: www.ourwebsite.com/sso?provider=vk&code=61095ab603f4fd98b5 using this code client send request to our server at endpoint 

[GET] /api/p/token?code=123&provider=vk

**parameters:**

 **code** - code which social network return to client
 
 **provider** - shortcut of sical network name it can be one of this: git, vk, fb, ggl, dis
 
 **response** will be json with token 
 
{
 
    "access_token" : "123fd545fg5"
    
} 

use this token in header to acess secured endpoints, header should be depending what social network do you use Git-Hub-Oauth-Token Vk-Oauth-Token Fb-Oauth-Token Ggl-Oauth-Token Dis-Oauth-Token

## secured api

## [GET] /api/s/ka
**stub for checking if token authorization is passed**

**required** one of the header **"Authorization X-Auth-Token Git-Hub-Oauth-Token Vk-Oauth-Token Fb-Oauth-Token Ggl-Oauth-Token Dis-Oauth-Token"**

return plain/text **true**

## [GET] /api/s/user
**get current user information according token**

**required** one of the header **"Authorization X-Auth-Token Git-Hub-Oauth-Token Vk-Oauth-Token Fb-Oauth-Token Ggl-Oauth-Token Dis-Oauth-Token"**

**produce :**

{
"id": string,
  
  "password": "string",
  
  "username": string,
  
  "login": string,
  
  "mail": string,
  
  "photo": string,
  
  "authorities": [{  
      "authority": "ROLE_USER", **or ROLE_ADMIN or ROLE_ANONYMOUS;**     
    }]
    
  "password": "", **always empty string**
  
  "accountNonExpired": boolean,
  
  "accountNonLocked": boolean,
  
  "credentialsNonExpired": boolean,
  
  "enabled": boolean

}


## public api


## [PUT] /api/p/user
**to register(add) new user in system**

**type :** application/json

**body:**

{

  "id": string,
  
  "password": "string",
  
  "username": string,
  
  "login": string,
  
  "mail": string,
  
  "photo": string
  
}
## [GET] /api/p/user/{userId}
**to get user by id**

**produce :**

{
"id": string,
  
  "password": "string",
  
  "username": string,
  
  "login": string,
  
  "mail": string,
  
  "photo": string,
  
  "authorities": [{  
      "authority": "ROLE_USER", **or ROLE_ADMIN or ROLE_ANONYMOUS;**     
    }]
    
  "password": "", **always empty string**
  
  "accountNonExpired": boolean,
  
  "accountNonLocked": boolean,
  
  "credentialsNonExpired": boolean,
  
  "enabled": boolean

}

## [GET] /api/p/nomination/time
**this will return time till nomination and timeout in milliseconds when time nomination will be 0 time will stoped and timeout will start сountdown, during this time system will define new prizewinner, after timeout reach 0 time and timeout will be discarded to max value 604800000 ms (1 week) and  600000 ms (10minutes) respectively. Endpoint refresh data every 60000ms**

**produce :**

{

   "time":604800000,
 
   "timeout":600000
 
 }
 
 ## [GET] /api/p/post?type="new"&page=1
 **to get list of post**
 
 **parameters:**
 
type - "new", "top","montly" **required list type**
          
page - integer **required page inside list there is 20 elements on each list**

**response:**

{

  "postList": [
  
    {
    
      "time": long,
      
      "responseTime": long,
      
      "metaData": null    
      
      "summary": null,
      
      "userId": null,
      
      "userName": string,
      
      "userPhoto": string,
      
      "clientId": string,
      
      "id": string,
      
      "header": string,
      
      "content": [
      
        {
        
          "url": string,
          
          "text": string,
          
          "type": "img" or "text"  or "video",
          
          "index": int,
          
        }
        
      ],
      
      "shortContent": null
      
    }
    
}

## [POST] /api/p/post
**add new post**

**consumes**

{

 "header": string;
 
 "content": [
 
    {
    
     "url": string;
     
     "text": string;
     
     "type": string "img","text","video";
     
     "index": int;
    }
  
  ]
  
}
