### Finding storage xss with the help of Electron app

Hi, This is my first writeup, I hope you like it.


I Will explain how i found Stored xss, Allow attacker to steal users' access_token.



let's call the website as `https://exemple.com`


## Intercept Electron app traffic 


while I was working on `exemple.com`, I Found they provide desktop app, The app was created using Electron app (Chromium and Node.js environment).


After installing the app, I Start searching way to intercept the HTTP traffic from the app, I tried first using Wireshark but didn't work, the Another way i found It was easy to use:

1- First we open the app file location.
2- Open linux shell or  windows powershell in the current directory.
3- Use the following command 

`app name  --args --proxy-server=http://127.0.0.1:8080`


`http://127.0.0.1:8080` for burp you can change it.


## Unrestricted file upload


After all done and the request was showing in burp suite, I start go through the app, Eventually i found we can upload image and the image location was on `https://cdn.exemple.com/image_path`.


The cdn.exemple.com was just s3 bucket, So now we need to try to upload html or svg to get xss, But there was filter on the file extension only png,jpg... allowed.


### Bypass

The other method I tried, is to set file extension to png, change content type to text/html, then but the xss payload in the image content.

Now i have Stored xss on cdn.exemple.com, but cdn.exemple.com doesn't have much impact.


![1-14](https://user-images.githubusercontent.com/47279932/170807342-8d8fffc9-e5d0-4867-bc59-c7661147c58a.png)

## Steal users access_ token using open redirect. 

Back to https://exemple.com i was work on, The application has two different api version, Each api has different way to authenticate.

The authentication on one of the api was, Redirect the user with random code, Exchange the code with access_token, Then redirect user with access_token, Exchange the access_token with jwt token, Now lets check if the we can redirect the access_token to our server.


After all the try to change the redirect url nothing works, We can only use `https://exemple.com/` as the redirect url, But still we can set the redirect to any path in the application, for example `https://exemple.com/any_path/*`.


So if we found other open redirect on `https://exemple.com/any_path/` we can redirect the user to `https://cdn.exemple.com/imag_path/` which contain our xss payload.

while searching i found the login page has redirect parameter `https://exemple.com/login?redirect=`, After some try and error, there was check and only allowed to set the redirect url to subdomain of exemple.com, But this is something we want, now we can complete the exploit.


## Full exploit

### Step one:
We upload our xss payload, the payload content will be javascript code, the javascript code filter the access_ token from URL hash and Then send the access_token to our server.

### Step two:
Now combine the two open redirect we found.

First redirect the user to generate access_token, then redirect the user to second open redirect on the login page, from the login page redirect the user to `https://cdn.exemple.com/xss_file_path`, That contains our payload there.


---------------------------------
My Twitter account :

`https://twitter.com/Majed_Refaea`
