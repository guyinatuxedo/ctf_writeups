Credit to HackUCF CTF workshop 9/25/16 for showing me how to do this

#Challenge:

I thought I found a perfect match but she ended up being my regEx girlfriend.
Note: You can't use newlines inside your match.
nc misc.chal.csaw.io 8001

This was a Misc challenge from CSAW Quals 2016 worth 100 points

#Solution:

When we nc into the server, we get something like the expression below.

```
[k-r]l[znou6\wG](tomato|dolphin)+3F+(phone|chair)[k-r]{11}[dWEObqr5gC](elephant|clementine)(bernie|alien)*(alien|cat){11}v+
```

Now from the hints that it gave us and the expression above we can tell that it is a regular expression (regex). They are used as search patterns, but with more more capabilities. Consider searching for “*.txt”, which would give us all txt files in the current directory. The regular expression equivalent to that is .*\.txt.
So for this, we are going to have to find a string that meets the requirements for the regex, then replicate that process a lot of times. We should probably script this out. Luckily for us, in python there is a library rstr with a function known as “xeger” that will perform that function for us. Just install the library using this command.

```
sudo pip install rstr
```

Now I have posted my code below for you to reference. One thing I would like to point out is the last while loop. That is there incase the first string the script outputs to meet the regex does have a space, which according to the hint cannot happen. So that while loop will generate new strings to meet that regex until the string no longer has a space

```
#this imports the two libraries, pwntools and rstr (what we will use to solvv)
from pwn import *
import rstr

#This establishes the connection that we will use
conn = remote('misc.chal.csaw.io', 8001)

#This line will take the first line of text from the server, which is junk
conn.recv()

#This is the loop that will perform all of the work
while True:
    
    #This will store the regex expression from the variable
    exp = conn.recv()


    #This will check to ensure that we don't have a flag, and we didn't make an issue
    if 'Irregular' in exp:
        print exp
        break
    if 'flag' in exp:
        print exp
        break

    #This will find a string value that meets the regex expression
    val = rstr.xeger(exp)
    print "String meeting regex value is: " + val

    #The server will not accept responses with a space in it, so we have to
    while '\n' in val[:-1]:
        print 'Extra line found in string value, correcting'
        val = rstr.xeger(exp)
        print "New string: " + val    
    
    #And finall send the server back the response to proceede to the next level
    conn.send(val)
    ```


Flag

flag{^regularly_express_yourself$}

Other Writeups
http://rawsec.ml/en/CSAW-2016-100-Regexpire-misc/

https://eugenekolo.com/blog/csaw-qual-ctf-2016/#regexpire

