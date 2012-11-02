===============================
 Pluggable Application Example
===============================


How I created this application:

| mkvirtualenv pluggable
| cd pluggable
| pip install django
| django-admin.py startproject pluggable
| cd pluggable
| git init


github related
==============


| git config --global user.email "user@sdfsdf"
| git config --global user.name "ztilmo"

| ssh-keygen -t rsa -C "user@sdfsdf" # and save the key to ~/.ssh/github

The upload the key to github.

| Host github
|  User git
|  Hostname github.com
|  IdentityFile ~/.ssh/github
|  IdentitiesOnly yes



# test to see if you can access github
| > ssh -T github
| Hi ztilmo! You've successfully authenticated, but GitHub does not provide shell access.


To just use the app:

| git clone github:ztilmo/pluggable.git




Idea & Requirement
==================

A website holds displays information on hospitals, hotels, churches and schools. These four have common attributes like name, address, city, state etc.. At the same time, these four will have attributes that are not common amoung these four entities. For eg, schools will have an attribute that takes a type_of_school that can have any of ['middle school', 'high school','elementary school']. Obviously, type_of_school will not be an attribute for hospitals or churches.

Any user can visit the site and create, add or edit (essentially CRUD without delete) a new school, hospital, church or a hotel. The site will also keep track of the changes made to these entities (like a wiki would).

The 'pluggable' app will be an abstract app from which four different apps will be created - i.e. school, hospital, church and a hotel. The model and view of these 4 entities will be subclassed from this pluggable app. 

Create
------

The user is shown a form where the information is filled out. Then the user is shown a preview of the information that was entered and is offered the chance to re-edit the information. Once the user is satisfied, the user can submit and the information is saved in the database. However, this data is not immediately available (through the read portion of CRUD) until this record is specifically approved by the admin (set status = approved)

Read
----

The user is shown the latest approved data in the database. The older data is preserved for history's sake (like a wiki). The page will also show the names of the people involved in getting the data to this state so far.


Update
------

The user is shown a form with information pre-populated. The user can edit the information and after editing, the user is shown a preview of the information that was entered and is offered the chance to re-edit the information. Once the user is satisfied, the user can submit and the information is saved in the database. The record is not actually updated, but a new record is created without a change in the life_id (see wiki topic below). However, this data is not immediately available (through the read portion of CRUD) until this record is specifically approved by the admin (set status = approved)

Delete
------

User cannot delete. Data can only be removed from the admin app.

Wiki type versioning
====================

It would be great to use django-reversion ( https://github.com/etianen/django-reversion/ ), but I do not think you do this easily if you wanted to only display data that has the approved field set to true. You could filter this in python code by iterating over the versions, looking at the values of version.field_dict. Doing this filter in the database is tricky, though, since the model data is stored as serialized JSON. This creates overhead, and therefore would be better to roll out our own.

Here is the idea:

1. pluggable will have a life_id that will get created during the 'Create'. Once created, this will not change.
#. pluggable will have a version_number that will get incremented everytime the record is updated. Update will actually create another copy with a new version number.

This is described below with an example:

A user named 'Lincoln' creates a record with the following attributes:
{'id:1,
'Name': 'Johnson High School', 
'user': 'Lincoln',
'Street':'123 Main St.', 
'life_id': '32',
'version_number': 0,
'Status': 'Approved'}

Then 'Kennedy' comes along and wants to correct the street to '123 Old Dr.'. After the 'Update' portion of the crud, Now you have two records in the table (thus you do not update, you created another record). The status of the newly created record is 'Submitted' (i.e. not yet approved).

{'id':1,
'Name': 'Johnson High School', 
'user': 'Lincoln',
'Street':'123 Main St.', 
'life_id': '32',
'version_number': 0,
'Status': 'Approved'}  # the original record

{'id':2,
'Name': 'Johnson High School', 
'user': 'Kennedy',
'Street':'123 Old Dr.', 
'life_id': '32',
'version_number': 1,
'Status': 'Submitted'}

Then the admin comes along and sees that someone has made the change, and decides to approve it. Below is the state of the database.

{'id':1,
'Name': 'Johnson High School', 
'user': 'Lincoln',
'Street':'123 Main St.', 
'life_id': '32',
'version_number': 0,
'Status': 'Approved'}  # the original record

{'id':2,
'Name': 'Johnson High School', 
'user': 'Kennedy',
'Street':'123 Old Dr.', 
'life_id': '32',
'version_number': 1,
'Status': 'Approved'} # status is now approved





To ponder on
============

1. Do we really need an 'update' since we are never really updating, but just 'create'ing all the time? Why not just have 'create' and 'read'?
#. When the admin approves something, does it make sense to create another record instead of changing the status?



