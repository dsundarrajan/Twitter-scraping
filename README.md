Topic : Twitter-scraping

# Install the required packages

#!pip install snscrape

#!pip install pymongo

#!pip install streamlit

# Import the required packages

import snscrape.modules.twitter as sntwitter

import pandas as pd

import pymongo

import datetime

import json

import streamlit as st

#Application Programming Interface (API)

myclient = pymongo.MongoClient("mongodb://localhost:27017/")

mydb = myclient["twitter_scraping_db"]

# Streamlit Graphical User Interface (GUI) 

#st.subheader("""Twitter Data scrap page""")

#with st.form(key='Twitter form'):
    
    #Search_term=st.text_input('Search here')
    #Start_date = st.date_input("From date",datetime.datetime.now())
    #End_date = st.date_input("To date",datetime.datetime.now())
    #limit = st.slider('Tweet Range',0,1000,step=50)
    #output_csv=st.radio('Download the file',['yes','No'])
    #file_name=st.text_input('Name the file:',max_chars=25)
    #submit_button =st.form_submit_button(label='Search')


# Search here ( #anything / Limits)
x = str(input('Enter the #tag word you want to search: '))
date_entry = input('Enter a date in YYYY-MM-DD format: ')
year, month, day = map(int, date_entry.split('-'))
date1 = datetime.date(year, month, day)
limit_days = int(input('Enter the number of days to limit: '))
tweet_limit = int(input('Enter the tweet limit: '))

end_date = date1
start_date = end_date - datetime.timedelta(limit_days)

tweet_list = []
tweet_list.clear()

#Collecting the twitter data
def keyword():
    
    query = '{} since:{} until:{}'.format(x, start_date, end_date)
    print(query)
    
    for i, tweet in enumerate(sntwitter.TwitterSearchScraper('{}'.format(query)).get_items()):
        
        if i > tweet_limit:
            break
            
        tweet_list.append({
            'Keyword': '{}'.format(x),
            'Tweet_Date' : tweet.date, 
            'Id': tweet.id, 
            'URL': tweet.url,
            'content': tweet.content,
            'username': tweet.user.username,
            'reply_count': tweet.replyCount,
            'retweet_count': tweet.retweetCount,
            'like_count': tweet.likeCount})
        
keyword()
df = pd.DataFrame(tweet_list)
df.to_csv('{}.csv'.format(x))

collection_1 = mydb['Collection_1']

df
