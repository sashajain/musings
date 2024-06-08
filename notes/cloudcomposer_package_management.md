# Pre-existing packages

Cloud Composer contains packages according to: 
https://cloud.google.com/composer/docs/concepts/versioning/composer-versions#images

# To get new packages

...  from pip + extra artifact registry, in Cloud Composer bucket create `/config/pip/pip.conf` with:

```
[global]
extra-index-url = https://us-central1-python.pkg.dev/example-project/example-repository/simple/
```

Update based on local requirements.txt via:

```
gcloud composer environments update ENVIRONMENT_NAME \
    --location LOCATION \
     --update-pypi-packages-from-file requirements.txt
```

