# node-deploy
node-deploy is a tool for deploying a node web app to an upstart+nginx machine automatically.

## Installation

### npm
```
npm install -g node-deploy
```

### GitHub
```
npm install -g https://github.com/martinrue/node-deploy/tarball/master
```

## Prerequisites
The remote machine must have `git`, `nginx`, `node` and `npm` installed before deployments can be made to it. The machine should also be configured for remote access via ssh.

## Usage
```no-highlight
usage: nd [init | deploy | remove]
```

* Run `nd init` to generate the deploy config files. You need to commit these files before deploying.
* Run `nd deploy` to begin a deployment.
* Run `nd remove` to stop and remove the app from the server.

Config files are stored in the `deploy` directory. Pass a custom directory as the second argument to `nd` if you'd like to use something else.

##Example
First run `nd init` and answer the questions. It will try to guess some settings for you – if you're happy with the guess, just hit enter to accept it:

```no-highlight
node v0.10.8 in ~/Desktop/app on master 
→ nd init
app url: martinrue.com
app name (app): martinrue.com
app entry point (blog.js): 
upstream port (4001): 
app path on server (/var/www): 
nginx sites-enabled path (/etc/nginx/sites-enabled): 
git clone URL (git@github.com:martinrue/delme.git): 
server SSH address: root@192.168.2.4
```

Second, commit and push the newly created config files:

```no-highlight
node v0.10.8 in ~/Desktop/app on master 
→ git add -A

node v0.10.8 in ~/Desktop/app on master 
→ git commit -m "add deploy config"
[master 9a0def5] add deploy config
 3 files changed, 30 insertions(+)
 create mode 100644 deploy/deploy.json
 create mode 100644 deploy/martinrue.com
 create mode 100644 deploy/martinrue.com.conf

node v0.10.8 in ~/Desktop/app on master with unpushed 
→ git push
Counting objects: 7, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 912 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
To git@github.com:martinrue/martinrue.git
   b973149..9a0def5  master -> master
```

And finally, deploy:

```no-highlight
node v0.10.8 in ~/Desktop/app on master 
→ nd deploy
deploying to root@192.168.2.4:/var/www/martinrue.com
```

## Notes
- Following a successful deploy, `nd` will wait a further 15 seconds (to account for the configured respawn limits in upstart) to verify the app process is still alive and well.

- As a shorthand, the `nd init`, `nd deploy`, `nd remove` commands can also be referred to by their first character, i.e. `nd i`, `nd d` and `nd r`.

- If no errors are reported, the command was successful. The respective zero or non-zero error code is returned to allow `nd` to be invoked by third party tools.

- It's assumed that **all** files in the `sites-enabled` nginx directory are valid config files, i.e. that your `nginx.conf` includes them something like this `include /etc/nginx/sites-enabled/*;`.

