# Hugo_issus



# 2024/10 换电脑 更新hugo新版本后
``` shell
hugo --environment _stack server -D
ERROR Error: external resource 'Vibrant' is not found
ERROR Error: external resource 'PhotoSwipe' is not found
ERROR Error: external resource 'KaTeX' is not found
```
+ 问题是新版本Hugo和旧版本主题不兼容 升级之后解决

``` shell
git pull && git submodule update --init --recursive
```

