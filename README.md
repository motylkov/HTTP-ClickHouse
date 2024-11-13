
# NAME

HTTP::ClickHouse - Perl driver for ClickHouse

# VERSION

Version 0.061

# SYNOPSIS

HTTP::ClickHouse - Perl interface to ClickHouse Database via HTTP.

# EXAMPLE

    use HTTP::ClickHouse;
 
    my $chdb =  HTTP::ClickHouse->new(
        host     => '127.0.0.1', 
        user     => 'Harry',
        password => 'Alohomora',
    );

    $chdb->do("create table test (id UInt8, f1 String, f2 String) engine = Memory");

    $chdb->do("INSERT INTO my_table (id, field_1, field_2) VALUES", 
        [1, "Gryffindor", "a546825467 1861834657416875469"],
        [2, "Hufflepuff", "a18202568975170758 46717657846"],
        [3, "Ravenclaw", "a678 2527258545746575410210547"],
        [4, "Slytherin", "a1068267496717456 878134788953"]
    );

    my $rows = $chdb->selectall_array("SELECT count(*) FROM my_table");  
    unless (@$rows) { $rows->[0]->[0] = 0; } # the query returns an empty string instead of 0
    print $rows->[0]->[0]."\n"; 


    if ($chdb->select_array("SELECT id, field_1, field_2 FROM my_table")) {
        my $rows = $chdb->fetch_array();
        foreach my $row (@$rows) {
            # Do something with your row
            foreach my $col (@$row) {
                # Do something
                print $col."\t";
            }
            print "\n";
        }
    }

    $rows = $chdb->selectall_hash("SELECT count(*) as count FROM my_table");
    foreach my $row (@$rows) {
        foreach my $key (keys %{$row}){
            # Do something
            print $key." = ".$row->{$key}."\t";
        }
        print "\n";
    }
 
    ...

    disconnect $chdb;


# DESCRIPTION

This module implements HTTP driver for Clickhouse OLAP database 


# SUBROUTINES/METHODS

### new

Create a new connection object with auto reconnect socket if disconnected.


    my $chdb =  HTTP::ClickHouse->new(
        host     => '127.0.0.1', 
        user     => 'Harry',
        password => 'Alohomora'
    );

options:

    host       => 'hogwards.mag',              # optional, default value '127.0.0.1'
    port       => 8123,                        # optional, default value 8123
    user       => 'Harry',                     # optional, default value 'default'
    password   => 'Alohomora',                 # optional
    database   => 'database_name',             # optional, default name "default"         
    nb_timeout => 10                           # optional, default value 25 second
    keep_alive => 1                            # optional, default 1 (1 or 0)
    debug      => 1                            # optional, default 0



### select_array & fetch_array

First step - select data from the table (readonly). It returns 1 if query completed without errors or 0.
Don't set FORMAT in query. TabSeparated is used by default in the HTTP interface.


Second step - fetch data.
It returns a reference to an array containing a reference to an array for each row of data fetched.


    if ($chdb->select_array("SELECT id, field_1, field_2 FROM my_table")) {
        my $rows = $chdb->fetch_array();
        foreach my $row (@$rows) {
            # Do something with your row
            foreach my $col (@$row) {
                # Do something
                print $col."\t";
            }
            print "\n";
        }
    }

### selectall_array


Fetch data from the table (readonly). 
It returns a reference to an array containing a reference to an array for each row of data fetched.


    my $rows = $chdb->selectall_array("SELECT count(*) FROM my_table");  
    unless (@$rows) { $rows->[0]->[0] = 0; } # the query returns an empty string instead of 0
    print $rows->[0]->[0]."\n"; 


### select_hash & fetch_hash


First step - select data from the table (readonly). 
It returns 1 if query completed without errors or 0.
Don't set FORMAT in query.


Second step - fetch data. 
It returns a reference to an array containing a reference to an array for each row of data fetched.


    if ($chdb->select_hash("SELECT id, field_1, field_2 FROM my_table")) {
        my $rows = $chdb->fetch_hash();
        foreach my $row (@$rows) {
            # Do something with your row
            foreach my $key (sort(keys %{$row})){
                # Do something
                print $key." = ".$row->{$key}."\t";
            }
            print "\n";
        }
    }


### selectall_hash


Fetch data from the table (readonly). 
It returns a reference to an array containing a reference to an hash for each row of data fetched.


    my $rows = $chdb->selectall_hash("SELECT id, field_1, field_2 FROM my_table");
    foreach my $row (@$rows) {
        # Do something with your row
        foreach my $key (sort(keys %{$row})){
            # Do something
            print $key." = ".$row->{$key}."\t";
        }
        print "\n";
    }


### do


Universal method for any queries inside the database, which modify data (insert data, create, alter, detach or drop table or partition).
It returns 1 if query completed without errors or 0.


    # drop
    $chdb->do("drop table test if exist");

    # create
    $chdb->do("create table test (id UInt8, f1 String, f2 String) engine = Memory");

    # insert
    $chdb->do("INSERT INTO my_table (id, field_1, field_2) VALUES", 
        [1, "Gryffindor", "a546825467 1861834657416875469"],
        [2, "Hufflepuff", "a18202568975170758 46717657846"],
        [3, "Ravenclaw", "a678 2527258545746575410210547"],
        [4, "Slytherin", "a1068267496717456 878134788953"]
    );


### disconnect

Disconnects http socket from the socket handle. Disconnect typically occures only used before exiting the program. 

    disconnect $chdb;

    # or

    $chdb->disconnect;


# SEE ALSO
[ClickHouse official documentation](https://clickhouse.com/docs)

# AUTHOR
Maxim Motylkov

# MODIFICATION HISTORY
See the Changes file.

# COPYRIGHT AND LICENSE
Copyright 2016 Maxim Motylkov


```
This module is free software, you can redistribute it and/or modify it under the same terms as Perl itself. 

THIS PACKAGE IS PROVIDED "AS IS" AND WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, WITHOUT LIMITATION,
THE IMPLIED WARRANTIES OF MERCHANTIBILITY AND FITNESS FOR A PARTICULAR PURPOSE.
```
