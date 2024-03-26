Reading and Writing Parquet Files from Oracle Cloud Object Storage
Introduction
This Python code demonstrates how to efficiently read and write Parquet files from Oracle Cloud Object Storage (OCI). Parquet files are widely used for storing columnar data and are commonly utilized in big data processing and analytics tasks.

Dependencies
ads library
pandas library
fsspec library
ocifs library
Setup
Install the required libraries by running:
Copy code
pip install ads pandas fsspec ocifs
Ensure you have appropriate access and permissions to Oracle Cloud Object Storage.
Replace the placeholders your_bucket and your_namespace with your OCI bucket and namespace details in the code.
Usage
Import the required libraries:

python
Copy code
import ads
from ads.common.auth import default_signer
import pandas as pd
import fsspec
from ocifs import OCIFilesystem
Set up authentication method:

python
Copy code
ads.set_auth("resource_principal")
Define your OCI bucket and namespace:

python
Copy code
bucket = "hosted-ds-datasets"
namespace = "bigadatascience"
Read a single large Parquet file:

python
Copy code
large_files = ["nyc_tlc/2009/01/data.parquet"]

for f in large_files:
    df = pd.read_parquet(f"oci://{bucket}@{namespace}/{f}",
                         storage_options=default_signer(), engine="pyarrow")
    print(f"File: {f}")
    print(df.head())
    print(f"Size: {df.shape}")
Read and concatenate multiple large Parquet files:

python
Copy code
from oci.auth.signers import get_resource_principals_signer

fs = OCIFileSystem(signer=get_resource_principals_signer())

relevant_files = fs.ls(f"{bucket}@{namespace}/nyc_tlc/2009/")

df = pd.concat([pd.read_parquet(f"oci://{f}/data.parquet",
                                storage_options=default_signer(), engine="pyarrow")
                for f in relevant_files], ignore_index=True)

print(df.shape)
Write the concatenated DataFrame to a new Parquet file in OCI:

python
Copy code
your_bucket = ""
your_namespace = ""

df.to_parquet(f"oci://{your_bucket}@{your_namespace}/taxi-data.parquet",
              storage_options=default_signer())
