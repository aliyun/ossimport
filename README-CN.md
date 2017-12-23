# OssImport

## 关于
OssImport可以将本地、其它云存储的数据迁移到OSS，它有以下特点：
- 支持的丰富的数据源，包括本地、七牛、百度BOS、AWS S3、Azure Blob、又拍云、腾讯云COS、金山KS3、HTTP、OSS等，并可根据需求扩展；
- 支持断点续传；
- 支持流量控制；
- 支持迁移指定时间后的文件、特定前缀的文件；
- 支持并行数据下载、上传；
- 支持单机模式和分布式模式，单机模式部署简单使用方便，分布式模式适合大规模数据迁移。

## 运行环境
- Java 1.7及以上。

## 部署方式

按照部署方式，OssImport可以分为*单机模式*和*分布式模式*，对于小于 ***1TB*** 的小规模数据数据迁移，单机模式即可完成。对于大规模的数据迁移，请使用分布式模式。

## 基本概念
- Job：数据迁移任务，对用户来说一个任务对应一个配置文件`local_job.cfg`。
- Task：数据迁移任务的子任务，任务按照 ***数据大小*** 和 ***文件个数*** 可以分成多个子任务*Task*，每个*Task*迁移部分文件。

## 文件目录
单机模式的文件结构如下：
```              
ossimport
├── bin
│   └── ossimport2.jar  # 包括Master、Worker、TaskTracker、Console四个模块的总jar
├── conf
│   ├── local_job.cfg   # Job配置文件
│   └── sys.properties  # 系统运行参数配置文件
├── console.bat         # Windows命令行，可以分布执行调入任务
├── console.sh          # Linux命令行，可以分布执行调入任务
├── import.bat          # Windows一键导入，执行配置文件为conf/local_job.cfg配置的数据迁移任务，包括启动、迁移、校验、重试
├── import.sh           # Linux一键导入，执行配置文件为conf/local_job.cfg配置的数据迁移任务，包括启动、迁移、校验、重试
├── logs                # 日志目录
└── README.md           # 说明文档，强烈建议使用前仔细阅读
```

**注意：**
- 配置文件 `conf/sys.properties` 和 `conf/local_job.cfg`，请根据实际参数配置；
- `README.md`说明文档，使用前请仔细阅读；
- ***\*.bat*** 为Windows下的命令， ***\*.sh*** 为Linux下的命令；
- *一键导入* 和 *Console分步执行* 请选择一种；
- 执行命令时请保证工作目录为 `import.bat/import.sh` 的同级目录，即直接执行 *import.bat* 或 *bash import.sh*。

## 运行服务

单机模式下，数据迁移任务有两种执行方式 *一键导入* 和 *分步执行*。*一键导入*，是对所有步骤的封装，按照脚本提示执行即可完成数据迁移。*分步执行*，执行启动服务、提交任务、重试失败子任务等步骤。**对于初级用户强烈建议使用一键导入**。

单机模式的配置文件是`conf/local_job.cfg`，数据迁移前请安装实际需求修改任务参数。默认任务名称为`local_test`，请不要修改。

### 一键导入
- 执行一键导入，Window在*cmd.exe*中执行 `import.bat`，Linux终端中执行 `bash import.sh`；
- 如果之前执行过程序，会提示有是否从上次的断点处继续执行，或者重新执行同步任务。对新的数据迁移任务，或者修改了同步的源端/目的端，请选择重新执行；
- Windows下任务开始后，会打开一个新的cmd窗口执行同步任务并显示日志，旧窗口会每隔10秒打一次任务状态，数据迁移期间不要关闭两个窗口；Linux下服务在后台执行；
- 当*Job*完成后，如果发现有任务失败了，会提示是否重试，输入 ***y*** 重试，输入 ***n*** 则跳过退出；
- 上传失败的原因请打开文件`master/jobs/local_test/failed_tasks/<tasktaskid>/audit.log`，请查看，确定失败原因。

### 分步执行
- 清除同名任务。如果以前运行过同名任务，需要从新执行任务，请先清除同名任务。如果没有运行过，或需要重试失败任务，不要执行清除命令。Window下在*cmd.exe*中执行 `console.bat clean`，Linux下在终端执行 `bash console.sh clean`。
- 提交数据迁移任务。OssImport不能提交同名任务，如果有请先清除。提交任务的配置文件为`conf/local_job.cfg`，默认任务名称为`local_test`。提交任务的命令，Window下在*cmd.exe*中执行 `console.bat submit`，Linux下在终端执行 `bash console.sh submit`。
- 启动服务。Windows下在*cmd.exe*中执行 `console.bat start`，Linux下在终端执行 `bash console.sh start`。
- 失败Task重试。由于网络或其它原因，子任务可能失败。失败重试只重试失败的Task，不会重试成功的Task。Windows下在*cmd.exe*中执行 `console.bat retry`，Linux下在终端执行 `bash console.sh retry`。
- 停止服务。Windows下在关闭窗口 `%JAVA_HOME%/bin/java.exe` 即可，Linux下在终端执行 `bash console.sh stop`。

**注意**：
- 没有特殊需要，请使用 ***一键导入*** 方式迁移数据。

## 常见任务失败原因
- 上传过程中源目录的文件发生了修改，`log/audit.log`里会提示`SIZE_NOT_MATCH`相关字样的错误，这种情况下老的文件已经上传成功，新的修改没有上传到OSS；
- 源文件在上传过程中被删除，导致下载的时候失败；
- 源文件名不符合OSS命名规范（不能以/开头，不能为空），导致上传到OSS失败；
- 下载数据源文件失败；
- 程序异常退出，任务状态为*Abort*，这种情况请联系我们。

## 建议
对于从云端（非本地）迁移到oss，且带宽资源不是很充足的用户，建议够买按量付费的ECS进行迁移(购买地址:https://ecs-buy.aliyun.com/#/postpay)。ECS配置：
- 付费方式选择按量付费；
- 地域选择OSS对应的区域；
- 带宽峰值选100M；
- 系统Linux/Windows随意；
- 其他全部选择最低配置。

在配置迁移服务时，将 *targetDomain* 设为带internal的内网域名；如果源端也是OSS，将 *srcDomain* 也设为带internal的内网域名，可以省掉从OSS源端下载的流量费，仅收取OSS访问次数的费用。
