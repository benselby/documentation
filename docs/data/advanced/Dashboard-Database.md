# Dashboard Database

## Who this is for

- Those who plan to contribute code to the lab's QC dashboard
- Those who want to set up their own test instance of a QC dashboard

Parts of this page may be out of date. For up to date documentation see [the dashboard's readme](https://github.com/TIGRLab/dashboard)

## Overview

The dashboard web application is backed by a PostgreSQL SQL database. The database engine is running on a dedicated virtual machine srv-postges (IP: 172.26.216.68). As ansible role `postgres` has been created to configure the server.

## Security

By default the the postgres database is only configured to accept connections from internal IP addresses. This access is controlled by iptables firewall and is configured via ansible.
Access to the `dashboard` database is is controlled by [postgres roles](https://www.postgresql.org/docs/9.1/static/sql-createrole.html).

- `dashboard` - full control
- `dashboard_read` - read only access

To grant a user access to the dashboard database:

1. User must be a valid local user on `srv-postgres` (usually configured via IPA), and able to login using ssh.
2. As a user with sudo privilages, login to `srv-postgres` and change to postgres user.

    $sudo su postgres
3. Run `psql`:

    $psql
4. Create the postgres user:

   \#CREATE USER \<username>;
5. Make the user a member of the appropriate role:

   \#GRANT \<rolename> TO \<username>;
6. Exit psql:

   \#\q

## Access the data

### From R

The libraries `RPostgresSQL` and `dplyr` make direct access to the database simple. The query below accesses the data and returns it in _long_ format. The _metrictype_ column describes the QC metric, while the value is in the _value_ column.

    library(RPostgreSQL)
    library(dplyr)

    # Link the database
    db_src <- src_postgres(dbname="dashboard",
                           host="172.26.216.68")


    # link all the tables
    db_studies <- tbl(db_src, "studies")
    db_sites <- tbl(db_src, "sites")
    db_sessions <- tbl(db_src, "sessions")
    db_scantypes <- tbl(db_src, "scantypes")
    db_scans <- tbl(db_src, "scans")
    db_metrictypes <- tbl(db_src, "metrictypes")
    db_metrics <- tbl(db_src, "scanmetrics")

    # main query performing all joins
    data <- db_metrics %>%
                left_join(db_metrictypes, by = c("metrictype_id"="id"), suffix=c('.metric', '.metrictype')) %>%
                left_join(db_scans, by = c("scan_id"="id") , suffix = c('', '.scan')) %>%
                left_join(db_scantypes, by = c("scantype_id"="id"), suffix = c('', '.scantype')) %>%
                left_join(db_sessions, by = c("session_id" = "id"), suffix = c('', '.session')) %>%
                left_join(db_sites, by = c("site_id"="id"), suffix = c("", ".site")) %>%
                left_join(db_studies, by = c("study_id"="id"), suffix = c("", ".study")) %>%
                select(study = nickname,
                       site = name.site,
                       session_id,
                       session = name.session,
                       series_number,
                       date,
                       is_phantom,
                       scan_id,
                       scan = name.scan,
                       scan_description = description,
                       scantype = name.scantype,
                       bl_comment,
                       metrictype = name,
                       metrictype_id,
                       value)

*N.B.* The _value_ field is returned as a character value, this is to allow storage of array data and other formats in the database. The optional code segment below limits the query to just DTI metrics and converts to numeric values.

    scan_pattern <- 'DTI%'
    data %>%
        mutate(is_blacklisted = !is.na(bl_comment)) %>%
        filter(!is_phantom) %>%
        filter(scantype %like% scan_pattern) %>%
        filter(!metrictype %in% c('modulename','version','inputfile','outlist_bX','clipcount','clipval')) %>%
        mutate(value_n=as.numeric(value))
