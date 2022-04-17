# PreWave-code

**This code challenges hasbeen done with re, pandas, numpy, request, and json packages**

## Considered Challenges
- [x] The contents (text in Alert) might be empty -  It is considered
- [x] A term might be as part of a combined word in alert for example the term of ```ig metall``` is a part of ```rechtswidrig metall``` in a alert text it seems that it is totally different - it considered as non term in alert
- [x] An alert content might have two or more text - It is considerd in the code
- [x] If in term the ```keepOrder=True``` and ```text=IG Metall``` the ```IG-Metall``` is not pick as finding text ??
- [x] It seems this code should not be capital or small letter sensitive so at first all the characters are changed to lower case

```python
import pandas as pd
import numpy as np
import re
import requests
import json
```

![Q1](https://github.com/m-r-tanha/PreWave-code/blob/main/Q1.png)
![Q2](https://github.com/m-r-tanha/PreWave-code/blob/main/Q2.png)
![Q3](https://github.com/m-r-tanha/PreWave-code/blob/main/Q3.png)
