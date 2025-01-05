### Finding storage xss with the help of Electron app

Hi, this is my first write-up. I hope you like it!

In this post, I will explain how I discovered a Stored XSS vulnerability that allows an attacker to steal a user's access token.

Let's refer to the website as https://example.com.


## Intercept Electron app traffic 

While working on `example.com`, I discovered that they provide a desktop app created using Electron (Chromium and Node.js environment).

After installing the app, I started searching for a way to intercept the HTTP traffic from the app. Initially, I tried using Wireshark, but it didn't work. Then I found an easier way to do it:

1- First we open the app file location.
2- Open linux shell or  windows powershell in the current directory.
3- Use the following command 

`app name  --args --proxy-server=http://127.0.0.1:8080`


`http://127.0.0.1:8080` for burp you can change it.


## Unrestricted file upload


Once I had set up the interception and could see the requests in Burp Suite, I started navigating through the app. Eventually, I found that users could upload images, and the image URL would be on `https://cdn.example.com/image_path`.

The cdn.example.com domain was essentially an S3 bucket, so I tried uploading an HTML or SVG file to check for XSS. However, the file extension filter only allowed PNG, JPG, etc.


### Bypass

I tried bypassing this filter by changing the file extension to `.png` while setting the content type to `text/html`. I then inserted the XSS payload inside the image content.

Now I had Stored XSS on `cdn.example.com`, but the impact was limited since the domain itself didn’t provide much functionality.




![1-14](https://user-images.githubusercontent.com/47279932/170807342-8d8fffc9-e5d0-4867-bc59-c7661147c58a.png)

## Steal users access_ token using open redirect. 

Going back to `https://example.com`, I found that the application uses two different API versions, and each has a different way of authenticating users.

One of the APIs involves redirecting the user with a random code, which is then exchanged for an access token. After that, the user is redirected with the access token, and it is exchanged for a JWT token. I wanted to check if I could redirect the access token to my server.

After several attempts to modify the redirect URL, nothing worked. The only allowed redirect URL was `https://example.com/`, but we could still set the path to any URL in the application (e.g., `https://example.com/any_path/*`).

If we found another open redirect on `https://example.com/any_path/`, we could redirect the user to `https://cdn.example.com/image_path/`, where our XSS payload was located.

While searching, I found that the login page had a redirect parameter, like this: `https://example.com/login?redirect=`. After some trial and error, I discovered that the redirect URL was only allowed to point to subdomains of example.com. This was actually useful for completing the exploit.


## Full exploit

### Step one:

First, upload the XSS payload. The payload will contain JavaScript code that filters the access token from the URL hash and sends it to our server.


### Step two:

Now, combine the two open redirects we found.

1- Redirect the user to generate the access token.

2- Then, redirect the user to the second open redirect on the login page.

3- From the login page, redirect the user to `https://cdn.example.com/xss_file_path`, which contains our XSS payload.


---------------------------------
My Twitter account :

`https://twitter.com/Majed_Refaea`
