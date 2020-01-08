
## USER REGISTRATION


*.../api/identity/register*
*This action registers user and sends confirmation email*


```
var userCredentials = 
{
    Email : "username@example.com",
    Password: "qwerty"
}


// send credential to server
var response =  http.Post(".../api/identity/register", userCredentials)


// check response
IF(response.code == 400)
{
    DisplayToUser(response.object.errors) // Example. 1) Email invalid 2) Password must have digit 3) User already exists!
}

IF(reponse.code == 200)
{
    DisplayToUser("Successfully registered. Please confirm your Email")
}



```

## User Login

*.../api/identity/login*
*This action login in user and return 2 tokens => access_token, refresh_token*

```

var userCredentials = 
{
    Email : "username@example.com",
    Password: "qwerty"
}

// send credential to server
var response =  http.Post(".../api/identity/login", userCredentials)

IF (response.code == 400)
{
    DisplayToUser(response.object.errors) // Example. 1) Email invalid 2) User did't register! ...
}

IF (reponse.code == 200)
{
  
    // get tokens from response
    var accessToken = response.object.access_token;
    var refreshToken = response.object.refresh_token;


    // set tokens to local storage
    LocalStorage.SetItem("accessToken", accessToken);
    LocalStorage.SetItem("refreshToken", refreshToken);
}

```

## Get Courses 

*api/courses*
*Return list of courses if User authenticated*

```
var accessToken = LocalStorage.GetItem("accessToken");

// attach access token to http
http.Headers.Add("Authorization", $'Bearer {accessToken}');

var response =  http.Post("api/courses");

// If access_tken expires 401 comes
if(response.code == 200)
{
    DisplayToUser(response.object.courses);
}
```



```
if(response == 401)
{
    // get your tokens from local storage and send them to server to get new tokens
    var refreshTokeb = LocalStorage.GetItem("refreshToken");

    // create new object and 
    var tokens = 
    {
         access_token = accessToken,
         refresh_token = refreshToken
    }

    var response = http.Post(".../api/identity/refreshtokens", tokens);
    
    // get tokens from response
    var accessToken = response.object.access_token;
    var refreshToken = response.object.refresh_token;

    // delete old tokens from storage
    LocalStorage.DeteteItem("accessToken");
    LocalStorage.DeteteItem("refreshToken");

    // set new tokens to  storage
    LocalStorage.SetItem("accessToken", accessToken);
    LocalStorage.SetItem("refreshToken", refreshToken);


    // now you have new tokens
    // now you can get customers from server
    
    var accessToken = LocalStorage.GetItem("accessToken");

    // attach access token to http
    http.Headers.Add("Authorization", $'Bearer {accessToken}');

    var response =  http.Post("api/courses");

    DisplayToUser(response.object.courses);

}
```
