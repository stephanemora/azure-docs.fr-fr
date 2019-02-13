---
title: Déployer OpenShift Container Platform dans Azure | Microsoft Docs
description: Déployez OpenShift Container Platform dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 82dd448bb408e7c4bb3576feee17aef66ee6d01d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730711"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Déployer OpenShift Container Platform dans Azure

Vous pouvez utiliser plusieurs méthodes pour déployer OpenShift Container Platform dans Azure :

- Vous pouvez déployer manuellement les composants d’infrastructure Azure nécessaires, puis suivre la [documentation OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Vous pouvez également utiliser un [modèle Resource Manager](https://github.com/Microsoft/openshift-container-platform/) existant qui simplifie le déploiement du cluster OpenShift Container Platform.
- Une autre option consiste à utiliser l’[offre de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pour toutes les options, vous devez avoir un abonnement Red Hat. Pendant le déploiement, l’instance Red Hat Enterprise Linux est inscrite dans l’abonnement Red Hat et associée à l’ID du pool contenant les droits pour OpenShift Container Platform.
Vérifiez que vous avez un nom d’utilisateur, un mot de passe et un ID de pool RHSM (Red Hat Subscription Manager) valides. Vous pouvez utiliser une clé d’activation, un ID d’organisation et un ID de pool. Vous pouvez vérifier ces informations en vous connectant au site https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Déployer à l’aide du modèle Resource Manager d’OpenShift Container Platform

Pour effectuer le déploiement à l’aide du modèle Resource Manager, vous utilisez un fichier de paramètres qui fournit les paramètres d’entrée. Pour personnaliser davantage le déploiement, dupliquez (fork) le dépôt GitHub et changez les éléments appropriés.

Voici une liste non exhaustive d’options de personnalisation courantes :

- Taille de machine virtuelle bastion (variable dans azuredeploy.json)
- Conventions de nommage (variables dans azuredeploy.json)
- Caractéristiques de cluster OpenShift, modifiées dans le fichier hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurer le fichier de paramètres

Le [modèle OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) a plusieurs branches disponibles pour différentes versions d’OpenShift Container Platform.  Selon vos besoins, vous pouvez effectuer le déploiement directement à partir du dépôt, ou dupliquer (fork) le dépôt et personnaliser les modèles ou les scripts avant le déploiement.

Utilisez la valeur de `appId` du principal de service créé précédemment pour le paramètre `aadClientId`.

L’exemple suivant montre un fichier de paramètres nommé azuredeploy.parameters.json avec toutes les entrées obligatoires.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Remplacez les paramètres par vos informations spécifiques.

Les paramètres peuvent différer selon les versions. Vérifiez quels sont les paramètres nécessaires pour la branche que vous utilisez.

### <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

> [!NOTE] 
> La commande suivante requiert Azure CLI 2.0.8 ou version ultérieure. Pour vérifier la version d’Azure CLI, exécutez la commande `az --version`. Pour mettre à jour la version de l’interface CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

L’exemple suivant déploie le cluster OpenShift et toutes les ressources associées dans un groupe de ressources nommé openshiftrg, avec le nom de déploiement myOpenShiftCluster. Le modèle est référencé directement à partir du dépôt GitHub, et un fichier de paramètres locaux nommé azuredeploy.parameters.json est utilisé.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La durée du déploiement varie en fonction du nombre total de nœuds déployés et des options configurées, avec un minimum de 30 minutes. Le nom de domaine complet du DNS Bastion et l’URL de la console OpenShift s’affichent dans le terminal à la fin du déploiement.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Si vous ne souhaitez pas lier la ligne de commande d’attente de la fin du déploiement, ajoutez `--no-wait` aux options pour le déploiement du groupe. La sortie du déploiement peut être récupérée à partir du portail Azure, dans la section du déploiement du groupe de ressources.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Déployer à l’aide de l’offre de la Place de marché Azure pour OpenShift Container Platform

La façon la plus simple de déployer OpenShift Container Platform dans Azure consiste à utiliser l’[offre de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Cette option est la plus simple, mais les possibilités de personnalisation sont limitées. L’offre de la Place de marché inclut les options de configuration suivantes :

- **Nœuds master** : trois (3) nœuds master avec le type d’instance configurable.
- **Nœuds infrastructure** : trois (3) nœuds infrastructure avec le type d’instance configurable.
- **Nœuds** : le nombre de nœuds est configurable (entre 2 et 9), tout comme le type d’instance.
- **Type de disque** : utilisation de Managed Disks.
- **Réseau** : prise en charge d’un réseau nouveau ou existant, ainsi que de la plage CIDR personnalisée.
- **CNS** : peut être activé.
- **Métriques** : peuvent être activées.
- **Journalisation** : peut être activée.
- **Fournisseur de cloud Azure** : Peut être activé.

## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift

Une fois le déploiement terminé, récupérez la connexion à partir de la section de sortie du déploiement. Connectez-vous à la console OpenShift dans un navigateur à l’aide de la valeur `OpenShift Console URL`. Vous pouvez également établir une connexion SSH à l’hôte Bastion. Dans l’exemple suivant, le nom d’utilisateur administrateur est clusteradmin et le nom de domaine complet du DNS d’adresses IP publiques Bastion est bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com :

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées quand vous n’en avez plus besoin.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes de déploiement d’OpenShift dans Azure](./openshift-troubleshooting.md)
- [Prise en main d’OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Contributeurs à la documentation

Merci à Vincent Power (vincepower) et à Alfred Sin (asinn826) pour leurs contributions à la mise à jour de cette documentation !
