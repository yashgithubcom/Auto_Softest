## Automated Software Testing using Python

This repository contains resources and examples for implementing automated software testing using Python. Automated testing is a crucial aspect of modern software development, ensuring code quality, reliability, and efficiency. With Python's robust testing frameworks, such as `unittest`, `pytest`, and `nose`, developers can write comprehensive test suites to automate the validation of their software applications.

### Description

Test Automation Framework using Selenium and Python with the following features:

- Framework is based on the Page Object Model.
- Reporting using Allure report.
- Reading locators from JSON file.
- Reading test data from JSON file.
- Integrated with TestRail to update test case status after each run.
- Framework can integrate with CodeShip.

### Install Dependencies

Install the required packages listed in `requirements.txt` using:
```bash
pip install -r requirements.txt
```

### Create New Test Case

To create a new test case using the Framework, follow these steps:

1. In the locators module, create a new locator for the element you would like to use, as shown below:
   ```json
   [{
       "pageName": "HomePage",
       "name": "link_login",
       "locateUsing": "xpath",
       "locator": "//a[contains(text(),'Log In')]"
   }]
   ```

2. In the test data module, add the test data needed for your test case, as below:
   ```json
   {
       "environment": "https://learn.letskodeit.com/",
       "browser": "firefox",
       "email": "test@email.com",
       "password": "abcabc"
   }
   ```

3. If the element exists in more than one page (Navigation element), use the navigation module to create a script for that navigation bar and add your navigation action to that element, as shown below:
   ```python
   def goToLoginPage(self):
       self.elementClick(*self.locator(self.homePage_locators, 'link_login'))
   ```

4. If the element exists in only one page, go to the page module and create a new script for that page, e.g., `login_page.py`, and add all the actions in that page, as shown below:
   ```python
   def login(self, email, password):
       self.sendKeys(email, *self.locator(self.loginPage_locators, 'input_email'))
       self.sendKeys(password, *self.locator(self.loginPage_locators, 'input_password'))
       self.elementClick(*self.locator(self.loginPage_locators, 'btn_login'))
   ```

5. In the test module, create a new script for your test case(s), e.g., `test_login.py`, and add your test case, as shown below:
   ```python
   @allure.story('epic_1') # story of the test case
   @allure.severity(allure.severity_level.MINOR) # severity of the test case
   @pytestrail.case('C48') # test case id on test rail
   def test_login_successfully(self):
   
       with allure.step('Navigate to login page'): # name of the test step
           self.homeNavigation.goToLoginPage()
           self.ts.markFinal(self.loginPage.isAt, "navigation to login page failed") # check if the navigation to login page occurs successfully
   
       with allure.step('Login'): # name of the test step
           self.loginPage.login(email=td.testData("email"), password=td.testData("password"))
           self.ts.markFinal(self.dashboardPage.isAt, "login failed") # check if login successfully
   ```

### Notes

- Use `@allure.story('[epic name]')` decorator before each test case to define the related epic/story.
- Use `@allure.severity(allure.severity_level.[severity])` decorator before each test case to define the severity of the test case (Minor/Major/Critical/Blocker).
- Use `@pytestrail.case('[test case id on testrail]')` decorator before each test case to define the related test case id on TestRail to make the script update run status on TestRail.

### Run the Test Case

To run the test case after creation, use one of the following commands:

- To run the test case and create an Allure report but without updating the status run on TestRail:
  ```bash
  py.test --alluredir=allure_report tests/test_login.py
  allure serve allure_report
  ```

- To run the test case, create an Allure report, and update the status of the run on TestRail:
  ```bash
  py.test --alluredir=allure_report tests/test_login.py --testrail
  allure serve allure_report
  ```

### Note

There are other options to run that you can search for, such as running all the test cases for a specific epic/story or with a specific severity.

### Integration with TestRail

To set up the integration with TestRail, edit `testrail.cfg` with your TestRail domain and credentials, as shown below:
```ini
[API]
url = https://[your testrail domain].testrail.io
email = [testrail email]
password = [testrail password]

[TESTRUN]
project_id = [project id]
```
