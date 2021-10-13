#!/bin/bash


TIMESTAMP=$(date +”%F-%H-%M”)
SECONDS=0

################# Nginx Configuration Backup #################
echo “————————————-“;
echo ” Starting Backup  “;
echo “————————————-“;
cp /etc/gitlab/gitlab.rb /home/truongtn/data/backups
cp /etc/gitlab/gitlab-secrets.json /home/truongtn/data/backups
gitlab-backup create
cp -r /data/backups/* /home/truongtn/data/backups/
rm -rf /data/backups/*

rclone move -P /home/truongtn/data/backups/ backups:GitLab_Backups/$TIMESTAMP
echo “————————————-“;
echo ” Finished Backup  “;
