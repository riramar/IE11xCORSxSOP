# IE11xCORS
IE11 is not following CORS specification for local files

IE11 is not following CORS specification for local files like Chrome and Firefox.
I've contacted Microsoft and they say this is not a security issue so I'm sharing it.
From my tests IE11 is not following CORS specifications for local files as supposed to be.
In order to prove I've created a malicious cors.html.
The cors.html will be able to get an skype token and perform get on the user profile. Instead of using alert() function I could send this information to a domain that I have control.
Of course the victim needs to open the file from his local drive or maybe another application can open an IE instance.
If the user is logged on a Microsoft account and open the html file with the content above with onload function instead of onclick I'd be able to get his profile data.
This is a simple scenario. An attacker would be able to get any data from any domain that do not require a unique ID (e.g. CSRF token) which the attacker doesn't have and is unable to get.
If you do the same test on Chrome or Firefox the browser will follow CORS specification and block the response content since no CORS headers is present in the response.
I tested on IE11 running on Win7SP1  with all security patches and it worked. On Win10 didn't work. I didn't test in any server with CORS enabled.
If you think in another malicious scenario please let me know.
