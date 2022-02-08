[Bandit](https://overthewire.org/wargames/bandit/) is an overthewire wargame that can be usefull to practice some basic Linux skills related to cybersecurity. 

In this page I will write down some interesting things that I have learned while doing each level of the wargame.

### Connect script
There are different levels for this game and each one has an user account to which you have to log in using a password or a key.

I have made this simple script easily to get connected to each level's account using a stored password.

You only need to create a file called `1` or `2` , `3` ... (the number of each level) containing the user password or private key. And run the script with the level you want to connect as argument.

```
#!/bin/bash
sshpass -f $1 ssh bandit$1@bandit.labs.overthewire.org -p 2220 || ssh -i $1 bandit$1@bandit.labs.overthewire.org -p 2220
```

## Ideas

### Find unique lines in a file
The `unique` command requires that the duplicated string are sorted together so:
```
cat $filename | sort | unique -u
```

