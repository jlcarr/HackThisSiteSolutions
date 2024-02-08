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
Looking at the source code we can see that the name of the file being used for the comparison, `password.php`, is given as a hidden field of the form.
We can go to this file and see it is not protected, and read the password: <https://www.hackthissite.org/missions/basic/3/password.php>

