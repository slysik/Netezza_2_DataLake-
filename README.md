# Netezza_2_DataLake-
Netezza Migrations: A performant script to extract Netezza data to ADLS 

-------------------------------
#!/bin/bash

# Variables
db_name=$1
table_name=$2
folder_name="/nzscratch/backup/temp"

path_name="$folder_name"/"$table_name"

# Create the directory for the backup files
# Each table will have its own directory
mkdir -p "$path_name"

# Truncate/reload data from the ETL table to ETL_AZURE table
nzsql -d $db_name -c "truncate table ${table_name};"
nzsql -d $db_name -f ./insert_scripts/${table_name}_insert.sql

# Back up the ETL_AZURE table to a CSV file
# The nz_backup_custom script is a modified version of the standard nz_backup script that uses any delimiter and writes to the directory/file specified with headers in each file
nz_backup_custom -db "$db_name" -t "$table_name" -format ascii -dir "$path_name" -file "$table_name" -delim 006 -streams 8

# Execute the AZCOPY command
./azcopy copy "$path_name" "<SAS key URL>" --recursive=true

exit 0
-------------------------------

