Netezza Data Extraction:

The script does setup, performs unload of Netezza table to CSV files and uses Azure Copy to copy the CSVs to ADLS.

Things to note:

The script is for a single table yet in comcination with NEtezza script (nz)tavles) can be called to list the tables in a database to execute the script as needed to loop through a list of tables.

Modified the IBM Neteza nz_backup command to add a header to each of the eight CSV files that get created - The is imprtant for Synapse Pipeline / ADF load to Synapse dedicated pool. 


Eight streams seems to be the sweet spot for the nz_backup command. The Netezza host bogs down too much with more streams than that.

The AZCOPY command uses a SAS key for the target container for authentication. You can modify the path as needed; you don’t have to write to the root container.

Mote: The truncate/reload section of the script is necessary because we had to create a separate database specifically for this migration because the Netezza VARCHAR datatype is not compatible with the Synapse VARCHAR datatype.  Converting Netezza VARCHAR to NVARCHAR solves that problem. (Another solution is to change the encoding of the CSV files before they’re copied to ADLS, but that doubles the duration of the data copy.) The other reason for the need to create the separate database is because the structures of the source and target tables is slightly different, and Synapse Pipeline default mappings require a one-to-one relationship between the source and target columns. This approach changes on the source rather than making any changes to existing target tables in Synapse.) 

Optionally: You can use a dynamically generated insert scripts for every table in the Synapse (ETL_AZURE) database. 



