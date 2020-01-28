![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Bring Offline Databases Online Before Backup
**Post Date: April 23, 2014**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>This script will Set all Offline Databases Online, and do a backup after the switch to online. I've also taken the liberty of adding some extra logic to use a network backup share, and add a time stamp to the file name.</p>  


## SQL-Logic
```SQL
use master;
set nocount on
declare @timestamp varchar(50)
declare @servername varchar(50)
declare @backup_offlinedb varchar(max)
declare @set_databases_online varchar(max)
set @servername = ( select @@servername )
set @timestamp = ( select rtrim(replace(replace(replace(convert(char, getdate(), 9), ':', '-'), 'AM', ' am'), 'PM', ' pm')) ) set @backup_offlinedb = "
set @set_databases_online = "
select @backup_offlinedb = @backup_offlinedb +
'backup database [' + name + '] to disk = "\\MyBackupShare\' + @servername + '\' + name + '\Full\' + name + ' Full ' + @timestamp + '.bkp" with format; ' + char(10) from sys.databases where name like 'EDDS%' and state_desc = 'offline'
select @set_databases_online = @set_databases_online +
'alter database [' + name + '] set online; ' + char(10)
from sys.databases where /*name like '%wildcard here%' and*/ state_desc = 'offline'
exec (@set_databases_online);
waitfor delay '00:00:30′ — wait 30 seconds for online operation to complete. you should be fine, but doesn't hurt to add some delay. exec (@backup_offlinedb); 
```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

