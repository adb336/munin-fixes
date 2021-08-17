# munin-fixes
Fix for different munin plugins, the quick and dirty ones...

iostat - this plugin has issues with the disk name "mmcblk0" and the partition variants "mmcblk0p*".

To fix it for raspbian OS put a comment character in from of

<p>
sub fetch_detailed() {
    if (open(DETAILED, "/proc/diskstats") or
        open(DETAILED, "/proc/partitions")) {

        while (<DETAILED>) {
            if (/^\s+(\d+)\s+\d+\s*\d*\s+([[:alpha:][:digit:]\/]+)\s+(.*)/) {
                my @fields = split(/\s+/, $3);
                my $tmpnam = $2;
                my $major  = $1;
             <b># if ($tmpnam =~ /\d+$/ and !$include_numbered) {
                #     # Special case for devices like cXdXpX,
                #     # like the cciss driver
                #     next unless $tmpnam =~ /\/c\d+d\d+$/
                # } </b>
                next unless grep { $_ } @fields;

                $tmpnam =~ s/\/[[:alpha:]]+(\d+)/\/$1/g;
                $tmpnam =~ s/^([^\/]+)\//$1/;
                $tmpnam =~ s/\/disc$//;

                $devs{"dev".$major."_".&get_disk_count($major)} =
                  {
                   major => $major,
                   name => $tmpnam,
                   rio => $fields[0],
                   rmerge => $fields[1],
                   rsect => $fields[2],
                   ruse => $fields[3],
                   wio => $fields[4],
                   wmerge => $fields[5],
                   wsect => $fields[6],
                   wuse => $fields[7],
                   running => $fields[8],
                   use => $fields[9],
                   aveq => $fields[10]
                  };
            }
        }
        close (DETAILED);
    }
}
</p>
