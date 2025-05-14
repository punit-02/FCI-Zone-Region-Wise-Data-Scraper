# 🏗️ FCI Zone-Region Wise Data Scraper

This project scrapes **zone-wise and region-wise storage data** from the [Food Corporation of India (FCI)](https://fci.gov.in/) official 
website using **Selenium** and **BeautifulSoup**, then saves the output to a CSV file for further analysis.

![Image](https://github.com/user-attachments/assets/6e36b420-204c-433e-8b90-7a4bfb5fda5a)

---

## ⚙️ How It Works

1. **Open Website:** It starts by launching the FCI website using Selenium and a Chrome browser.
2. **Switch to English:** Clicks the translate button to switch the content into English for consistent data scraping.
3. **Dropdown Selection:** Iterates through a predefined list of zones and regions, selecting each combination from the dropdown.
4. **Scrape Data:** Uses BeautifulSoup to extract the key values such as total depots, rice/wheat stock, and total capacity for each region.
5. **Store in DataFrame:** Appends all collected data into a single pandas DataFrame.
6. **Export to CSV:** Saves the final result to a file named `zone_region_data.csv`.

![Image](https://github.com/user-attachments/assets/0f01b61e-c813-41c4-9189-a682aab9d11b)
---

# 📊 Sample Output (CSV Structure)

| Zone       | Region       | Total No. Of Depots | Total Capacity | Rice Stock | Wheat Stock | Total Stock |
| ---------- | ------------ | ------------------- | -------------- | ---------- | ----------- | ----------- |
| East Zone  | Bihar Region | 48                  | 4,69,494 MT    | ...        | ...         | ...         |
| North Zone | Delhi Region | ...                 | ...            | ...        | ...         | ...         |
| ...        | ...          | ...                 | ...            | ...        | ...         | ...         |

---

# 📝 Notes

1. ✅ Make sure your ChromeDriver version matches your installed Chrome browser.

2. ⏳ Use time.sleep() smartly for better page load handling, or integrate WebDriverWait for more robust performance.

3. 📍 Add exception handling in future updates to manage dropdown or network issues.

4. 🧼 You can further clean and process the scraped data in the DataFrame if needed.
   
---

## 🧾 Code

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import Select
import time
from bs4 import BeautifulSoup
import pandas as pd

# Setup ChromeDriver
driver_path = "C:/Users/Asus/Data Science/Web Scrapping/chromedriver.exe"
service = Service(driver_path)
options = Options()
driver = webdriver.Chrome(service=service, options=options)
driver.get("https://fci.gov.in/")

# Click Translate to English
translate_button = driver.find_element(By.XPATH,'//*[@id="offcanvasSetting"]/div[2]/ul/li[3]/div/a')
translate_button.click()
time.sleep(3)

# List of Zone-Region pairs
zone_region_pairs = [
    ("East Zone", "Bihar Region"),("East Zone", "Jharkhand Region"),("East Zone", "Odisha Region"), ("East Zone", "WB Region"), 
    ("North East Zone", "Arunachal Region"),("North East Zone", "Assam Region"),("North East Zone", "Manipur Region"),
    ("North East Zone", "Nagaland Region"),("North East Zone", "NEF Region"),
    ("North Zone","Delhi Region" ), ("North Zone", "Haryana Region"), ("North Zone","HP Region"), ("North Zone","J&K Region"),
    ("North Zone", "Punjab Region"),("North Zone","Rajasthan Region"),("North Zone","UP Region"),("North Zone","Uttarakhand Region"),
    ("South Zone","A P Region" ),("South Zone","Karnataka Region" ),("South Zone","Kerala Region" ),("South Zone","Tamil Nadu Region" ),
    ("South Zone","Telangana Region" ),
    ("West Zone","Chhattisgarh Region" ),("West Zone","Gujarat Region" ),("West Zone","Maharastra Region" ),("West Zone", "MP Region")
]

# List to store all region-wise data
all_data = []

# Loop through all zone-region pairs
for zone_name, region_name in zone_region_pairs:
    zone_dropdown = Select(driver.find_element(By.ID, 'zone'))
    zone_dropdown.select_by_visible_text(zone_name)
    time.sleep(3)

    region_dropdown = Select(driver.find_element(By.ID, 'region'))
    region_dropdown.select_by_visible_text(region_name)
    time.sleep(3)

    selected_zone = zone_dropdown.first_selected_option.text
    selected_region = region_dropdown.first_selected_option.text

    soup = BeautifulSoup(driver.page_source, 'html.parser')
    titles = [d.text.strip() for d in soup.find_all("p", class_="result-text mb-0 screen-reader font-adjust")]
    values = [d.text.strip() for d in soup.find_all("p", class_="result-no mt-0 mb-1 screen-reader font-adjust")]

    if len(titles) == len(values):
        row = {"Zone": selected_zone, "Region": selected_region}
        for title, value in zip(titles, values):
            row[title] = value
        all_data.append(row)
    else:
        print(f"⚠️ Mismatch in titles and data for {selected_zone} - {selected_region}")

# Save all data to DataFrame and CSV
df = pd.DataFrame(all_data)
df.to_csv("zone_region_data.csv", index=False)
print("✅ Data saved to zone_region_data.csv")
```
---

# Conclusion
1. Demonstrated automation and data extraction
2. Delivered clean, structured government data
3. Ready for further analysis and reporting

 ---

 # Technology Used
 * Python with Selenium + Beautiful Soup


[Presentation.pdf](https://github.com/user-attachments/files/20204138/Presentation.pdf)

