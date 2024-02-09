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
