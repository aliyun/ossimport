# OssImport ChangeLog

## Version: 2.3.1; Date: 2017-12-20
### Change description
- Fixed: The configuration isAuditAfterImport is not effective

## Version: 2.3.0; Date: 2017-12-15
### Change description
- Added: Data migration through Express Connect
- Fixed: Group-developed simplified code formatting

## Version: 2.2.4; Date: 2017-10-23
### Change description
- Added: Version-based management and support for the "version" command to facilitate troubleshooting. For more information, see [#OSS-2793](https://workitem.aone.alibaba-inc.com/project/240655/issue/12720334)
- Fixed: Process memory control to prevent the kill action due to excessive memory usage. For more information, see [#OSS-2794](https://workitem.aone.alibaba-inc.com/project/111257/issue/12720608)
- Fixed: Single-thread uploading of local large files optimized to multithread concurrent uploading
- Fixed: System.getProperty configuration item introduced to enable configuration using configuration files. For more information, see [#OSS-2756](https://workitem.aone.alibaba-inc.com/issue/12427969?stat=1.5.10&toPage=1&projectId=40654008)
- Fixed: Abnormal information logging

## Version: 2.2.3; Date: 2017-10-10
### Change description
- Added: UPYUN's support for file download from CDN
- Added: Support for network time-out setting in configuration files
- Fixed: NPE reported by createSingleThreadTask of UPYUN. For more information, see [#OSS-27870](https://aone.alibaba-inc.com/issue/12671326)
- Fixed: Default value of dispatcherThreadNum changed to 1
- Fixed: Abnormal running of Linux stand-alone systems after being started in a non-working directory

## Version: 2.2.2; Date: 2017-09-11
### Change description
- Fixed: Dynamic loading of configuration files
- Fixed: Retry level optimization from Task to File
- Fixed: Support for isSkipExistFile in the Job configuration file

## Version: 2.2.1; Date: 2017-07-29
### Change description
- Fixed: Throttling ineffective during HTTP-based data migration
- Fixed: Windows scripts available in English
- Added: README content

## Version: 2.2.0; Date: 2017-07-12
### Change description
- Added: Migration from Azure Blob to OSS
- Added: Linux command line console.sh and one-click execution script import.sh added to the stand-alone version
- Added: Windows command line console.sh and one-click execution script import.bat added to the stand-alone version, while other .bat scripts are removed
- Added: Linux command line console.sh added to the distributed version
- Fixed: Name optimization, including "slave" changed to "worker", "dispatcher" to "tasktracker", namespace "ossimport2" to "ossimport", and JAR name changed
- Fixed: The Ubuntu directory for AWK is /usr/bin/awk instead of /bin/awk; directories are removed from AWK and other commands.
