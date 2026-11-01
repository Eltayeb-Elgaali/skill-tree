```bash
In /root/deploy.sh, the rsync command drops the trailing slash on $SRC so it
copies the folder instead of its contents. Fix that line and leave the rest
unchanged.
```