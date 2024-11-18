# Selenium Overview
<details>

## Definition
Selenium is an umbrella project for a range of tools and libraries that enable and support the automation of web browsers. It provides extensions to emulate user interaction with browsers, a distribution server for scaling browser allocation
  1. **WebDriver**: uses browser automation APIs provided by browser vendors to control the browser and run tests
  2. **IDE (Integrated Development Environment)**: is Chrome and Firefox extension and is generally the most efficient way to develop test cases by recording the users’ actions in the browser for you, using existing Selenium commands, with parameters defined by the context of that element. 
  2. **Grid**: run test cases in different machines across different platforms

![image](https://github.com/user-attachments/assets/7ae2ec01-d4e5-4b22-88bd-17075d8f6243)

## Advantages

![image](https://github.com/user-attachments/assets/6fc43f70-48f3-4fb7-aa4c-c9b2fe378b15)

</details>

# Selenium Webdriver
<details>
  <summary>Multi-Browser Compatibility</summary>

![image](https://github.com/user-attachments/assets/0813d356-9769-4e29-9d96-9dc6b7724201)

</details>
<details>
  <summary>How it works</summary>

WebDriver talks to a browser through a driver. Communication is two-way: WebDriver passes commands to the browser through the driver, and receives information back via the same route.

![image](https://github.com/user-attachments/assets/bcfa7c9e-e8a8-4e46-9f60-fce5fe81ee01)

</details>
<details>
  <summary>Wait Strategy</summary>

  ## Implicit waits
Set as global setting for every element  location call for the entire session with the timeouts capability in the browser options, or with a driver method
  ```c#
   driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(2);
  ```
  ## Explicit waits
Poll the application for a specific condition to evaluate as true before it exits the loop and continues to the next command in the code, specify the exact condition to wait for in each place it is needed
  ```c#
  WebDriverWait wait = new WebDriverWait(driver, TimeSpan.FromSeconds(2))
   {
         PollingInterval = TimeSpan.FromMilliseconds(300),
   };
   wait.IgnoreExceptionTypes(typeof(ElementNotInteractableException));

   wait.Until(d => {
        revealed.SendKeys("Displayed");
        return true;
   });
  ```
</details>

# Test Framework/Runner
<details>
  
WebDriver **only** communicate with the browser via any of the methods above. We need frameworks/runner come into play for extra works or additional features like:
* Assertions 
* Reporting 
* Support Given/When/Then behavior.
* Use before/after hooks 
* Run things in groups or in parallel
* Organize code

Test framework should matches the language bindings, e.g., 
* NUnit for .NET
* JUnit for Java
* RSpec for Ruby, etc.

The test framework is responsible for running and executing your WebDriver and related steps in your tests.

![image](https://github.com/user-attachments/assets/98cbe643-611d-4fec-97fb-13bec24e7090)

</details>

# Page Object Model (POM)
<details>
  
* a design pattern or a framework that we use in Selenium using which one can create an object repository of the different web elements across the application. By this way, the code becomes easy to maintain and reduces code duplicity
* In the Page Object Model framework, we create a class file for each web page. This class file consists of different web elements present on the web page
* Test scripts then use these elements to perform different actions

![image](https://github.com/user-attachments/assets/a921bc62-cac6-4144-9c63-827cab3fe7b6)

</details>

# Common Errors
<details>
  <summary>NoSuchElementException</summary>

  ## Likely Cause
  * looking for the element in the wrong place (perhaps a previous action was unsuccessful).
  * looking for the element at the wrong time (the element has not shown up in the DOM, yet)
  * The locator has changed since you wrote the code

  ## Possible Solutions
  * Make sure you are on the page you expect to be on, and that previous actions in your code completed correctly
  * Make sure you are using a proper Waiting Strategy
  * Update the locator with the browser’s devtools console or use a browser extension like: SelectorsHub
</details>
<details>
  <summary>StaleElementReferenceException</summary>
An element goes stale when it was previously located, but can not be currently accessed. Elements do not get relocated automatically; the driver creates a reference ID for the element and has a particular place it expects to find it in the DOM. If it can not find the element in the current DOM, any action using that element will result in this exception.

  ## Likely Cause
  * You have refreshed the page, or the DOM of the page has dynamically changed.
  * You have navigated to a different page.
  * You have switched to another window or into or out of a frame or iframe

  ## Possible Solutions
  * The DOM has changed
    * Always relocate the element every time you go to use it
    * If it is stale, exception can be caught, the element relocated with the stored locator, and the method re-tried
  * The Context has changed
    * if you move to a different context — like a different window or a different frame or iframe — , you need to make sure to switch back to the correct context before using the element.
  * The Page has changed
    * navigate back to the correct location and relocate it.
</details>
<details>
  <summary>ElementClickInterceptedException</summary>
Try to click an element, but the click would instead be received by a different element.

  ## Likely Cause
  * UI Elements Overlapping
  * Animations

  ## Possible Solutions
  * Use Explicit Waits: `ExpectedCondition.ToBeClickable()` with `WebDriverWait`
  * Scroll the Element into View: `WebDriver.executeScript('window.scrollBy(0,-250)')` or `Actions.moveToElement(element)`
</details>
<details>
  <summary>InvalidSessionIdException</summary>
Sometimes the session you’re trying to access is different than what’s currently available

  ## Likely Cause
  * This usually occurs when the session has been deleted (e.g. `driver.quit()`) or if the session has changed, like when the last tab/browser has closed (e.g. `driver.close()`)
  * Animations

  ## Possible Solutions
  * Check your script for instances of `driver.close()` and `driver.quit()`, and any other possible causes of closed tabs/browsers. It could be that you are locating an element before you should/can.
</details>

# Locators
<details>
  
## Traditional Locators
![image](https://github.com/user-attachments/assets/a7af2313-8a81-47b2-b8a2-b503b4229e3e)
## Utilizing Locators
* ByChained:  enables  to chain two By locators together in hierachy
```c#
// instead of
WebElement fruits = driver.findElement(By.id("fruits"));
WebElement fruit = fruits.findElement(By.className("tomatoes"));
// to
By example = new ByChained(By.id("login-form"), By.id("username-field"));
WebElement username_input = driver.findElement(example);
  ```

* ByAll:  finding elements that mach either of your By locators
```c#
By example = new ByAll(By.id("password-field"), By.id("username-field"));
            List<WebElement> login_inputs = driver.findElements(example);
```
* Relative Locators: support in Selenium 4
```c#
By emailLocator = RelativeLocator.with(By.tagName("input")).above(By.id("password"));
By passwordLocator = RelativeLocator.with(By.tagName("input")).below(By.id("email"));
By cancelLocator = RelativeLocator.with(By.tagName("button")).toLeftOf(By.id("submit"));
By submitLocator = RelativeLocator.with(By.tagName("button")).toRightOf(By.id("cancel"));
By emailLocator = RelativeLocator.with(By.tagName("input")).near(By.id("lbl-email"));
By submitLocator = RelativeLocator.with(By.tagName("button")).below(By.id("email")).toRightOf(By.id("cancel"));
  ```
## Evaluating the Shadow DOM
```c#
WebElement shadowHost = driver.findElement(By.cssSelector("#shadow_host"));
SearchContext shadowRoot = shadowHost.getShadowRoot();
WebElement shadowContent = shadowRoot.findElement(By.cssSelector("#shadow_content"));
```

</details>
