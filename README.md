# Vagrant LEMP PHP 7.0

A simple Vagrant LEMP setup running PHP7.

## Requirements

To get up and running with this environment, you first need to have Virtualbox and Vagrant installed on your system.
If you don't already have those, visit the downloads pages below and follow the instructions for your operating system:

* [Virtualbox Downloads](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant Downloads](https://www.vagrantup.com/downloads.html)

## What's inside?

- Official Ubuntu 16.04 x64 LTS (Xenial Xerus)
- Nginx
- PHP7 with some extensions
- MariaDB
- Composer

## How to use

- Clone this repository into your project
- Run ``vagrant up``
- Navigate to ``http://192.168.33.100/`` 

## Database

- For mysql the default user is root with password 'pass': mysql -u root -ppass
