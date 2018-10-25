---
title: Déployer OKD dans Azure | Microsoft Docs
description: Déployez OKD dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
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
ms.openlocfilehash: e525bf0a5aa9bda7fdbbcefc4cb5b683c7fabc3b
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426224"
---
# <a name="deploy-okd-in-azure"></a>Déployer OKD dans Azure

Vous pouvez utiliser l’une des deux manières de déployer OKD (anciennement OpenShift Origin) dans Azure :

- Vous pouvez déployer manuellement tous les composants d’infrastructure Azure nécessaires, puis suivre la [documentation](https://docs.okd.io/3.10/welcome/index.html) d’OKD.
- Vous pouvez également utiliser un [modèle Resource Manager](https://github.com/Microsoft/openshift-origin) existant qui simplifie le déploiement du cluster OKD.

## <a name="deploy-by-using-the-okd-template"></a>Déployer avec le modèle OKD

Utilisez la valeur `appId` du principal de service créé précédemment pour le paramètre `aadClientId`.

L’exemple suivant crée un fichier de paramètres nommé azuredeploy.parameters.json avec toutes les entrées requises.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
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
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Déployer à l’aide d’Azure CLI


> [!NOTE] 
> La commande suivante requiert Azure CLI 2.0.8 ou version ultérieure. Pour vérifier la version d’Azure CLI, exécutez la commande `az --version`. Pour mettre à jour la version de l’interface CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

L’exemple suivant déploie le cluster OKD et toutes les ressources associées dans un groupe de ressources nommé myResourceGroup, avec le nom de déploiement myOpenShiftCluster. Le modèle est référencé directement à partir du dépôt GitHub à l’aide d’un fichier de paramètres locaux nommé azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La durée du déploiement varie en fonction du nombre total de nœuds déployés, avec un minimum de 25 minutes. L’URL de la console OKD et le nom DNS du maître OpenShift s’affichent sur le terminal à l’issue du déploiement.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Se connecter au cluster OKD

Une fois le déploiement terminé, connectez-vous à la console OKD dans votre navigateur à l’aide de la valeur `OpenShift Console Uri`. Vous pouvez aussi vous connecter au maître OKD à l’aide de la commande suivante :

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées quand vous n’en avez plus besoin.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes liés au déploiement d’OpenShift](./openshift-troubleshooting.md)
- [Bien démarrer avec OKD](https://docs.okd.io/latest/getting_started/index.html)
