# Krypton 0


## Overview

-----------------
On the OverTheWire/Leviathan website, we are told how connecting to the bandit server as a specific user works.

Each user corresponds to a different level:
- Krypton0 -> Level 0
- Krypton1 -> Level 1
- ...
- Krypton33 -> Level 33

Each level is hiding a flag, which is used as the password for the following level. Meaning, one must solve `Level n` to play `Level n+1`.

We always start out connecting to the `krypton.labs.overthewire.org` server on `port 2231` for each level.
Again, the users on the server specify the level. Each starting point is the same:

``` text
➜  ~  echo "S1JZUFRPTklTR1JFQVQ=" | base64 --decode    
KRYPTONISGREAT%                                                                                                                                     
```

BOOM we decoded the first password for `krypton1`. 