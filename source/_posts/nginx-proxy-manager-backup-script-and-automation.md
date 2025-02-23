---
title: nginx proxy manager backup script and automation
date: 2025-02-22 15:58:49
tags:
    - code
    - nginx proxy manager
    - automation
---
I have been using nginx proxy manager for many years to take care of my reverse proxy requests so that I can expose my services to the internet. It is easy to use and running very stably, never had any issues during so many years. I never really have to reinstalled it or anything. So I never back up the service either. But recently I just think about backing up the service since I have time to do so, once I setup the script, I can use cron job to automate it, then I do not have to worry about it any more. 

So I did come up with the following bash script with the help of ChatGPT and customized it a little bit.
- My Nginx Proxy Manager is running on my HP mini pc which is running ubuntu OS, I use docker compose file  to run NPM. The server ip is 192.168.1.99
- I set up the port forward on my router to forward port 80 and 443 to my HP mini pc server 192.168.1.99
- This way whenever I am trying to reach my service from the internet, the request will be sent to my NPM instance and NPM will forward the request to the respective server and port
- The backup file will be moved to a dedicted directory called backup so that later we can use find command to delete the backup files which are older than 7 days. If we leave the backup file in the same directory, later find will have some permission issue when it has to search the whole npm directory
- The backup file will be sent to my synology using SCP command. I already setup the publick key authorization so that I do not have to use the passsword, this way I can use the cron job to take care of it without manua interruption
- Another thing is when scp to synology, due to some recent changes on synology, I am not able to scp the file directly to /volume1/stone/backup, the whole /volume1 directory is not accessible, there is some permission error. Instead I had to scp to synology /home directory, which is the same directory as /var/services/homes/stone, after that I can go to synology and move the backup files to my backuup directory which is /volume1/stone/backup/npm

``` bash
#!bash

# add cron job
# sudo crontab -e
# run this backup srcript every day at 2 am
# 0 2 * * * /home/stone/backup_npm.sh >> /var/log/backup_npm.log 2>&1

# CONTAINER_NAME="nginx-proxy-manager"
BACKUP_DIR="$HOME/docker/nginx-proxy-manager"
TIMESTAMP=$(date +"%Y-%m-%d")
BACKUP_FILE="npm_backup_$TIMESTAMP.tar.gz"
REMOTE_USER="stone"
REMOTE_HOST="192.168.1.235"
REMOTE_PATH="/home"
RETENTION_DAYS=7

echo "🚀 Stopping Nginx Proxy Manager..."
docker compose -f $BACKUP_DIR/docker-compose.yml down

echo "📦 Creating backup archive: $BACKUP_FILE..."
sudo tar -czvf "$BACKUP_FILE" -C "$BACKUP_DIR" .
mv $BACKUP_FILE $BACKUP_DIR/backup (mv the backup file to a dedicated folder so that we can use find to delete it later)

echo "🚀 Restarting Nginx Proxy Manager..."
docker compose -f $BACKUP_DIR/docker-compose.yml up -d

echo "📡 Transferring backup to Synology..."
scp $BACKUP_DIR/backup/$BACKUP_FILE $REMOTE_USER@$REMOTE_HOST:$REMOTE_PATH/

if [ $? -eq 0 ]; then
        echo "✅ Backup successfully transferred!"
else
        echo "❌ SCP transfer failed!"
        exit 1
fi

# Cleanup old backup locally
echo "🗑  Removing backups older than $RETENTION_DAYS days..."
find $BACKUP_DIR/backup -type f -name "npm-backup-*.tar.gz" -mtime +$RETENTION_DAYS -exec rm {} \; (find command will have some permission issue if the backup files are in the $BACKUP_DIR)

echo "✅ Backup process completed successfully!"

```