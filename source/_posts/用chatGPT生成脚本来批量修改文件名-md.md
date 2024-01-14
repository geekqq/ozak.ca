---
title: 用chatGPT生成脚本来批量修改文件名.md
date: 2024-01-14 15:06:16
tags:
    - chatGPT
    - script
---
昨天晚上在整理文件的时候，需要把一堆文件上传到synology上分享给朋友，结果发现有几个文件上传失败。原因是文件名里有"/"，这个是群晖系统不支持的。我也不知道为什么有几个文件名里会有这个符号，不过就像用chatGPT试试看能不能生成一个脚本来修改一下文件名，把文件名里的"/"替换成"-"，结果发现是可以的。chatGPT真的是帮忙了很多啊。脚本很简单。直接cd到文件目录，然后用for loop循环，找到文件名里需要替换的部分，然后用mv命令重命名就可以了。
``` Bash
for file in *.jpg; do
  new_name="${file//IMAGE /}" #这一步降文件名里的"IMAGE "删除，然后存在变量$new_name中
  new_name="${new_name//:/-}" #这一步讲文件名里的/替换为-
  mv "$file" "new_name"
done
```
然后下面还用了一个另外一个版本，现生成一个脚本文件rename_file.sh, 然后里面的命令如下：
``` Bash
#!/bin/bash
directory_path='.'
if [ "$#" -eq 1 ]; then
    directory_path="$1"
fi

cd "$directory_path" || exit

for file in *; do
    if [ -f "$file" ]; then
        new_name=$(echo "$file" | tr ':' '-')  # replace the ':' with '-'
        mv "$file" "$new_name"
        echo "Renamed: $file to $new_name"
    fi
done
echo "All files renamed successfully"
```

之后把这个脚本文件修改权限，增加x权限之后就可以运行了。

``` Bash
./rename_file.sh /path/to/your/directory
```
如果不加argument那就是默认在当前文件夹运行。
