## Migrating Your Source from DEM to Github

In the backend, DEM stores its files in Sequential Datasets. The migration tool we use to migrate your source into GitHub requires the files to be stored in a PDS. The first step required for migrating your source out of DEM into Github is to copy your sequential files into a PDS.

{%
   include-markdown "../../includes/zos/user-note.md"
%}
​

#### How to Organize your PDSs to make migration easier

Given the structure recommended to use in GitHub, it makes sense to group your source PDSs into language-based datasets.

For example, if you have a limited number of PL/I code, you may want to create a PDS called `PLI` that you store your PL/I code in. If you were to save your PL/I Include files, you could make an `INCLUDE` dataset. This makes migration to GitHub very easy as there will be a direct mapping between this dataset and the target GitHub repo directory.

For some applications, you may have more source files (or some type of conflict in your source), that prevent you from storing it all in one dataset. You could split the source into different topic areas or modules, then upload them to GitHub using that same convention.
​

#### Migrate Source to Github

Now that you have your source files saved in PDSs, follow the [Migrating Your Source from PDS (Partitioned Data Set) to Github](#migrating-your-source-from-pds-partitioned-data-set-to-github) steps below to complete the migration.
