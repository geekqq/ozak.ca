---
title: A python script to download Bing wallpaper
date: 2023-12-21 21:14:44
tags:
    - script
    - python
    - coding
---

今天想起来以前的一个脚本，用来下载当天的 bing 的壁纸，还都挺好看的，但是忘记怎么写了。就问了 chatGPT 给出了正解。已经成功下载了两天的壁纸。



``` python
import requests
import os
from datetime import datetime

def fetch_bing_wallpaper():
    # Bing Wallpaper API URL
    api_url = "https://www.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1&mkt=en-US"
    
    # Send a GET request to the API
    response = requests.get(api_url)
    
    save_directory = "/Users/geekqq/Pictures/bing-wallpapers/"    

    # Check if the request was successful (status code 200)
    if response.status_code == 200:
        # Parse the JSON response
        data = response.json()

        # Get the image URL
        image_url = f"https://www.bing.com{data['images'][0]['url']}"

        # Get the current date for naming the file
        current_date = datetime.now().strftime("%Y%m%d")

        # Download the image
        image_response = requests.get(image_url)
        if image_response.status_code == 200:
            # Save the image to a local file
            with open(os.path.join(save_directory, f"bing_wallpaper_{current_date}.jpg"), "wb") as file:
                file.write(image_response.content)
            print("Bing Wallpaper downloaded successfully.")
        else:
            print("Failed to download Bing Wallpaper.")
    else:
        print(f"Failed to fetch Bing Wallpaper. Status Code: {response.status_code}")

if __name__ == "__main__":
    fetch_bing_wallpaper()
```
如果要修改壁纸保存的地址，可以自行修改脚本中的 save_directory 的变量为自己的保存路径。
保存之后修改脚本的权限就可以使用了：
``` shell
sudo chmod +x download_bing_wallpaper.py
python download_bing_wallpaper.py
```

我还建立了一个 cronjob 让脚本每天自动下载壁纸：
``` shell
0 0 * * * python /Users/geekqq/shell/download_bing_wallpaper.py
```
对了，建立 cronjob 也很简单，用这个命令就可以打开编辑器来修改或者增加 cronjob 了：
``` shell
sudo crontab -e
```
