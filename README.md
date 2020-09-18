# mongodb-gcs-backup

This project aims to provide a simple way to perform a MongoDB server/db backup using `mongo-tools` and to upload it to Google Cloud Storage. It was greatly inspired from [`birotaio
/mongodb-gcs-backup`](https://github.com/birotaio/mongodb-gcs-backup).

### Configuration

The following table lists the configurable parameters you can set up.

Environment Variable | Required | Default | Description
---------------------|----------|---------|-------------
`BACKUP_DIR` | No | `/tmp` | The path where the `mongodump` result will be temporarily stored.
`BOTO_CONFIG_PATH` | No | `/root/.boto` | The path where `gsutil` will search for the boto configuration file.
`GCS_BUCKET` | Yes |  | The bucket you want to upload the backup archive to.
`GCS_KEY_FILE_PATH` | NO |  | The location where the GCS serviceaccount key file will be mounted. If not provided, gsutil will try to excute in the container context.(This would work if using Google Cloud Run with right service account)
`MONGODB_HOST` | No | `localhost` | The MongoDB server host.
`MONGODB_PORT` | No | `27017` | The MongoDB port.
`MONGODB_DB` | No |  | The database to backup. By default, a backup of all the databases will be performed.
`MONGODB_USER` | No |  | The MongoDB user if any.
`MONGODB_PASSWORD` | No |  | The MongoDB password if any. (Please make sure the password is URL encoded!)
`MONGODB_OPLOG` | No |  | `true` if you want to perform a `mongodump` with the `--oplog` flag, false otherwise.
`SLACK_ALERTS` | No |  | `true` if you want to send Slack alerts in case of failure.
`SLACK_WEBHOOK_URL` | No |  | The Incoming WebHook URL to use to send the alerts.
`SLACK_CHANNEL` | No |  | The channel to send Slack messages to.
`SLACK_USERNAME` | No |  | The user to send Slack messages as.
`SLACK_ICON` | No |  | The Slack icon to associate to the user/message.


You can set all of these variables within your `values.yaml` file under the `env` dict key.


### Usage

#### Run locally

You can run the script locally:

    cd /path/to/mongodb-gcs-backup
    chmod +x backup.sh
    GCS_BUCKET=<gs://bucket_name> \
    ./backup.sh

Please note that you can set any environment variable described in the previous section! As an example, to enable the Slack alerts on failure:

    SLACK_ALERTS=true \
    SLACK_WEBHOOK_URL=<webhook_url> \
    SLACK_CHANNEL=<slack_channel> \
    SLACK_USERNAME=<slack_username> \
    SLACK_ICON=<slack_icon> \
    GCS_BUCKET=<gs://bucket_name> \
    ./backup.sh


#### Run within Google Cloud Run

##### Build the container to upload to your own gcr.io repor

    gcloud builds submit --tag gcr.io/<GCP project id>/<image name>

##### Run with Google Cloud Run