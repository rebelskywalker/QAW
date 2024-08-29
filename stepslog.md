## Installed linux subsytem on windows using wsl 
- followed instructions on command prompt including restart
## Running ubuntu linkux
- Was stuck in an install loop, this should take only a few minutes.
- afer waiting for about 20 minutes, i used ctrl+c to breal the loop per a forum reccommendation
- Noticed that i was then able to create my username, and then password (had to enter twice after username to bring up password prompt , it took an extra CRLF)
## Used Curl to download nodejs from https://deb.nodesource.com/setup_18.x | sudo -E bash -
- entered the command above
- then followed with sudo apt install -y nodejs
## Checking installed using which to returnf irst found directory contianing those files, could also use program file name with -v option to check version
- which npm
- which nodejs
## nodejs -v
- Returned v18.20.4
## 
{
  "name": "",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {},
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "playwright": "^1.39.0"
  },
  "devDependencies": {
    "@playwright/test": "^1.39.0",
    "@types/node": "^20.8.9"
  }
}
