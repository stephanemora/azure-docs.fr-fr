---
title: Prérequis d’Avere vFXT - Azure
description: Prérequis pour Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5642f3acd108d0d3f504fc132522936d1b5ab870
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082583"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Se préparer à la création du système Avere vFXT

Cet article décrit les tâches prérequises pour la création d’un cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Créer un abonnement

Commencez par créer un abonnement Azure. Utilisez un abonnement distinct pour chaque projet Avere vFXT, afin de pouvoir suivre facilement toutes les ressources et dépenses des projets, protéger les autres projets d’une possible limitation des ressources lors du provisionnement et simplifier le nettoyage.  

Pour créer un abonnement Azure dans le portail Azure :

* Accédez au [panneau Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Cliquez sur le bouton **+ Ajouter** situé en haut
* Si vous y êtes invité, connectez-vous
* Sélectionnez une offre, puis suivez les étapes permettant de créer un abonnement

## <a name="configure-subscription-owner-permissions"></a>Configurer des autorisations de propriétaire d’abonnement

Un utilisateur disposant d’autorisations de propriétaire pour l’abonnement doit créer le cluster vFXT. Des autorisations de propriétaire d’abonnement sont nécessaires pour effectuer, entre autres, les actions suivantes :

* Accepter les conditions du logiciel Avere vFXT
* Créer le rôle d’accès du nœud de cluster 

Deux solutions de contournement s’offrent à vous si vous ne voulez pas accorder l’accès propriétaire aux utilisateurs qui créent le vFXT :

* Un propriétaire de groupe de ressources peut créer un cluster si les conditions suivantes sont remplies :

  * Un propriétaire d’abonnement doit [accepter les conditions du logiciel Avere vFXT](#accept-software-terms) et [créer le rôle de l’accès du nœud de cluster](#create-the-cluster-node-access-role). 
  * Toutes les ressources Avere vFXT doivent être déployées à l’intérieur du groupe de ressources, notamment les suivantes :
    * Contrôleur de cluster
    * Nœuds de cluster
    * Stockage d'objets blob
    * Éléments réseau
 
* Un utilisateur sans privilèges de propriétaire peut créer à l'avance des clusters vFXT en utilisant le contrôle d'accès basé sur les rôles (RBAC) pour attribuer des privilèges à l'utilisateur. Cette méthode permet à ces utilisateurs de bénéficier d'autorisations importantes. [Cet article](avere-vfxt-non-owner.md) explique comment créer un rôle d'accès pour autoriser des non-propriétaires à créer des clusters.

## <a name="quota-for-the-vfxt-cluster"></a>Quota pour le cluster vFXT

Vous devez disposer d’un quota suffisant pour les composants Azure suivants. Si nécessaire, [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Les machines virtuelles et les composants SSD répertoriés ici sont destinées au cluster vFXT proprement dit. Vous devez obtenir un quota supplémentaire pour les machines virtuelles et les disques SSD que vous prévoyez d’utiliser pour votre batterie de serveurs de calcul.  Vérifiez que le quota est activé pour la région dans laquelle vous envisagez d’exécuter le flux de travail.

|Composant Azure|Quota|
|----------|-----------|
|Machines virtuelles|E32s_v3 3 ou plus|
|Stockage SSD Premium|200 Go d’espace de système d’exploitation, et de 1 To à 4 To d’espace de cache par nœud |
|Compte de stockage (facultatif) |v2|
|Stockage back-end de données (facultatif) |Un nouveau conteneur d’objets blob LRS |

## <a name="accept-software-terms"></a>Accepter les conditions d'utilisation du logiciel

> [!NOTE] 
> Cette étape n’est pas nécessaire si un propriétaire d’abonnement crée le cluster Avere vFXT.

Pendant la création du cluster, vous devez accepter les conditions d'utilisation du logiciel Avere vFXT. Si vous n’êtes pas propriétaire d’abonnement, demandez à un propriétaire d’abonnement d’accepter préalablement les conditions. Cette étape ne doit être effectuée qu’une fois par abonnement.

Pour accepter les conditions du logiciel à l’avance : 

1. Ouvrez un cloud shell dans le portail Azure ou en accédant à <https://shell.azure.com>. Connectez-vous avec votre ID d’abonnement.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Émettez la commande suivante pour accepter les conditions d’utilisation et activer l’accès programmatique du système Avere vFXT pour l’image de logiciels Azure : 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>Créer des rôles d'accès 

Le [contrôle d'accès en fonction du rôle](../role-based-access-control/index.yml) (RBAC) donne au contrôleur et aux nœuds de cluster vFXT l'autorisation d'effectuer les tâches nécessaires.

* Le contrôleur de cluster a besoin d'une autorisation pour créer et modifier les machines virtuelles afin de créer le cluster. 

* Dans le cadre d'une opération de cluster normale, les nœuds vFXT individuels doivent effectuer diverses opérations telles que la lecture des propriétés des ressources Azure, la gestion du stockage et le contrôle des paramètres d'interface réseau des autres nœuds.

Avant de pouvoir créer votre cluster Avere vFXT, vous devez définir un rôle personnalisé à utiliser avec les nœuds du cluster. 

Pour le contrôleur de cluster, vous pouvez accepter le rôle par défaut du modèle. La valeur par défaut confère au contrôleur de cluster les privilèges de propriétaire du groupe de ressources. Si vous préférez créer un rôle personnalisé pour le contrôleur, consultez [Rôle d'accès du contrôleur personnalisé](avere-vfxt-controller-role.md).

> [!NOTE] 
> Seul un propriétaire d'abonnement, ou un utilisateur doté du rôle de Propriétaire ou d'Administrateur d'accès utilisateur, peut créer des rôles. Les rôles peuvent être créés à l'avance.  

### <a name="create-the-cluster-node-access-role"></a>Créer le rôle d’accès du nœud de cluster

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Vous devez créer le rôle du nœud de cluster avant de pouvoir créer le cluster Avere vFXT pour Azure.

> [!TIP] 
> Les utilisateurs internes de Microsoft doivent utiliser le rôle existant nommé « Avere Cluster Runtime Operator » au lieu d’essayer d’en créer un. 

1. Copiez ce fichier. Ajoutez votre ID d'abonnement sur la ligne AssignableScopes.

   (La version actuelle de ce fichier est stockée dans le référentiel github.com/Azure/Avere sous le nom [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
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

1. Enregistrez le fichier sous le nom ``avere-operator.json`` ou sous un nom de fichier similaire facile à mémoriser. 


1. Ouvrez un interpréteur de commandes Azure Cloud et connectez-vous avec votre ID d'abonnement (décrit [plus haut dans ce document](#accept-software-terms)). Utilisez cette commande pour créer le rôle :

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

Le nom du rôle est utilisé lors de la création du cluster. Dans cet exemple, il s'agit du nom ``avere-operator``.

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Créer un point de terminaison de service de stockage dans votre réseau virtuel (si nécessaire)

Un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) conserve le trafic d’objets Blob Azure en local au lieu de le routage en dehors du réseau virtuel. Il est recommandé de n’importe quel vFXT Avere pour un cluster Azure qui utilise des objets Blob Azure pour le stockage de données back-end. 

Si vous êtes en fournissant un réseau virtuel existant et la création d’un conteneur d’objets Blob Azure pour votre stockage back-end dans le cadre de la création du cluster, vous devez disposer d’un point de terminaison de service dans le réseau virtuel pour le stockage de Microsoft. Ce point de terminaison doit exister avant de créer le cluster, ou la création échoue. 

Un point de terminaison de service de stockage est recommandée pour n’importe quel vFXT Avere pour un cluster Azure qui utilise le stockage Blob Azure, même si vous ajoutez le stockage ultérieurement. 

> [!TIP] 
> * Ignorez cette étape si vous créez un nouveau réseau virtuel dans le cadre de la création du cluster. 
> * Cette étape est facultative si vous ne créez pas de stockage d’objets Blob lors de la création du cluster. Dans ce cas, vous pouvez créer le point de terminaison de service ultérieurement si vous décidez d’utiliser des objets Blob Azure.

Créer le point de terminaison de service de stockage à partir du portail Azure. 

1. Dans le portail, cliquez sur **Réseaux virtuels** à gauche.
1. Sélectionnez le réseau virtuel pour votre cluster. 
1. Cliquez sur **Points de terminaison de service** à gauche.
1. Cliquez sur **Ajouter** dans la partie supérieure.
1. Laissez le service en tant que ``Microsoft.Storage`` et choisissez le sous-réseau du cluster.
1. Dans la partie inférieure, cliquez sur **Ajouter**.

   ![Capture d’écran du portail Azure montrant les étapes de création du point de terminaison de service](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Étape suivante : Créer le cluster vFXT

Après avoir respecté ces prérequis, vous pouvez passer à la création du cluster proprement dit. Pour obtenir des instructions, lisez [Déployer le cluster vFXT](avere-vfxt-deploy.md).