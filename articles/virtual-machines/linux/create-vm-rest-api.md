---
title: Créer une machine virtuelle Linux à l’aide de l’API REST Azure | Microsoft Docs
description: Découvrez comment créer une machine virtuelle Linux dans Azure utilisant Managed Disks et l’authentification SSH avec l’API REST Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928180"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Créer une machine virtuelle Linux utilisant l’authentification SSH avec l’API REST

Dans Azure, une machine virtuelle est définie par divers paramètres tels que l’emplacement, la taille du matériel, l’image du système d’exploitation et les informations d’identification d’ouverture de session. Cet article explique comment utiliser l’API REST pour créer une machine virtuelle Linux utilisant l’authentification SSH.

Pour créer ou mettre à jour une machine virtuelle, utilisez l’opération *PUT* suivante :

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Créer une demande

Pour créer la demande *PUT*, vous devez impérativement utiliser le paramètre `{subscription-id}`. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Vous devez définir les paramètres `{resourceGroupName}` et `{vmName}` pour vos ressources, ainsi que le paramètre `api-version`. Cet article utilise `api-version=2017-12-01`.

Les en-têtes suivants sont requis :

| En-tête de requête   | Description |
|------------------|-----------------|
| *Content-Type :*  | Requis. Défini sur `application/json`. |
| *Authorization :* | Requis. Défini sur un [jeton d’accès](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |

Pour plus d’informations sur la création de la demande, consultez [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Composants d’une demande/réponse de l’API REST).

## <a name="create-the-request-body"></a>Créer le corps de la demande

Les définitions courantes suivantes permettent de générer un corps de demande :

| NOM                       | Obligatoire | type                                                                                | Description  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | chaîne                                                                              | Emplacement de la ressource. |
| Nom                       |          | chaîne                                                                              | Nom de la machine virtuelle. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Spécifie les paramètres matériels de la machine virtuelle. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Spécifie les paramètres de stockage des disques de machine virtuelle. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Spécifie les paramètres du système d’exploitation de la machine virtuelle. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Spécifie les interfaces réseau de la machine virtuelle. |

Pour obtenir la liste complète des définitions disponibles dans le corps de la demande, consultez [Virtual machines create or update request body definitions](/rest/api/compute/virtualmachines/createorupdate#definitions) (Création ou mise à jour des définitions de corps de demande par les machines virtuelles).

### <a name="example-request-body"></a>Exemple de corps de demande

L’exemple de corps de demande suivant définit une image Ubuntu LTS-18.04 qui utilise des disques managés Premium. L’authentification par clé publique SSH est utilisée et la machine virtuelle utilise une carte d’interface réseau virtuelle (NIC) existante que vous avez [précédemment créée](../../virtual-network/virtual-network-network-interface.md). Indiquez votre clé publique SSH dans le champ *osProfile.linuxConfiguration.ssh.publicKeys.keyData*. Si nécessaire, vous pouvez [générer une paire de clés SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Réponses

Il existe deux réponses de réussite pour l’opération de création ou de mise à jour d’une machine virtuelle :

| NOM        | type                                                                              | Description |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Créé | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Date de création     |

Pour plus d’informations sur les réponses des API REST, consultez [Process the response message](/rest/api/azure/#process-the-response-message) (Traiter le message de réponse).

### <a name="example-response"></a>Exemple de réponse

Une réponse *201 Créé* condensée à partir de l’exemple de corps de demande précédent qui crée une machine virtuelle affiche un ID *vmId* assigné et l’état *provisioningState* *Creating* :

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les API REST Azure ou d’autres outils de gestion tels qu’Azure CLI 2.0 ou Azure PowerShell, consultez les rubriques suivantes :

- [Azure Compute provider REST API](/rest/api/compute/) (API REST du fournisseur Calcul Azure)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Module Azure PowerShell](/powershell/azure/overview)
