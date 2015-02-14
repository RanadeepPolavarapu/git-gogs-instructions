git-gogs-instructions
======================
An end to end installation of `gogs` a self-hosted git-scm service written in Go.

# Installation Steps

Step 1 - Install prerequisites:  
--------------------
    sudo apt-get -y update
    sudo apt-get -y update
    sudo apt-get -y install build-essential curl git mercurial make binutils bison gcc

Step 2 - Install Golang:
--------------------
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
       go1.2
       go1.2.1
       go1.2.2
       go1.2rc2
       go1.2rc3
       go1.2rc4
       go1.2rc5
       go1.3
       go1.3.1
       go1.3.2
       go1.3.3
       go1.3beta1
       go1.3beta2
       go1.3rc1
       go1.3rc2
       go1.4
       go1.4.1
       go1.4beta1
       ...

Let's install the latest _stable_ version:

gvm install go1.4.1
