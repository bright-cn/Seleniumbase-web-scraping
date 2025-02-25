# 使用 SeleniumBase 进行网页抓取

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com)

使用 SeleniumBase 的高级功能与分步指南来简化网页抓取。对 Selenium 网页抓取感兴趣？可查看此指南：[使用 Selenium 进行网络爬虫指南](https://www.bright.cn/blog/how-tos/using-selenium-for-web-scraping)。

## 什么是 SeleniumBase？

SeleniumBase 是一个基于 Selenium/WebDriver API 的 Python 浏览器自动化框架，除了常规测试，它也可用于网页抓取，并且内置了例如跳过验证码、规避机器人检测等高级功能。

## SeleniumBase 与 Selenium：功能及 API 对比

| 功能                      | SeleniumBase                                   | Selenium                                 |
|---------------------------|------------------------------------------------|------------------------------------------|
| 内置测试运行器           | 与 pytest、pynose、behave 无缝集成             | 需要手动设置测试运行环境                  |
| 驱动自动管理             | 自动下载并匹配浏览器驱动                       | 需要手动下载并配置                         |
| 网页自动化逻辑           | 将多步操作合并为单个方法调用                   | 需要多行代码才能实现                      |
| 选择器处理               | 自动识别 CSS 或 XPath 选择器                   | 需要显式指定选择器类型                    |
| 超时处理                 | 默认超时避免脚本直接报错                       | 未显式设置超时时会立即报错                |
| 错误输出                 | 简洁且可读的错误信息                           | 较为冗长且不易读的错误日志                |
| 仪表盘与报告             | 内置仪表盘、报告及截图功能                     | 无内置的仪表盘和报告功能                  |
| 桌面图形界面工具         | 提供可视化的测试运行器                         | 缺乏桌面图形界面工具                      |
| 测试录制工具             | 内置测试录制功能                               | 需要手动编写脚本                          |
| 测试用例管理             | 提供 CasePlans 来管理测试用例                  | 无内置的测试用例管理功能                  |
| 数据应用支持             | 附带 ChartMaker 功能来创建数据应用             | 不支持额外数据应用                        |

## 使用 SeleniumBase 进行网页抓取：分步指南

### 步骤 #1：项目初始化

```bash
mkdir seleniumbase-scraper
cd seleniumbase-scraper
python -m venv env
```

激活虚拟环境：

- 在 Linux/macOS 上: `./env/bin/activate`
- 在 Windows 上: `env/Scripts/activate`

安装 SeleniumBase：

```bash
pip install seleniumbase
```

### 步骤 #2：SeleniumBase 测试环境配置

```python
from seleniumbase import SB

with SB() as sb:
    pass
```

运行脚本：

```bash
python3 scraper.py --headless
```

### 步骤 #3：连接至目标页面

```python
sb.open("https://quotes.toscrape.com/")
```

### 步骤 #4：选取包含名言的元素

```python
quote_elements = sb.find_elements(".quote")
```

### 步骤 #5：抓取名言数据

```python
from selenium.webdriver.common.by import By

for quote_element in quote_elements:
    text_element = quote_element.find_element(By.CSS_SELECTOR, ".text")
    text = text_element.text.replace("“", "").replace("”", "")
    author_element = quote_element.find_element(By.CSS_SELECTOR, ".author")
    author = author_element.text
    tags = [tag.text for tag in quote_element.find_elements(By.CSS_SELECTOR, ".tag")]
```

### 步骤 #6：将名言存入数组

```python
quotes.append({"text": text, "author": author, "tags": tags})
```

### 步骤 #7：实现翻页逻辑

```python
while sb.is_element_present(".next"):
    sb.click(".next a")
```

### 步骤 #8：导出抓取的数据

```python
import csv

with open("quotes.csv", mode="w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=["text", "author", "tags"])
    writer.writeheader()
    for quote in quotes:
        writer.writerow({"text": quote["text"], "author": quote["author"], "tags": ";".join(quote["tags"])})
```

### 步骤 #9：整合所有步骤

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

运行爬虫：

```bash
python3 script.py --headless
```

## SeleniumBase 高级抓取用例

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

### 绕过简单的反爬虫检测

```python
from seleniumbase import SB

with SB(uc=True) as sb:
    url = "https://www.scrapingcourse.com/antibot-challenge"
    sb.uc_open_with_reconnect(url, reconnect_time=4)
    sb.uc_gui_click_captcha()
    sb.save_screenshot("screenshot.png")
```

### 绕过复杂的反爬虫检测

```python
from seleniumbase import SB

with SB(uc=True, test=True) as sb:
    url = "https://gitlab.com/users/sign_in"
    sb.activate_cdp_mode(url)
    sb.uc_gui_click_captcha()
    sb.sleep(2)
    sb.save_screenshot("screenshot.png")
```

## 结论

SeleniumBase 提供了许多适用于网页抓取的高级功能，包括 UC 模式和 CDP 模式等，用于绕过反爬虫检测。如果需要更全面坚固的解决方案，建议考虑使用类似 [Bright Data 的 Scraping Browser](https://www.bright.cn/products/scraping-browser) 这样的云端浏览器。
