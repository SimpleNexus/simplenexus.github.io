---
layout: post
title: "Using LastPass CLI and AESCrypt to Encrypt Files"
tagline: ""
author: "Kaden Barlow"
---

# Introduction
I can't tell you how many times I have tried to get family and friends to install a password manager, most of them (although not all) complain that it is too tedious and that because they aren't celebrities or other high profile targets they don't think that its worth it. After this kind of response, I figure its unlikely that I will be able to convince them to encrypt their sensitive documents either. Neither of these things are really that hard, and in my opinion they are absolutely necessary. Today I will go into detail about two tools, one for password management and one for securely encrypting files. Although anyone could use the tools that I am going to talk about today because they both have great user interfaces, I will be diving into their CLIs and expaining how, as a power user, you can make the most of them. After all, this is a tech blog right? I will explain how to install and use these tools assuming you are running MacOS. If this isn't true you will have to make some slight adjustments but the principles should work all the same.

# Storing Passwords in LastPass
[Last Pass](https://www.lastpass.com/) is a password manager that stores encrypted passwords. If you aren't using a password manager yet (shame on you... do you even care about your security?) you should definitely check out LastPass. It is free for a single user and in addition to their web application they have mobile apps for both Android and iOS. It is very easy to use and lives up to its name. Since I have installed LastPass I have never gone to a website and wondered "Which password did I use for this site again?". Like any good password manager, LastPass will generate a random password for a given length and complexity that you ask for. This means that my google account password can be 75 characters long and I don't have to remember it. A simple monthly backup (which I encrypt) of passwords makes sure that I won't loose access to any of my accounts if I loose access to LastPass.

I had been using LastPass for over a year when I found out they had a CLI. I almost fell out of my chair! (I know I'm a little weird...) [Here is the Github Repo](https://github.com/lastpass/lastpass-cli). Once again the installation is pretty simple as shown in the repo readme, just run `brew install lastpass-cli --with-pinentry`. Here are a few useful commands once you have the CLI installed. You can also checkout the full documentation [here](https://lastpass.github.io/lastpass-cli/lpass.1.html).

```
# login to LastPass
lpass login --trust email_address

# list stored passwords
lpass ls

# show a given password on the command line
lpass show password_name --password

# generate a password with n characters
lpass generate password_name n

# and here is a little function just for kicks and giggles that copies a password to the clip board
password(){echo $(lpass show $1 --password) | pbcopy}
```

# Encrypting and Decrypting with AESCrypt
As it says on their [website](https://www.aescrypt.com), AESCrypt is a file encryption software application that uses the industry standard Advanced Encryption Standard (AES) to easily and securely encrypt files. AESCrypt can be used by anyone because of an easy interface, but it also comes with a CLI which I will take advantage off later in this post. Installing AESCrypt on a Mac is really easy. Simply run `brew install aescrypt` from your terminal. There are a few critical commands that you will want to know if you are going to use the CLI.

```
# encrypting a file
aescrypt -e filename

# generate a key file from a password
aescrypt_keygen -p password secret.key

# encrypt a file using a key file
aescrypt -e -k secret.key filename

# decrypting a file
aescrypt -d filename

# decrypting a file with a key file
aescrypt -d -k secret.key filename
```

# Using LastPass and AESCrypt Together
One of the things that makes AESCrypt easy to use is that it can derive a encryption key from a password that you supply. This means that you can encrypt a whole file securely using a password thats easy to remember. While this may seem like a good idea for you forgetful folks out there, this actually though is something that weakens the security of the encryption. If someone knows that a file was encrypted using AESCrypt they can try to decrypt the file by guessing your password, which may not take that long depending on how strong your password is. That is where LastPass CLI comes in. We can use LastPass to generate and store a secure password to use with AESCrypt.

Let's generate a secure password using the LastPass CLI to use with AESCrypt. The command `lpass generate EncryptionKey 1024` will generate a random password that is 1024 characters long. Good luck randomly guessing that! Now I have combined what we have already learned into the following bash functions which can be used to easily encrypt and decrypt files.

```
# returns the password you just created
encryptionkey(){echo $(lpass show EncryptionKey --password)}

# uses AESCrypt and the password to encrypt a file. Example: encrypt test.txt
encrypt(){ aescrypt -e -p $(encryptionkey) $1 }

# uses AESCrypt and the password to decrypt a file. Example: encrypt test.txt
decrypt(){ aescrypt -d -p $(encryptionkey) $1 }

# the following two functions are for encrypting and decrypting directories
encrypt_dir(){
  temp="$1.tar.gz"
  tar -cvzf $temp $1
  aescrypt -e -p $(encryptionkey) $temp
  rm $temp
}

decrypt_dir(){
  aescrypt -d -p $(encryptionkey) $1
  directory=$(echo "$1" | cut -f 1 -d '.')
  tar -xvzf "$directory.tar.gz"
  rm "$directory.tar.gz"
}
```

Go ahead and give these functions a try or write your own. These work for me, but I am by no means a bash wizard. At the very least you should now be able to encrypt and decrypt files securely without having to remember a password or locate a key file! Hopefully
