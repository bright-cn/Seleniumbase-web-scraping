# 使用 SeleniumBase 进行网页爬虫

[![推广](https://github.com/bright-cn/Awesome-Web-Scraping/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner%20CN.png)](https://www.bright.cn/)

利用 SeleniumBase 的高级功能和分步指南，简化网页爬虫流程。对 Selenium 网页爬虫感兴趣？查看[本指南](https://www.bright.cn/blog/how-tos/using-selenium-for-web-scraping)。

## 什么是 SeleniumBase？

SeleniumBase 是一个基于 Selenium/WebDriver API 构建的 Python 浏览器自动化框架。它支持从测试到数据抓取等任务，并提供绕过 CAPTCHA 和规避机器人检测等功能。

## SeleniumBase 与 Selenium：功能和 API 对比

| 功能                  | SeleniumBase                                      | Selenium                                    |
|--------------------------|---------------------------------------------------|---------------------------------------------|
| 内置测试运行器    | 与 pytest、pynose 和 behave 集成        | 测试集成需要手动设置  |
| 驱动管理        | 自动下载匹配的浏览器驱动            | 需要手动下载和配置           |
| 网页自动化逻辑     | 将多个步骤合并为一次方法调用            | 需要编写多行代码             |
| 选择器处理        | 自动检测 CSS 或 XPath 选择器               | 需要明确指定选择器类型            |
| 超时处理         | 提供默认超时设置以防止失败              | 未明确设置超时时会立即失败|
| 错误输出            | 简洁、易读的错误消息                    | 冗长且较难解读的错误日志      |
| 控制面板和报告   | 内置控制面板、报告和截图功能     | 没有内置控制面板或报告功能         |
| 桌面 GUI 应用程序 | 用于运行测试的可视化工具                     | 缺少桌面 GUI 工具                     |
| 测试录制器            | 内置测试录制器                            | 需要手动编写脚本              |
| 测试用例管理     | 提供 CasePlans                                | 没有内置测试用例管理功能            |
| 数据应用支持         | 包含用于数据应用的 ChartMaker                 | 没有用于数据应用的其他工具           |

## 使用 SeleniumBase 进行网页爬虫：分步指南

### 第 1 步：初始化项目

```bash
mkdir seleniumbase-scraper
cd seleniumbase-scraper
python -m venv env
```

激活虚拟环境：

- 在 Linux/macOS 上：`./env/bin/activate`
- 在 Windows 上：`env/Scripts/activate`

安装 SeleniumBase：

```bash
pip install seleniumbase
```

### 第 2 步：设置 SeleniumBase 测试

```python
from seleniumbase import SB

with SB() as sb:
    pass
```

运行脚本：

```bash
python3 scraper.py --headless
```

### 第 3 步：连接到目标页面

```python
sb.open("https://quotes.toscrape.com/")
```

### 第 4 步：选择引语元素

```python
quote_elements = sb.find_elements(".quote")
```

### 第 5 步：抓取引语数据

```python
from selenium.webdriver.common.by import By

for quote_element in quote_elements:
    text_element = quote_element.find_element(By.CSS_SELECTOR, ".text")
    text = text_element.text.replace("“", "").replace("”", "")
    author_element = quote_element.find_element(By.CSS_SELECTOR, ".author")
    author = author_element.text
    tags = [tag.text for tag in quote_element.find_elements(By.CSS_SELECTOR, ".tag")]
```

### 第 6 步：填充引语数组

```python
quotes.append({"text": text, "author": author, "tags": tags})
```

### 第 7 步：实现爬虫逻辑

```python
while sb.is_element_present(".next"):
    sb.click(".next a")
```

### 第 8 步：导出抓取的数据

```python
import csv

with open("quotes.csv", mode="w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=["text", "author", "tags"])
    writer.writeheader()
    for quote in quotes:
        writer.writerow({"text": quote["text"], "author": quote["author"], "tags": ";".join(quote["tags"])})
```

### 第 9 步：整合所有代码

```python
from seleniumbase import SB
from selenium.webdriver.common.by import By
import csv

with SB() as sb:
    sb.open("https://quotes.toscrape.com/")
    quotes = []
    while sb.is_element_present(".next"):
        quote_elements = sb.find_elements(".quote")
        for quote_element in quote_elements:
            text_element = quote_element.find_element(By.CSS_SELECTOR, ".text")
            text = text_element.text.replace("“", "").replace("”", "")
            author_element = quote_element.find_element(By.CSS_SELECTOR, ".author")
            author = author_element.text
            tags = [tag.text for tag in quote_element.find_elements(By.CSS_SELECTOR, ".tag")]
            quotes.append({"text": text, "author": author, "tags": tags})
        sb.click(".next a")
    with open("quotes.csv", mode="w", newline="", encoding="utf-8") as file:
        writer = csv.DictWriter(file, fieldnames=["text", "author", "tags"])
        writer.writeheader()
        for quote in quotes:
            writer.writerow({"text": quote["text"], "author": quote["author"], "tags": ";".join(quote["tags"])})
```

运行抓取工具：

```bash
python3 script.py --headless
```

## SeleniumBase 网页爬虫的高级用例

### 自动填写并提交表单

```python
from seleniumbase import BaseCase
BaseCase.main(__name__, __file__)

class LoginTest(BaseCase):
    def test_submit_login_form(self):
        self.open("https://quotes.toscrape.com/login")
        self.type("#username", "test")
        self.type("#password", "test")
        self.click("input[type=\"submit\"]")
        self.assert_text("Top Ten tags")
```

运行测试：

```bash
pytest login.py
```

### 绕过简单的反机器人技术

```python
from seleniumbase import SB

with SB(uc=True) as sb:
    url = "https://www.scrapingcourse.com/antibot-challenge"
    sb.uc_open_with_reconnect(url, reconnect_time=4)
    sb.uc_gui_click_captcha()
    sb.save_screenshot("screenshot.png")
```

### 绕过复杂的反机器人技术

```python
from seleniumbase import SB

with SB(uc=True, test=True) as sb:
    url = "https://gitlab.com/users/sign_in"
    sb.activate_cdp_mode(url)
    sb.uc_gui_click_captcha()
    sb.sleep(2)
    sb.save_screenshot("screenshot.png")
```

## 总结

SeleniumBase 提供 UC 模式和 CDP 模式等高级网页抓取功能，可用于绕过反机器人措施。如需更强大的解决方案，可以考虑使用基于云的浏览器，例如 [Bright Data 的抓取浏览器](https://www.bright.cn/products/scraping-browser)。
