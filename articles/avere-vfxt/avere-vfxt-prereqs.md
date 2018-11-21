---
title: Prérequis d’Avere vFXT - Azure
description: Prérequis pour Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d32c664049b7e7c1231e78c552e7c61d016fbe84
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286756"
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
* Autoriser le nœud de contrôleur de cluster à gérer des groupes de ressources et des réseaux virtuels 
* Autoriser le contrôleur de cluster à créer et à modifier les nœuds de cluster 

Deux solutions de contournement s’offrent à vous si vous ne voulez pas accorder l’accès propriétaire aux utilisateurs qui créent le vFXT :

* Un propriétaire de groupe de ressources peut créer un cluster si les conditions suivantes sont remplies :

  * Un propriétaire d’abonnement doit [accepter les conditions du logiciel Avere vFXT](#accept-software-terms-in-advance) et [créer le rôle de l’accès du nœud de cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Toutes les ressources Avere vFXT doivent être déployées à l’intérieur du groupe de ressources, notamment les suivantes :
    * Contrôleur de cluster
    * Nœuds de cluster
    * Stockage d'objets blob
    * Éléments réseau
 
* Un utilisateur ne disposant d’aucun privilège de propriétaire peut créer des clusters vFXT si un rôle d’accès supplémentaire est créé et lui est préalablement affecté. Toutefois, ce rôle donne des autorisations importantes à ces utilisateurs. [Cet article](avere-vfxt-non-owner.md) explique comment autoriser des non-propriétaires à créer des clusters.

## <a name="quota-for-the-vfxt-cluster"></a>Quota pour le cluster vFXT

Vous devez disposer d’un quota suffisant pour les composants Azure suivants. Si nécessaire, [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Les machines virtuelles et les composants SSD répertoriés ici sont destinées au cluster vFXT proprement dit. Vous devez obtenir un quota supplémentaire pour les machines virtuelles et les disques SSD que vous prévoyez d’utiliser pour votre batterie de serveurs de calcul.  Vérifiez que le quota est activé pour la région dans laquelle vous envisagez d’exécuter le flux de travail.

|Composant Azure|Quota|
|----------|-----------|
|Machines virtuelles|Au moins trois D16s_v3 ou E32s_v3|
|Stockage SSD Premium|200 Go d’espace de système d’exploitation, et de 1 To à 4 To d’espace de cache par nœud |
|Compte de stockage (facultatif) |v2|
|Stockage back-end de données (facultatif) |Un nouveau conteneur d’objets blob LRS |

## <a name="accept-software-terms-in-advance"></a>Accepter les conditions du logiciel à l’avance

> [!NOTE] 
> Cette étape n’est pas nécessaire si un propriétaire d’abonnement crée le cluster Avere vFXT.

Avant de pouvoir créer un cluster, vous devez accepter les conditions d’utilisation du logiciel Avere vFXT. Si vous n’êtes pas propriétaire d’abonnement, demandez à un propriétaire d’abonnement d’accepter préalablement les conditions. Cette étape ne doit être effectuée qu’une fois par abonnement.

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

## <a name="next-step-create-the-vfxt-cluster"></a>Étape suivante : Créer le cluster vFXT

Après avoir respecté ces prérequis, vous pouvez passer à la création du cluster proprement dit. Pour obtenir des instructions, lisez [Déployer le cluster vFXT](avere-vfxt-deploy.md).