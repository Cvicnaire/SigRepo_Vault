Functions that search the feature tables

-  getFeatures <- retrieve the entire feature table. the only inputs are "assay_type" (e.g. transcriptomics) and "organism" (e.g. Homo sapiens).

If the user wants to know what feature name (ensembl id) to use for a gene (e.g. CCR6 gene), they retrieve the entire table and search for the feature name themself.