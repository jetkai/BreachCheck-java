# BreachCheck
Simple API to check if you have been breached within an RSPS Database

Please do not abuse or misuse this tool as this is a free tool intended for the large amount of RSPS players who have compromised passwords (~60%).

This tool will allow you to check if a user's password/hash is compromised from a known-list of leaked RSPS databases.
The password will be hashed using SHA-1 by default and sent to an API that will compare the hash. JSON data will be returned, mentioning if the password is in a breach.

All passwords are hashed on the back-end and include around ~800K unique passwords from various RSPS database leaks (from 2009 - present).

You can implement this to your LoginDecoder (on account creation) or a ChangePassword command for example. [THROTTLE THESE REQUESTS]

Calls to the API are not logged and I would HIGHLY recommend sending hashed passwords and NOT plain-text.

Supported inputs:
- MD5
- SHA-1
- SHA-256
- PLAIN-TEXT

BCrypt is not available. Further hashing algorithms added upon request.

Request Data Example:

//MD5
http://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=25ab1f0f2d6386a2702867cd82573ada

//SHA-1
http://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=403926033d001b5279df37cbbe5287b7c7c267fa

//SHA-256
http://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&hash=ed8779a2222dc578f2cffbf308411b41381a94ef25801f9dfbe04746ea0944cd

//PLAIN-TEXT
http://api.rsps.tools/jetkai/breachcheck?token=39439e74fa27c09a4&password=123123

Return Data Example:
	{
		"token":"39439e74fa27c09a4",
		"hash":"cbfdac6008f9cab4083784cbd1874f76618d2a97",
		"databaseBreach":"Stoned 2021 ~800K Unique Passwords (15+ RSPS Databases)",
		"hashType":"SHA-1",
		"breached":true
	}
