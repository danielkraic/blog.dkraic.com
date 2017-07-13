+++
date = "2017-07-13T20:42:51+02:00"
title = "PostgreSQL"
tags = [ "dev", "db" ]
+++

* Default port is 5432
* postgresql.conf: listen_addresses '*'
* pg_hba.conf: host all all 127.0.0.1/32 trust, host all all 0.0.0.0/0 md5

## data types

### numeric

* int, int8	
* serial, serial4, serial8
* numeric(s, p) - Decimal numbers; s is scale and p is precision.
* double precision - Floating point numbers.

### string

* varchar(n) (a.k.a. character varying) - Max of n characters, no trailing spaces.
* char(n) - Padded to n characters.
* text - Unlimited text.


### date and time

* date - YYYY-MM-DD
* timestamp - 2009-07-01 23:00
* timestamp with time zone - 2009-07-01 23:00:00-04
* time - 23:14:20
* time with time zone - 23:14:20-04	
* interval - SELECT TIMESTAMP '2009-07-01 23:14:20' + INTERVAL '4 months 2 days 10 hours 9 seconds'
* daterange, tsrange, tstzrange - SELECT '(2012-07-01, 2013-08-31]'::daterange;

### other types

* boolean - true/false
* bytea - Byte array used for storing binary objects, such as files.
* JSON
* JSONB

#### array

```sql
SELECT ARRAY['john','jane'];
SELECT ARRAY(SELECT emp_name FROM employees); 
SELECT array_agg(e.emp_name) FROM employees;
```

#### enum

```sql
CREATE TYPE cloth_colors AS ENUM ('red','blue','green');
```

### global variables

* CURRENT_TIMESTAMP, now() - Returns current date and time with timezone.
* CURRENT_DATE- Returns current date without the time.
* CURRENT_TIME- Returns current time without the date.

## functions

### string

* || (string || string, string || number) - String concatenation.
* left, right, substring - Returns left x elements, right x elements, or substring from position x for y number of elements.
* length - Number of characters in string.
* lpad, rpad - Left and right pad. lpad('A', 5, 'X') => ‘XXXXA’ rpad('A', 5, 'X') => ‘AXXXX’
* lower, upper, initcap - Lower, upper, proper case.
* md5 - Calculates the MD5 hash.
* quote\_ident - Quotes keywords and expressions not suitable for identity when unquoted. quote\_ident('in') => “in” quote\_ident('big') => big
* quote_literal - Escapes both single and double quotes.
* quote\_nullable - Similar to quote\_literal but doesn’t quote NULL.
* replace - replace('1234abcv', '1234', 'joe ') => joeabcv
* split\_part - Takes a delimited string and returns the nth item. split\_part('abc|def', '|', 2) =>def
* string_agg - SQL aggregate function that aggregates a set of values into a string.
* strpos(text, subtext) - Returns numeric position of subtext within text.
* trim, btrim, ltrim, rtrim - Trim spaces in string.

### date and time

* age(timestamp, timestamp) - Returns an interval spanned between timestamp1 and timestamp2.
* age(timestamp) - Difference from current time.
* date\_part(text, timestamp), date\_part(text, interval) - date\_part('day', timestamp '2009-07-04 11:05:45') => 4 date\_part('hour', interval '560 minutes') => 9
* date\_trunc(text, timestamp | timestamptz | date) - date\_trunc('hour', '2014-01-15 10:30 PM'::timestamp) => 2014-01-15 22:00:00
* operators +, -, / (for intervals only) - You can add (or subtract) intervals to datetimes. You can perform addition and subtraction between two * datetimes. You can divide intervals into smaller intervals.
* generate\_series(timestamp, timestamp, [interval]) - Generate rows of timestamps.

### array

* || - Array concatenation. ARRAY[1,2,3] || ARRAY[3,4,5] => {1,2,3,3,4,5}
* unnest - Converts an array to rows. SELECT anum FROM unnest(ARRAY[1,2,3])
* array_agg - SQL aggregate function that aggregates a set of values into an array.
* array\_upper(anyarray, dimension) array\_lower(anyarray, dimension) - Returns upper/lower bound of the requested array dimension. SELECT array\_upper(ARRAY[ARRAY['a'], ARRAY['b']],1); outputs: 2
* array\_to\_string(anyarray, delimiter\_text) - Converts an array to a text delimited by the delimiter. array\_to\_string(ARRAY[12,34], '|') => ‘12|34’

## psql

```bash
psql –U username –p 5432 –h localhost –d dzone
psql –h localhost -U postgres –p 5432 –f /path/to/pgdumpall.sql
psql -h someserver -p 5432 -U postgres -d dzone -H -c "SELECT * FROM pg_tips" -o tips.html
psql -U postgres –p 5432 -d dzone -c "CREATE TABLE test(some_id serial PRIMARY KEY, some_text text);"
psql -h localhost -U someuser -d dzone -f /path/to/scriptfile.sql -o /path/to/outputfile.txt
```

|command|description|
|----|-------|
|\q|Quit|
|:q|Cancel out of more screen|
|\\?|Help on psql commands|
|\h some\_command|Help on SQL commands|
|\c dbname|Switch database|
|\l|List all databases|
|\dt|List tables|
|\dt+|List tables with more info|
|\dt+ table1|Get table detailed info|
|\d sometable|List columns, data types, and constraints for a table.|
|\df|List functions|
|\df+ table1|Get function code|
|\dn|List schemas|
|\dv|List views|
|\dtv p*|List tables and views that start with p.|
|\du|List user/group roles and their group memberships and server level permissions.|
|\x|Show query output in the pretty-format.|
|\i somefile|Execute SQL script stored in a file.|
|\o somefile|Output contents to file.|
|\timing|Toggle query timing on and off; when on, query output includes timing information.|


## create user

```sql
CREATE USER myuser WITH PASSWORD 'mypassword';
CREATE DATABASE testdb;
GRANT ALL PRIVILEGES ON DATABASE testdb to myuser;
```

## admin tasks

```bash
# Create a compressed backup
pg_dump -h someserver -p 5432 -U someuser -F -c -b -v -f "/somepath/somedb.backup" somedb
# Create a compressed backup of select tables
pg_dump -h localhost -p 5432 -U someuser -F -c -b -f "C:/somedb.backup" -t "someschema.table1" -t "someschema.table2" -v somedb
# Create a compressed backup excluding a particular schema
pg_dump -h localhost -p 5432 -U someuser -F -c -b -f "C:/somedb.backup" -N someschema -v somedb
# Restore a compressed backup
pg_restore –h localhost –d db_to_restore_to –U someuser /path/to/somedb.backup
# Restore select schemas from backup
pg_restore –h localhost –d db_to_restore_to –U someuser -n someschema1 -n someschema2 /path/to/somedb.backup
# Output a table of contents from backup file
pg_restore -l -f "C:/toc.txt" "C:/somedb.backup"
# Restore only items in the table of contents
pg_restore -h localhost -d db_to_restore -U someuser -L "C:/toc.txt" "C:/somedb.backup"
```


