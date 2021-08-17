# munin-fixes
Fix for different munin plugins, the quick and dirty ones...

iostat - this plugin has issues with the disk name "mmcblk0" and the partition variants "mmcblk0p*".

To fix it for raspbian OS put a **comment character** in front of

```perl
# if ($tmpnam =~ /\d+$/ and !$include_numbered) {
#     # Special case for devices like cXdXpX,
#     # like the cciss driver
#     next unless $tmpnam =~ /\/c\d+d\d+$/
# }
```
