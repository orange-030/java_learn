# 基本操作 
## 打开浏览器
 ```python
    <from selenium import webdriver
    import time
    driver = webdriver.Chrome()   # 创建实例
    driver.maximize_window()  # 浏览器窗口最大化 
    driver.get("http://www.baidu.com") # 请求百度首页
    time.sleep(6) # 睡眠六秒
    driver.quit() # 退出
    browser = webdriver.Chrome()
    browser.get("https://3416230579.github.io/page/index.html")
```
## 常用 
> 利用 xpath 获取 id='xpathname' 标签对象并自动填值
```python
    elemt = browser.find_element_by_xpath(r'//*[@id="xpathname"]')
    elemt.send_keys("我的 xpath")
> 操作一（根据网页的id和name属性寻找）

    elemt = browser.find_element_by_id("element_id") #根据 id 获取对象
    elemt = browser.find_element_by_name("element_id") #根据 name 获取对象

    print(elemt.tag_name) #返回标签名
    print(elemt.text) #返回标签的值

    elemt.send_keys("哈哈哈")#给标签输入值
> 操作二

    elemt = browser.find_element_by_link_text("find_element_by_link_text")
    print(elemt.tag_name) #返回标签名
    print(elemt.text) #返回标签的值
    elemt.click()#点击
> 操作三
利用 css选择器 获取 class='highlight' 标签对象并自动填值
    elemt = browser.find_element_by_css_selector(".highlight")
    elemt.send_keys("啦啦啦")

> 操作四
获取跳转后页面的源码
    time.sleep(2)
    elemt = browser.find_element_by_link_text("find_element_by_link_text")
    elemt.click()
    browser.switch_to_window(browser.window_handles[1])
    print(browser.page_source)

> 操作五 操作弹出框
    time.sleep(2)
    elem = browser.find_element_by_tag_name("button")
    elem.click()

    time.sleep(2)
    browser.switch_to_alert().accept()  # 切换到弹出框操作

> 操作六  跳转和回退操作
    time.sleep(2)
    elem = browser.find_element_by_link_text("forward_back")
    elem.click()  # 点击跳转
    time.sleep(1)
    browser.back()  # 点击回退  
    time.sleep(2)
    browser.forward()  # 调到上一次点击
    time.sleep(1)
    browser.back()  # 回退

> 操作七 Cookies 的操作
    browser = webdriver.Chrome()
    browser.get("https://www.baidu.com")
    print(browser.get_cookies()) # 输出全部的 cookie 的信息
    添加一个 cookie 
    browser.add_cookie({"name":"luchangyin", "domian":"www.baidu.com","value":"肥牛冲天"})
    print(browser.get_cookies())
    browser.delete_all_cookies()  # 全部删除
    print(browser.get_cookies())

> 操作八 自动打开百度并根据关键字搜索相关的内容
    from selenium.webdriver.common.keys import Keys
    browser = webdriver.Chrome()
    browser.get("https://www.baidu.com")
    elem = browser.find_element_by_id("kw")
    elem.send_keys("python爬虫")  # 输入
    time.sleep(2)  # 休眠
    elem.send_keys(Keys.RETURN)  # 回车
    time.sleep(3)
    browser.quit() # 关闭







