---
title: "Sed Cheatsheet"
date: 2021-11-28T23:45:36-05:00
draft: true
---

### Common commands:

`echo test.txt | sed 's/STRING_TO_REPLACE/NEW_STRING/'` 

Replace a string with another string

`sed -i 's/STRING_TO_REPLACE/STRING_TO_REPLACE_IT/NEW_STRING' test.txt`

Edit file in place

`sed -n '1,3p' test.txt` 

Print line1-3, use `-n` to prevent reprint everything

`sed -n -e '1,4p' -e '31,34p' test.txt`

use `-e` to make multiple selections

### More:

[https://www.gnu.org/software/sed/manual/sed.html](https://www.gnu.org/software/sed/manual/sed.html)

[https://www.howtogeek.com/666395/how-to-use-the-sed-command-on-linux/](https://www.howtogeek.com/666395/how-to-use-the-sed-command-on-linux/)