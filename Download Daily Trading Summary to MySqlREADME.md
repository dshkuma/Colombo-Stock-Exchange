# Colombo-Stock-Exchange
#This will down load Colombo Stock Exchange Daily Data and Record in MySql


 from selenium import webdriver
 from selenium.webdriver.common.keys import Keys
 from selenium.webdriver.common.action_chains import ActionChains
 from time import sleep
 from selenium.webdriver.support import expected_conditions as EC
 from selenium.webdriver.support.wait import WebDriverWait
 from selenium.webdriver.common.by import By
 from selenium.webdriver.common.keys import Keys
 import pandas as pd
 import time
 from bs4 import BeautifulSoup
 import math
 import mysql.connector
 from sqlalchemy import create_engine
 from datetime import date
 
 
 hostname="127.0.0.1"
 dbname="cse"
 uname="root"
 pwd="admin"
 
my_connect = create_engine("mysql+pymysql://{user}:{pw}@{host}/{db}"
				.format(host=hostname, db=dbname, user=uname, pw=pwd))
 
 cse = ""
 #driver = webdriver.Chrome()
 driver = webdriver.Chrome('E:/PythonD/NMKPY/chromedriver_win32/chromedriver.exe')  # Optional argument, if not specified will search path.
  # Open the website
 driver.get('https://www.cse.lk/pages/trade-summary/trade-summary.component.html')
 
driver.find_element_by_xpath("//select[@name='DataTables_Table_0_length']/option[text()='All']").click()

row_count = len(driver.find_elements_by_xpath("//table[@id='DataTables_Table_0']/tbody/tr"))
column_count = len(driver.find_elements_by_xpath("//table[@id='DataTables_Table_0']/tbody/tr/td"))
column_count = column_count / row_count



#print(tbldat)
print(row_count)
print(column_count)

 for i in range(row_count):	 
	 html=driver.page_source
	 soup=BeautifulSoup(html,'html.parser')
	 div=soup.select_one("#DataTables_Table_0")
	 table=pd.read_html(str(div))
	 frames = [table[0]]
	 result=pd.concat(frames,ignore_index=True)
	 cse = pd.DataFrame(result) 

    
today = date.today().strftime('%Y-%m-%d')
cse['dat'] = today   

value = input('Do You want to Update Database y or n : ')
# print(value)
if value.lower() == "y":
    
    sql="SELECT max(dat) as dat FROM csedat "
    my_data = pd.read_sql(sql,con=my_connect)
    txtdat = my_data.iloc[0]["dat"]
    
    today = date.today().strftime('%Y-%m-%d')
    if txtdat == today:
        print('Not Updated Database')
    else:
        print('Updated Database')
        cse.to_sql('csedat', my_connect, index=False,if_exists='append')
   
   
else:
    print('Not Updated Database')
    
	
  
