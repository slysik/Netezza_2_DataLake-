Overview: 

The following provides a reusable Netezza migration framework to extract terabytes Netezza data using a single script. The following script leverages Netezza data extraction best practices.

No  ETL tool required 
No cost other than ADLS storage.

Problem Statement: How to make it super easy and fast to extract Netezza data for Appliance migrations to Azure. 

The script in this repo provides a proven data extraction process through one simple script.  This solution extracts the data leveraging Netezza built in scripts.  This code utilizes the following Azure resources - Bash scrpt, AZCopy 

Netezza Data Extraction:

The script does the setup, performs unload of Netezza table to CSV files and uses Azure Copy to copy the CSVs to ADLS.

Things to note:

The script is for a single table yet in comcination with NEtezza script (nz)tavles) can be called to list the tables in a database to execute the script as needed to loop through a list of tables.

Modified the IBM Neteza nz_backup command to add a header to each of the eight CSV files that get created - The is imprtant for Synapse Pipeline / ADF load to Synapse dedicated pool. 
The data is unloaded in multile streams to CSV file.s  
Please use eight streams as its proven to be the sweet spot for the Netezza nz_backup utility script. 

For larger NEtezza appliance you can use 16 : 2 rack or higher. 
The AZCOPY command uses a SAS key for the target container for authentication. You can modify the path as needed; you don’t have to write to the root container.

Note: 
The truncate/reload section of the script is necessary to create a separate database specifically for migration because the Netezza VARCHAR datatype is not compatible with the Synapse VARCHAR datatype.  Converting Netezza VARCHAR to NVARCHAR solves that problem. 
Another solution is to change the encoding of the CSV files before they’re copied to ADLS, yet that doubles the duration of the data copy.) The other reason for the need to create the separate database is because the structures of the source and target tables is slightly different, and Synapse Pipeline default mappings require a one-to-one relationship between the source and target columns. This approach changes on the source rather than making any changes to existing target tables in Synapse.
Optionally: You can use a dynamically generated insert scripts for every table in the Synapse (ETL_AZURE) database. I will add the step to loop thru each database to be migrated and then list out each table to be extracted.



