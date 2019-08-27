## [**BMEG**](https://bmeg.io/) Beginner Tutorial 
Compiled: August 26, 2019 using schema bmeg_rc2

Mekdes Hilete

Genomic data, meaning data relating to DNA, is used for
- detecting,
- diagnosing, and
- treating diseases.

In the field of cancer research, there are various projects (thousands of them) out there that have individually tested drug responses and recorded their data.
	
To perform thorough analyses of various drugs, compiling data from different sources is crucial. This can be useful in conducting downstream analysis. However, different data sources are scattered. That's where BMEG comes in. 

###  BMEG: The Big Solution
#####  Bio Medical Evidence Graph
Simply put, BMEG is a set up to run analyses of data that are compiled from various sources into one server. It is a tool that helps us get a comprehensive version of recorded data. BMEG allows for the user to easily access raw data from multiple sources all in one convenient spot. Ultimately, the user can compile data from multiple sources into a single spreadsheet using BMEG. Furthermore, BMEG harmonizes data into one alias, rather than requireing the user to conduct this harmonization themselves. Data sources may have overlaps, which can reinforce existing knowledge. And in cases where there are no overlaps, different data sources can complement each other. 

A growing number of data sources have been loaded onto BMEG. And BMEG will continue to expand its database by incorporating additional data from new sources.

Now you know what BMEG is and what it does, you can start using BMEG. Follow the step-by-step tutorial below.

### How to Use BMEG

This six-step tutorial is created to introduce you to BMEG. Because this tutorial is designed to get you started from the basics, **no prior knowledge is necessary**. As you go through the tutorial, **remember that obtaining data from BMEG for your specific analysis is the ultimate goal**, so everything we will do before then is to set things up for that last step. This tutorial will focus on three main areas: **1.** setting up the right tools to use BMEG, **2.** accessing BMEG, and **3.** conducting analysis.

>***OPTION:*** The code used in this tutorial can be found in a user-friendly, interactive ipython scrpit in Google Colaboratory. Follow this link (***PUT THE LINK TO GOOGLE COLAB SCRIPT***) to open the code in a browser and follow along with the tutorial. When you click on the link, it will take you to your Google Drive. Choose the option at the top center of the page that says **"Open with Google Colaboratory"**.(If you have a Jupyter Notebook already installed, you can choose the option of downloading). Once in Colaboratory, you can view the code. On the top left corner, hit the **"Open in Playground"** button, then hit **"Run anyway"**, and **"yes"**. Now follow along. 

#### 1. Grip Set up and Authentication

This is a one time setup step.

##### 1.1  Getting set up with gripql
    

Gripql (GRaph Integration Platform query language) is a python library for interacting with a GRIP server. Ultimately, gripql provides a graph interface on top of a variety of existing database technologies. You can find out more about GRIP right <a href="https://bmeg.github.io/grip/" target="_blank" >here</a>.

  

Gripql is a python library for GRIP. Go ahead and install it using this <a href="https://pypi.org/project/gripql/" target="_blank" >link</a> in terminal.

 ``` bash
 pip install gripql
 ```
 
##### 1.2 Authentication
    

Your next step is connecting your Google account to our website. We use this step to be able to authenticate that you are a BMEG user. Press the link to go to our <a href="https://bmeg.io/analyze/getting_started/" target="_blank" >authentication page</a>
  and click on “Authenticate with Google” which will redirect you to Google to verify identity. Then you will be granted interactive and API access.

  

Or if you would rather do the set up and authentication from your  terminal, you can use the bash code down below. This code achieves two main goals. The ``pip install gripql`` part installs the gripql library for python and the other two lines of code below it are made to help you authenticate using your Google credentials. Run the following in terminal as per the above authentication page.

  
``` bash
# Make query using curl
curl -H "OauthEmail: <email>" -H "OauthAccessToken: <token>=" -H "OauthExpires: <length>" https://bmeg.io/api/v1/graph

# Create credential file
echo '{"OauthEmail":"<email>","OauthAccessToken":"<token>=","OauthExpires":<length>} ' > /tmp/bmeg_credentials.json
```

> The following blocks of code are written in python. Therefore, it would be beneficial to have python installed on your device. Instructions on installing python can be found <a href="https://www.python.org/downloads/" target="_blank" >here</a>.
.
>
>To install the libraries used in this tutorial, you will most likely use ``pip`` so it would be useful to install ``pip`` as well. Instructions on installing ``pip`` are <a href="https://pypi.org/project/pip/" target="_blank" >here</a>. If you have a python version 2.7.9 or newer, ``pip`` comes included.

#### 2. Situate Libraries and Connect to server

Each of the libraries listed below have different functionalities that ultimately help us in our analysis. To be able to import the libraries in our code, we first need to install them (if we don’t already have them). Follow the links below to install the libraries on your computer. The links will take you to a page with instructions on how to install each library for different systems.
    
- <a href="https://pypi.org/project/pandas/" target="_blank" >Instructions</a>
 on installing pandas - open source python library that helps us perform fast data cleaning, preparation, and analysis.
    
- <a href="https://www.scipy.org/install.html" target="_blank" >Instructions</a>
 on installing numpy - a multidimensional container for scientific computing that allows us to do calculations on different collections of values.
    
- json - (JavaScript Object Notation) is a data interchange language. This package usually comes with python. Check if you already have it installed.

