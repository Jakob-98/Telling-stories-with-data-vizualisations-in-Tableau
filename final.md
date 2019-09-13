[Back](https://portfolio.jakobs.dev)
# Final project
### Choosing the methodology
Alongside applying my newly learnt knowledge about data vizualisations, there was another (..) I wanted to make use of for my final project. I wanted to learn & apply web scraping using python, I had tried doing it before but I had never applied it in a real context. Using googles [dataset search](https://toolbox.google.com/datasetsearch) I found [Probulica](https://www.propublica.org/), a nonprofit organization which, among other things, releases datasets containing information regarding the revenues of various private and public organizations like universities. Using & editing the code of a handy [tutorial](https://towardsdatascience.com/web-scraping-html-tables-with-python-c9baba21059) I found online, I was able to scrape 30 pages worth of tables regarding revenue information of universities across the US. 

```
import time
import requests
import lxml.html as lh
import pandas as pd

for z in range(30):
    z+=1
    url = 'https://projects.propublica.org/nonprofits/search?c_code%5Bid%5D=3&ntee%5Bid%5D=2&page='+str(z)+'&q=University&state%5Bid%5D='


    page = requests.get(url)

    doc = lh.fromstring(page.content)

    tr_elements = doc.xpath('//tr')

    col=[]
    
    i=0

    for t in tr_elements[0]:
        i+=1
        name=t.text_content()
        print ('%d:"%s"'%(i,name))
        col.append((name,[]))

    for j in range(1,len(tr_elements)):
        #T is our j'th row
        T=tr_elements[j]

        #If row is not of size 10, the //tr data is not from our table 
        if len(T)!=4:
            break

        #i is the index of our column
        i=0

        #Iterate through each element of the row
        for t in T.iterchildren():
            data=t.text_content() 
            #Check if row is empty
            if i>0:
            #Convert any numerical value to integers
                try:
                    data=int(data)
                except:
                    pass
            #Append the data to the empty list of the i'th column
            col[i][1].append(data)
            #Increment i for the next column
            i+=1

    [len(C) for (title,C) in col]

    Dict={title:column for (title,column) in col}
    df=pd.DataFrame(Dict)

    df.head()

    df.to_csv('out'+str(z)+'.csv')
    
    time.sleep(1)

```

Merging the datafiles:
```
import pandas as pd
import glob

path = r'C:/Users/jakob/jupyter/webscrape' # use your path
all_files = glob.glob(path + "/*.csv")

li = []

for filename in all_files:
    df = pd.read_csv(filename, index_col=None, header=0)
    li.append(df)

frame = pd.concat(li, axis=0, ignore_index=True)
frame.to_csv('outtotal.csv')
```

[Back](https://portfolio.jakobs.dev)
