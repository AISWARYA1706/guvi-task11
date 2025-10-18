# guvi-task11
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
import time

#  FIXTURE
@pytest.fixture
def setup():
    options = Options()
    options.add_argument("--headless")  # run without opening browser window
    service = Service()  # assumes chromedriver is in PATH
    driver = webdriver.Chrome(service=service, options=options)
    driver.maximize_window()
    yield driver
    driver.quit()

#  TEST CASE 1: Validate Login button URL
def test_login_button_url(setup):
    driver = setup
    driver.get("https://www.guvi.in/")
    time.sleep(3)

    login_btn = driver.find_element(By.LINK_TEXT, "Login")
    login_btn.click()
    time.sleep(3)

    assert driver.current_url == "https://www.guvi.in/sign-in/", \
        f"Expected URL is https://www.guvi.in/sign-in/ but got {driver.current_url}"

# TEST CASE 2: Validate Username & Password Fields -
def test_login_fields_visible(setup):
    driver = setup
    driver.get("https://www.guvi.in/sign-in/")
    time.sleep(3)

    username = driver.find_element(By.ID, "email")  # or By.NAME, based on page source
    password = driver.find_element(By.ID, "password")

    assert username.is_displayed() and username.is_enabled(), "Username field not ready"
    assert password.is_displayed() and password.is_enabled(), "Password field not ready"
#  TEST CASE 3: Positive Login
def test_positive_login(setup):
    driver = setup
    driver.get("https://www.guvi.in/sign-in/")
    time.sleep(3)

    driver.find_element(By.ID, "email").send_keys("your_valid_email@example.com")
    driver.find_element(By.ID, "password").send_keys("your_valid_password")
    driver.find_element(By.XPATH, "//button[contains(text(),'Login')]").click()
    time.sleep(5)

    assert "dashboard" in driver.current_url.lower(), "Login failed with valid credentials"

#  TEST CASE 4: Negative Login
def test_negative_login(setup):
    driver = setup
    driver.get("https://www.guvi.in/sign-in/")
    time.sleep(3)

    driver.find_element(By.ID, "email").send_keys("invalid@example.com")
    driver.find_element(By.ID, "password").send_keys("wrongpass")
    driver.find_element(By.XPATH, "//button[contains(text(),'Login')]").click()
    time.sleep(3)

    # Check if error message appears
    error = driver.find_element(By.XPATH, "//*[contains(text(),'Invalid')]")
    assert error.is_displayed(), "Error message not shown for invalid credentials"
