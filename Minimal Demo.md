**The package in the current state only allows a minimal demo. This encompasses Transcriptomics and Proteomics signatures. The functions available are;

  

addSignature

getSignature

updateSignature

searchSignature

deleteSignature

  

This is the core loop of the SigRepo application available for testing. To use these functions please open R-Studio and navigate to the SigRepo package. One inside the main project directory, open the SigRepo.Rproj. When inside the project, navigate to vignettes/signature-tutorials.Rmd. This is documentation that will take you through the minimal demo of the package. This tutorial will work for both persistent and local versions of the SigRepo application.

  

# Minimal DEMO PDF VERSION

---

  

The SigRepo package includes a suite of functions for easily storing and managing biological signatures and its constituents. Currently, Sigrepo is capable of storing, searching, and retrieving signatures and its signature collections from a MySQL Database of choice. See here for how set-up the MySQL database with the appropriate schema.

In order to interact with a suite of functions in the SigRepo package, the input data must represent an R6 object for the representation of signatures and signature collections, and they can be created using our proprietary package, OmicSignature.

For more information click the links below.

·         [Overview of the object structure](https://montilab.github.io/OmicSignature/articles/ObjectStructure.html)

·         [Create an OmicSignature (OmS)](https://montilab.github.io/OmicSignature/articles/CreateOmS.html)

·         [Create an OmicSignatureCollection (OmSC)](https://montilab.github.io/OmicSignature/articles/CreateOmSC.html)

For demonstration purposes, we will provide OmicSignature data to  walk through the steps of how to use the SigRepo package to store, retrieve, and interact with a list of signatures stored in the SigRepo Database.

  

Loading packages


`        `

|                                                                                                                                                                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| # Load tidyverse package  <br>library(tidyverse)  <br>  <br># Load SigRepo package  <br>library(SigRepo)  <br>  <br># Load OmicSignature package  <br>library(OmicSignature) |

# Connecting to the Persistent SigRepo Server

---

There are three types of user accounts: 

1. Admin: has READ and WRITE access to all signatures in the database. 
    
2. Editor: has READ and WRITE access to ONLY their own uploaded signatures in the database. 
    
3. Viewer: has ONLY READ access to see a list of signatures that are publicly available in the database but DO NOT HAVE WRITE access to the database.
    

Once you have a valid account, to connect to our SigRepo database, one can use the newConnHandler() function to create a handler which contains user credentials to establish a connection to our database. For this tutorial we have a testing connection handler that has permissions to access all of the functions in this demo.

|   |
|---|
|# Create a connection handler  <br>conn_handler <- SigRepo::newConnHandler(  <br>  dbname = "sigrepo",  <br>  host = "142.93.67.157",  <br>   port = 3306,  <br>   user = "montilab",  <br>  password = "sigrepo"  <br>)|

# Loading Signatures

---

  

Here, we provide two signature objects that come with the package for demonstration. In live scenarios, you will be asked to transform your signature into the OmicSignature Object

1.      omic_signature_AGS_OmS (LLFS_Aging_Gene_2023)

2.      omic_signature_MDA_CYP (CYP181 knockdown in breast cancer cell line)

|   |
|---|
|# Getting the signature path  <br>signature_path <- base::system.file("inst/data/signatures", package = "SigRepo")  <br>  <br># Reading in the signature objects  <br>omic_signature_AGS_OmS <- base::readRDS(base::file.path(signature_path, "omic_signature_AGS_OmS.RDS"))  <br>omic_signature_MDA_CYP <- base::readRDS(base::file.path(signature_path, "omic_signature_MDA_CYP.RDS"))|

  

# Uploading a Signature

---

  

The addSignature() function allows users to upload a signature to the database.

IMPORTANT NOTE:

·         The user MUST HAVE an editor or admin account to use this function.

## Example 1: Uploading the omic_signature_AGS_OmS signature

|   |
|---|
|SigRepo::addSignature(  <br>  conn_handler = conn_handler,  <br>  omic_signature = omic_signature_AGS_OmS  <br>)  <br>#>  ID of the uploaded signature: 150|

## Example 2: Upload omic_signature_MDA_CYP signature

|   |
|---|
|SigRepo::addSignature(  <br>  conn_handler = conn_handler,  <br>  omic_signature = omic_signature_MDA_CYP,  <br>  return_missing_features = TRUE            # Whether to return a list of missing features during upload. Default is FALSE.  <br>)  <br>  <br>#>  ID of the uploaded signature: 158|

## Example 3: Creating an OmicSignature object and then uploading it into the database

This code is transforming diffanal data into the omic signature object, this is an example of how the process would work.

|   |
|---|
|# Create signature metadata  <br>metadata <- base::list(  <br>  # required attributes:  <br>  signature_name = "Myc_reduce_mice_liver_24m",  <br>  organism = "Mus Musculus",  <br>  direction_type = "bi-directional",  <br>  assay_type = "transcriptomics",  <br>  phenotype = "Myc_reduce",  <br>   <br>  # optional and recommended:  <br>  covariates = "none",  <br>  description = "mice MYC reduced expression",  <br>  platform = "GPL6246", # use GEO platform ID  <br>  sample_type = "liver", # use BRENDA ontology  <br>   <br>  # optional cut-off attributes.  <br>  # specifying them can facilitate the extraction of signatures.  <br>  logfc_cutoff = NULL,  <br>  p_value_cutoff = NULL,  <br>  adj_p_cutoff = 0.05,  <br>  score_cutoff = 5,  <br>   <br>  # other optional built-in attributes:  <br>  keywords = c("Myc", "KO", "longevity"),  <br>  cutoff_description = NULL,  <br>  author = NULL,  <br>  PMID = 25619689,  <br>  year = 2015,  <br>   <br>  # example of customized attributes:  <br>  others = list("animal_strain" = "C57BL/6")  <br>)  <br>  <br># Create difexp object  <br>difexp <- base::readRDS(file.path(system.file("extdata", package = "OmicSignature"), "difmatrix_Myc_mice_liver_24m.rds")) %>% dplyr::rename(feature_name = ensembl)  <br>colnames(difexp) <- OmicSignature::replaceDifexpCol(colnames(difexp))  <br>  <br># Create signature object  <br>signature <- difexp %>%  <br>  dplyr::filter(abs(score) > metadata$score_cutoff & adj_p < metadata$adj_p_cutoff) %>%  <br>  dplyr::select(probe_id, feature_name, score) %>%  <br>  dplyr::mutate(direction = ifelse(score > 0, "+", "-"))  <br>  <br># Create signature object  <br>omic_signature <- OmicSignature::OmicSignature$new(  <br>  metadata = metadata,  <br>  signature = signature,  <br>  difexp = difexp  <br>)  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Organism is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m created.  <br>  <br># Add signature to database  <br>SigRepo::addSignature(  <br>  conn_handler = conn_handler,    # A handler contains user credentials to establish connection to a remote database  <br>  omic_signature = omic_signature, # An R6 object obtained from OmicSignature::OmicSignature()  <br>  visibility = FALSE,             # Whether to make signature public or private. Default is FALSE.  <br>  return_signature_id = FALSE,    # Whether to return the uploaded signature id. Default is FALSE.  <br>  verbose = TRUE                  # Whether to print diagnostic messages. Default is TRUE.  <br>)  <br>#> Uploading signature metadata to the database...  <br>#> Saving difexp to the database...  <br>#> now dyn.load("C:/Users/vicna/AppData/Local/R/win-library/4.5/curl/libs/x64/curl.dll") ...  <br>#> Adding user to the signature access table of the database...  <br>#> Adding signature feature set to the database...  <br>#> Finished uploading.  <br>#> ID of the uploaded signature: 168|

# Search for a list of signatures

---

  

The searchSignature() function allows users to search for all or a specific set of signatures that are available in the database.

## Example 1: Search for all signatures

|   |
|---|
|signature_tbl <- SigRepo::searchSignature(conn_handler = conn_handler)|

  

## Example 2: Search for a specific signature, e.g., signature_name = “LLFS_Aging_Gene_2023”.

|   |
|---|
|signature_tbl <- SigRepo::searchSignature(  <br>  conn_handler = conn_handler,  <br>  signature_name = "LLFS_Aging_Gene_2023"  <br>)|

# Retrieve a list of omic signatures

---

  

The getSignature() function allows users to retrieve a list of omic signature objects that they are PUBLICLY available in the database.

IMPORTANT NOTE:

·         Users MUST HAVE an editor or admin account to use this function.

·         Users can ONLY RETRIEVE a list of signatures that are publicly available in the database including their own uploaded signatures.

·         If a signature is PRIVATE and belongs to other user in the database, users will need to be given an editor permission from its owner in order to access, retrieve, and edit their signatures.

## Example 1: Retrieve all signatures that are publicly available or owned by the user in the database

|   |
|---|
|signature_list <- SigRepo::getSignature(conn_handler = conn_handler)  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m_v1 created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m_v2 created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m_v3 created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m_v4 created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object LLFS_Aging_Gene_2023 created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object CYP181 knockdown in breast cancer cell line created.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m created.|

## Example 2: Retrieve a specific signature that is publicly available or owned by the user in the database, e.g., signature_name = “LLFS_Aging_Gene_2023”

|   |
|---|
|LLFS_oms <- SigRepo::getSignature(  <br>  conn_handler = conn_handler,  <br>  signature_id = "LLFS_Aging_Gene_2023"  <br>)  <br>#> There are no signatures returned from the search parameters.|

# Deleting a signature

---

  

The deleteSignature() function allows users to delete a signature from the database.

IMPORTANT NOTE:

·         Users MUST HAVE an editor or admin account to use this function.

·         Users can ONLY DELETE their own uploaded signatures or were given an editor permission from its owner to access, retrieve, and edit their signatures.

·         Users can ONLY DELETE a signature one at a time.

For example: You want to remove signature_name = “LLFS_Aging_Gene_2023” from the database.

|   |
|---|
|# 1. Let's search for signature_name = "LLFS_Aging_Gene_2023" in the database  <br>signature_tbl <- SigRepo::searchSignature(  <br>  conn_handler = conn_handler,  <br>  signature_name = "LLFS_Aging_Gene_2023"  <br>)  <br>  <br># 2. If the signature exists, remove it from the database  <br>if(nrow(signature_tbl) > 0){  <br>  SigRepo::deleteSignature(  <br>conn_handler = conn_handler,  <br>signature_id = signature_tbl$signature_id  <br>  )  <br>}  <br>#> Error in SigRepo::deleteSignature(conn_handler = conn_handler, signature_id = signature_tbl$signature_id):  <br>#> 'signature_id' must have a length of 1 and cannot be empty.|

# Update a signature

---

  

The updateSignature() function allows users to update a specific signature in the SigRepo database.

IMPORTANT NOTE:

·         Users MUST HAVE an editor or admin account to use this function.

·         Users can ONLY UPDATE their own uploaded signatures or were given an editor permission from its owner to access, retrieve, and edit their signatures.

·         Users can ONLY UPDATE a signature one at a time.

For example: If the platform information in the previous uploaded signature, “Myc_reduce_mice_liver_24m”, is incorrect, and you wish to update the platform information with the correct value, e.g., platform = “GPLXXXXX”. You can use the updateSignature() function as follows:

|   |
|---|
|# 1. Revise the metadata object with new platform = GPLXXXXX  <br>metadata_revised <- base::list(  <br>  # required attributes:  <br>  signature_name = "Myc_reduce_mice_liver_24m",  <br>  organism = "Mus Musculus",  <br>  direction_type = "bi-directional",  <br>  assay_type = "transcriptomics",  <br>  phenotype = "Myc_reduce",  <br>   <br>  # optional and recommended:  <br>  covariates = "none",  <br>  description = "mice MYC reduced expression",  <br>  platform = "GPLXXXXX", # use GEO platform ID  <br>  sample_type = "liver", # use BRENDA ontology  <br>   <br>  # optional cut-off attributes.  <br>  # specifying them can facilitate the extraction of signatures.  <br>  logfc_cutoff = NULL,  <br>  p_value_cutoff = NULL,  <br>  adj_p_cutoff = 0.05,  <br>  score_cutoff = 5,  <br>   <br>  # other optional built-in attributes:  <br>  keywords = c("Myc", "KO", "longevity"),  <br>  cutoff_description = NULL,  <br>  author = NULL,  <br>  PMID = 25619689,  <br>  year = 2015,  <br>   <br>  # example of customized attributes:  <br>  others = list("animal_strain" = "C57BL/6")  <br>)  <br>  <br># 2. Create difexp object  <br>difexp <- readRDS(file.path(system.file("extdata", package = "OmicSignature"), "difmatrix_Myc_mice_liver_24m.rds")) %>% dplyr::rename(feature_name = ensembl)  <br>colnames(difexp) <- OmicSignature::replaceDifexpCol(colnames(difexp))  <br>  <br># 3. Create signature object  <br>signature <- difexp %>%  <br>  dplyr::filter(abs(score) > metadata_revised$score_cutoff & adj_p < metadata_revised$adj_p_cutoff) %>%  <br>  dplyr::select(probe_id, feature_name, score) %>%  <br>  dplyr::mutate(direction = ifelse(score > 0, "+", "-"))  <br>  <br># 4. Create the updated OmicSignature object  <br>updated_omic_signature <- OmicSignature::OmicSignature$new(  <br>  signature = signature,  <br>  metadata = metadata_revised,  <br>  difexp = difexp  <br>)  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Organism is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m created.  <br># Now, let's search for Myc_reduce_mice_liver_24m in the database  <br># in which we would like to revise the value of platform to GPLXXXXX  <br>signature_tbl <- SigRepo::searchSignature(  <br>  conn_handler = conn_handler,  <br>  signature_name = metadata_revised$signature_name  <br>)  <br>  <br># Update the signature with the revised omic_signature object  <br>if(nrow(signature_tbl) > 0){  <br>  SigRepo::updateSignature(  <br>conn_handler = conn_handler,  <br>signature_id = signature_tbl$signature_id,  <br>omic_signature = updated_omic_signature  <br>  )  <br>}  <br>#> Warning in private$checkMetadata(metadata, v = print_message): Platform is not  <br>#> in the predefined list. Ignore this message if intentional.  <br>#>   [Success] OmicSignature object Myc_reduce_mice_liver_24m created.  <br>#>  signature_id = '168' has been updated.  <br>Finally, let's look up signature_name = "Myc_reduce_mice_liver_24m" and see if the value of platform has been changed.  <br>signature_tbl <- SigRepo::searchSignature(  <br>  conn_handler = conn_handler,  <br>  signature_name = "Myc_reduce_mice_liver_24m"  <br>)  <br>  <br>if(nrow(signature_tbl) > 0){  <br>  knitr::kable(  <br>signature_tbl,  <br>row.names = FALSE  <br>  )  <br>}|**