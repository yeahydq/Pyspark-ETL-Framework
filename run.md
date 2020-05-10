https://github.com/ensonmj/pyspark-template


## Step 1. local development

  1. vscode (setup .vscode/launch.json)
  2. [normal run] choose the 'etl_job.py' and 'run' or 'run code'
  3. [unit test] choose the 'test_etl_job.py' and 'run code'

## Step 2. local unit test

```bash
# better create virtual env
python -m unittest tests/test_*.py
```

## Step 3. cluster testing

   0. Create folder

```bash
   mkdir -p /tmp/spark/logs
```

   1. local spark-submit

```bash
sudo pip3 install pipenv

sh ./build_dependencies.sh

$SPARK_HOME/bin/spark-submit \
--master 'local[*]' \
--py-files packages.zip \
--files configs/JobRPM001_config.json \
jobs/etl_job.py

```

   2. cluster spark-submit (need to run within the cluster)

```bash
git config --global url."https://0b4446c109ba648212926902510d456a9f0a0b78@github.com".insteadOf "https://github.com"

git clone https://github.com/yeahydq/Pyspark-ETL-Framework.git

wget https://github.com/yeahydq/Pyspark-ETL-Framework/archive/master.zip
unzip master.zip
cd Pyspark-ETL-Framework*

SPARK_HOME=/opt/spark
$SPARK_HOME/bin/spark-submit \
--master spark://54.255.135.54:7077 \
--py-files packages.zip \
--files configs/etl_config.json \
jobs/etl_job.py


3. run in GCP


gcloud dataproc clusters create my-cluster --region=asia-east1 --image-version 1.5.1-debian10

zip -ru9 packages.zip dependencies -x dependencies/__pycache__/\*

gsutil cp jobs/etl_job.py gs://dy-awesome-bucket/demo/etl_job.py
gsutil cp packages.zip gs://dy-awesome-bucket/demo/packages.zip
gsutil cp configs/JobRPM001_config.json gs://dy-awesome-bucket/demo/JobRPM001_config.json
gsutil cp tests/test_data/recipes/recipes.json gs://dy-awesome-bucket/demo/recipes.json
gcloud dataproc jobs submit pyspark \
--cluster=my-cluster \
--region=asia-east1 \
--py-files=packages.zip \
--files configs/JobRPM001_config.json,configs/transformation.sql,tests/test_data/recipes/recipes.json \
jobs/etl_job.py -- \
--files JobRPM001_config.json,transformation.sql

gcloud dataproc jobs submit pyspark \
--cluster=my-cluster \
--region=asia-east1 \
--py-files=gs://dy-awesome-bucket/demo/packages.zip \
gs://dy-awesome-bucket/demo/etl_job.py -- \
--files gs://dy-awesome-bucket/demo/JobRPM001_config.json


gcloud dataproc clusters delete my-cluster --region=asia-east1
