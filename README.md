# Building On-Premises IBM Generative AI Empowered RAG Solutions to Enhance User Productivity

Watsonx is IBM's generative AI platform, officially launched on May 9, 2023 at the annual Think conference. The newly branded platform includes watsonx.ai, watsonx.data and watsonx.governance. You can find more details on watsonx at https://www.ibm.com/watsonx. 

As customers increasingly adopt cloud services available from IBM, Microsoft, Amazon, Google and others, customers may choose to stay on-prem for some workloads for security and other business reasons. Fortunately, IBM offers a complete on-prem infrastructure for generative AI services that not all service providers can do to meet unique business requirements. 

This document focuses on the on-prem watsonx.ai (current version 5.0.1) infrastructure deployment and AI powered retrieval-augmented generation solution development. It also covers the use of watsonx Orchestrate to build a user friendly chatbot without custom code required.

## The On-Prem watsonx.ai Deployment

The on-prem watsonx.ai Deployment including several key components.

- Red Hat OpenShift cluster in a server or virtualized environment
- IBM Cloud Pak for Data control plane (not the full suite)
- IBM watsonx.ai
- Nvidia GPU (hardware) and Operators for OpenShift
- IBM watsonx Orchestrate
- IBM Watson Document Understanding (WDU)
- IBM watsonx Discovery aka ElasticSearch
- IBM Fusion Storage aka General Parallel File System (GPFS)

### Meeting Prerequisites for watsonx.ai

For the customer engagement we worked with OpenShift 4.14. However, it is highly recommended that you work with OpenShift 4.15 or higher for supportability.

Before installing watsonx.ai, it is important that you meet all prerequisites, including both hardware and software. For more details check [System requirements](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=planning-system-requirements).

