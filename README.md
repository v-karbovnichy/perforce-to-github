# perforce-to-github (from Adam)

1. It must be run inside the SRCROOT directory (after running be4). 
2. It requires the following params:
- p4 repo prefix, e.g. //OPSC_Gold/rte
- release, e.g. 1.1.5.12_OZ106871
- cloned github repo, e.g. /data/asiemion/gittests/repos/GOLD-RTE-rte

3. It runs p4 client -o and parses the View section. 
It replaces '*' with actual file names. It skips useless mappings which occur in clientview - directory/file is mapped to the same name. 
It also is able to handle a few repos with nonstandard branch naming convention, e.g. GOLD-JavaDev-ant, GOLD-common-system_messages.

Sample usage:
```
migrate_p4_to_github //OPSC_Gold/rte dev 1.1.5.12_OZ106871 /data/asiemion/gittests/repos/GOLD-RTE-rte
```
- *param 1:* perforce repository prefix
- *param 2:* rel|dev (according to naming convention
- *param 3:* release number
- *param 4:* cloned github repo location on the local disk

You have to edit the script before the first usage to set the variables.

Please note that tool will create configuration file & commit it to given repository (but do not push it).


## Known issues:

Right now known issues have to be solved by manually editing workspace configuration file. Any improvements welcome!

### 1. Does not handle file renaming during translation 
File names left as original ones, for example for clientview:
```
        //Component/common/apache/apache-2.0.63/build/httpd-2.0.63-x86_64-unknown-linux-gnu-.* //REL_OPSC_Gold_AddOn_dcd_rel_R2.3.2.0.1_MPestka/Component/apache/apache_Linux_2.6.18.*
        //Component/common/apache/apache-2.0.63/build/httpd-2.0.63-x86_64-Linux_RHEL_6.* //REL_OPSC_Gold_AddOn_dcd_rel_R2.3.2.0.1_MPestka/Component/apache/apache_Linux_RHEL_6.*
```
actual mapping will be incorrect as right-side file names will equal to left-side.

proper mapping should be:
```
  {
    "repo": "GOLD-common-apache",
    "branch": "master",
    "tag": null,
    "mappings": {
      "apache-2.0.63/build/httpd-2.0.63-x86_64-Linux_RHEL_6.README": "Component/apache/apache_Linux_RHEL_6.README",
      "apache-2.0.63/build/httpd-2.0.63-x86_64-Linux_RHEL_6.tar.gz": "Component/apache/apache_Linux_RHEL_6.tar.gz",
      "apache-2.0.63/build/httpd-2.0.63-x86_64-Linux_RHEL_6.tar.gz.md5": "Component/apache/apache_Linux_RHEL_6.tar.gz.md5",
      "apache-2.0.63/build/httpd-2.0.63-x86_64-unknown-linux-gnu-.README": "Component/apache/apache_Linux_2.6.18.README",
      "apache-2.0.63/build/httpd-2.0.63-x86_64-unknown-linux-gnu-.tar.gz": "Component/apache/apache_Linux_2.6.18.tar.gz",
      "apache-2.0.63/build/httpd-2.0.63-x86_64-unknown-linux-gnu-.tar.gz.md5": "Component/apache/apache_Linux_2.6.18.tar.gz.md5"
    }
  }
```






