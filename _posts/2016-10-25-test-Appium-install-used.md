---
layout:     post
title:      "Appium 的安装和使用"
subtitle:   "Appium"
date:       2016-10-24
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Appium 的安装和使用

- 基于 WebDrive 协议的一个自动化测试框架，可以对 Android、iOS 以及混合开发的移动端应用进行自动化测试。
- 优势：可以使用 Python 、Ruby 、 Java 、 JavaScript、Php 、 C# 等语言进行开发。
- 劣势：Appium 在 Android 官方的自动化框架 UiAutomator 和 iOS 官方的 UiAutomation 上进行的封装，所以效率上没有直接使用官方的框架好。
- 目前已经有了 GUI 版本，环境配置更加方便和快速。
- [官网](http://appium.io/)

## 安装

- [最新 GUI 版本下载](https://bitbucket.org/appium/appium.app/downloads/AppiumForWindows_1_4_13_1.zip)


## 使用

### 配置

![](http://i.imgur.com/dJHDKBh.png)

![](http://i.imgur.com/f5ZviwP.png)


### App 信息

- Package： com.xxx.xxx
- Launch Activity： com.xxx.xxx.ui.activity.WelcomeActivity
- Device Name:4d003d169c0c4037，可以通过 adb devices 命令查询到
- PlatformVersion:测试设备的 Android 版本号

## 脚本的编写（以 Java 为例，其他语言的 Api 类似）

### 定位
- 常用 Api 通过各种属性定位
- 在 Android 测试中，一般使用 findElementsById() 或者 findElementById() 方法就够了，假如需要定位的控件没有 id的话，可以让开发人员帮忙添加上。

- T findElementById(String id)
- T findElementByName(String using)
- T findElementByClassName(String using)
- T findElementByXPath(String using)
- List<T> findElementsById(String id)：通过 id 获取到定位
- List<T> findElementsByName(String using)：通过 name 获取到定位
- List<T> findElementsByClassName(String using)：通过类名获取到定位
- List<T> findElementsByXPath(String using)：通过 XPath 定位（定位比较准，不会存在歧义的一种定位方法，就是比较麻烦，万一客户端的布局文件进行了修改，XPath 需要重写）

#### 示例

- driver.findElement(By.xpath(".//*[@text='Add Contact']"))
- driver.findElementById("com.xxx.xxx:id/login_btn");
- driver.findElement(By.id("com.xxx.xxx:id/login_btn"));
- driver.findElementsById("com.xxx.xxx:id/login_btn").get(0)

### 点击

- void click()

### EditText 等控件输入文本

- void sendKeys(CharSequence... keysToSend)

### 滑动

- swipe(int startx, int starty, int endx, int endy, int duration)
	-  startx:开始坐标 x 
	-  starty:开始坐标 y
	-  endx:结束坐标 x
	-  endy:结束坐标 y
	-  duration:滑动持续时间

- 向左滑动，手机分辨率为1080P,即1920×1080，坐标可以写成从（900，500）滑动到（400，500）。（该写法为 Android 中的 sp ,针对兼容性问题，目前在 Android 使用的是 dp ，所以这里用 sp 的话，会在不同分辨率的机子上出现问题，因此针对不同的设备，在脚本运行前需要进行代码的检查和校验）

- Android 坐标系

	- Android 坐标系是以 x 轴的正方向和 y 轴的下方向为坐标系，即数学中的第四象限

		![](http://i.imgur.com/VtRhIYZ.png)

	

### 截图

- <X> X getScreenshotAs(OutputType<X> outputType)

### 缩放
- 缩：
	- void pinch(WebElement el)
		- webElement:一般通过 findViewBy... 方法可以获取到 WebElement 的子类
	- void pinch(int x, int y)
		- （x,y）：坐标
- 放：
	- void zoom(WebElement webElement)：缩放每个控件
		- webElement:一般通过 findViewBy... 方法可以获取到 WebElement 的子类
	- void zoom(int x, int y)：通过（x,y）坐标缩放
		- （x,y）：坐标

### 隐藏键盘

- void hideKeyboard()

### 获取和设置横竖屏

- ScreenOrientation getOrientation()：获取手机横竖屏信息
	- 返回值是一个枚举类型，通过 value() 方法可以得到手机屏幕的横竖屏信息。
	- landscape：横屏
	- portrait：竖屏

- void rotate(ScreenOrientation orientation)：设置手机横竖屏
	- orientation：枚举类型
		- ScreenOrientation.LANDSCAPE
		- ScreenOrientation.PORTRAIT

### 等待时间

- Thread.sleep(1000)：当前线程睡眠时间

- 在一定时间内等待某个元素的出现

            WebDriverWait wait = new WebDriverWait(driver, 10);
            wait.until(new ExpectedCondition<WebElement>() {
                @Override
                public WebElement apply(WebDriver d) {
                    return d.findElement(By.id("com.xxx.xxx:id/backlog_list_layout"));
                }
            });

- 等待某个元素的出现

	    private void waitForVisible(WebDriver driver, final By by, int waitTime) {
	        WebDriverWait wait = new WebDriverWait(driver, waitTime);
	        for (int i = 0; i < waitTime; i++) {
	            try {
	                driver.findElement(by);
	                break;
	            } catch (Exception e) {
	                driver.manage().timeouts().implicitlyWait(1, TimeUnit.SECONDS);
	            }
	        }
	        wait.until(ExpectedConditions.visibilityOfElementLocated(by));
	    }

### 判断某个元素是否显示在屏幕上

- boolean isDisplayed()

### 断言

- 断言，在 org.junit.Assert 包里，判断是否与预期结果一致。

- void assertNull(Object object)
- void assertNotNull(Object object)
- void assertEquals(Object expected, Object actual) 

### 应用工具类Api

- app 是否安装：boolean isAppInstalled(String bundleId)：
	- bundleId：应用的包名
	- 返回值：返回一个布尔值，true 表示已安装了该应用

- 安装 apk:void installApp(String appPath)：
	- appPath：apk路径

- 移出指定包名的app:void removeApp(String bundleId)
	- bundleId：应用的包名
- 运行app:void launchApp()
- 关闭app:void closeApp()
- app放到后台：void runAppInBackground(int seconds)
	- seconds：后台放置时间

- 执行命令：Response execute(String command)
	- command：命令
	- 返回值是一个 Resonse，可以解析 Response 来判断是否执行成功。


## 代码示例

### Java

	package com.saucelabs.appium;
	
	import io.appium.java_client.AppiumDriver;
	import io.appium.java_client.android.AndroidDriver;
	import org.junit.After;
	import org.junit.Before;
	import org.junit.Test;
	import org.openqa.selenium.By;
	import org.openqa.selenium.WebElement;
	import org.openqa.selenium.remote.DesiredCapabilities;
	
	import java.io.File;
	import java.net.URL;
	import java.util.List;
	
	import static org.junit.Assert.assertEquals;
	
	public class AndroidTest {
	
	    private AppiumDriver<WebElement> driver;
	
	    @Before
	    public void setUp() throws Exception {
	        File classpathRoot = new File(System.getProperty("user.dir"));
	        File appDir = new File(classpathRoot, "../../../apps/ApiDemos/bin");
	        File app = new File(appDir, "ApiDemos-debug.apk");
	        DesiredCapabilities capabilities = new DesiredCapabilities();
	        capabilities.setCapability("deviceName","Android Emulator");
	        capabilities.setCapability("platformVersion", "4.4");
	        capabilities.setCapability("app", app.getAbsolutePath());
	        capabilities.setCapability("appPackage", "io.appium.android.apis");
	        capabilities.setCapability("appActivity", ".ApiDemos");
	        driver = new AndroidDriver<>(new URL("http://127.0.0.1:4723/wd/hub"), capabilities);
	    }
	
	    @After
	    public void tearDown() throws Exception {
	        driver.quit();
	    }
	
	    @Test
	    public void apiDemo(){
	        WebElement el = driver.findElement(By.xpath(".//*[@text='Animation']"));
	        assertEquals("Animation", el.getText());
	        el = driver.findElementByClassName("android.widget.TextView");
	        assertEquals("API Demos", el.getText());
	        el = driver.findElement(By.xpath(".//*[@text='App']"));
	        el.click();
	        List<WebElement> els = driver.findElementsByClassName("android.widget.TextView");
	        assertEquals("Activity", els.get(2).getText());
	    }
	
	}

### Python

	import os
	from time import sleep
	
	import unittest
	
	from appium import webdriver
	
	# Returns abs path relative to this file and not cwd
	PATH = lambda p: os.path.abspath(
	    os.path.join(os.path.dirname(__file__), p)
	)
	
	class SimpleAndroidTests(unittest.TestCase):
	    def setUp(self):
	        desired_caps = {}
	        desired_caps['platformName'] = 'Android'
	        desired_caps['platformVersion'] = '4.2'
	        desired_caps['deviceName'] = 'Android Emulator'
	        desired_caps['app'] = PATH(
	            '../../../sample-code/apps/ApiDemos/bin/ApiDemos-debug.apk'
	        )
	
	        self.driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)
	
	    def tearDown(self):
	        # end the session
	        self.driver.quit()
	
	    def test_find_elements(self):
	        el = self.driver.find_element_by_accessibility_id('Graphics')
	        el.click()
	        el = self.driver.find_element_by_accessibility_id('Arcs')
	        self.assertIsNotNone(el)
	
	        self.driver.back()
	
	        el = self.driver.find_element_by_accessibility_id("App")
	        self.assertIsNotNone(el)
	
	        els = self.driver.find_elements_by_android_uiautomator("new UiSelector().clickable(true)")
	        self.assertGreaterEqual(12, len(els))
	
	        self.driver.find_element_by_android_uiautomator('text("API Demos")')
	
	
	    def test_simple_actions(self):
	        el = self.driver.find_element_by_accessibility_id('Graphics')
	        el.click()
	
	        el = self.driver.find_element_by_accessibility_id('Arcs')
	        el.click()
	
	        self.driver.find_element_by_android_uiautomator('new UiSelector().text("Graphics/Arcs")')
	
	
	if __name__ == '__main__':
	    suite = unittest.TestLoader().loadTestsFromTestCase(SimpleAndroidTests)
	    unittest.TextTestRunner(verbosity=2).run(suite)

> [GitHub示例代码](https://github.com/appium/sample-code/tree/master/sample-code/examples)

## App 示例代码

### 测试环境下，瑞信登录，进入待办1000次，并截图

	import io.appium.java_client.AppiumDriver;
	import io.appium.java_client.android.AndroidDriver;
	import io.appium.java_client.android.AndroidElement;
	import org.apache.commons.io.FileUtils;
	import org.junit.After;
	import org.junit.Assert;
	import org.junit.Before;
	import org.junit.Test;
	import org.openqa.selenium.By;
	import org.openqa.selenium.OutputType;
	import org.openqa.selenium.WebDriver;
	import org.openqa.selenium.WebElement;
	import org.openqa.selenium.remote.DesiredCapabilities;
	import org.openqa.selenium.remote.SessionId;
	import org.openqa.selenium.support.ui.ExpectedCondition;
	import org.openqa.selenium.support.ui.ExpectedConditions;
	import org.openqa.selenium.support.ui.WebDriverWait;
	
	import java.io.File;
	import java.io.IOException;
	import java.net.URL;
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import java.util.List;
	import java.util.concurrent.TimeUnit;
	
	
	public class AppTest {
	    private AppiumDriver<AndroidElement> driver;
	
	    @Before
	    public void setUp() throws Exception {
	        // set up appium
	        File classpathRoot = new File(System.getProperty("user.dir"));
	        File appDir = new File(classpathRoot, "apps");
	        File app = new File(appDir, "ruixin.apk");
	        DesiredCapabilities capabilities = new DesiredCapabilities();
	        capabilities.setCapability("deviceName", "4d003d169c0c4037");
	        capabilities.setCapability("platformVersion", "5.0.1");
	        capabilities.setCapability("app", app.getAbsolutePath());
	        capabilities.setCapability("appPackage", "com.xxx.xxx");
	        capabilities.setCapability("appActivity", ".ui.activity.WelcomeActivity");
	        driver = new AndroidDriver<>(new URL("http://127.0.0.1:4723/wd/hub"), capabilities);
	    }
	
	    @After
	    public void tearDown() throws Exception {
	        driver.removeApp("com.xxx.xxx");
	        SessionId sessionId = driver.getSessionId();
	        sessionId = null;
	        driver.quit();
	    }
	
	
	    @Test
	    public void appLogin() {
	        waitForVisible(driver, By.id("com.xxx.xxx:id/login_btn"), 10);
	        if (driver.findElementsById("com.xxx.xxx:id/login_btn").get(0).isDisplayed()) {
	            AndroidElement userName = driver.findElementsById("com.xxx.xxx:id/login_username_edit_text").get(0);
	            userName.sendKeys("lijingbo02");
	            AndroidElement password = driver.findElementsById("com.xxx.xxx:id/login_password_edit_text").get(0);
	            password.click();
	
	            driver.hideKeyboard();
	            //判断登录框是否输入了
	            Assert.assertEquals("lijingbo02@cnpc.com.cn", userName.getText());
	            //等待2秒
	            driver.manage().timeouts().implicitlyWait(2, TimeUnit.SECONDS);
	
	            password.click();
	            password.sendKeys("1");
	            //隐藏弹出的键盘
	            driver.hideKeyboard();
	            //判断输入的密码不为空
	            Assert.assertNotNull(password.getText());
	            //点击登录按钮
	            driver.findElementsById("com.xxx.xxx:id/login_btn").get(0).click();
	            //登录等待
	            driver.manage().timeouts().implicitlyWait(5, TimeUnit.SECONDS);
	            task();
	        }
	    }
	
	
	    private void task() {
	        AndroidElement androidElement = driver.findElementsById("com.xxx.xxx:id/main_tab_apps").get(0);
	        driver.zoom(androidElement);
	        waitForVisible(driver, By.id("com.xxx.xxx:id/main_tab_msgs"), 10);
	        //跳转到应用页面
	        driver.findElementsById("com.xxx.xxx:id/main_tab_apps").get(0).click();
	        //等待5秒
	        driver.manage().timeouts().implicitlyWait(5, TimeUnit.SECONDS);
	        //判断GridView是否显示
	        if (driver.findElementsById("com.xxx.xxx:id/application_grid_view").get(0).isDisplayed()) {
	            System.out.println("待办开始时间：" + getCurrentTime());
	            for (int i = 0; i < 1000; i++) {
	                AndroidElement daibanElement = driver.findElementsById("com.xxx.xxx:id/item_content_linear").get(0);
	                daibanElement.click();
	                driver.manage().timeouts().implicitlyWait(2, TimeUnit.SECONDS);
	                taskLogin(i);
	            }
	            System.out.println("待办结束时间：" + getCurrentTime());
	        }
	    }
	
	    boolean isFirst = true;
	
	    //待办登录
	//    @Test
	    public void taskLogin(int time) {
	        driver.manage().timeouts().implicitlyWait(3, TimeUnit.SECONDS);
	        if (isFirst) {
	            AndroidElement userName = driver.findElementsById("com.xxx.xxx:id/login_username_edit_text").get(0);
	            userName.sendKeys("yangjianfeng1");
	
	            driver.hideKeyboard();
	            Assert.assertEquals("yangjianfeng1", userName.getText());
	            //等待1秒
	            driver.manage().timeouts().implicitlyWait(1, TimeUnit.SECONDS);
	            AndroidElement password = driver.findElementById("com.xxx.xxx:id/login_password_edit_text");
	            if (password.getText().trim().equals("")) {
	                System.out.println("password为null");
	                password.click();
	                password.sendKeys("1");
	                //隐藏弹出的键盘
	                driver.hideKeyboard();
	            }
	            isFirst = false;
	        }
	        driver.manage().timeouts().implicitlyWait(1, TimeUnit.SECONDS);
	        //点击登录按钮
	        driver.findElementsById("com.xxx.xxx:id/login_btn").get(0).click();
	        System.out.println("第 " + time + " 次登录");
	        backTask(time);
	    }
	
	    //截图
	    public void getScreen(int time) {
	        String fileRoute = "/testing/screen/";
	        String picname = fileRoute + String.valueOf(time) + ".png";
	        File screen = driver.getScreenshotAs(OutputType.FILE);
	        System.out.println(picname);
	        File screenFile = new File(picname);
	        try {
	            FileUtils.copyFile(screen, screenFile);
	            System.out.println("第 " + time + " 截图");
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    //从待办列表返回
	    public void backTask(int time) {
	        driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	        List<AndroidElement> elementsById = driver.findElementsById("com.xxx.xxx:id/backlog_list_layout");
	        getScreen(time);
	        if (null != elementsById && elementsById.size() > 0) {
	            AndroidElement backButton = elementsById.get(0);
	            if (backButton.isDisplayed()) {
	                backButton.click();
	            }
	            //长时间操作，可能会出现无法获取到backButton的情况，做判断，list是否为null或者size为0；
	        } else if (null == elementsById || elementsById.size() == 0) {
	            //等待10秒，直到能找到backButton元素
	            getScreen(-1);
	            WebDriverWait wait = new WebDriverWait(driver, 10);
	            wait.until(new ExpectedCondition<WebElement>() {
	                @Override
	                public WebElement apply(WebDriver d) {
	                    return d.findElement(By.id("com.xxx.xxx:id/backlog_list_layout"));
	                }
	            });
	            driver.findElementsById("com.xxx.xxx:id/backlog_list_layout").get(0).click();
	
	        }
	    }
	
	    private String getCurrentTime() {
	        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd:HH-mm-ss");
	        return df.format(new Date());
	    }
	
	
	    private void waitForVisible(WebDriver driver, final By by, int waitTime) {
	        WebDriverWait wait = new WebDriverWait(driver, waitTime);
	        for (int i = 0; i < waitTime; i++) {
	            try {
	                driver.findElement(by);
	                break;
	            } catch (Exception e) {
	                driver.manage().timeouts().implicitlyWait(1, TimeUnit.SECONDS);
	            }
	        }
	        wait.until(ExpectedConditions.visibilityOfElementLocated(by));
	    }
	}