Also, install the latest version of OpenShift `oc` command tool and [Cloud Pak for Data cli](https://github.com/IBM/cpd-cli), `cpd-cli`.

### OpenShift AI

OpenShift AI is one dependency for watsonx.ai 5.0.0 and future releases. Follow the steps outlined in [Preparing OpenShift AI for use in IBM Cloud Pak for Data](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.8/html/installing_and_uninstalling_openshift_ai_self-managed/preparing-openshift-ai-for-ibm-cpd_prepare-openshift-ai-ibm-cpd.html#preparing-openshift-ai-for-ibm-cpd_prepare-openshift-ai-ibm-cpd)

Do not use the [Installing and deploying OpenShift AI](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.8/html/installing_and_uninstalling_openshift_ai_self-managed/installing-and-deploying-openshift-ai_install#installing-and-deploying-openshift-ai_install) document. Instead, use the document for CP4D.

### Nvidia Operators

Watsonx.ai requires GPUs. When Nvidia GPUs are used, install two required operators for OpenShift per documentation. More details on [Installing operators for services that require GPUs](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=software-installing-operators-services-that-require-gpus). If other branded GPUs are used, follow instructions from IBM/Red Hat or the hardware provider.

- Complete Installing the [Node Feature Discovery (NFD) Operator](https://docs.nvidia.com/datacenter/cloud-native/openshift/23.9.2/install-nfd.html) in the NVIDIA GPU Operator on Red Hat OpenShift Container Platform documentation.
- Complete Installing the [NVIDIA GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/openshift/23.9.2/install-gpu-ocp.html) in the NVIDIA GPU Operator on Red Hat OpenShift Container Platform documentation.

### Deploying Cloud Pak for Data control Plane

The Cloud Pak for Data (CP4D) control plane is required before you can install watsonx.ai. 

Check details on [Installing the IBM Cloud Pak for Data control plane](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=data-installing-cloud-pak).

Note that you can install some of CP4D components or services in the same namespace or different ones. For the latter, you will need to take an extra step, [Tethering projects to the IBM Cloud Pak for Data control plane](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=data-tethering-projects-control-plane).

For more details on CP4D components, check [Determining which IBM Cloud Pak for Data components to install](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=information-determining-which-components-install.

### Installing watsonx.ai

Ensure that you have installed all prerequisites, for example, OpenShift AI and Nvidia operators. Also, ensure that all of the components that are associated with an instance of Cloud Pak for Data are installed at the same release, e.g. version 5.0.1.

For detailed instructions, check [Installing IBM watsonx.ai](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=watsonxai-installing).

### Adding watsonx.ai foundation models

You are now ready to add one or more foundation models or large language models (LLM). See installation details at [Adding foundation models to IBM watsonx.ai](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=setup-adding-foundation-models).

### Adding custom models

Out of the box, IBM watsonx.ai provides support for two dozens or so foundation models, include the Granite series, Meta's Llama3 models, Google's flan models, and Mixtral models. You can find the complete list of [supported models](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=solutions-supported-foundation-models) here. 

However, you can add custom models that are available at HuggingFace or else where. For more details, check [Planning to deploy a custom foundation model](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=model-planning-deploy-custom-foundation)

Once watsonx.ai and LLMs are deployed, you can build python notebooks to work with the large language models that are deployed in the environment.

## Installing watsonx Orchestrate



### Multicloud Object Gateway 

https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=orchestrate-installing

Multicloud Object Gateway is installed and configured.	If this task is not complete, see Installing Multicloud Object Gateway.
The secrets that enable watsonx Orchestrate to connect to Multicloud Object Gateway exist.	If this task is not complete, see Creating secrets for services that use Multicloud Object Gateway.

https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=software-installing-multicloud-object-gateway


### Knative Eventing


Red Hat OpenShift Serverless Knative Eventing is installed and configured.	If this task is not complete, see Installing Red Hat OpenShift Serverless Knative Eventing.
IBM App Connect in containers is installed and configured.	If this task is not complete, see Installing App Connect in containers.


export ES_URL=https://b0fbe999-393c-4323-975e-daeff4aca6d5.bn2a2uid0up8mv7mv2ig.databases.appdomain.cloud:32199

export ES_USER=ibm_cloud_40ac9d23_9fd8_4185_984e_406dd7610175

export ES_PASSWORD=813658601b6c9da84e103ae8413ca91df8f01f459b4fa29467baa9625dbeba82

export MODEL_ID="sentence-transformers/all-MiniLM-L6-v2"



#run locally

eland_import_hub_model \
  --url $ES_URL \
  -u $ES_USER -p $ES_PASSWORD --insecure \
  --hub-model-id $MODEL_ID \
  --task-type text_embedding \
  --start

#or use docker

docker run -it --rm elastic/eland \
    eland_import_hub_model \
      --url $ES_URL -u $ES_USER -p $ES_PASSWORD --insecure \
      --hub-model-id $MODEL_ID \
      --start

python3 -m pip install 'eland[pytorch]<=8.11'

spec:
  watsonAssistants:
    config:
      configOverrides:
        store:
          extra_vars:
            store:
              NODEJS_HEAP_SIZE: 1792
          resources:
            store:
              limits:
                memory: 2Gi
              requests:
                memory: 2Gi

{
    "knn": {
        "field": "embedding",
        "query_vector_builder": {
            "text_embedding": {
                "model_id": "sentence-transformers__all-minilm-l6-v2",
                "model_text": "$QUERY"
            }
        },
        "k": 10,
        "num_candidates": 100
    },
    "_source": [
        "id",
        "text"
    ]
}

## Acknowledgement

I'd like to acknowledge many IBMers who contributed to the work upon which the document is based.

- IBM watsonx sales team - Travis Jeanneret, Will Hawkins, Gary Walker, MIKE MCMAHON
- IBM Client Engineering team - DARREN D'Amato, Tabitha Brett, Vahi Guner, Jeniya Tabassum, Alex Cravalho, Ryan Menossi
- IBM Expert Labs -  Darshan Vala, Jun Ahn  
- IBM Watsonx product teams -  Albert Grankin, Angad Singh, Casey Honaker, Zach Shu, MANU THAPAR,  Mike Whitley