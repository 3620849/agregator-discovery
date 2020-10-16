# Structure

https://github.com/3620849/agregator-discovery - discovery server

https://github.com/3620849/agregator-auth - endpoint microservice

https://github.com/3620849/agregator-data-server - data microservice

https://github.com/3620849/file-storage - image storage microservice

https://github.com/3620849/agregator-gui - web gui (Angular)

https://github.com/Alekssandr/Degradator - android gui


# Documentation
SERVER LOCATION Francfurt https://server.pakdim.com
## USER AUTHORIZATION

All endpoinds separated on two types public and secured

Secured endpoints contain in path **"/s"** and require token in header

**Example:**

          [GET] /api/s/ka
          
Public endpoints contain in path **"p"** and do not require any headers

**Example:**

          [GET] /api/p/user/{userId}

#Anonymous

All client need store clientId in their local memory and send each request as header **Client-Id**

If user has only clientId and no other valid tokens like X-Auth-Token he will be threated as user with role ROLE_ANONYMOUS 

Some endpoint like [GET] /api/s/ka allow access only with roles ROLE_USER and ROLE_ADMIN

If client doesn't send client id and not authorized (have no token) client id will be generated authomaticaly but all actions 

(add post, like, dislike,comment) can be removed from system in any time can be not considering in some calculations
          
To access secured endpoints as user with ROLE_USER or ROLE_ADMIN client need use headers like ( headers in priority order):

X-Auth-Token **for token base authorization**

M-Token **for  Oauth 2.0 authorization Google useing Google**

Dis-Oauth-Token **for Oauth 2.0 authorization Disqus**

Ggl-Oauth-Token **for Oauth 2.0 authorization Google**

Fb-Oauth-Token **for Oauth 2.0 authorization Facebook**

Vk-Oauth-Token **for Oauth 2.0 authorization Vkontakte**

Git-Hub-Oauth-Toke **for Oauth 2.0 authorization GITHUB**

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

## Oauth 2.0 Authentication 
To access through social networks like facebook there is endpoint wich allow you to get redirection url

## [GET] /api/p/systemSettings



response is set of redirect link which you can use for authentication at social network

property clientId will be generate each time new and it is augurs to define anonymouse users can be userd as their uid.

{

  "git_userAuthorizationUrl": "https://github.com/login/oauth/authorize?client_id=1234",
  
  "vk_userAuthorizationUrl": "https://oauth.vk.com/authorize?client_id=1234",
  
  "fb_userAuthorizationUrl": "https://www.facebook.com/v6.0/dialog/oauth?client_id=1234",
  
  "ggl_userAuthorizationUrl": "https://accounts.google.com/o/oauth2/v2/auth?client_id=1234",
  
  "dis_userAuthorizationUrl": "https://disqus.com/api/oauth/2.0/authorize/?client_id=1234"
  
  "clientId": "fgfgf-fgifuig-figufg"
  
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
  
  "markList": [
    
    {
        
	"messageId" : string,
        
	"time" : long,
        
	"userId" : string, 
	
        "value" : byte **like 1 dislike -1**
     }
  
  ]
  
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
 
 ## [GET] /api/p/message?type="new"&skip=0
 **to get list of post**
 
 type -parameter string can be "new","top","montly"
 
 skip -parameter long to define how many messages(posts) need to be skiped before to show next list of messages
 
 **produces:**
 
 **Content-Type application/json**
 
{
 
"messageList":[

 {"id":string,
 
clientId:string,

"time":long,
			
"responseTime":long, //time when response was send
			
"type":"POST" or "COMMENT"
			
"ancestorId":null,
			
"parentPostId":null, //if it is comment type show to which post it is attached
			
"markList":null,
			
"summary":{"like":int,"dislike":int,"comment":int,"views":int,"loads":int},
			
"userId":string, //author id
			
"userName":string, //author
			
"userPhoto":string, //author
						
"header":string", //not aplicable for comment type
			
"content":[{

"url":string,
					
"text":string
					
"type":string,
					
index":int

}]
				
"shortContent":[{

"url":string,
					
"text":string
					
"type":string,
					
index":int
}]
			
}]

}	
 
 
 
## [PUT] /api/p/message
**add new post or comment**
**Content-Type application/json**
**consumes:**

{
   "header":"hello world",

   "content":[
   
   {"text":"hello world","type":"text"},

   {"url":"https://developers.google.com/web/tools/chrome-devtools/remote-debugging/imgs/remote-debugging.png","type":"img"}
   
   {"url": "https://dg-1987.s3.eu-central-1.amazonaws.com/img/6fdf4362-0afc-434e-8f97-cae83feaa97d.mp4", "type": "video"}

   ]
}

**to add message as comment you need specify ancestorId if we commenting post ancestorId == parentPostId if we commenting another comment ancestorId == id of that comment**
{"parentPostId":"5ed021b21bcc2e2fc35c999e",

"ancestorId":"5ed022101bcc2e2fc35c999f",

"content":[{"text":string,"type":"text"}]}

## [POST] /api/p/upload
consumes image jpeg/jpg/png/ store it on Amazon S3 storage and return back public url of image

**Content-Type multipart/form-data**

**Produces :

{

   "url": string

}

## [GET] /api/p/likeOrDislike?messageId="someId"&value=1
like or dislike

**consumes parameters**

messageId - id of post/comment which user want to like/dislike

value - byte 1-like -1 - dislike

## [POST] /api/p/message

get info about message

**consumes:**

{

	"idsList":["someId"]
}

**produces same message list as [GET] /api/p/message?type="new"&skip=0**

## [GET] /api/p/comment/{id}

get comment list off particular post where id is id of post

**consume**

id - string path variable

**produce json**

{"messageList": [

{"id": string,
 
clientId:string,

"time":long,

"responseTime":long,

"type":"COMMENT",

"ancestorId":"5ed021b21bcc2e2fc35c999e",

"parentPostId":"5ed021b21bcc2e2fc35c999e",

"markList":null,

"summary":{"like":0,"dislike":0,"comment":0,"views":0,"loads":0},

"userId":"string",

"userName":string,

"userPhoto":string 

"content":[{ "text":string,"type":"text"}],

,"comments":[] -**subcoments**

}]} 

## [DELETE] /api/s/message/{messageId}
**remove message for authorized usesers 24hours , anonymouse 2minutes, admin internal**

**consumes:**

messageId - string path variable
