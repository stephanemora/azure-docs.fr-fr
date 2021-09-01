---
title: Effectuer une rotation des informations d’identification du principal de service pour un cluster Azure Red Hat OpenShift (ARO)
description: Découvrez comment effectuer une rotation des informations d’identification du principal de service pour Azure Red Hat OpenShift (ARO).
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090868"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>Effectuer une rotation des informations d’identification du principal de service pour votre cluster Azure Red Hat OpenShift (ARO)
Cet article fournit les détails nécessaires à la rotation des informations d’identification des principaux services dans les clusters Azure Red Hat OpenShift ([ARO](https://github.com/Azure/ARO-RP)).

## <a name="before-you-begin"></a>Avant de commencer
L’article suppose qu’il existe déjà un cluster ARO avec les dernières mises à jour appliquées.

Les exigences minimales pour Azure CLI pour la rotation des informations d’identification du principal de service dans un cluster ARO sont la version 2.24.0.

Pour vérifier la version d’Azure CLI, exécutez :
```azurecli-interactive
# Azure CLI version
az --version
```
Pour installer ou mettre à niveau Azure CLI, suivez [Installer Azure CLI](/cli/azure/install-azure-cli).

Les instructions suivantes utilisent la syntaxe bash.

## <a name="service-principal-credential-rotation"></a>Rotation des informations d’identification du principal du service
>[!IMPORTANT]
>  La rotation des informations d’identification du principal de service peut prendre jusqu’à 2 heures en fonction de l’état du cluster.

La rotation des informations d’identification du principal de service propose deux méthodes :
 - [Rotation automatisée des informations d’identification du principal de service](#Automated-Service-Principal-Credential-Rotation)
 - [client-id fourni par l’utilisateur et rotation des informations d’identification du principal de service client-secret](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)

### <a name="automated-service-principal-credential-rotation"></a>Rotation automatisée des informations d’identification du principal de service <a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  La rotation automatisée des informations d’identification du principal de service requiert la création du cluster ARO avec Azure CLI version 2.24.0 ou ultérieure.

La rotation automatisée des informations d’identification du principal de service vérifie si le principal du service existe et fait pivoter ou crée un principal de service.

Faites pivoter automatiquement les informations d’identification du principal de service à l’aide de la commande suivante :

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>client-id fourni par l’utilisateur et rotation des informations d’identification du principal de service client-secret <a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


Faites pivoter manuellement les informations d’identification du principal de service avec l’ID client et le secret client fournis par l’utilisateur avec les instructions suivantes :

Récupérez le clientId du principal de service (`--client-id`) et définissez-le comme variable d’environnement `SP_ID`.
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
Générez un nouveau secret sécurisé (`--client-secret`) pour le principal de service à l’aide de la variable `SP_ID` ci-dessus. Stockez le nouveau secret sécurisé comme variable d’environnement `SP_SECRET`.
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
Faites pivoter les informations d’identification du principal de service à l’aide des variables d’environnement ci-dessus.
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>Dépanner
### <a name="service-principal-expiration-date"></a>Date d’expiration du principal du service
Les informations d’identification du principal de service ont une date d’expiration d’un an et doivent être pivotées dans ce laps de temps.

Si la date d’expiration est passée, les erreurs suivantes sont possibles :
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
Pour vérifier la date d’expiration des informations d’identification du principal de service, exécutez la commande suivante :
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
Si les informations d’identification du principal de service sont expirées, mettez-les à jour à l’aide d’une des deux méthodes de rotation des informations d’identification.

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>L’application AAD de cluster contient une clé secrète client avec une description vide
L’erreur suivante se produit lors de l’utilisation de la [rotation automatisée des informations d’identification du principal de service](#Automated-Service-Principal-Credential-Rotation) :
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
Le cluster n’a pas été créé à l’aide d’Azure CLI 2.24.0 ou version ultérieure. Utilisez plutôt la méthode [client-id fourni par l’utilisateur et rotation des informations d’identification du principal de service client-secret](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation) à la place.

### <a name="azure-cli-aro-update-help"></a>Aide sur la mise à jour d’ARO avec Azure CLI
Pour plus d’informations, consultez la commande d’aide Azure CLI pour la mise à jour d’ARO :
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
