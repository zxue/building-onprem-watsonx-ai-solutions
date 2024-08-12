
# How to Deploy WDU to OpenShift

Watson Document Understanding is a standard embeddable library designed to be the primary delivery method for state-of-the-art document conversion in IBM products. It is an inner source library built on top of the best AI OSS enhanced with input from Research, Watson Discovery, Automation and GBS. Watson Document Understanding is a component of Watson Core, and it is developed under Watson Core’s inner source model.

## Deployment

Whitelist the JFrog image registry if necessary (for air gap deployment).

```
docker-na.artifactory.swg-devops.com/wcp-ai-foundation-team-docker-local/wdu-runtime
```

Download and unzip the wdu_deploy.zip file (from Box folder)

Update the kustomization.yaml file

```
cd path/to/deploy_folder
```

make the following changes and save the Kustomization file.

choose the delopment option, e.g. standalone vs. multi
```
    #- overlays/standalone/
    - overlays/multi/
    #  - overlays/mm/operator
    #  - overlays/mm/static
```

change wdu version from 0.12.0 to the latest release.

```
    - name: wdu-runtime
      newName: docker-na.artifactory.swg-devops.com/wcp-ai-foundation-team-docker-local/wdu-runtime
      newTag: 0.12.0
```

Update secrets. Run the following commands to update all kustomize Secrets and ConfigMaps.

```
export ARTIFACTORY_USERNAME="xxx"
export ARTIFACTORY_API_KEY="xxx"

grep -rl "<artifactory-username>" ./ | xargs sed -i'' -e 's/<artifactory-username>/'"${ARTIFACTORY_USERNAME}"'/g' 
grep -rl "<artifactory-password>" ./ | xargs sed -i'' -e 's/<artifactory-password>/'"${ARTIFACTORY_API_KEY}"'/g'
```


Build Kubernetes resources with kustomize.

```
kubectl kustomize . > resources.yaml
```

Delete current deployments before redeploying. If this is the first time deploying, you can skip this step.
```
kubectl delete -f resources.yaml
```
Log in to the OpenShift cluster using `oc login ...` and apply resources to the cluster.

```
oc login xxx
oc create namespace wdu
kubectl apply -f resources.yaml
```

Check pods status and troubleshoot any issue. 

- the default storage class supports file system
- delete the two pods in the wdu namespace if necessary

## Access to the REST API

After you have deployed WDU you can access the installation through an Openshift Route.

You can fetch the available route by running the following command:
```
oc project wdu
oc get route main-route
```

By default, WDU's route is named main-route and is located in the same namespace as the deployment.

The endpoint listed under HOST will be the url of the WDU e.g. http://xxx.com. For a specific example, the swagger UI would be available at http://xxx.com/docs.

## Troubleshooting

One common issue is that the credentials were missing or incorrect. For example, the model download pod may have errors that look like this.

```
There was a problem when trying to write in your cache folder (/.cache/huggingface/hub). You should set the environment variable TRANSFORMERS_CACHE to a writable directory.
dotenv failed, probably due to invocation as script instead of module
{"channel": "DU.TOOLKIT.ARTUTILS", "exception": null, "level": "warning", "message": "Request to https://na.artifactory.swg-devops.com/artifactory/wcp-ai-foundation-team-watson-du-generic-local/models/doc_processing/doc_processing_v0-0-1_2023-09-23_19_01_36-991706.zip returned status code 401", "num_indent": 0, "thread_id": 139754192619328, "timestamp": "2024-06-26T03:20:55.721381"}

HTTPError: 401 Client Error:  for url: 
https://na.artifactory.swg-devops.com/artifactory/wcp-ai-foundation-team-watson-
du-generic-local/models/doc_processing/doc_processing_v0-0-1_2023-09-23_19_01_36
-991706.zip
```

Check and update the two credentials files, pull_secret.json under patches | pul_secret | secrets and artifactory_vars.env under patches | movels_pvc | secrets.

pull_secret.json

```
{
    "auths": {
        "docker-na.artifactory.swg-devops.com/wcp-ai-foundation-team-docker-local": {
            "username": "xxx",
            "password": "xxx",
            "email": "xxx"
        },
        "docker-na-public.artifactory.swg-devops.com/wcp-ai-foundation-team-docker-local": {
            "username": "xxx",
            "password": "xxx",
            "email": "xxx"
        },
        "cp.icr.io/cp": {
            "username": "cp",
            "password": "<entitlement-key>"
        }
    }
}
```

artifactory_vars.env

```
ARTIFACTORY_USERNAME=xxx
ARTIFACTORY_API_KEY=xxx
```