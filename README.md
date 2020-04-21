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
          
To access secured endpoints client need use headers like:

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

Every secure endpoint require token to identify user, if no toke will be provided or token be corrupted or expired server will return error with status **401**

## secured api


## [GET] /api/s/ka
**stub for checking if token authorization is passed**

**required** one of the header **"Authorization X-Auth-Token Git-Hub-Oauth-Token Vk-Oauth-Token Fb-Oauth-Token Ggl-Oauth-Token Dis-Oauth-Token"**

return plain/text **true**

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
