# Windows下Git Bash 中文乱码问题

## git bash 不能输入中文

解决方法:

+ 打开git bash, 右键 -> Options
+ 在Options中选择Text项, Locale -> zh_CH, Character set -> UTF-8
+ 重启git bash即可

## git bash 中文显示乱码

解决方法:

```bash
git config --global core.quotepath false
```

