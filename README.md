# Setting Up Databricks Cluster via CLI

This guide walks you through setting up a Databricks cluster using the Databricks CLI (Command Line Interface). The Databricks CLI provides a convenient way to interact with Databricks resources programmatically.

## Prerequisites

Before starting, ensure you have the following prerequisites:

- Python installed on your machine.
- `pip` package manager installed.
- Databricks account

## Installation

First, install the Databricks CLI using `pip`:

```bash
pip install databricks-cli
```


## Authentication

To authenticate with Databricks, you'll need the Databricks URL HOST and Access Token. You can obtain the Access Token from your Databricks account settings:

1. Navigate to your Databricks account settings.
2. Click on "Developer".
3. Next, click on "Manage" under "Access token" to generate a new token.

Once you have the Access Token, configure it using:

```bash
databricks configure --token
```

## Cluster Configuration and Library Setup

Before setting up the cluster and installing libraries, you need to configure the cluster and specify any required libraries.

### Cluster Configuration

Create a `cluster-config.json` file with your cluster configuration. Customize this file based on your requirements. An example configuration might look like this:

*JSON file for Databricks on the AWS platform:*
```json

{
  "cluster_name": "SampleCluster",
  "spark_version": "7.3.x-scala2.12",
  "num_workers": 1,
  "node_type_id": "i3.xlarge",
  "driver_node_type_id": "i3.xlarge",
  "custom_tags": {
    "Infa_Generated_Tag": "Infa_Generated_Tag"
  },
  "spark_env_vars": {
    "PYSPARK_PYTHON": "/databricks/python3/bin/python3"
  },
  "enable_elastic_disk": false,
  "aws_attributes": {
    "ebs_volume_count": 3,
    "zone_id": "us-east-1e",
    "ebs_volume_size": 100,
    "spot_bid_price_percent": 100,
    "first_on_demand": 1
  }
}

```
*JSON file for Databricks on the Azure platform:*
```json
{
  "cluster_name": "SampleCluster",
  "spark_version": "7.3.x-scala2.12",
  "num_workers": 1,
  "node_type_id": "Standard_DS3_v2",
  "driver_node_type_id": "Standard_DS3_v2",
  "custom_tags": {
    "Infa_Generated_Tag": "Infa_Generated_Tag"
  },
  "spark_env_vars": {
    "PYSPARK_PYTHON": "/databricks/python3/bin/python3"
  },
  "enable_elastic_disk": false
}
```
Then, proceed to create the cluster using the following command:

```bash
databricks clusters create --json "$(cat /path/to/cluster-config.json)"
```
## Library Setup
After configuring the cluster, you may need to install additional libraries. Create a cluster-requirements.json file listing the libraries you need:

```json
{
  "cluster_id": "cluster-id",
  "libraries": [
    {"pypi": {"package": "package-name", "version": "1.0.0"}}
  ]
}
```

Replace "cluster-id" with the ID of your cluster obtained from:
```bash 
databricks clusters list
```

## Installing Required Libraries
Once the cluster is configured and libraries are specified, proceed to install the required libraries using the following command:
```bash
databricks libraries install --json "$(cat /path/to/cluster-requirements.json)"
```

## Import file from local
To import a file from your local machine to the Databricks workspace, follow these steps:

*for import file*

```bash
databricks workspace import --file /path/to/local/file.py

```
*for import directory*
```bash
  databricks workspace import-dir sourcepath/to/dir targetpath/to/dir
```

*or* 
 
create directory

```bash
databricks fs mkdir dbfs:/<directory>
```
Replace <directory_path> with the path where you want to create the directory in Databricks.


copy file from local to databricks directory 
```bash
databricks fs cp -r sourcepath/to/dir targetpath/to/dir
```
Replace /local/path/to/source_dir with the path to the source directory on your local system, <directory_path> with the path to the directory in Databricks where you want to copy the files, and target_dir with the name you want to give to the target directory in Databricks.

## Creating a Databricks jobs
The following the  example uses the CLI to create a Databricks jobs. This task run the specified Databricks notebook.
Create a `Databricks_jobs.json` 

```json
{
  "name": "My hello notebook job",
  "tasks": [
    {
      "task_key": "my_hello_notebook_task",
      "notebook_task": {
        "notebook_path": "/Workspace/Users/someone@example.com/hello",
        "source": "WORKSPACE"
      },
      "libraries": [
        {
          "pypi": {
            "package": "wheel==0.41.2"
          }
        }
      ],
      "new_cluster": {
        "spark_version": "13.3.x-scala2.12",
        "node_type_id": "i3.xlarge",
        "num_workers": 1,
        "spark_env_vars": {
          "PYSPARK_PYTHON": "/databricks/python3/bin/python3"
        }
      }
    }
  ]
}
```

run the command in terminal
```bash
databricks jobs create --json "$(cat /path/to/databricks_jobs.json)"
```


To check if your Databricks job is working, you can use the following command in your terminal:

  ```bash
  databricks jobs list
  ```

This command lists all the jobs currently configured in your Databricks workspace. You can inspect the output to verify if your job appears in the list and check its status.

Make sure to execute this command in your terminal where the Databricks CLI is installed and configured with access to your Databricks workspace.
