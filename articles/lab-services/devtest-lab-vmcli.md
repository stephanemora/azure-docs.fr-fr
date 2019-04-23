---
title: Créer et gérer des machines virtuelles dans DevTest Labs avec l’interface de ligne de commande Azure | Microsoft Docs
description: Apprenez à utiliser Azure DevTest Labs pour créer et gérer des machines virtuelles avec Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795934"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Créer et gérer des machines virtuelles avec DevTest Labs à l’aide de l’interface de ligne de commande Azure
Ce démarrage rapide vous guide dans la création, démarrage, connexion, la mise à jour et nettoyage d’un ordinateur de développement dans votre laboratoire. 

Avant de commencer :

* Si aucun laboratoire n’a été créé, la procédure à suivre se trouve [ici](devtest-lab-create-lab.md).

* [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli). Pour démarrer, exécutez az login pour créer une connexion avec Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Créer et vérifier la machine virtuelle 
Avant d’exécuter des commandes associées dev/test, définir le contexte Azure approprié à l’aide de la `az account set` commande :

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

La commande pour créer une machine virtuelle est : `az lab vm create`. Le groupe de ressources pour le laboratoire, le nom de laboratoire et le nom de machine virtuelle sont tous obligatoires. Le reste des arguments varient selon le type de machine virtuelle.

La commande suivante crée une image basée sur Windows à partir de la Place de marché Azure. Le nom de l’image est la même que vous voyez lorsque vous créez une machine virtuelle à l’aide du portail Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

La commande suivante crée une machine virtuelle basée sur une image personnalisée disponible dans le laboratoire :

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Le **type d’image** argument est passé de **galerie** à **personnalisé**. Le nom de l’image correspond à ce que vous voyez si vous deviez créer la machine virtuelle dans le portail Azure.

La commande suivante crée une machine virtuelle à partir d’une image de place de marché avec ssh authentification :

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Vous pouvez également créer des machines virtuelles basées sur les formules en définissant le **type d’image** paramètre **formule**. Si vous devez choisir un réseau virtuel spécifique pour votre machine virtuelle, utilisez le **-nom du réseau virtuel** et **sous-réseau** paramètres. Pour plus d’informations, consultez [créer az lab vm](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Vérifiez la disponibilité de la machine virtuelle.
Utilisez le `az lab vm show` pour vérifier que la machine virtuelle est disponible avant de démarrer et s’y connecter. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Démarrer et se connecter à la machine virtuelle
La commande suivante démarre une machine virtuelle :

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Se connecter à une machine virtuelle : [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Bureau à distance](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Mettre à jour la machine virtuelle
L’exemple de commande suivant s’applique à des artefacts à une machine virtuelle :

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Listez les artefacts disponibles dans le laboratoire.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Arrêter et supprimer la machine virtuelle    
L’exemple de commande suivant arrête une machine virtuelle.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Supprimez une machine virtuelle.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
Consultez le contenu suivant : [Documentation Azure CLI sur Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 