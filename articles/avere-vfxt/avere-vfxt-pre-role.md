---
title: Créer un rôle Avere sans contrôleur - Avere vFXT pour Azure
description: Méthode de création du rôle RBAC requis sans machine virtuelle de contrôleur de cluster
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670026"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Créer le rôle d’accès du runtime de cluster Avere vFXT sans contrôleur

Ce document explique comment créer le rôle d’accès du nœud de cluster à partir de la ligne de commande avant de créer la machine virtuelle du contrôleur de cluster. 

Pour le créer à partir du contrôleur de cluster, consultez [Create the cluster node access role (Créer le rôle d’accès du nœud de cluster)](avere-vfxt-deploy.md#create-the-cluster-node-access-role). L’image du contrôleur inclut un fichier de prototype de rôle. Vous pouvez mettre à jour le fichier avec votre ID d’abonnement et l’utiliser pour définir le rôle localement sur la machine virtuelle du contrôleur.

## <a name="create-an-azure-rbac-role"></a>Créer un rôle RBAC Azure

Le système Avere vFXT utilise le [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) pour autoriser les nœuds de cluster vFXT à effectuer les tâches requises.  

Dans le cadre d’un cluster vFXT normal, les nœuds vFXT individuels doivent effectuer diverses opérations, comme lire les propriétés des ressources Azure, gérer le stockage et contrôler les paramètres d’interface réseau des autres nœuds. 

Ce rôle est utilisé pour les nœuds vFXT uniquement, et non pour la machine virtuelle du contrôleur de cluster.  

Pour créer le rôle avant le contrôleur, procédez comme suit : 

1. Ouvrez Azure Cloud Shell dans le portail Azure ou accédez à [https://shell.azure.com](https://shell.azure.com).

1. Utilisez la commande Azure CLI pour basculer vers votre abonnement vFXT :

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Utilisez ces commandes pour télécharger la définition de rôle à partir de l’image marketplace et la modifier. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Modifiez le fichier pour inclure votre ID d’abonnement et supprimer la ligne située au-dessus. Enregistrez le fichier sous le nom ``avere-cluster.json``.

   ![Éditeur de texte de la console indiquant l’ID d’abonnement et la sélection de l’option « remove this line » (supprimer cette ligne) pour la suppression](media/avere-vfxt-edit-role.png)

5. Créez le rôle :  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Lorsque vous créez le cluster, fournissez le nom du rôle (dans ce cas, `avere-cluster`). Le script de création de cluster assigne le rôle aux nœuds du cluster nouvellement créé. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Exemple de définition du rôle du runtime du nœud de cluster

> [!IMPORTANT] 
> Cet exemple de définition provient d’une version pre-GA du produit. Si la version que vous obtenez avec la distribution de produit actuelle est différente, utilisez plutôt cette version.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```