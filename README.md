# UK Policing - Public Domain Data
The UK Police Service, via the UK Home Office, makes a number of policing-related data sets available publically for download. These data sets are (usually) available for all 43 Forces in England and Wales and consist of the following three types of data:
- Reported crimes for a given location, in a given period per Force 
- Crime outcomes for all crimes reported in a given period per Force
- Stop-and-search statistics recorded in a given period by each Force

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
<details>
  <summary>police-data-stop-search</summary>

```PUT _index_template/police_data_stop_search_template
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
}```
</details>
  
## Logstash Pipelines

# Ingesting UK Police data into Elasticsearch

## Data file and folder structure


## Running Logstash to ingest the data
### Ingesting into a single Elasticsearch cluster


### Ingesting into an Elastic Data Mesh



