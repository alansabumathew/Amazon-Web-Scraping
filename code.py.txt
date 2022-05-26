#!pip install python-csv
#!apt update
#!apt install chromium-chromedriver
#!pip install selenium

import csv
from bs4 import BeautifulSoup
from selenium import webdriver
options = webdriver.ChromeOptions()
options.add_argument('--headless')
options.add_argument('--no-sandbox')
options.add_argument('--disable-dev-shm-usage')

url='https://www.amazon.in/s?k=bags&crid=2M096C61O4MLT&qid=1653308124&sprefix=ba%2Caps%2C283&ref=sr_pg'
driver = webdriver.Chrome(options=options)
driver.get(url)




len(results)

item=results[0]
atag=item.h2.a

name=atag.text.strip()

url='https://www.amazon.in/s?k=bags&crid=2M096C61O4MLT&qid=1653308124&sprefix=ba%2Caps%2C283&ref=sr_pg'+atag.get('href')

price_parent=item.find('span','a-price')
price=price_parent.find('span','a-offscreen').text

rating=item.i.text

def extract_record(item):

  atag=item.h2.a
  name=atag.text.strip()

  url='https://www.amazon.in/s?k=bags&crid=2M096C61O4MLT&qid=1653308124&sprefix=ba%2Caps%2C283&ref=sr_pg'+atag.get('href')
  
  try:
    price_parent=item.find('span','a-price')
    price=price_parent.find('span','a-offscreen').text
  except AttributeError:
     return
 
  try:
    rating=item.i.text
    review_count=item.find('span','a-size-base s-light-weight-text').text
  except AttributeError:
   rating=''
   review_count=''

  result=(url,name,price,rating,review_count)
  return result

records=[]
results=soup.find_all('div',{'data-component-type':'s-search-result'})



for page in range(1,21):
    driver.get(url.format(page))
    soup=BeautifulSoup(driver.page_source,'html.parser')
    results=soup.find_all('div',{'data-component-type':'s-search-result'})

    for item in results:
      record=extract_record(item)
      if record:
        records.append(record)
driver.close()

for row in records:
  print(row[0])
  print(row[1])
  print(row[2])
  print(row[3])
  print(row[4])
  print()

def get_url(search_term):
  template='https://www.amazon.in/s?k=bags&crid=2M096C61O4MLT&qid=1653308124&sprefix=ba%2Caps%2C283&ref=sr_pg'
  search_term=search_term.replace(' ','+')

  url=template.format(search_term)

  url+='&page{}'
  
  return url

with open('results2.csv','w',newline='',encoding='UTF8') as f:
    thewriter=csv.writer(f)
    thewriter.writerow(['Url','Name','Price','Rating','Review Count'])
    thewriter.writerows(records)

  