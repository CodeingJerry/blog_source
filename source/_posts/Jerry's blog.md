---
title: Jerry
tags:
- Hello
categories:
- 初见
- jerry
---
Welcome to Jerry's blog
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` js
if (hexo-config('codeblock.copy_button.style') == 'mac') {
  .highlight-wrap {
    background: #21252b;
    border-radius: 5px;
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, .4);
    padding-top: 30px;

    &::before {
      background: #fc625d;
      border-radius: 50%;
      box-shadow: 20px 0 #fdbc40, 40px 0 #35cd4b;
      content: ' ';
      height: 12px;
      left: 12px;
      margin-top: -20px;
      position: absolute;
      width: 12px;
    }
  }
}
```
<img src="/images/duck.jpg" width=50% height=50% align=center/>
``` python
def start_chrome():
    driver = webdriver.Chrome(executable_path='./chromedriver')
    driver.start_client()
    return driver


# 定位到关注者列表
def find_strangers():
    list_sel = 'div.List-item'
    elems = driver.find_elements_by_css_selector(list_sel)
    return elems
```
<!-- more -->
More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)