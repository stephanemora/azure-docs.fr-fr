---
title: Créer, tester et déployer des conteneurs sur Azure Kubernetes Service à l’aide de GitHub Actions
description: Découvrez comment utiliser GitHub Actions pour déployer votre conteneur sur Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595363"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions pour un déploiement sur le service Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) vous donne la possibilité de créer un flux de travail de cycle de vie de développement logiciel automatisé. L’action Kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) faciliter les déploiements sur les clusters Azure Kubernetes Service. L’action définit le contexte de cluster AKS cible, qui peut être utilisé par d’autres actions comme [azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master), [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master), etc. ou exécuter des commandes kubectl.

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow.

Pour un workflow ciblant AKS, le fichier comporte trois sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | Connexion à un registre de conteneurs privé (ACR) |
|**Créer** | Création et envoi de l’image conteneur via une transmission de type push  |
|**Déployer** | 1. Définition du cluster AKS cible |
| |2. Création d’un secret générique/du registre Docker dans le cluster Kubernetes  |
||3. Déploiement sur le cluster Kubernetes|

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous pouvez créer un [principal de service](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) avec la commande [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dans [Azure CLI](https://docs.microsoft.com/cli/azure/). Vous pouvez exécuter cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Dans la commande ci-dessus, remplacez les espaces réservés par votre ID d’abonnement et votre groupe de ressources. La sortie correspond aux informations d’identification de l’attribution de rôle qui permettent d’accéder à votre ressource. La commande doit générer un objet JSON similaire à celui-ci.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copiez cet objet JSON, que vous pouvez utiliser pour vous authentifier à partir de GitHub.

## <a name="configure-the-github-secrets"></a>Configurer les secrets GitHub

Suivez les étapes de configuration des secrets :

1. Dans [GitHub](https://github.com/), accédez à votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

    ![secrets](media/kubernetes-action/secrets.png)

2. Collez le contenu de la commande `az cli` ci-dessus en tant que valeur de la variable secrète. Par exemple : `AZURE_CREDENTIALS`.

3. De même, définissez les secrets supplémentaires suivants associés aux informations d’identification du registre de conteneurs et configurez-les dans l’action de connexion Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Vous verrez les secrets comme indiqué ci-dessous une fois qu’il seront définis.

    ![kubernetes-secrets](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Créer une image conteneur et la déployer dans un cluster Azure Kubernetes Service

La génération et l’envoi des images conteneur via une transmission de type push s’effectuent à l’aide de l’action `Azure/docker-login@v1`. Pour déployer une image conteneur sur AKS, vous devez utiliser l’action `Azure/k8s-deploy@v1`. Cette action a cinq paramètres :

| **Paramètre**  | **Explication**  |
|---------|---------|
| **namespace** | (Facultatif) Choisissez l’espace de noms Kubernetes cible. Si l’espace de noms n’est pas fourni, les commandes s’exécutent dans l’espace de noms par défaut. | 
| **manifests** |  (Obligatoire) Chemin d’accès aux fichiers manifestes qui seront utilisés pour le déploiement. |
| **images** | (Facultatif) URL de ressource complète de la ou des images à utiliser pour les substitutions dans les fichiers manifestes. |
| **imagepullsecrets** | (Facultatif) Nom d’un secret du registre Docker déjà configuré dans le cluster. Chaque nom de secret est ajouté sous le champ imagePullSecrets pour les charges de travail trouvées dans les fichiers manifestes d’entrée. |
| **kubectl-version** | (Facultatif) Installe une version spécifique du fichier binaire kubectl. |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Déployer des images dans un cluster Azure Kubernetes Service

Flux de travail de bout en bout de création d’images conteneur et de déploiement de ces dernières sur un cluster Azure Kubernetes Service.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez trouver notre ensemble d’actions regroupées dans différents référentiels GitHub, chacun contenant de la documentation et des exemples pour vous aider à utiliser GitHub pour les opérations de CI/CD et à déployer vos applications sur Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [actions-workflow-samples](https://github.com/Azure/actions-workflow-samples)
