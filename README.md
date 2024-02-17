# HackThisSiteSolutions
Solutions to the Hack This Site challenges.

## Solutions
### Basic
#### Level 1(the idiot test)
Inspect the source code of the form and you should see a comment of the form:

```
<!-- the first few levels are extremely easy: password is {password} -->
```

Simply paste in the given password.

#### Level 2
The hint says the password comparison file hasn't been uploaded, so if there's not password to compare to, simply press submit!

#### Level 3
Looking at the source code we can see that the name of the file being used for the comparison, `password.php`, is given as a hidden input of the form.
We can go to this file and see it is not protected, and read the password: <https://www.hackthissite.org/missions/basic/3/password.php>

#### Level 4
Looking at the source code we can see the "Send password to Sam" button is part of a form with a hidden input with Sam's email, sam@hackthissite.org.
We can simply update this field to our email by manually editing the HTML, or running the following JS in the Command window:

```JavaScript
document.querySelector('input[name="to"]').setAttribute('value', '{your-email}')
```

Note you can only use the email you used to register with HackThisSite.
An email will be sent to your with the password.

#### Level 5
This level can be solved the same as the previous level. Looking into it, it turns out the difference between the two is this level has CORS, so it cannot be solved by making your own local copy of the HTML page.

#### Level 6
Playing with the encryption system, it's pretty quick to see it works by incrementing the ASCII values of each character by its position in the string.
We can determine this by starting entering single characters, then 2 characters and seeing the next one is incremented, and so forth.
We can determine it's ASCII based by inputing `0z`, `0Z`, or `09` and see they increment to `0{`, `0[` and `0:` respectively.
We can invert the encrypted password with 1 line of Python:

```Python
''.join(chr(ord(c)-i) for i,c in enumerate(pw))
```

#### Level 7
This level allows us to inject unix commands by appending to the input the the `cal` command.
The first thing we can do is see what other files are in the directory by injecting the `ls` command like so:

```bash
2000; ls -la
```

which shows us these files:

```
index.php
level7.php
cal.pl
.
..
k1kh31b1n55h.php
```

We can then go to this page, <https://www.hackthissite.org/missions/basic/7/k1kh31b1n55h.php>, and find the password.

#### Level 8
Here we have another chance to use Unix command injection, this time via an unprotected SSI (Server Side Includes), which we are hinted at by the `.shtml` page extension.
We can find a guide to injection on the OWASP site here: <https://owasp.org/www-community/attacks/Server-Side_Includes_(SSI)_Injection>
Now we see the page we're directed to is <https://www.hackthissite.org/missions/basic/8/tmp/{random}.shtml>, and we are told the password in `/var/www/hackthissite.org/html/missions/basic/8/` so we just need to check one directory higher.
The can just injection:

```SSI
<!--#exec cmd="ls .." -->
```

Which will show is the files:

```
au12ha39vc.php index.php level8.php tmp
```

We can finish of by going to <https://www.hackthissite.org/missions/basic/8/au12ha39vc.php> to see the password.

