![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 如何批量更新服务器作业调度SQL
#### Here’s How To Mass Update Agent Job Schedules With SQL
**发布-日期: 2015年9月14日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这是我编写的可通过多个服务器更新特定作业调度的逻辑。 它旨在找到作业，调取该指定作业ID和调度ID（提供的作业只有一个调度），然后将其更新为除周六和周日外每晚运行。该作业在凌晨12:00（午夜）运行。 它是在星期六运行，但注意星期六凌晨12点是一天中的第一个小时，即该作业检查备份是从前一天晚上（星期五晚上）开始。 您不需要检查周末的备份，因为这部分数据维护窗口通常不备份。


## English
Here’s some logic I wrote up which allows you to update a particular Job schedule across multiple servers. It’s designed to find the Job, Pull the Job ID and Schedule ID for that particular Job (provided is has only one schedule attached to it), then it updates it to run nightly excluding Saturday Night, Sunday Night. The Jobs runs at 12:00am (midnight). it does run on Saturday, but remember 12:00am on Saturday is the first hour of the day so what happens is the Job is checking the backups from the night before (Friday Night). You don’t need to check backups for the weekends because that’s the maintenance window where backups are typically not taken.

---
## Logic
```SQL
use master;
set nocount on
 
-- update job to run every night at 12:00am excluding saturday night, sunday night, and monday night.在除去周六，周日和周一外的每晚凌晨12:00运行作业数据
declare @update_job_schedule    varchar(max)
set     @update_job_schedule    = ''
select  @update_job_schedule    = @update_job_schedule +
'use msdb;' + char(10) +
'exec msdb.dbo.sp_attach_schedule @job_id=''' + cast(sj.job_id as varchar(255)) + ''',@schedule_id=' + cast(sjsched.schedule_id as varchar(255)) + char(10) + 
'exec msdb.dbo.sp_update_schedule @schedule_id=' + cast(sjsched.schedule_id as varchar(255)) + ', @freq_interval=124' + char(10)
from
    msdb..sysjobs sj join msdb..sysjobsteps sjs on sj.job_id = sjs.job_id
    join msdb..sysjobschedules sjsched on sj.job_id = sjsched.job_id
where
    sj.name = 'SEND SQL BACKUP ALERTS'  --> put your job name here.
 
exec    (@update_job_schedule)


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

