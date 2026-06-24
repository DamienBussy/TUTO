# Tuto to connect ssh - git

### Generate ssh key

On your vm you need to generate a new key ssh: 

```bash
ssh-keygen -t ed25519 -C "damien.bussy.developper@gmail.com"
```

Add this key to identity : 
```bash
ssh-add ~/.ssh/id_ed25519
```

Get public content ssh key : 
```bash
cat ~/.ssh/id_ed25519.pub
```

### Add this content on your github account
To add you have to go to `Settings -> SSH or GPG key -> New ssh key` 
Choose `Authentication` 
And add the public content.

### Signing your commits 
To have a verified commits you make same bat you choose `Signing`.

And after you need to configure your git on your vm : 
```bash
git config --global user.email "youremail@gmail.com"
git config --global user.name "Damien Bussy"
```

After you configure user you need to add a signing key for the user : 
Get all ssh key:
```bash
ls ~/.ssh
```
Choose your key: and config git like this:
```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```


### Potential problem
If your project on your vm has no same your user git so the commits will not pass.
To fix that you need to kow who is owner of your folder project:
```bash
ls -ld /var/www/portfolio.damienbussy
```
If you have a result like this: `drwxrwxrwx 10 root root 4096 Jun 14 10:25 /var/www/portfolio.damienbussy` user dam that we had added previously to the ssh-key has no effect. You need to give owner role to dam on the folder `/var/www/portfolio.damienbussy`.
To do this use command:
```bash
sudo chown -R dam:dam /var/www/portfolio.damienbussy
``` 
