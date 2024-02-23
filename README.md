# HackThisSiteSolutions
Solutions to the Hack This Site challenges.

## Solutions
### Basic Challenges
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

Which will show the files:

```
au12ha39vc.php index.php level8.php tmp
```

We can finish off by going to <https://www.hackthissite.org/missions/basic/8/au12ha39vc.php> to see the password.

#### Level 9
This level hints that it is very similar to the previous level, and in fact we should use the vulnerability in the previous level's page to solve it.
We need to go back to Level 8 and do a different injection to list the directory of level 9:

```SSI
<!--#exec cmd="ls ../../9" -->
```

Which will show the files:

```
index.php p91e283zc3.php
```

We can finish off by going to <https://www.hackthissite.org/missions/basic/9/p91e283zc3.php> to see the password.

#### Level 10
This level's page doesn't provide much, but the description says we should use Javascript and there is a "more temporary and "hidden" approach to authenticating users".
The final piece to the puzzle is to inspect the request made after submitting to the password form, and we can see in the Cookie field that we have `level10_authorized=no;`
So we can try to override this with JavaScript inb the Console before submitting:

```JavaScript
document.cookie = "level10_authorized=no;"
```

Now submitting any password will work.

#### Level 11
This level is fairly cryptic.
The inital page has a comment saying `<!--We even have our own collection - if you could find it!-->` and also says a random song is the best.
If we refresh the page we will see a different song name. If we Google each of these song's we'll see they are all by Elton John.
So we need to find the site's collection of Elton John song.
Well, as it turns out we can access the `e` directory at <https://www.hackthissite.org/missions/basic/11/e/>, and can contnue forward to <https://www.hackthissite.org/missions/basic/11/e/l/t/o/n/> at which point we see nothing left in the directory.
It's at this point we need the level's hint that it's about Apache.
If we Google "Apache hidden files" we quickly stumble upon `.htaccess` files, and indeed <https://www.hackthissite.org/missions/basic/11/e/l/t/o/n/.htaccess> exists.
It mention a file of the format `DaAnswer.*` and indeed going to  <https://www.hackthissite.org/missions/basic/11/e/l/t/o/n/DaAnswer> or <https://www.hackthissite.org/missions/basic/11/e/l/t/o/n/DaAnswer.txt> gives us the message `The answer is short! Just look a little harder.`
Reading between the lines, the answer is literally `short`.
But where to put it? Well in all the other challenges is was the `index.php`, and indeed <https://www.hackthissite.org/missions/basic/11/index.php> has a password submit form where we can put the answer.


### Realistic Challenges
#### Level 1: Uncle Arnold's Local Band Review
We can see that band we want to promote, Raging Inferno, has the lowest average rating.
Judging from the trail of decimal places, the rating is probably a simple mean of all votes submitted.
The vote box gives us values `1`-`5`, but we can simply change the value of the input to a much larger value to artificially inflate the rating beyong a single vote's weight.
Like so:

```HTML
<option value="6">1</option>
```

The level will actually accept `6` to consider it hacked, but in theory we'd keep trying larger and larger values until we finally have a big enough influence on the result.
Afterwards pressing the "vote!" button will complete the level.

#### Level 2: Chicago American Nazi Party
Looking at the source of the first page, we can see there is a hidden link to an "update" page, this is clearly where we should go to gain access to the site.
We are brought to a login page, and inspecting the source code here shows nothing glaring. Trying typical username/password combinations doesn't work either. So since this is our only point of entry, makes sense to try an injection attack.
Giving a single quote, `'`, to the username field causes a message about an SQL error to appear, so this confirms we're on the right track.
The classic `' OR 1=1 --` indeed solves the level.

#### Level 3: Peace Poetry: HACKED
Looking at the source code, there is a comment saying the old page has been moved to <https://www.hackthissite.org/missions/realistic/3/oldindex.html> so we should go there: perhaps we can re-hack the site in a similar way.
On this page we see there is a place to both read and submit poetry, this is an entry point. <https://www.hackthissite.org/missions/realistic/3/submitpoems.php>
On the page we see a note "Poems will be stored online immediately but will not be listed on the main poetry page until it has a chance to be looked at." This implies a file will be made and stored on the server. Perhaps we can replace the `index.html`.
Using `index.html` as the name doesn't work, but they're proably stored in a subdirectory, so `../index.html` does indeed work!
The last piece is we need to copy the source code of the `oldindex.html` page into the body that is written to the new `index.html`.

