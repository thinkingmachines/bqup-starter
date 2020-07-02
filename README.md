# bq-starter
![](https://github.com/thinkingmachines/heartbeat-bqups/workflows/Bqup/badge.svg)
> Starter template for bq and bqup

## Setup

1. Create a new repository and choose this as template

2. Modify the schedule to run in `bqup.yml`.

3. Go to Settings -> Secrets and add the following secret environment variables
    - `GCP_SA_KEY` - Generated Service Account key (JSON) that has access to the dataset you want to backup
    - `GCP_PROJECT_ID` - Project ID in GCP of the project you want to backup
