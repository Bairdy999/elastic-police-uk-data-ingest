# UK Policing - Public Domain Data
The UK Police Service, via the UK Home Office, makes a number of policing-related data sets available publically for download. These data sets are (usually) available for all 43 Police Forces in England and Wales and consist of the following three types of data:
- Reported crimes for a given location, in a given period, per Police Force (Street data)
- Crime outcomes for all crimes reported in a given period per Police Force (Outcomes data)
- Stop-and-search statistics recorded in a given period by each Police Force (Stop-and-search data)

## Downloading UK Police Data
Data can be downloaded from the following links:

| Download Type | Raw URL | Description |
| -- | -- | -- |
| [Custom Download](https://data.police.uk/data/) | https://data.police.uk/data/ | Allows data for any period up to the last 3 years to be downloaded for individual, multiple, or all, Forces. The three data types listed above can be optionally included in the download |
| [Archive Dowbload](https://data.police.uk/data/archive/) | https://data.police.uk/data/archive | Allows all data for all Forces to be downloaded for predefined periods going back to December 2013. Note that each available download covers a number of months over several years, with overlaps |
  
> [!TIP]
> The latest [data archive](https://data.police.uk/data/archive/latest.zip) can be downloaded each month as soon as it's published. This allows the data to be kept up to date if required

# Why ingest into Elasticsearch?
## Rich Data Sets
Given the public nature of the data, the data content itself (including geopoint and location data) and the volume of data available, it makes an ideal candidate for ingesting into Elasticsearch. The data sets are suitable for exploring several concepts when using Elasticsearch and making use of its' latest capabilities.
## Data Mesh Usage
Additionally, given the segmented nature of the data into discrete data sets per UK Police Force, it is ideal for use in proof-of-concepts relating to implementing an [Elastic Data Mesh](https://github.com/Bairdy999/elastic-data-mesh). Data specific to a given Force can be ingested into each Elasticsearch cluster in such a data mesh and cross-cluster-search utilised to search across the data in all clusters in the data mesh.

# Elastic Stack Artefacts
## Index Templates
[police-data-stop-search.json](Elasticsearch/templates/police-data-stop-search.json)
<details>
  <summary>police-data-stop-search.json</summary>
  
```
PUT _index_template/police_data_stop_search_template
{
  "index_patterns": [
    police-data-stop-search*"
  ],
  "template": {
    "mappings": {
      "properties": {
        "force": {
          "properties": {
            "falls_within": {
              "type": "keyword",
              "ignore_above": 256
            },
            "reported_by": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "location": {
          "type": "geo_point"
        },
        "person": {
          "properties": {
            "age_range": {
              "type": "keyword",
              "ignore_above": 256
            },
            "gender": {
              "type": "keyword",
              "ignore_above": 256
            },
            "officer_defined_ethnicity": {
              "type": "keyword",
              "ignore_above": 256
            },
            "self_defined_ethnicity": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "policing_operation": {
          "properties": {
            "part_of": {
              "type": "boolean"
            },
            "name": {
              "type": "keyword",
              "ignore_above": 1024
            }
          }
        },
        "reporting": {
          "properties": {
            "month": {
              "type": "integer"
            },
            "period": {
              "type": "date",
              "format": "yyyy-MM",
              "copy_to": "stop_search_content"
            },
            "year": {
              "type": "integer"
            }
          }
        },
        "stop": {
          "properties": {
            "date": {
              "type": "date"
            },
            "legislation": {
              "type": "text"
            },
            "object_of_search": {
              "type": "keyword",
              "ignore_above": 256
            },
            "outcome": {
              "type": "keyword",
              "ignore_above": 256
            },
            "outcome_linked_to_search": {
              "type": "boolean"
            },
            "removal_of_clothing": {
              "type": "boolean"
            },
            "type": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
      }
    }
  }
}
```
</details>
    
## Logstash Pipelines

# Ingesting UK Police data into Elasticsearch

## Data file and folder structure
The data is downloaded in a single .zip file per download that contains a number of folders for each month/year, with a number of CSV files per force for the same period in each month folder. For example, given a download containing data for 2022:
- Folders in the downloaded .zip file will be of the format YYYY-MM, i.e. for 2022 it will be 2022-01 through to 2022-12
- For each Force, there are up to three CSV files for each month, for each of Street, Outcomes and Stop-and-search data
- The CSV files are of the format YYYY-MM-<force_name><data_type>.csv, e.g for Cambridgeshire, the following files will exist:
  - `2022-01-cambridgeshire-outcomes.csv`
  - `2022-01-cambridgeshire-stop-and-search.csv`
  - `2022-01-cambridgeshire-street.csv`
- The above is repeated for each Force in each month/year folder

To illustrate the file structure, the structure for 2022 is shown in the image below:  

<img width="876" height="562" alt="image" src="https://github.com/user-attachments/assets/599d41e0-4519-443d-b083-5f9e8b2d8ff4" />


## Running Logstash to ingest the data

Assuming the Police data has been unzipped to `/opt/data/UK/Police/crime-data`, the following file input path combinations can be used to ingest the data to Elasticsearch using Logstash. The CSV files themselves will be of the format:
- `/opt/data/UK/Police/crime-data/YYYY-MM/YYYY-MM-<force_name>-<data_type).csv`

| File Input Path | Files Matched |
| -- | -- |
!     `/opt/data/UK/Police/crime-data/*/*-stop-and-search.csv` | Stop-and-search CSV files for all Forces, for all year/month periods  | 

### Ingesting into a single Elasticsearch cluster


### Ingesting into an Elastic Data Mesh



