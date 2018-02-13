# perforce-to-github (from Adam)

It must be run inside the SRCROOT directory (after running be4). 
It requires the following params:
- p4 repo prefix, e.g. //OPSC_Gold/rte
- release, e.g. 1.1.5.12_OZ106871
- cloned github repo, e.g. /data/asiemion/gittests/repos/GOLD-RTE-rte

It runs p4 client -o and parses the View section. It replaces '*' with actual file names. It skips useless mappings which occur in clientview - directory/file is mapped to the same name. It also is able to handle a few repos with nonstandard branch naming convention, e.g. GOLD-JavaDev-ant, GOLD-common-system_messages.

Sample usage:
migrate_p4_to_github //OPSC_Gold/rte 1.1.5.12_OZ106871 dev /data/asiemion/gittests/repos/GOLD-RTE-rte

You have to edit the script before the first usage to set the variables.


### Known issues:

# does not handle file renaming during translation 
File names left as original ones, for example for clientview:
        //Component/common/apache/apache-2.0.63/build/httpd-2.0.63-x86_64-unknown-linux-gnu-.* //REL_OPSC_Gold_AddOn_dcd_rel_R2.3.2.0.1_MPestka/Component/apache/apache_Linux_2.6.18.*
        //Component/common/apache/apache-2.0.63/build/httpd-2.0.63-x86_64-Linux_RHEL_6.* //REL_OPSC_Gold_AddOn_dcd_rel_R2.3.2.0.1_MPestka/Component/apache/apache_Linux_RHEL_6.*
actual mapping will be incorrect as right-side file names will equal to left-side.

proper mapping should be:
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


# import from multiple branches improperly merged
for example:
        //Component/qa/RessourceKit/rel/R1.1.9/... //REL_OPSC_Gold_AddOn_dcd_rel_R2.1.2_MPestka/OTS/RessourceKit/...
        //Component/qa/RessourceKit/rel/R1.1.15/tools/sim_comlayer.c //REL_OPSC_Gold_AddOn_dcd_rel_R2.1.2_MPestka/OTS/RessourceKit/tools/sim_comlayer.c
actual mapping:

  {
    "repo": "GOLD-Component-RessourceKit",
    "branch": "rel-R1.1.15",
    "tag": null,
    "mappings": {
      "tools/sim_comlayer.c": "OTS/RessourceKit/tools/sim_comlayer.c",
      "...": "OTS/RessourceKit"
    }
  }

proper mapping (split into 2 repos in right order):

  {
    "repo": "GOLD-Component-RessourceKit",
    "branch": "rel-R1.1.9",
    "tag": null,
    "mappings": {
      "...": "OTS/RessourceKit"
    }
  },
  {
    "repo": "GOLD-Component-RessourceKit",
    "branch": "rel-R1.1.15",
    "tag": null,
    "mappings": {
      "tools/sim_comlayer.c": "OTS/RessourceKit/tools/sim_comlayer.c"
    }
  }






zamiast do 2 (lub więcej) osobnych mappingów (branch jest nadpisywana ostatnią wartością i wszystkie mappings należą do tego samego brancha)
