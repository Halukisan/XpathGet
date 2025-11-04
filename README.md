# XpathGet
包含自研正文定位算法，可实现定位页面正文内容，并将页面内容清理无关标签后转化为MD格式。HTML转MD

1. 创建虚拟环境，按照requirement要求安装依赖（可能写的不全，自己看一下，beautifulsoup和....某个忘记写了）
2. 运行zGetContentByXpath.py文件`python zGetContentByXpath.py api`
3. 然后按照以下代码进行解析

```python
import requests

with open("htmlcontent.html", 'r', encoding='utf-8') as f:
    html_content = f.read()

response = requests.post(
    "http://localhost:8000/extract",
    json={"html_content": html_content}
)

print("Status Code:", response.status_code)
print("Response Text:", response.text)

if response.status_code == 200:
    try:
        result = response.json()
        markdown_content = result.get("markdown_content", response.text)  
    except ValueError:
        markdown_content = response.text

    with open("htmlcontent.md", 'w', encoding='utf-8') as f:
        f.write(markdown_content)
    print("Markdown content saved to htmlcontent.md")
else:
    print("Request failed!")
```
4. 对于服务端的部署：你需要上传wwebdriver_pool.py文件和zGetContentByXpath.py和requirement_updated.txt三个文件，记得创建虚拟环境
5. 使用` nohup gunicorn -k uvicorn.workers.UvicornWorker zGetContentByXpath:app --bind 0.0.0.0:8000 --workers 4 > gunicorn.log 2>&1 &`启动api服务
6. 使用`ps aux | grep gunicorn`查看运行情况
