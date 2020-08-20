# deploy-gae-action

This action allows you to deploy an application to [Google App Engine](https://cloud.google.com/appengine/).

This action was forked from https://github.com/rosslh/deploy-gae-action.

## Usage
```yaml
- name: Initialize Google Cloud SDK
  uses: dougpetronilio/deploy-gae-action@master
  with:
    service_account_email: ${{ secrets.GCP_SA_EMAIL }}
    service_account_key: ${{ secrets.GCP_SA_KEY }}
    project_id: ${{ secrets.PROJECT_ID }}
    gae_variables: ${{ secrets.GAE_VARIABLES }}
    gae_config_path: ./server/app.yaml

- name: Deploy app to Google App Engine
  run: |
    # This client-secret.json is converted by GCP_SA_KEY.
    gcloud auth activate-service-account ${{ secrets.GCP_SA_EMAIL }} --key-file=client-secret.json
    gcloud config set project ${{ secrets.PROJECT_ID }}
    gcloud -q app deploy app.yaml --promote
    
    # Suppose you need a cron task.
    gcloud -q app deploy cron.yaml
```
## Inputs

* `service_account_email`: (Required) The service account email which will be used for authentication.

* `service_account_key`: (Required) The service account key which will be used for authentication. This key should be [created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys), encoded as a [Base64](https://en.wikipedia.org/wiki/Base64) string (eg. `cat my-key.json | base64` on macOS or Linux), and stored as a [secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). 

* `project_id`: (Required) The project_id is google cloud platform project id. See this [page](https://console.cloud.google.com/home/dashboard) to get it.

* `gae_variables`: (Optional) App engine variables. This should be a json object encoded as a base64 string. This will be written into the app.yaml file at the root of your project. A sample variable file is shown [here](##sample-variable-file).

* `gae_config_path`: (Optional) The path of the GAE config file. Defaults to `./app.yaml`.

## Note
Use `272.0.0` Google Cloud SDK by default on [GitHub-hosted runners](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/software-installed-on-github-hosted-runners#ubuntu-1804-lts). If you need latest version, please refer to this [action](https://github.com/GoogleCloudPlatform/github-actions/tree/master/setup-gcloud). But in most cases, this is enough.

## Sample variable file
```json
{
  "beta_settings": {
    "cloud_sql_instances": "my_sql_instance"
  },
  "env_variables": {
    "DB_CONNECTION": "mysql",
    "DB_HOST": "127.0.0.1",
    "DB_PORT": 3306,
    "DB_USER": "mydbuser",
    "DB_DATABASE": "my_database",
    "DB_PASSWORD": "password",
    "DB_SOCKET": "/cloudsql/mys_sql_instance"
  }
}
```

On a Mac you can encode this into a base64 string by running the following command;

```shell script
base64 -i myvariables.json
```


## License
The scripts and documentation in this project are released under the [MIT License](LICENSE)
