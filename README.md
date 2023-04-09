# 个人博客搭建和部署

kangxiatao.github.io[Hexo + GitHub Pages](https://www.kangxiatao.github.io/2021/06/28/22/clg99w9ta000074ikgbiv47n9/)

kangxiatao.love[Hexo + GitHub Pages](https://www.kangxiatao.love/2021/06/28/22/clg99w9ta000074ikgbiv47n9/)

[借助vercel部署](https://vercel.com/)

传源码做个备份吧

## 百度和谷歌收录问题

Github把百度墙了，可用vercel+自己的域名

Hexo的icarus主题网站验证添加在```blog\themes\icarus\layout\common\head.jsx```中

## 分支问题

在repo的链接后面指定分支即可

```
deploy:
  type: git
  branch: main
  repo: 
    github: git@github.com:kangxiatao/kangxiatao.github.io.git,main
    # gitee: https://gitee.com/kangxiatao/kangxiatao.git
```

## 吐槽

gitee pages 总是审核不给过

vercel的域名被墙了，需要自己的域名