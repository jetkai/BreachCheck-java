[up-shield]: https://img.shields.io/uptimerobot/status/m788787797-6044d62f9e978ab17c5fd1b9?style=flat-square
[up-url]: https://api.rsps.tools
[uptime-shield]: https://img.shields.io/uptimerobot/ratio/m788787797-6044d62f9e978ab17c5fd1b9?style=flat-square
[uptime-url]: https://api.rsps.tools

# RSPS/Gaming Database Breach Checker
[![Status][up-shield]][up-url]
[![Uptime][uptime-shield]][uptime-url]

`(~Uptime affected by updates)`

## About

**Please do not abuse or misuse this tool as this is a free tool intended for the large amount of RSPS players who have compromised passwords (~60%).**

This tool will allow you to check if a user's password/hash is compromised from a known-list of leaked RSPS databases.
The password will be hashed using SHA-1 by default and sent to an API that will compare the hash. JSON data will be returned, mentioning if the password is in a breach.

All passwords are hashed on the back-end and include around ~800K unique passwords from various RSPS database leaks (from 2009 - present)

You can implement this to your LoginDecoder (on account creation) or a ChangePassword command for example. [THROTTLE THESE REQUESTS]

Calls to the API are not logged and I would HIGHLY recommend sending hashed passwords and NOT plain-text.
API is Cloudflare Rate Limited and is set to 1000 requests a minute (PER IP). If you require more requests per minute, message me. If you are receiving error "429", please adjust your usage.

## API - api.rsps.tools/jetkai/breachcheck

### Supported algorithms:
- MD5, SHA-1, SHA-256, SHA-512 & PLAIN-TEXT

**BCrypt is not available.** Further hashing algorithms added upon request.

— Can use HTTP or HTTPS protocol

— Data is sorted by most commonly used passwords and hashed

— Returns hashPos (this is how common the hash is, such as 123 being 0 - the most common)

— Option to view/download up-to 100,000 of the most common hashes (as json format)

Request Example 1. Query if Hash/Password is on Breach List:
---
### Request Data Example:
```
(required) token : <String>
(optional-default="") hash : <String> [HASHES ONLY]
(optional-default="") password : <String> [PLAN TEXT PASSWORDS ONLY]

//MD5
https://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=25ab1f0f2d6386a2702867cd82573ada

//SHA-1
https://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=403926033d001b5279df37cbbe5287b7c7c267fa

//SHA-256
https://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=ed8779a2222dc578f2cffbf308411b41381a94ef25801f9dfbe04746ea0944cd

//SHA-512
https://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=0e2d148eff53f3b82ee3aa6f62c9ef8e3ceeddff865a733c294db55023b121e81f5ffdde83dc07e274c7389d1e1e430c20d582889a6399c32811fff47f260be6

//PLAIN-TEXT
https://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&password=123123
```
### Return Data Example:
```
{
	"token": "39439e74fa27c09a4",
	"hash": "ed8779a2222dc578f2cffbf308411b41381a94ef25801f9dfbe04746ea0944cd",
	"hashPos": 2,
	"severity": "Top 100 Common Passwords",
	"databaseBreach": "Stoned 2021 ~800K Unique Passwords (15+ RSPS Databases)",
	"hashType": "SHA-256",
	"breached": true
}
```

Request Example 2. View/Download X amount of hashes as a JSON, sorted by most commonly used: [New Feature]
---
### Request Data Example:
```
(required) viewhashes : true, false
(required) hashtype : md5, sha1, sha256, sha512
(optional-default=0) amount : 0 -> 100000
(optional-default=false) download : true, false

//Top 5 most common MD5 Hashes
https://api.rsps.tools/jetkai/breachcheck?viewhashes=true&hashtype=md5&amount=5

//Top 5 most common SHA-1 Hashes
https://api.rsps.tools/jetkai/breachcheck?viewhashes=true&hashtype=sha1&amount=5

//Top 5 most common SHA-256 Hashes
https://api.rsps.tools/jetkai/breachcheck?viewhashes=true&hashtype=sha256&amount=5

//Top 5 most common SHA-512 Hashes
https://api.rsps.tools/jetkai/breachcheck?viewhashes=true&hashtype=sha512&amount=5
```
### Return Data Example:
```
{
     "hashes": [
        "7c4a8d09ca3762af61e59520943dc26494f8941b",
        "40bd001563085fc35165329ea1ff5c5ecbdbbeef",
        "99efc50a9206bde3d7a8e694aad8e138ca7dc3f7",
        "403926033d001b5279df37cbbe5287b7c7c267fa",
        "8cb2237d0679ca88db6464eac60da96345513964"
     ]
}
```
Implement this into a Server Source (Example):
---
This is an Example implemententation... I would recommend still giving an option for players to use these passwords, just use this as a message warning. This example shows how to block newly created accounts from using ANY breached password.
1. Copy the **BreachCheckAPI.java** file over to the utils folder
2. Copy the **commons-codec-1.15.jar** library file over to your libs filder
3. Add the **commons-codec-1.15.jar** library to your compiler / IDE
4. Open the **PlayerLoading.java** file
5. Find:
```
		if (!file.exists()) {
			return LoginResponses.NEW_ACCOUNT;
		}
```
6. Replace with
```
		if (!file.exists()) {
			BreachCheckAPI bca = new BreachCheckAPI();
			bca.setPassword(player.getPassword());
			return bca.isBreached() ? LoginResponses.LOGIN_COULD_NOT_COMPLETE : LoginResponses.NEW_ACCOUNT;
		}
```
7. Edit response 13 within your Client.java file (on your client) and change the message to resemble "Your password is too weak, use another password" or "This is a commonly used password, please use another".
8. Finished product:

![image](https://user-images.githubusercontent.com/26250917/126927573-6b1f3ddc-b232-451e-90fd-acead577463d.png)

9. You can also add this to the ::changepassword command as-well, refusing to allow the password to be changed (or warn)
