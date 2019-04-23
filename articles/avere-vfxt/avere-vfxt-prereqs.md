---
title: Prérequis d’Avere vFXT - Azure
description: Prérequis pour Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786421"
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

Un utilisateur disposant d’autorisations de propriétaire pour l’abonnement doit créer le cluster vFXT. Autorisations de propriétaire d’abonnement sont nécessaires pour accepter les termes du service logiciel et effectuer d’autres actions. 

Il existe certains scénarios de solution de contournement qui permettent un propriétaire non créer un vFTX Avere pour un cluster Azure. Ces scénarios impliquent la restriction de ressources et l’affectation des rôles supplémentaires au créateur. Dans ces deux cas, le propriétaire d’un abonnement doit également [accepter les termes du contrat de logiciel vFXT Avere](#accept-software-terms) avance. 

| Scénario | Restrictions | Rôles d’accès requis pour créer le cluster vFXT Avere | 
|----------|--------|-------|
| Administrateur de groupe de ressources | Le réseau virtuel, le contrôleur de cluster et les nœuds de cluster doivent être créées dans le groupe de ressources | [Administrateur des accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator) et [contributeur](../role-based-access-control/built-in-roles.md#contributor) rôles, les deux étendus au groupe de ressources cible | 
| Réseau virtuel externe | Le contrôleur de cluster et les nœuds de cluster sont créés dans le groupe de ressources, mais un réseau virtuel existant dans un autre groupe de ressources est utilisé. | (1) [administrateur des accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator) et [contributeur](../role-based-access-control/built-in-roles.md#contributor) rôles étendues pour le groupe de ressources vFXT ; et (2) [collaborateur de Machine virtuelle](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [l’accès utilisateur Administrateur](../role-based-access-control/built-in-roles.md#user-access-administrator), et [Avere contributeur](../role-based-access-control/built-in-roles.md#avere-contributor) rôles étendus au groupe de ressources de réseau virtuel. |
 
Une alternative consiste à créer un rôle de contrôle (RBAC) d’accès en fonction du rôle personnalisé avance et affecter des privilèges à l’utilisateur, comme expliqué dans [cet article](avere-vfxt-non-owner.md). Cette méthode permet à ces utilisateurs de bénéficier d'autorisations importantes. 

## <a name="quota-for-the-vfxt-cluster"></a>Quota pour le cluster vFXT

Vous devez disposer d’un quota suffisant pour les composants Azure suivants. Si nécessaire, [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Les machines virtuelles et les composants SSD répertoriés ici sont destinées au cluster vFXT proprement dit. Vous devez obtenir un quota supplémentaire pour les machines virtuelles et les disques SSD que vous prévoyez d’utiliser pour votre batterie de serveurs de calcul.  Vérifiez que le quota est activé pour la région dans laquelle vous envisagez d’exécuter le flux de travail.

|Composant Azure|Quota|
|----------|-----------|
|Machines virtuelles|3 E32s_v3 ou plus|
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