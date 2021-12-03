[![Maven Central](https://img.shields.io/maven-central/v/io.github.bonigarcia/webdrivermanager.svg)](https://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.github.bonigarcia%20a%3Awebdrivermanager)
[![Build Status](https://github.com/bonigarcia/wdm-agent-example/workflows/build/badge.svg)](https://github.com/bonigarcia/wdm-agent-example/actions)
[![badge-jdk](https://img.shields.io/badge/jdk-8-green.svg)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
[![License badge](https://img.shields.io/badge/license-Apache2-green.svg)](https://www.apache.org/licenses/LICENSE-2.0)
[![Backers on Open Collective](https://opencollective.com/webdrivermanager/backers/badge.svg)](#backers)
[![Sponsors on Open Collective](https://opencollective.com/webdrivermanager/sponsors/badge.svg)](#sponsors)
[![Support badge](https://img.shields.io/badge/stackoverflow-webdrivermanager_java-green.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/webdrivermanager-java)
[![Twitter Follow](https://img.shields.io/twitter/follow/boni_gg.svg?style=social)](https://twitter.com/boni_gg)

# WebDriverManager Agent Example [![][Logo]][GitHub Repository]

This repository contains a Maven repository with examples to automate the [Selenium WebDriver] binaries management using [WebDriverManager] as **Agent**. This repository is open source, released under the terms of [Apache 2.0 License].

## Usage

In order to use WebDriverManager Agent from tests in a Maven project, this example first includes the WebDriverManager dependency in the `pom.xml` using the `test` scope:

```xml
    <dependency>
        <groupId>io.github.bonigarcia</groupId>
        <artifactId>webdrivermanager</artifactId>
        <version>${wdm.version}</version>
        <scope>test</scope>
    </dependency>
```

Then, the Java agent (``-javaagent``) is configured using the two Maven plugins: ``maven-dependency-plugin`` (which allows to manipulate Maven dependencies) and ``maven-surefire-plugin`` (which executes the unit tests in the Maven lifecycle), as follows: 

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <version>${maven-dependency-plugin.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>properties</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${maven-surefire-plugin.version}</version>
                <configuration>
                    <argLine>-javaagent:${io.github.bonigarcia:webdrivermanager:jar}</argLine>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

When the Maven tests are executed (``mvn test``), WebDriverManager Agent will check the objects being created in the JVM. Just before Selenium WebDriver objects are instantiated in the tests (``org.openqa.selenium.chrome.ChromeDriver``, ``org.openqa.selenium.firefox.FirefoxDriver``, ``org.openqa.selenium.opera.OperaDriver``, ``org.openqa.selenium.edge.EdgeDriver``, or ``org.openqa.selenium.ie.InternetExplorerDriver``), the proper WebDriverManager setup call is executed to manage the required driver (*chromedriver*, *geckodriver*, *msedgedriver*, etc).  

The tests contained in this project are the following (notice there is no setup for *chromedriver* nor *geckodriver*).

```java
class ChromeTest {

    WebDriver driver;

    @BeforeEach
    void setupTest() {
        driver = new ChromeDriver();
    }

    @AfterEach
    void teardown() {
        driver.quit();
    }

    @Test
    void test() {
        // Your test code here
    }

}
```

```java
class FirefoxTest {

    WebDriver driver;

    @BeforeEach
    void setupTest() {
        driver = new FirefoxDriver();
    }

    @AfterEach
    void teardown() {
        driver.quit();
    }

    @Test
    void test() {
        // Your test code here
    }

}
```

When the test are executed through the ``maven-surefire-plugin`` (i.e. running ``mvn test``), the WebDriverManager Agent resolves the proper driver, and the tests are executed correctly:

```
$ mvn test
...
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running io.github.bonigarcia.wdm.agent.test.ChromeTest
2020-05-12 15:38:49 [main] DEBUG io.github.bonigarcia.wdm.WdmAgent.transform(86) -- WebDriverManager Agent is going to resolve the driver for Chrome
2020-05-12 15:38:49 [main] DEBUG i.g.b.wdm.cache.ResolutionCache.checkKeyInResolutionCache(183) -- Resolution chrome=81 in cache (valid until 15:23:29 13/05/2020 CEST)
2020-05-12 15:38:49 [main] INFO  i.g.bonigarcia.wdm.WebDriverManager.resolveDriverVersion(571) -- Using chromedriver 81.0.4044.138 (since Chrome 81 is installed in your machine)
2020-05-12 15:38:49 [main] DEBUG i.g.bonigarcia.wdm.WebDriverManager.manage(520) -- Driver chromedriver 81.0.4044.138 found in cache
2020-05-12 15:38:49 [main] INFO  i.g.bonigarcia.wdm.WebDriverManager.exportDriver(615) -- Exporting webdriver.chrome.driver as /home/boni/.m2/repository/webdriver/chromedriver/linux64/81.0.4044.138/chromedriver
Starting ChromeDriver 81.0.4044.138 (8c6c7ba89cc9453625af54f11fd83179e23450fa-refs/branch-heads/4044@{#999}) on port 31286
2020-05-12 15:38:50 [main] DEBUG i.g.b.wdm.agent.test.ChromeTest.test(54) -- The title of https://bonigarcia.github.io/selenium-jupiter/ is Selenium-Jupiter: JUnit 5 extension for Selenium
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.814 s - in io.github.bonigarcia.wdm.agent.test.ChromeTest
[INFO] Running io.github.bonigarcia.wdm.agent.test.FirefoxTest
2020-05-12 15:46:04 [main] DEBUG io.github.bonigarcia.wdm.WdmAgent.transform(86) -- WebDriverManager Agent is going to resolve the driver for Firefox
2020-05-12 15:46:04 [main] DEBUG i.g.b.wdm.cache.ResolutionCache.checkKeyInResolutionCache(183) -- Resolution firefox=76 in cache (valid until 15:45:41 13/05/2020 CEST)
2020-05-12 15:46:04 [main] INFO  i.g.bonigarcia.wdm.WebDriverManager.resolveDriverVersion(571) -- Using geckodriver 0.26.0 (since Firefox 76 is installed in your machine)
2020-05-12 15:46:04 [main] DEBUG i.g.bonigarcia.wdm.WebDriverManager.manage(520) -- Driver geckodriver 0.26.0 found in cache
2020-05-12 15:46:04 [main] INFO  i.g.bonigarcia.wdm.WebDriverManager.exportDriver(615) -- Exporting webdriver.gecko.driver as /home/boni/.m2/repository/webdriver/geckodriver/linux64/0.26.0/geckodriver
2020-05-12 15:46:07 [main] DEBUG i.g.b.wdm.agent.test.FirefoxTest.test(54) -- The title of https://bonigarcia.github.io/selenium-jupiter/ is Selenium-Jupiter: JUnit 5 extension for Selenium
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 6.163 s - in io.github.bonigarcia.wdm.agent.test.FirefoxTest
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  10.017 s
[INFO] Finished at: 2020-05-12T15:46:10+02:00
[INFO] ------------------------------------------------------------------------
```

## Help

If you have questions on how to use WebDriverManager properly with a special configuration or suchlike, please consider asking a question on [Stack Overflow] and tag it with  *webdrivermanager-java*.

## Support

WebDriverManager is part of [OpenCollective], an online funding platform for open and transparent communities. You can support the project by contributing as a backer (i.e., a personal [donation] or [recurring contribution]) or as a [sponsor] (i.e., a recurring contribution by a company).

### Backers

<a href="https://opencollective.com/webdrivermanager" target="_blank"><img src="https://opencollective.com/webdrivermanager/backers.svg?width=890"></a>

### Sponsors

<a href="https://opencollective.com/webdrivermanager" target="_blank"><img src="https://opencollective.com/webdrivermanager/sponsor.svg?width=890"></a>

## About

WebDriverManager (Copyright &copy; 2015-2021) is a project created and maintained by [Boni Garcia] licensed under [Apache 2.0 License].

[Apache 2.0 License]: https://www.apache.org/licenses/LICENSE-2.0
[Boni Garcia]: https://bonigarcia.github.io/
[Selenium WebDriver]: https://docs.seleniumhq.org/projects/webdriver/
[WebDriverManager]:https://github.com/bonigarcia/webdrivermanager/
[Logo]: https://bonigarcia.github.io/img/webdrivermanager.png
[GitHub Repository]: https://github.com/bonigarcia/wdm-agent-example
[Stack Overflow]: https://stackoverflow.com/questions/tagged/webdrivermanager-java
[OpenCollective]: https://opencollective.com/webdrivermanager
[donation]: https://opencollective.com/webdrivermanager/donate
[recurring contribution]: https://opencollective.com/webdrivermanager/contribute/backer-8132/checkout
[sponsor]: https://opencollective.com/webdrivermanager/contribute/sponsor-8133/checkout
