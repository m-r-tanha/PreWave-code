# PreWave-code

**This code challenge has been done with re, Pandas, NumPy, request, and JSON packages**

## Considered Challenges
- [x] The contents (text in Alert) might be empty -  It is considered
- [x] A term might be as part of a combined word in alert data for example the term of ```ig metall``` was a part of ```rechtswidrig metall``` in an alert text it seems that it is totally different - it considered as non term in alert
- [x] An alert content might have two or more text - It is considerd in the code
- [x] If in term the ```keepOrder=True``` and ```text=IG Metall``` the ```IG-Metall``` is not picked as finding text (??)
- [x] It seems this code should not be capital or small letter sensitive so at first all the characters are changed to lower case.

```python
import pandas as pd
import numpy as np
import re
import requests
import json

# loading data with REST APIs
response_testQueryTerm = requests.get('''https://services.prewave.ai/adminInterface/api/testQueryTerm?
                                      key=mohammad:6f1622263ad73405987b4340e1f88e0f3df51af8c46cc64c2d4a31cff5e05d92''')
testQueryTerm = response_testQueryTerm .json()

response_testAlerts = requests.get('''https://services.prewave.ai/adminInterface/api/testAlerts?
                                   key=mohammad:6f1622263ad73405987b4340e1f88e0f3df51af8c46cc64c2d4a31cff5e05d92''')
testAlerts = response_testAlerts.json()

# Finding function if the word is in phrase
def word_in(word, phrase):
    #print(word, "::::", phrase)
    word = word.lower() # to consider all type of word all charachter should be in lower
    phrase = phrase.lower() # to consider all type of phrase all charachter should be in lower
    return word in phrase

df = pd.DataFrame()


for term in testQueryTerm:

  if term['keepOrder']== True: # if keepOrder = True
     for alert in testAlerts:
        if alert['contents'] != []: # the Content should not be empty
           for g in range(len(alert['contents'])): # if the content has more than one text item
               word = term['text'].lower()
                # find the word if it is at beginning or end of the phrase, or it has "," or "." after itself
                if re.findall('(\s%s$)|(\s%s[\s\,\.])|(^%s)|(\@%s)'%(word, word, word, word), 
                             alert['contents'][g]['text'].lower()) != []:
                       list = [[alert['id'], term['id'], term['text'], term['language'], term['keepOrder']]]
                       df = df.append([pd.DataFrame(list)])
  
  elif term['keepOrder']== False: # if keepOrder = False
      text_split = term['text'].split() # retrieve the different parts of a multiple terms
      for t in text_split:
            for alert in testAlerts:
               if alert['contents'] != []:   # the Content should not be empty
                  for g in range(len(alert['contents'])):  # if the content has more than one text item
                     word = t.lower()
                     if re.findall('(\s%s$)|(\s%s[\s\,])|(^%s)|(\@%s)'%(word, word, word, word),
                                   alert['contents'][g]['text'].lower()) != []:
                        list = [[alert['id'], term['id'], t, term['language'], term['keepOrder']]]
                        df = df.append([pd.DataFrame(list)])

df.columns =['testAlerts_ID', 'testQueryTerm_ID', 'text', 'language','keeporder']

# Different types of tables have been shown by the Q1, Q2 and Q3 to show non-duplicate and duplicate matches
Q1 = pd.crosstab(df["testAlerts_ID"],df['text'], rownames=['testAlerts_ID'], colnames=["text"])
Q2 = pd.crosstab(df["testAlerts_ID"],df['testQueryTerm_ID'], rownames=['testAlerts_ID'], colnames=["testQueryTerm_ID"])
Q3 = pd.pivot_table(df, index=['testAlerts_ID','testQueryTerm_ID'], columns = 'text').
                        replace([0.0, 1.0, np.nan],[u'\u2713', u'\u2713', '-'])
```

![Q3](https://github.com/m-r-tanha/PreWave-code/blob/main/Q3.png)
![Q1](https://github.com/m-r-tanha/PreWave-code/blob/main/Q1.png)
![Q2](https://github.com/m-r-tanha/PreWave-code/blob/main/Q2.png)

