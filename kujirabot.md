#Installation guide for kujira-fin-market-making and kujira-fin-helper

##1. Create vm on my Proxmox
```sh
sudo qm clone 9000 777 --name kujibot1
sudo qm set 777 --sshkey ~/.ssh/id_rsa.pub
sudo qm set 777 --ipconfig0 ip=192.168.1.111/24,gw=192.168.1.1
sudo qm resize 777 scsi0 +50G
sudo qm start 777
```
##2. Install kujira-fin-market-making ( i all install under root)
```sh
cd /srv

git clone https://github.com/kht2199/kujira-fin-market-making.git
cd kujira-fin-market-making/

yarn install
```
If you not get error below skip this, go to 2.2:
```
Command 'yarn' not found, but can be installed with:
apt install cmdtest
```
You need run :
```sh
apt install cmdtest
```
After this, you need run:
``` sh
yarn install
```
If you get error :
```
00h00m00s 0/0: : ERROR: [Errno 2] No such file or directory: 'install'
```
You need run this commands:
```sh
sudo apt remove cmdtest
sudo apt remove yarn
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update
sudo apt-get install yarn -y
```

Now :) Run:
```sh
yarn cache clean
yarn install
```
If you get error:
```
error @fastify/accept-negotiator@1.0.0: The engine "node" is incompatible with this module. Expected version ">=14". Got "10.19.0"
```
We need install nvm version bigger (for kujira-fin-helper needed >=17.0.0 ) , I installed 17.9.1
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```
Check  ~/.bashrc:
```sh 
cat ~/.bashrc
```
you can see : 
```sh 
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

Next
```sh
nvm ls-remote
nvm install 17.9.1
```
And again :
```sh
yarn install
```
Output:
```sh
yarn install v1.22.19
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
warning " > axios-cache-adapter@2.7.3" has unmet peer dependency "axios@~0.21.1".
warning " > ts-loader@9.3.1" has unmet peer dependency "webpack@^5.0.0".
[4/4] Building fresh packages...
Done in 15.74s.
```

2.2 Install pm2 ( useful utility)
```sh
npm install pm2 -g
```
check :
```sh
pm2 ls
```

2.3 Edit environment file `env`

Write your data in MNEMONIC,TELEGRAM_BOT,TELEGRAM_CHAT_ID

2.4. Start 
```sh
pm2 start
```
Check log 
```sh
pm2 ls
```
and see :) :
```
1|prisma-s | /usr/bin/bash: dotenv: command not found
```
You can resolve : 
```sh
npm install -g dotenv-cli
```
and restart :
```sh 

# pm2 ls
┌─────┬──────────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name             │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼──────────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ kuji             │ default     │ N/A     │ fork    │ 38570    │ 2m     │ 0    │ online    │ 0%       │ 62.0mb   │ root     │ disabled │
│ 1   │ prisma-studio    │ default     │ N/A     │ fork    │ 0        │ 0      │ 15   │ errored   │ 0%       │ 0b       │ root     │ disabled │
└─────┴──────────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘

# pm2 restart 1
Use --update-env to update environment variables
[PM2] Applying action restartProcessId on app [1](ids: [ '1' ])
[PM2] [prisma-studio](1) ✓
┌─────┬──────────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name             │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼──────────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ kuji             │ default     │ N/A     │ fork    │ 38570    │ 3m     │ 0    │ online    │ 0%       │ 62.0mb   │ root     │ disabled │
│ 1   │ prisma-studio    │ default     │ N/A     │ fork    │ 38725    │ 0s     │ 15   │ online    │ 0%       │ 780.0kb  │ root     │ disabled │
└─────┴──────────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
```


##3 Install kujira-fin-helper

3.1 Git clone
```sh 
cd /srv
git clone https://github.com/kht2199/kujira-fin-helper.git
cd kujira-fin-helper/
``` 
3.2 Create file ecosystem.config.js
```sh
cat > ecosystem.config.js <<EOF
module.exports = {
  apps : [{
    name   : "kuji-helper",
    script : "yarn start"
  }]
}
EOF
```
3.3 Install dependencies
```sh
yarn install
```
3.4 Start service
```sh
pm2 start
```

##4. All of your services are available on:

kuji - http://localhost:3000 
prisma-studio - http://localhost:5555
kuji-helper - http://localhost:3001