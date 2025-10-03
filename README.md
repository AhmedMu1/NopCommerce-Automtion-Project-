# NopCommerce-Automtion-Project-
Testing NopCommerce website by using selenium automation tool and java 
# NopCommerce Automation Testing Project

## 1️⃣ Project Overview

This project is an **automated testing suite** for the [nopCommerce demo website](https://demo.nopcommerce.com/).
The main goal is to test functionalities such as:

* Selecting random categories and subcategories
* Newsletter subscription using database-driven data
* Product comparison features

The project uses **Selenium WebDriver** for browser automation and **TestNG** for test management.

---

## 2️⃣ Technologies & Tools

* **Java 24** – programming language
* **Selenium WebDriver 4.35** – automation tool for web testing
* **TestNG** – test framework for managing and running tests
* **Maven** – project build and dependency management
* **MySQL** – database for storing test data (emails)
* **ChromeDriver** – driver for Chrome browser automation

---

## 3️⃣ Project Structure

```
NopCommerce-Automation/
│
├─ src/main/java/          --> Page Object classes (e.g., NewsletterPage.java, CompareProductPage.java)
├─ src/test/java/          --> Test classes (e.g., NewsletterTest.java)
├─ src/main/resources/     --> Database SQL file and other resources
├─ pom.xml                 --> Maven dependencies and plugins
└─ README.md               --> Project documentation
```

---

## 4️⃣ Database Setup

### 4.1 Local Database

The project uses a MySQL database named `Nopcommerce` with one table `subscribe`:

```sql
CREATE DATABASE Nopcommerce;
USE Nopcommerce;

CREATE TABLE subscribe (
    email VARCHAR(50) PRIMARY KEY
);

INSERT INTO subscribe (email) VALUES ('example@email.com');
```

### 4.2 Connecting the Database

Update your database connection in `DataBaseConnection.java`:

```java
private static final String URL = "jdbc:mysql://localhost:3306/Nopcommerce";
private static final String USER = "root";       // your MySQL username
private static final String PASSWORD = "password"; // your MySQL password
```

---

## 5️⃣ Using DataProvider in Tests

The project uses **TestNG DataProvider** to read emails from the database and feed them to tests:

```java
@DataProvider(name = "emailsProvider")
public Object[][] getEmails() throws Exception {
    Connection conn = DataBaseConnection.getConnection();
    Statement stmt = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
    ResultSet rs = stmt.executeQuery("SELECT email FROM subscribe");

    rs.last();
    int rowCount = rs.getRow();
    rs.beforeFirst();

    Object[][] data = new Object[rowCount][1];
    int i = 0;

    while (rs.next()) {
        data[i][0] = rs.getString("email");
        i++;
    }

    rs.close();
    stmt.close();
    conn.close();

    return data;
}
```

**Example usage in a test:**

```java
@Test(dataProvider = "emailsProvider", dataProviderClass = TestDataProvider.class)
public void subscribeNewsletterTest(String email) {
    NewsletterPage newsletterPage = new NewsletterPage(driver);
    newsletterPage.subscribeNewsletter(email);
}
```

---

## 6️⃣ How to Run the Project

1. **Clone the repository:**

```bash
git clone https://github.com/yourusername/NopCommerce-Automation.git
```

2. **Open in IntelliJ IDEA** (or any Java IDE).

3. **Make sure MySQL is running** and the database is created as described above.

4. **Run tests via Maven:**

```bash
mvn test
```

5. **Test Execution Notes:**

   * Tests are automated for Chrome browser.
   * `WebDriverWait` is used for dynamic elements.
   * If clicks are intercepted, scrolling or explicit waits may be required.

---

## 7️⃣ Key Functionalities

| Feature                   | Description                                                           |
| ------------------------- | --------------------------------------------------------------------- |
| Random Category Selection | Chooses a category and subcategory randomly and navigates to it       |
| Newsletter Subscription   | Reads emails from database and subscribes automatically               |
| Product Comparison        | Automates adding products to compare and verifies the comparison page |
| Database-Driven Tests     | Uses TestNG DataProvider to connect to MySQL and feed test data       |

---

## 8️⃣ Handling Dynamic Elements

* **WebDriverWait** is used for clickable elements:

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.elementToBeClickable(element)).click();
```

* For elements that may be **intercepted**, scroll into view:

```java
((JavascriptExecutor) driver).executeScript("arguments[0].scrollIntoView(true);", element);
```

---

## 9️⃣ Known Issues / Notes

* Click interception may occur if elements overlap; scrolling or waiting may be required.
* Ensure **ChromeDriver** version matches your Chrome browser version.
* MySQL connection must be configured correctly to avoid `SQLException`.
* Stale elements may appear after page updates; use `WebDriverWait` and re-locate elements after navigation.

---

## 🔟 Contact

* For questions, reach out: **[your.email@example.com](mailto:your.email@example.com)**
* Project Author: **Ahmed Mohamed**

---

## 1️⃣1️⃣ Optional Enhancements

* Deploy MySQL to a free online server for shared access.
* Use **Page Object Model (POM)** for better test maintenance.
* Add **screenshots on test failure** using Selenium `TakesScreenshot`.
* Integrate with **GitHub Actions** for automated test execution.