- os - in python, this module provides functions for interacting with the operating system. This package should already be installed. Check if you already have it.

Now we have taken care of that, let's begin.

Import python libraries
``` python
import gripql
import pandas as pd
import numpy as np
import os
```
  

This code here will connect you to the BMEG server and create a graph handle. As you might have already known, **the BMEG server is where all the data lives.** To grab any type of data, we must establish a connection with the server. The first line in this code block sets up a connection with the BMEG server using grip and the second line creates a graph handle based on a specific graph schema. **In other words, while the first line sets up a connection with bmeg, the second line chooses a specific schema type. It is recommeneded to use the most recent schema which is found** <a href="https://bmeg.io/explore/schema" target="_blank" >**here**</a>



``` python 
conn = gripql.Connection("https://bmeg.io/api", credential_file = '/tmp/bmeg_credentials.json')
G = conn.graph("bmeg_rc2") 
```
#### 3. Query data from a particular data source

Traverse graph to grab all data from one data source (e.g. CCLE). See how the graph is set up by viewing the current <a href="https://bmeg.io/explore/schema" target="_blank" >schema</a>


>**NOTE:** gripql is the language we use to trasverse the schema graph. <a href="https://bmeg.github.io/grip/docs/queries/operations/" target="_blank" >This link</a> contains many gripql operations that can help you. 

Here, you’ll be grabbing all the data from one data source (e.g. CCLE) found within BMEG. In the first line of this code block, we are creating what is essentially an empty list and naming it ``p``. Then within the for loop, we are searching for a vertex with the label “project”. In our if statement, we ask if the data project identification starts with CCLE and if it does, we bring out that data source under the name ``p``.

``` python
p = []
# Start query at vertex and look for labels starting with Project
for row in G.query().V().hasLabel("Project"):
    if row.data.project_id.startswith("CCLE"):
        p.append(row.gid)
```

#### 4. Pull out data of interest from node properties

Accessing a whole data source will likely mean that you will have a lot of information you don’t need. For this reason, we need to filter our data to only get precise information on the things we want to know about. This code is made to filter data by only keeping data points of interest.

For example, in this tutorial, I am interested in looking at the drug response that a given drug has on a cell line. Therefore I’m interested in 1) drug name 2) which cell line the drug was tested on and 3) what the drug response was (EC50).


+ BMEG nodes that will be traversing through: Project > **Case** > Sample > Aliquot > **DrugResponse** > **Compound** 
+ Bolded are nodes that we will be pulling node properties (data). 

``` python
# Traverse through schema nodes
q = G.query().V(p).out("cases").as_("CASE").out("samples").out("aliquots").out("drug_response").as_("DRUGRESP").out("compounds").as_("COMP")

# Render node properties of interest (cell line, drug, EC50 value)
q = q.render(["$CASE._data.case_id", "$COMP._gid", "$DRUGRESP._data.ec50"])
```
If you want to learn more about graph traversal in BMEG, go to our **Graph Traversal Tutorial** right **here** **(A link to the Graph Traversal Tutorial webpage)**

#### 5. Save data locally

We need to save our data file so we can access it whenever we want. But before that, we need to re-format our data so that it can be saved easily. 

First, populate data into a dictionary.

+ ``data`` keys = desired columns and values = a nested dictionary where keys = desired rows and values = entry
+ In our example we use keys = cell lines and values = a nested dictionary where keys = drugs and values = EC50 values

Now let’s format the data so that we can save it as a .tsv file. To do this we will populate the data in a dictionary then create a DataFrame from that dictionary. And finally save this DataFrame as a .tsv file.

``` python
data = {}
for row in q:
    # If cell line not in dict > add a new key to dict
    if row[0] not in data:
        data[row[0]] = { row[1] :  row[2] } 
    # If cell line already added to dict > add a new key to nested dict
    else:
        data[row[0]][row[1]] = row[2]
```

Second, create a pandas DataFrame for readability purposes.

``` python
# Create pandas df
drugDF = pd.DataFrame(data)

# Sort rows by cell line name, sort cols by drug name 
drugDF = drugDF.sort_index().sort_index(axis=1) 
```

Let’s take a quick break and view what our data looks like:


``` python
drugDF
```
If you have run all the code correctly upto this point, you would see something like this:
![](https://lh4.googleusercontent.com/vyEs5WXhZ3vCh7fIkqJWQHbm9TN_AXTCO65wPwhkCVEJwQmbuQsEO7El8xIhAz8wEMFfVjf7DQvSC3FVPErpPlv0jcRYou17ezxlZDc8HB399QZqDrBx52XeieIq9dNYSypZICFa)

Now save it as a .tsv file. Replace “where-to-save” with the path where you'd like to save this file on your machine and replace "name-of-drug-tsv" with the file name you'd like.
``` python
# Save
output_path = "where-to-save-file"
ofn_drug = "name-of-drug-tsv"

os.chdir(output_path)
drugDF.to_csv(ofn_drug, sep="\t")
```

#### 6. Continue with downstream analysis

Congratulations on making it to the last step of this tutorial! You now have produced a data file from BMEG and can continue with your analysis.

You can check out some downstream analysis examples <a href="https://bmeg.io/analyze/examples/" target="_blank" >here</a>.

If you have any questions or need more support, contact us using <a href="https://gitter.im/bmeg/" target="_blank" >Gitter</a>. We will get back to you as soon as possible.
