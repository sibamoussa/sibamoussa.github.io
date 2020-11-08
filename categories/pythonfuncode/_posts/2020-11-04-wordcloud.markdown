---
layout: single
title:  "Generate a word cloud from a PDF file"
date:   2020-11-04
categories: 
  - images
  - data
  - pds
---

Including visuals to emphasize an information and summarize a document is always nice, but not many of us can do this in a time efficient manner. 

Python makes **word cloud generation** a **simple and quick** process. 

<figure class="half">
  <a href="/images/Mask.jpg"><img src="/images/Mask.jpg" width="100"></a>
    <figcaption>Mask image used to generate the word cloud.</figcaption>
</figure>


Let's go through all the steps to get that fancy word cloud for tomorrow's presentation.

<ol>
<li> Download Python </li>
<li> If you do not want to use python, you can install anaconda to access Jupyter notebook</li>
<li> Open terminal through clicking on start -> type: cmd -> press enter</li>
<li> Install required packages by typing into terminal the following: </li>
</ol>

  ``` 
  py.exe -m pip install --user numpy scipy matplotlib ipython jupyter pandas sympy nose pdfminer textblob
  ```
<ol start="5">
<li> Create a folder through Jupyter Notebook where you would like to store all of your files Note: I prefer making it on my desktop</li>
<li> Save a) the pdf of choice b) the python file (opened through Jupyter notebook) in that same folder</li>
<li>Copy the code below</li>
</ol>
```
#importing necessary packages
import numpy as np
import pandas as pd
from os import path
from PIL import Image
from wordcloud import WordCloud, STOPWORDS, ImageColorGenerator
import textblob
import matplotlib.pyplot as plt
import seaborn as sns
from textblob import TextBlob


import sys
import io
from pdfminer.pdfparser import PDFParser
from pdfminer.pdfdocument import PDFDocument
from pdfminer.pdfpage import PDFPage
from pdfminer.pdfpage import PDFTextExtractionNotAllowed
from pdfminer.pdfinterp import PDFResourceManager
from pdfminer.pdfinterp import PDFPageInterpreter
from io import StringIO
from pdfminer.layout import LAParams
from pdfminer.converter import TextConverter
import re

## Define Functions for Text Cleaning
def extract_text_from_pdf(pdf_path):
    resource_manager = PDFResourceManager()
    fake_file_handle = io.StringIO()
    converter = TextConverter(resource_manager, fake_file_handle)
    page_interpreter = PDFPageInterpreter(resource_manager, converter)
    with open(pdf_path, 'rb') as fh:
        for page in PDFPage.get_pages(fh, 
                                      caching=True,
                                      check_extractable=True):
            page_interpreter.process_page(page)
        text = fake_file_handle.getvalue()
    # close open handles
    converter.close()
    fake_file_handle.close()
    if text:
        return text
text = extract_text_from_pdf(r'\Users\admin\Desktop\Projects\NLP_Project\JES2.pdf')
print(text)


## To remove numerical text
def remove_num(text):
    text = ''.join([i for i in text if not i.isdigit()])
    return text
    
  
    text = np.vectorize(remove_num)(text)
print(text)

## To remove punctuation:

from string import punctuation
def remove_punct(text):
    text = ' '.join(word.strip(punctuation) for word in text.split() if word.strip(punctuation))
    return text
 
def remove_u(text):
    text = text.replace('?','')
    text = text.replace('•','')
    text = text.replace("@",'')
    text = text.replace(";",'')
    text = text.replace("/",'')
    text = text.replace("-",'')
    text = text.replace(",",'')
    text = np.vectorize(remove_punct)(text)
print(text)

## Remove stopwords
stop_words = ["McGill" "i", "me", "my", "myself", "we", "our", "ours", "ourselves", "you", "your", "yours", "yourself", "yourselves", "he", "him", "his", "himself", "she", "her", "hers", "herself", "it", "its", "itself", "they", "them", "their", "theirs", "themselves", "what", "which", "who", "whom", "this", "that", "these", "those", "am", "is", "are", "was", "were", "be", "been", "being", "have", "has", "had", "having", "do", "does", "did", "doing", "a", "an", "the","Mr", "and", "but", "if", "or", "because", "as", "until", "while", "of", "at", "by", "for", "with", "about", "against", "between", "into", "through", "during", "before", "after", "above", "below", "to", "from", "up", "down", "in", "out", "on", "off", "over", "under", "again", "further", "then", "once", "here", "there", "when", "where", "why", "how", "all", "any", "both", "each", "few", "more", "most", "other", "some", "such", "no", "nor", "not", "only", "own", "same", "so", "than", "too", "very", "s", "t", "can", "will", "just", "don", "should", "now"]
def remove_stopwords(text):
    word_list = text.split()
    word_list = [word for word in word_list if word not in stop_words]
    text = ' '.join(word_list)
    return text
    
text = np.vectorize(remove_stopwords)(text)
text = text.tolist()
print(text)

#setting mask image
mask = np.array(Image.open(r'\Users\admin\Desktop\flask.jpg'))
plt.imshow(mask)
plt.axis("off")

# lower max_font_size, change the maximum number of word and lighten the background:
#creating wordcloud
wordcloud = WordCloud(mask=mask, width=2000, height=1000,contour_color="black", max_words=100,relative_scaling = 0, background_color = "white").generate(text)
image_colors = ImageColorGenerator(mask)
plt.figure(figsize=[20,15])
plt.imshow(wordcloud.recolor(color_func=image_colors), interpolation="bilinear")
plt.axis("off")
_=plt.show()
```
<ol start="9">
<li>Save a mask image as a template for the wordcloud</li>
<li>Run the code</li>
<li>Voila!</li>
  
  <figure class="full">
    <a href="/images/WordCloud.jpeg"><img src="/images/WordCloud.jpeg"></a>
    <figcaption>Generated Word Cloud.</figcaption>
</figure>
</ol>
