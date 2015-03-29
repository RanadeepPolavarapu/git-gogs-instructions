git-gogs-instructions
======================
An end to end installation of `gogs` a self-hosted git-scm service written in Go.

# Installation Steps

Step 1 - Install prerequisites:  
--------------------
    sudo apt-get -y update
    sudo apt-get -y upgrade
    sudo apt-get -y install build-essential git

Step 2 - Install Golang
--------------------
Install `gvm` prerequisites:  

    sudo apt-get -y install curl git mercurial make binutils bison gcc build-essential

A list of all available Golang versions at the time of writing this document.

    root@git:~# gvm listall
    
    gvm gos (available)
    
       go1
       go1.0.1
       go1.0.2
       go1.0.3
       go1.1
       go1.1.1
       go1.1.2
       go1.1rc2
       go1.1rc3
       ...
       go1.4
       go1.4.1
       go1.4beta1
       ...

Let's install the latest **stable** version:

    root@git:~# gvm install go1.4.1
    Installing go1.4.1...
     * Compiling...
    root@git:~# 

Let us tell `gvm` which version of `go` we wish to use:

    root@git:~# gvm use go1.4.1 [--default]
    Now using version go1.4.1

`gvm` auto sets `$GOROOT` and `$GOPATH` environment variables however, it sets them into `.gvm/` directory. Let's set them to the normal `$HOME/go` dir.
Add to `~/.bashrc`:  

    export GOROOT=$HOME/go
    export PATH=$PATH:$GOROOT/bin

Do a final check to see if `go` is installed and running fine:

    root@git:~# go version
    go version go1.4.1 linux/amd64

Step 3 - Install PostgreSQL
--------------------
Install PostgreSQL:

    sudo apt-get install postgresql postgresql-contrib libpq-dev

Login as the `postgres` user:

    root@git:~# sudo su - postgres
    postgres@git:~$

Create a `gogs` user in PostgreSQL and specify a custom password:  
Use `dropuser` if you mess up.  

    postgres@git:~$ createuser -P gogs
    Enter password for new role: 
    Enter it again: 
    postgres@git:~$ 

Create a PostgreSQL Database:

    postgres@git:~$ psql
    psql (9.3.6)
    Type "help" for help.
    
    postgres=# create database gogs;
    CREATE DATABASE
    postgres=# \l
                                      List of databases
       Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
    -----------+----------+----------+-------------+-------------+-----------------------
     gogs      | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
     postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
     template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
               |          |          |             |             | postgres=CTc/postgres
     template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
               |          |          |             |             | postgres=CTc/postgres
    (4 rows)
    
    postgres=# GRANT ALL PRIVILEGES ON DATABASE gogs TO postgres;
    GRANT
    postgres=# \l
                                      List of databases
       Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
    -----------+----------+----------+-------------+-------------+-----------------------
     gogs      | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =Tc/postgres         +
               |          |          |             |             | postgres=CTc/postgres
     postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
     template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
               |          |          |             |             | postgres=CTc/postgres
     template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
               |          |          |             |             | postgres=CTc/postgres
    (4 rows)

Step 4 - Install Redis cache
--------------------
Install Redis:

    apt-get -y install redis-server
    
Check if Redis is successfully installed:

    root@git:~# redis-server
    [10196] 14 Feb 12:47:05.738 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
    [10196] 14 Feb 12:47:05.740 * Max number of open files set to 10032
                    _._                                                  
               _.-``__ ''-._                                             
          _.-``    `.  `_.  ''-._           Redis 2.8.4 (00000000/0) 64 bit
      .-`` .-```.  ```\/    _.,_ ''-._                                   
     (    '      ,       .-`  | `,    )     Running in stand alone mode
     |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
     |    `-._   `._    /     _.-'    |     PID: 10196
      `-._    `-._  `-./  _.-'    _.-'                                   
     |`-._`-._    `-.__.-'    _.-'_.-'|                                  
     |    `-._`-._        _.-'_.-'    |           http://redis.io        
      `-._    `-._`-.__.-'_.-'    _.-'                                   
     |`-._`-._    `-.__.-'    _.-'_.-'|                                  
     |    `-._`-._        _.-'_.-'    |                                  
      `-._    `-._`-.__.-'_.-'    _.-'                                   
          `-._    `-.__.-'    _.-'                                       
              `-._        _.-'                                           
                  `-.__.-'                                               

Step 5 - Install Docker
------------------------
As of writing this: Docker is at `v1.5.0`:

    sudo sh -c "wget -qO- https://get.docker.io/gpg | apt-key add -"
    sudo sh -c "echo deb http://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
    sudo apt-get -y update
    sudo apt-get -y install lxc-docker

Double check Docker version: 

    root@git:~# docker --version
    Docker version 1.5.0, build a8a31ef
    root@git:~# docker version
    Client version: 1.5.0
    Client API version: 1.17
    Go version (client): go1.4.1
    Git commit (client): a8a31ef
    OS/Arch (client): linux/amd64
    Server version: 1.5.0
    Server API version: 1.17
    Go version (server): go1.4.1
    Git commit (server): a8a31ef
    root@git:~# 

Step 6 - Install Python `pip` (optional)
-----------------------------------------
    sudo apt-get -y install python-dev python3-dev python-pip python3-pip

Step 7 - Install fig
---------------------
    curl -L https://github.com/docker/fig/releases/download/1.0.1/fig-`uname -s`-`uname -m` > /usr/local/bin/fig; chmod +x /usr/local/bin/fig

**OR**

Install via `pip`:  

    sudo pip install -U fig

Check `fig` installation:

    root@git:~# fig --version
    fig 1.0.1
