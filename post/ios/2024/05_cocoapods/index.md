# 05_cocoapods


#### cocoapods 升级记录
1.12.1 => 1.14.0
``` bash
➜  ~ gem list --local | grep cocoapods
cocoapods (1.12.1)
cocoapods-core (1.12.1)
cocoapods-deintegrate (1.0.5)
cocoapods-downloader (1.6.3)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.1)
cocoapods-trunk (1.6.0)
cocoapods-try (1.2.0)
➜  ~ sudo gem install cocoapods -v 1.14.0
Password:
Fetching cocoapods-1.14.0.gem
Fetching xcodeproj-1.24.0.gem
Fetching concurrent-ruby-1.3.3.gem
Fetching cocoapods-core-1.14.0.gem
Fetching cocoapods-downloader-2.1.gem
Successfully installed xcodeproj-1.24.0
Successfully installed cocoapods-downloader-2.1
Successfully installed concurrent-ruby-1.3.3
Successfully installed cocoapods-core-1.14.0
Successfully installed cocoapods-1.14.0
Parsing documentation for xcodeproj-1.24.0
Installing ri documentation for xcodeproj-1.24.0
Parsing documentation for cocoapods-downloader-2.1
Installing ri documentation for cocoapods-downloader-2.1
Parsing documentation for concurrent-ruby-1.3.3
Installing ri documentation for concurrent-ruby-1.3.3
Parsing documentation for cocoapods-core-1.14.0
Installing ri documentation for cocoapods-core-1.14.0
Parsing documentation for cocoapods-1.14.0
Installing ri documentation for cocoapods-1.14.0
Done installing documentation for xcodeproj, cocoapods-downloader, concurrent-ruby, cocoapods-core, cocoapods after 3 seconds
5 gems installed
➜  ~ gem list --local | grep cocoapods   
cocoapods (1.14.0, 1.12.1)
cocoapods-core (1.14.0, 1.12.1)
cocoapods-deintegrate (1.0.5)
cocoapods-downloader (2.1, 1.6.3)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.1)
cocoapods-trunk (1.6.0)
cocoapods-try (1.2.0)
➜  ~ sudo gem uninstall cocoapods  1.12.1
Gem '1.12.1' is not installed

Select gem to uninstall:
 1. cocoapods-1.12.1
 2. cocoapods-1.14.0
 3. All versions
> 1
Successfully uninstalled cocoapods-1.12.1
➜  ~ gem list --local | grep cocoapods   
cocoapods (1.14.0)
cocoapods-core (1.14.0, 1.12.1)
cocoapods-deintegrate (1.0.5)
cocoapods-downloader (2.1, 1.6.3)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.1)
cocoapods-trunk (1.6.0)
cocoapods-try (1.2.0)
➜  ~ sudo gem uninstall cocoapods-core   

Select gem to uninstall:
 1. cocoapods-core-1.12.1
 2. cocoapods-core-1.14.0
 3. All versions
> 1
Successfully uninstalled cocoapods-core-1.12.1
➜  ~ sudo gem uninstall cocoapods-downloader

Select gem to uninstall:
 1. cocoapods-downloader-1.6.3
 2. cocoapods-downloader-2.1
 3. All versions
> 1
Successfully uninstalled cocoapods-downloader-1.6.3
```


