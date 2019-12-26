---
layout: post
title: goweb项目搭建
no-post-nav: true
category: others
tags: [go]
excerpt: go web
---

## 1. bee和beego安装

- 设置proxy（科学上网）加快下载速度

  ```
  export GOPROXY=https://goproxy.io
  ```

  

- 执行go get -u github.com/astaxie/beego和go get -u   github.com/beego/bee

- 如果出现 一下问题

  ​	go: github.com/derekparker/delve@v1.3.2: parsing go.mod: unexpected module  	path "github.com/go-delve/delve"

  ​	设置export GO111MODULE=off，下载安装完毕后，重新设置成 

  ​    export GO111MODULE=on	

  ## 2. 集成swagger生成aip项目

  - 执行 bee api projectname

  - bee run -gendoc=true -downdoc=true

  - 默认访问http://localhost:8080/swagger来查看api信息

  - 如果出现 一下问题 bee generate docs 报错 Failed to generate the docs

    1. 不使用 GO Modules 管理项目。

    2. 打个补丁

       找到analyseControllerPkg`（`bee/generate/swaggergen/g_docs.go::

       analyseControllerPkg`) ，加上

       ```go
       if pkgRealpath == "" {
               for _, wg := range gopaths {
                   wg, _ = filepath.EvalSymlinks(filepath.Join(wg, pkgpath))
                   if utils.FileExists(wg) {
                       pkgRealpath = wg
                       break
                   }
               }
           }
       ```

       修改后的文件

       ```go
           pkgRealpath := ""
       
           wg, _ := filepath.EvalSymlinks(filepath.Join(vendorPath, pkgpath))
           if utils.FileExists(wg) {
               pkgRealpath = wg
           } else {
               wgopath := gopaths
               for _, wg := range wgopath {
                   wg, _ = filepath.EvalSymlinks(filepath.Join(wg, "src", pkgpath))
                   if utils.FileExists(wg) {
                       pkgRealpath = wg
                       break
                   }
               }
           }
           if pkgRealpath == "" {
               for _, wg := range gopaths {
                   wg, _ = filepath.EvalSymlinks(filepath.Join(wg, pkgpath))
                   if utils.FileExists(wg) {
                       pkgRealpath = wg
                       break
                   }
               }
           }
           if pkgRealpath != "" {
               if _, ok := pkgCache[pkgpath]; ok {
                   return
               }
               pkgCache[pkgpath] = struct{}{}
           } else {
               beeLogger.Log.Fatalf("Package '%s' does not exist in the GOPATH or vendor path", pkgpath)
           }
       ```