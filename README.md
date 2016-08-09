# NAME

redis\_memtop.pl - Display memory usage of redis keys

# SYNOPSIS

    redis_memtop.pl [--server|-s SERVER] [--help|-h] [--man] FILTER

    Options:
        --server, -s    SERVER_STRING   use IP:PORT or /path/to/socket
                                        (default is 127.0.0.1:6379)
        --help, -h                      brief help message
        --man                           full manual

# DESCRIPTION

This is a simple script to show relative memory usage of keys is redis
database. You can pass `FILTER` as an argument which will be supplied to the
KEYS redis command to filter keys which interest you. The default is '\*', to
display all the keys.

Consider this example output:

    $ redis-cli set key1 $(perl -e 'print "A"x1024')
    OK
    $ redis-cli set key2 $(perl -e 'print "A"x2048')
    OK
    $ redis-cli set key3 $(perl -e 'print "A"x512')
    OK
    $ perl redis_memtop.pl 'key*'
    Key                  Perc           Serialized
    key3        22.2222222222         16
    key1        30.5555555556         22
    key2        47.2222222222         34

The records are sorted by the 'Perc' column. The 'Key' column contains the key
name. The 'Perc' column contains the relative percentage. The percentage is
only computed between the three keys requested, not the whole database. The
script uses 'DEBUG OBJECT" redis command for every key and watches for
'serializedlength', which is the length of the data after serialization, not in
memory, but the ratio should be more or less the same.  The 'Serialized' column
is provided for completness.

Running DEBUG OBJECT on too many keys can take a long time and possibly slow
the database down, so be careful. For larger databases and more features see
https://github.com/snmaynard/redis-audit.
