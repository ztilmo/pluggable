===============================
 Pluggable Application Example
===============================


To create this application:

mkvirtualenv pluggable
cd pluggable
pip install django
django-admin.py startproject pluggable
cd pluggable
git init


github related
==============


git config --global user.email "user@sdfsdf"
git config --global user.name "ztilmo"

ssh-keygen -t rsa -C "user@sdfsdf" # and save the key to ~/.ssh/github



Host github
 User git
 Hostname github.com
 IdentityFile ~/.ssh/github
 IdentitiesOnly yes



# test
> ssh -T github
Hi ztilmo! You've successfully authenticated, but GitHub does not provide shell access.

git clone github:ztilmo/pluggable.git


