# OssImport ChangeLog

## 版本号：2.3.1 日期：2017-12-20
### 变更内容
- 修复：配置项isAuditAfterImport不生效的问题

## 版本号：2.3.0 日期：2017-12-15
### 变更内容
- 增加：支持使用高速通道迁移数据
- 修复：集团开发规约格式化代码

## 版本号：2.2.4 日期：2017-10-23
### 变更内容
- 增加：版本化管理，支持version命令，方便问题排除，详见[#OSS-2793](https://workitem.aone.alibaba-inc.com/project/240655/issue/12720334)
- 修复：进程内存控制，防止占用过大内存被杀，详见[#OSS-2794](https://workitem.aone.alibaba-inc.com/project/111257/issue/12720608)
- 修复：本地大文件上传优化，从单线程上传优化成多线程并发上传
- 修复：通过System.getProperty配置项变成在配置文件中配置，详见[#OSS-2756](https://workitem.aone.alibaba-inc.com/issue/12427969?stat=1.5.10&toPage=1&projectId=40654008)
- 修复：把异常信息记录到日志

## 版本号：2.2.3 日期：2017-10-10
### 变更内容
- 增加：又拍云支持从CDN下载文件
- 增加：支持配置文件设置网络超时时间
- 修复：又拍createSingleThreadTask报NPE，详见[#OSS-27870](https://aone.alibaba-inc.com/issue/12671326)
- 修复：dispatcherThreadNum的默认值修改为1
- 修复：Linux单机版在非工作目录下启动无法正常运行

## 版本号：2.2.2 日期：2017-09-11
### 变更内容
- 修复：配置文件动态加载
- 修复：重试级别优化，从Task到File
- 修复：Job配置文件支持isSkipExistFile

## 版本号：2.2.1 日期：2017-07-29
### 变更内容
- 修复：http等迁移数据时流控无效
- 修复：Windows脚本改成英文
- 增加：README的内容

## 版本号：2.2.0 日期：2017-07-12
### 变更内容。
- 增加：支持Azure Blob迁移到OSS
- 增加：单机版本，增加Linux命令行console.sh、一键执行脚本import.sh
- 增加：单机版本，增加Windows命令行console.sh、一键执行脚本import.bat，去除其它bat
- 增加：分布式版本，增加Linux命令行console.sh
- 修复：命名优化，slave->worker, dispatcher->tasktracker, 命名空间ossimport2->ossimport, Jar名称变更
- 修复：awk在Ubuntu的目录是/usr/bin/awk而不是/bin/awk，awk等命令去除目录
