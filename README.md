# OssImport

## About this tool
OssImport is a tool to migrate local data or other cloud storage data to OSS. Here're the major features:
- Most common data sources, including local data, Qiniu, Baidu BOS, AWS S3, Azure Blob, Upyun , Tecent COS, Kingsoft KS3, HTTP, OSS, etc.
- Support checkpoint to resume from a failed data migration.
- Traffic throughput throttling.
- Flexible data filtering, based on modified time, file prefix, etc.
- Parallel upload and download. Configurable parallelism.
- Scalable. It supports both single machine mode and distributed mode.

## Runtime Environment
- Java 1.7 or higher

## Deployment

OssImport has two deployment modes: *Standalone* and *Distributed*. For small size data (< 30TB), Standalone is good enough. While for large data migration, distribured mode is recommended.

## Basic Concept
- Job：The whole data migration work. In distributed mode, a job runs on all machines. A job has its own configuration file 'local_job.cfg'.
- Task：A sub-job of the data migration job. Depends on data size and file counts, one task could have multiple sub tasks. Each task is responsible for a part of the files.

## Directory structure
Standalone:
````````            
ossimport
├── bin
│   └── ossimport2.jar  # Includes Master、Worker、TaskTracker、Console model's code in one jar
├── conf
│   ├── local_job.cfg   # Job configuration file
│   └── sys.properties  # System run-time properties
├── console.bat         # Windows bat file for advanced usage. It has different switches to support run steps one by one.
├── console.sh          # Linux bash file for advanced usage. It has different switches to support run steps one by one.
├── import.bat          # Windows all-in-one cmd. It run the whole job according to conf/local_job.cfg，including start, transfer, audit, and retry.
├── import.sh           # Linux all-in-one cmd.It run the whole job according to conf/local_job.cfg，including start, transfer, audit, and retry.
├── logs                # Logs
└── README.md           # Read me file. Strongly recommend to read before starting.
````````

**Notes：**
- Two configuration files need to change: 'conf/sys.properties' and 'conf/local_job.cfg';
- Read 'README.md' before start.
- ***\*.bat*** is for windows， ***\*.sh*** is for Linux/Mac.
- Choose either *all-in-one* cmd or *Console* with different steps----the later is more flexible and could allow you to resume the work from a specific step.
- Make sure run the tool under the current folder of 'import.bat/import.sh'. That is you should type *import.bat* or *bash import.sh* to run the tool.

## Run steps
In standalone mode，there're two ways to run the job: all-in-one cmd (import.bat or import.sh) or step by step with Console.bat/Console.sh. All-in-one cmd is the wrapper of all steps. Basically it autmatically run all the steps, including start service, submit job, retry failed tasks, etc. **It's highly recommded, especially for beginner users**.

The config for standalone mode is 'conf/local_job.cfg'. Make sure to specify the actual data source and target parameters before the start. The default job name is 'local_test', please do not change it.

### All-in-one cmd
- For Windows users, run `import.bat` in cmd.exe; For Linux/Mac users, run `import.sh` in bash/terminal.
- If the tool has been run on the machine before, it would prompt to resume the previous job. Type 'Yes' to resume or 'No' to start a new job. If the config is changed, please make sure to start a new job.
- In Windows, after the cmd runs, it will start a new cmd window to run the job and show the logs. The original window will refresh the job status at every 10s. Please don't close these two cmd.exe. 
- In Linux/Mac, there's no second terminal window as the job runs in backend.
- After the job is finished, if there're failed tasks，it will prompt to ask for retry. Type `Y` to retry or `N` to skip and exit.
- For troubleshooting on failed tasks, please check out `master/jobs/local_test/failed_tasks/<tasktaskid>/audit.log`.

### Step by step with Console.sh/Console.bat
- Clear the previous job with same name, if you don't want to resume from it. To clean the previous job, run `console.bat clean` for Windows and `bash console.sh clean` for Linux/Mac.
- Submit job. OssImport cannot start a new job with same name of previous job. So make sure do the clear step first.The config file for job submit is `conf/local_job.cfg`，and the default job name is `local_test`. To submit job, run `console.bat submit` for Windows and `bash console.sh submit` for Linux/Mac.
- Start service. Run `console.bat start` for Windows and `bash console.sh start` for Linux/Mac.
- Retry failed tasks. Tasks could fail due to network or other reasons. To retry these **failed tasks**, just run 'console.bat retry' for Windows and 'bash console.sh retry' for Linux/Mac. Successful tasks would **not** re-run during the retry.
- Stop Service. For Windows, just close Window `%JAVA_HOME%/bin/java.exe`. For Linux/Mac, run `bash console.sh stop`.

**Warnings**：
- Again, please use **import.bat** or **import.sh** to migrate the data whenever possible.

## Common failure reasons
- The source file is updated during its upload, In this case, you may see 'SIZE_NOT_MATCH' in 'log/audit.log'. When it happens, old file has been uploaded, but the updated one is not.
- The source file is deleted during its upload.
- The source file's name does not follow the OSS naming rules (such as cannot start with '/' and not empty).
- Download source file failed due to permission issue or network issue(e.g timeout).
- Application crashes. When it happens, the task status is *Abort*. Please contact us. Thanks.

## Suggestions
For data migration from other cloud, and if you don't have high speed bandwith to access that data,we recommend to use ECS to run the tool.
You could onboard ECS from https://ecs-buy.aliyun.com/#/postpay. And we have following suggetions about how to use ECS:
- Pay with on-demand option.
- Use ECS machines with the same region of the target OSS.
- 100M bandwidth (or more).
- Both Linux and Windows are ok.
- For other configuration (such as CPU, memory, disk, etc), basic model is ok.
- Use internal domain name for the **"targetDomain"** parameter. If the source data comes from OSS of the same region, then **"srcDOmain"** should be internal domain name as well. Using internal domain is not only faster, but also cheaper---it could save the cost of data transfer fee in the download.