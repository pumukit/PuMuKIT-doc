PuMuKIT Migration Guide
=======================

To do a migration, there are two possibilities depending of your Pumukit installation. In some version is also necessary execute another especific steps after do the migration (you can see this changes in the migration guides)

- Installation using git:

In this case you only have to do a 'git checkout' to the desired tag.

For example, if you want to migrate from 3.4 to 3.9 you must stop pumukit, do git checkout 3.9.x in the folder and then launch pumukit again

- Installation using composer.json

In this case you must change the selected branch in the file and after execute "composer update" to update



1. [Guide to migrate from PuMuKIT 2.6 to PuMuKIT 3.0](migration_guides/from2.6to3.0.md)
2. [Guide to migrate from PuMuKIT 3.0 to PuMuKIT 3.1](migration_guides/from3.0to3.1.md)
3. [Guide to migrate from PuMuKIT 3.4 to PuMuKIT 3.5](migration_guides/from3.4to3.5.md)
4. [Guide to migrate from PuMuKIT 3.8 to PuMuKIT 3.9](migration_guides/from3.8to3.9.md)

