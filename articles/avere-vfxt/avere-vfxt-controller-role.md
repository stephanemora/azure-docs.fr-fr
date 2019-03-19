---
title: Rôle d’accès du contrôleur personnalisé - Avere vFXT pour Azure
description: Créer un rôle d’accès personnalisé pour le contrôleur de cluster Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c408efa7ca01928e25ac03f5ca63d0aef7d88839
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770033"
---
# <a name="customized-controller-access-role"></a>Rôle d’accès du contrôleur personnalisé

Le contrôleur de cluster Avere vFXT utilise une identité managée et le contrôle d’accès en fonction du rôle (RBAC) pour lui permettre de créer et de gérer le cluster. 

Par défaut, le [rôle de propriétaire intégré](../role-based-access-control/built-in-roles.md#owner) est assigné au contrôleur de cluster. L’accès du contrôleur est en outre limité à son groupe de ressources. Il ne peut pas modifier les éléments situés en dehors du groupe de ressources du cluster.

Cet article explique comment créer votre propre rôle d’accès pour le contrôleur de cluster au lieu d’utiliser le paramètre par défaut. 

## <a name="edit-the-role-prototype"></a>Modifier le prototype de rôle

Commencez avec le rôle de prototype disponible à l’adresse <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Ajoutez l’ID d’abonnement du déploiement Avere vFXT pour Azure dans l’instruction AssignableScopes. Personnalisez le nom et ajoutez ou modifiez des définitions si nécessaire. 

Soyez vigilant si vous limitez les privilèges. La création du cluster peut échouer si le contrôleur ne dispose pas de droits d’accès suffisants. 

Pour mieux comprendre les privilèges dont le contrôleur de cluster a besoin pour créer un cluster, [ouvrez un ticket de support](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Enregistrez votre définition de rôle personnalisé sous un fichier .json. 

## <a name="define-the-role"></a>Définir le rôle 

Suivez ces étapes pour ajouter la définition de rôle personnalisé à votre abonnement. 

1. Ouvrez Azure Cloud Shell dans le portail Azure ou accédez à [https://shell.azure.com](https://shell.azure.com).

1. Utilisez la commande Azure CLI pour basculer vers votre abonnement vFXT :

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Créez le rôle :

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Utilisez votre nom de fichier et chemin d’accès à la place de ```/avere-contributor-custom.json``` dans cet exemple. 

Enregistrez le résultat de la commande de définition de rôle. Il contient l’identificateur de rôle que vous devez fournir au modèle de création du cluster. 

## <a name="find-the-role-id"></a>Rechercher l’ID de rôle

Le modèle de déploiement Avere vFXT a besoin de l’identificateur global unique (GUID) du rôle pour assigner un rôle personnalisé au contrôleur. 

Le GUID du rôle est une chaîne de 32 caractères sous la forme suivante : 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Pour rechercher le GUID de votre rôle, utilisez cette commande avec le nom de votre rôle dans le paramètre ```--name```.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Entrez cette chaîne dans le champ **Avere cluster create role ID** (ID de rôle de création de cluster Avere) lors du déploiement d’Avere vFXT pour Azure.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment déployer Avere vFXT pour Azure dans [Déployer le cluster vFXT](avere-vfxt-deploy.md)
