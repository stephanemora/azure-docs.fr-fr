---
title: Utiliser l’identité AAD avec votre service web
titleSuffix: Azure Machine Learning
description: Utilisez l’identité AAD avec votre service web dans Azure Kubernetes Service pour accéder à des ressources cloud au cours d’un scoring.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122637"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Utiliser Azure AD Identity avec votre service web de machine learning dans Azure Kubernetes Service

Dans cette rubrique de guide pratique, vous allez apprendre à attribuer une identité Azure Active Directory (AAD) à votre modèle Machine Learning déployé dans Azure Kubernetes Service. Le projet [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) permet aux applications d’accéder de manière sécurisée à des ressources cloud avec AAD à l’aide d’une [identité managée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) et de primitives Kubernetes. Votre service web peut ainsi accéder de manière sécurisée à vos ressources Azure sans avoir à incorporer des informations d’identification ou à gérer des jetons directement à l’intérieur de votre script `score.py`. Cet article décrit les étapes nécessaires à la création et à l’installation d’une identité Azure dans votre cluster Azure Kubernetes Service, ainsi qu’à l’attribution de l’identité à votre service web déployé.

## <a name="prerequisites"></a>Conditions préalables requises

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’[extension Azure Machine Learning pour Visual Studio Code](tutorial-setup-vscode-extension.md).

- Un accès à votre cluster AKS à l’aide de la commande `kubectl`. Pour plus d’informations, consultez [Se connecter au cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster).

- Un service web Azure Machine Learning déployé sur votre cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Créer et installer une identité Azure dans votre cluster AKS

1. Pour déterminer si le contrôle d’accès en fonction du rôle (RBAC) est activé dans votre cluster AKS, utilisez la commande suivante :

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Cette commande retourne la valeur `true` si le contrôle d’accès en fonction du rôle est activé. Cette valeur détermine la commande à utiliser à l’étape suivante.

1. Pour installer [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) dans votre cluster AKS, utilisez l’une des commandes suivantes :

    * Si le **contrôle d’accès en fonction du rôle (RBAC)** est activé dans votre cluster AKS, utilisez la commande suivante :
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Si le **contrôle d’accès en fonction du rôle (RBAC)** n’est pas activé dans votre cluster AKS, utilisez la commande suivante :
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        La sortie de la commande ressemble au texte suivant :

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Créez une identité Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) en suivant les étapes indiquées dans la page du projet AAD Pod Identity.

1. [Installez l’identité Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) en suivant les étapes indiquées dans la page du projet AAD Pod Identity.

1. [Installez la liaison d’identité Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) en suivant les étapes indiquées dans la page du projet AAD Pod Identity.

1. Si l’identité Azure créée à l’étape précédente ne se trouve pas dans le même groupe de ressources que votre cluster AKS, effectuez la procédure décrite dans [Définir les autorisations pour MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) en suivant les étapes indiquées dans la page du projet AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Affecter une identité Azure au service web de machine learning

Les étapes suivantes utilisent l’identité Azure créée dans la section précédente, puis affectez-la à votre service web AKS par le biais d’une **étiquette de sélecteur**.

Commencez par identifier le nom et l’espace de noms de votre déploiement dans votre cluster AKS auquel vous voulez affecter l’identité Azure. Vous pouvez obtenir ces informations en exécutant la commande suivante. Les espaces de noms doivent être le nom de votre espace de travail Azure Machine Learning et le nom de votre déploiement doit être le nom de votre point de terminaison, tel qu’indiqué dans le portail.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Ajoutez l’étiquette du sélecteur d’identité Azure à votre déploiement en modifiant la spécification du déploiement. La valeur du sélecteur doit être celle que vous avez définie à l’étape 5 de la section [Installer la liaison d’identité Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Modifiez le déploiement pour ajouter l’étiquette du sélecteur d’identité Azure. Accédez à la section suivante sous `/spec/template/metadata/labels`. Vous devez normalement voir des valeurs telles que `isazuremlapp: “true”`. Ajoutez l’étiquette aad-pod-identity comme indiqué ci-dessous.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Pour vérifier que l’étiquette a été correctement ajoutée, exécutez la commande suivante.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Pour voir l’état de tous les pods, exécutez la commande suivante.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Une fois que les pods sont opérationnels, les services web pour ce déploiement peuvent alors accéder à des ressources Azure par le biais de votre identité Azure sans avoir à incorporer les informations d’identification dans votre code. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Affecter les rôles appropriés à votre identité Azure

[Affectez votre identité managée Azure avec les rôles appropriés](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) pour accéder à d’autres ressources Azure. Vérifiez que les rôles que vous affectez ont les **actions de données** appropriées. Par exemple, le [rôle de lecteur des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) aura des autorisations de lecture sur votre objet blob de stockage, alors que le [rôle de lecteur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) générique n’en aura probablement pas.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Utiliser une identité Azure avec votre service web de machine learning

Déployez un modèle sur votre cluster AKS. Le script `score.py` peut contenir des opérations pointant vers les ressources Azure auxquelles votre identité Azure a accès. Vérifiez que vous avez installé vos dépendances de bibliothèque de client nécessaires pour la ressource à laquelle vous tentez d’accéder. Voici quelques exemples de la façon dont vous pouvez utiliser votre identité Azure pour accéder à différentes ressources Azure à partir de votre service.

### <a name="access-key-vault-from-your-web-service"></a>Accéder à un coffre de clés à partir de votre service web

Si vous avez donné à votre identité Azure un accès en lecture à un secret à l’intérieur d’un **coffre de clés**, votre `score.py` peut y accéder à l’aide du code suivant.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Accéder à un objet blob à partir de votre service web

Si vous avez donné à votre identité Azure un accès en lecture à des données à l’intérieur d’un **objet blob de stockage**, votre `score.py` peut y accéder à l’aide du code suivant.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation de la bibliothèque de client Azure Identity Python, consultez le [dépôt](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) sur GitHub.
* Pour obtenir un guide détaillé sur le déploiement de modèles sur des clusters Azure Kubernetes Service, consultez le [guide pratique](how-to-deploy-azure-kubernetes-service.md).