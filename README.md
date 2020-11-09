# Tarea_PCE2_UOC

# from selenium import webdriver
# from selenium.webdriver.common.by import By
# from selenium.webdriver.support.ui import WebDriverWait as wait
from selenium.webdriver.support import expected_conditions as EC
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
driver = webdriver.Chrome('/Users/ester/Downloads/chromedriver')
driver.get("https://www.realclearpolitics.com/epolls/latest_polls/")
wait(driver, 10).until(EC.element_to_be_clickable((By.XPATH,
"//span[text()='Poll/Map']"))).click()
elems = driver.find_elements_by_xpath("//a[contains(@href,
'/president/us/general_election')][not(@href = following::a/@href)]")
list3 = []
for elem in elems:
if elem.get_attribute("href").count("vs") == 1: list3.append(elem.get_attribute("href"))
datasets = []
for i in list3:
page = requests.get(i)
soup = BeautifulSoup(page.content, 'html.parser')
heading = [th.get_text() for th in soup.find("tr").find_all("th")] + ['Election Year']
datasets.append(heading)
for row in soup.find_all("tr")[1:]:
dataset = list(td.get_text() for td in row.find_all("td"))
election = dataset.append(i.split('/')[4])
if len(dataset) > 6:
datasets.append(dataset)
df = pd.DataFrame(datasets,columns=['Poll', 'Date', 'Sample', 'MoE', 'Democratic Party',
'Republican Party', 'Spread', 'Election Year'])
df = df.drop_duplicates()
now = datetime.now()
date_time = now.strftime("%m_%d_%Y_%H_%M_%S")
df.to_csv(str(date_time + 'eleccionesUSA.csv'), index = False, encoding='utf-8')
