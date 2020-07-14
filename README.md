# bqup-starter

A starter template for automated [bqup](https://github.com/thinkingmachines/bqup)s

## Setup

### Github Actions

####  Using a template

1. Create a repository and choose `bqup-starter` as template
    or go to https://github.com/thinkingmachines/bqup-starter and click use this template.

2. Modify the schedule to run in `bqup.yml`.

3. Go to Settings -> Secrets and add the following secret environment variables
    - `GCP_SA_KEY` - Generated Service Account key (JSON) that has access to the dataset you want to backup
    - `GCP_PROJECT_ID` - Project ID in GCP of the project you want to backup

#### Manual

1. Create a repository in Github. eg. `<name>-bqups`

2. Clone the repository to your local machine

3. Create a `.github/workflows` directory inside the project folder
    ```sh
    mkdir -p .github/workflows
    ```

4. Create a `yml` file inside `.github/workflows` and copy the following contents. You can name it anything you want but for simplicity, you can just name it `bqup.yml`.

    ```yaml
    name: bqup
    on:
      schedule:
        - cron: '0 4 * * *' # Runs the bqup daily at 4am UTC. Feel free to change this to your needs
    jobs:
      bqup:
        name: Bqup
        runs-on: ubuntu-latest
        steps:
          - name: Run Checkout
            uses: actions/checkout@v2
            with:
              ref: master
          - name: Setup Python
            uses: actions/setup-python@v2
            with:
              python-version: '3.7'
          - name: Run Bqup
            env:
              GOOGLE_APPLICATION_CREDENTIALS: keyfile.json
              GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
              GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
            run: |
              echo "$GCP_SA_KEY" > keyfile.json
              pip3 install bqup
              bqup -p $GCP_PROJECT_ID -f -d bqups
          - name: Push bqup
            run: |
              git add -A bqups
              git status
              git config user.name BQUP
              git config user.email engineering@thinkingmachin.es
              git diff-index --quiet HEAD || (git commit -m "Automated bqup" && git push origin master)
    ```

5. Go to Settings -> Secrets in Github and add the following secret environment variables
    - `GCP_SA_KEY` - Generated Service Account key (JSON) that has access to the dataset you want to backup
    - `GCP_PROJECT_ID` - Project ID in GCP of the project you want to backup

6. (Optional) Add a badge to your `README.md` to easily see if the bqup successfully ran.
    ```md
    ![](https://github.com/<OWNER>/<REPOSITORY-NAME>/workflows/bqup/badge.svg)
    ```


## References
* https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow
