---
author: albert
tags: ethical-hacking
---
During a brute-force attack, an attacker submits repeatably passwords from a list of common passwords with the hope of finding the correct one. In this post, I share my attempt of brute-force attack on a password protected ZIP file using a simple script I've written in Python3.
<!--more-->

---

**Disclaimer:** <br />
The script and the post were written for educational purposes only.

You can download the script [here](https://github.com/AlbertRtk/ethical_hacking/blob/main/brute_force_zip.py).

---

So, I had to start by doing some research on the topic. ZIP files can be managed in Python code with ```zipfile``` module. Additionally, to make the process of password cracking look fancier, I decided to use ```tqdm```.

```python
import zipfile
from tqdm import tqdm
```

I asked my wife to choose some word-based password and protect a ZIP file with it. [Here](https://www.kaggle.com/wjburns/common-password-list-rockyoutxt) I've found a list of common passwords, ```rockyou.txt```. I placed the files in the project directory tree and assigned their paths to variables.

```python
""" User's input """
ZIP_FILE_PATH = r'.\zip2hack.zip'
DICT_FILE_PATH = r'.\brute_force_dict\rockyou.txt'
```

Then, I wrote the ```brute_force_zip``` function. The function takes as arguments the path to the attacked ZIP file and the path to the TXT file with passwords. Passwords from the TXT file are read into an array. Next, the function tries to extract zipped files using passwords from the list. If it succeeds, the loop is broken  and the correct password is returned. If the proper password will not be found in the list, then ```None``` is returned.

```python
def brute_force_zip(zip_file, dict_file):
    """
    Brute force attack on password protected zip file.

    :param zip_file: path to zip file to be attacked
    :param dict_file: txt file with list of passwords
    :return: found password or None if password was nit found
    """
    with open(dict_file, 'r', encoding='utf-8', errors='replace') as f:
        pwd_list = f.read().split('\n')

    with zipfile.ZipFile(zip_file) as zf:
        for next_pwd in tqdm(pwd_list):
            password = next_pwd.strip()
            try:
                zf.extractall(pwd=bytes(password, encoding='utf-8'))
            except:
                pass
            else:
                return password
```

Finally, it was possible to call the function for the test ZIP file, ```zip2hack.zip```. As the output, the script in the end prints the correct password or the information that the password has not been found.

```python
if __name__ == '__main__':
    found_pwd = brute_force_zip(ZIP_FILE_PATH, DICT_FILE_PATH)

    if found_pwd:
        print(f'Password found: {found_pwd}')
    else:
        print('Password not found :-(')
```

### Let's run it!
``` 
python .\brute_force_zip.py
```

So, what is my wife's password? Who is guarding the file? ;-)

<a href="/assets/images/blog/2020-10-24/zip_brute_force_attack.jpg">
<img class="full_width" src="/assets/images/blog/2020-10-24/zip_brute_force_attack.jpg">
</a>
