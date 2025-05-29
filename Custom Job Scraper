from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException, ElementClickInterceptedException
import time
import pandas as pd
import re
import pyfiglet
from colorama import Fore, Style


def print_fsociety_logo():
    ascii_art = pyfiglet.figlet_format("FSOCIETY", font="ansi_shadow")
    print("\033[32m" + ascii_art + "\033[0m")
    print(Fore.LIGHTGREEN_EX + "\U0001F480" + "fsociety - Custom Job Scraper Starting..." + "\U0001F480" + Style.RESET_ALL)


if __name__ == "__main__":
    print_fsociety_logo()

URL = input(Fore.LIGHTGREEN_EX + "Enter Your URL: " + Style.RESET_ALL)
Name_File = input(Fore.LIGHTGREEN_EX + "Enter your File Name: " + Style.RESET_ALL)
chrome_driver_path = r"C:\WebDriver\chromedriver.exe"


options = Options()
options.headless = False
#options.add_argument("--window-size=1920,1080")
options.add_argument("--disable-blink-features=AutomationControlled")

driver = webdriver.Chrome(service=Service(chrome_driver_path), options=options)

try:
    driver.get(URL)
    time.sleep(5)

    def scroll_down():
        driver.execute_script("window.scrollBy(0, 1000);")
        time.sleep(1)
    last_height = 0
    same_height_count = 0
    max_same_scrolls = 3

    while True:
        for _ in range(10):
            scroll_down()

        try:
            mehr_button = driver.find_element(By.XPATH, "//button[contains(text(), 'Mehr Ergebnisse')]")
            driver.execute_script("arguments[0].scrollIntoView({behavior: 'smooth', block: 'center'});", mehr_button)
            time.sleep(2)
            mehr_button.click()
            print(Fore.LIGHTGREEN_EX + "🟢 Clicked 'Mehr Ergebnisse laden'" + Style.RESET_ALL)
            time.sleep(5)
        except NoSuchElementException:
            print(Fore.LIGHTGREEN_EX + "ℹ️ 'Mehr Ergebnisse laden' not found." + Style.RESET_ALL)
        except ElementClickInterceptedException:
            print(Fore.LIGHTGREEN_EX + "⚠️ Click intercepted — retrying with JS click" + Style.RESET_ALL)
            driver.execute_script("arguments[0].click();", mehr_button)
            time.sleep(5)

        new_height = driver.execute_script("return document.body.scrollHeight")
        if new_height == last_height:
            same_height_count += 1
        else:
            same_height_count = 0
        last_height = new_height

        if same_height_count >= max_same_scrolls:
            print(Fore.LIGHTGREEN_EX + "✅ Scrolling complete." + Style.RESET_ALL)
            time.sleep(2)
            break

    print(Fore.LIGHTGREEN_EX + "🔍 Extracting job links..." + Style.RESET_ALL)
    time.sleep(2)
    job_links = set()
    job_elements = driver.find_elements(By.XPATH, "//a[contains(@href, '/stellen/')]")
    for el in job_elements:
        link = el.get_attribute("href")
        if link and link.startswith("https://www.ausbildung.de/stellen/"):
            job_links.add(link)
    time.sleep(2)
    print(Fore.LIGHTGREEN_EX + f"🎉 Collected {len(job_links)} job links." + Style.RESET_ALL)
    time.sleep(2)

finally:
    driver.quit()

data = []
for link in sorted(job_links):
    match = re.search(r"-in-([a-zäöüß\-]+)", link)
    city = match.group(1).replace("-", " ").title() if match else "Unknown"
    sort_key = city.split()[0] if city != "Unknown" else "Unknown"
    data.append({"Job Link": link, "City": city, "SortKey": sort_key})

df = pd.DataFrame(data)
df = df.sort_values(by="SortKey").drop(columns=["SortKey"])

df_final = pd.DataFrame({
    "Job Link": df["Job Link"],
    "": "",
    "  ": "",
    "City": df["City"],
})


df_final.to_excel(str(Name_File) + ".xlsx", index=False)
print(Fore.LIGHTGREEN_EX + "File Created in App folder" + Style.RESET_ALL)


for i in range(10, 0, -1):
    print(Fore.LIGHTGREEN_EX + f"Exiting in {i}" + Style.RESET_ALL)
    time.sleep(1)


def print_fsociety_logo_end():
    ascii_art = pyfiglet.figlet_format("FSOCIETY", font="ansi_shadow")
    print("\033[32m" + ascii_art + "\033[0m")
    print(Fore.LIGHTGREEN_EX + "\U0001F480" + "Custom Job Scraper Published By fsociety" + "\U0001F480" + Style.RESET_ALL)


if __name__ == "__main__":
    print_fsociety_logo_end()

time.sleep(5)

quit()
