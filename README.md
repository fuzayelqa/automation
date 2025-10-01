# Parametrized Pytest with Selenium 
# Author- Ozih Uddin Al Fuzayel


import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
import time


# Fixture to initialize and quit the browser
@pytest.fixture
def browser():
    driver = webdriver.Chrome()
    driver.maximize_window()
    yield driver
    driver.quit()


# Test data: (username, password)
login_data = [
    ("valid_email@example.com", "correct_password"),
    ("invalid_email@example.com", "wrong_password"),
    ("", "some_password"),                     # Empty username
    ("some_user@example.com", ""),             # Empty password
    ("", "")                                   # Both empty
]


@pytest.mark.parametrize("username,password", login_data)
def test_login(browser, username, password):
    # Replace with the actual login page URL
    browser.get("https://www.facebook.com/")

    # Locate and fill the username and password fields
    browser.find_element(By.ID, "email").send_keys(username)
    browser.find_element(By.ID, "pass").send_keys(password)

    # Click login button
    browser.find_element(By.NAME, "login").click()

    time.sleep(3)  # Wait for result - adjust or replace with proper wait
    
    # Example validation: Check if the login failed message appears
    if username and password == "correct_password":
        assert "login" not in browser.current_url.lower()  # Assuming successful login redirects
    else:
        assert "login" in browser.current_url.lower() or "error" in browser.page_source.lower()
