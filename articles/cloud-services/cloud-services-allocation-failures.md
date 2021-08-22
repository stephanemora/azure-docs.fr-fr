---
title: Résolution des problèmes d’échec d’allocation de Cloud Service (classique) | Microsoft Docs
description: Découvrez comment résoudre des problèmes d’échec d’allocation lorsque vous déployez Azure Cloud Services. Découvrez le fonctionnement de l’allocation et la raison pour laquelle l’allocation peut échouer.
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 52dad4daeefa7ee5e37ee0fa12fa5fc790a81a66
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093640"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Résolution des problèmes d’échec de répartition lorsque vous déployez des Azure Cloud Services (classique) dans Azure

> [!IMPORTANT]
> [Azure Cloud Services (support étendu)](../cloud-services-extended-support/overview.md) est un nouveau modèle de déploiement basé sur Azure Resource Manager pour le produit Azure Cloud Services. En raison de ce changement, les instances Azure Cloud Services qui s’exécutent sur le modèle de déploiement basé sur Azure Service Manager ont été renommées Cloud Services (classique). Tous les nouveaux déploiements doivent passer par [Cloud Services (support étendu)](../cloud-services-extended-support/overview.md).


## <a name="summary"></a>Résumé

Lorsque vous déployez des instances sur un service cloud ou ajoutez de nouvelles instances de rôle Web ou de rôle de travail, Microsoft Azure alloue des ressources de calcul. Vous pouvez parfois recevoir des erreurs lorsque vous effectuez ces opérations avant même d’avoir atteint les limites de votre abonnement Azure. Cet article explique les causes de certains échecs d’allocation courants et propose des solutions possibles. Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Fonctionne de l’allocation en arrière-plan

Les serveurs des centres de données Azure sont partitionnés en clusters. Une nouvelle demande d'allocation de service cloud est tentée dans plusieurs clusters. Lorsque la première instance est déployée sur un service cloud (intermédiaire ou de production), ce service cloud est épinglé sur un cluster. Les déploiements ultérieurs du service cloud s'effectueront dans le même cluster. Dans cet article, nous parlerons « d’épinglage à un cluster ». La Figure 1 ci-dessous illustre le cas d'une allocation normale dans laquelle la tentative est exécutée dans plusieurs clusters. La Figure 2 illustre le cas d'une allocation épinglée au Cluster 2, là où est hébergé le service cloud CS_1 existant.

![Schéma d’allocation](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Raisons de l’échec d’une allocation

Lorsqu'une demande d'allocation est épinglée à un cluster, il y a plus de risques de ne pas trouver de ressources disponibles puisque le pool de ressources disponibles est limité à un cluster. En outre, si votre demande d’allocation est épinglée à un cluster alors que le type de ressource que vous avez demandé n’est pas pris en charge par ce cluster, votre demande échouera, même si le cluster comporte des ressources disponibles. La Figure 3 ci-dessous illustre le cas d’une allocation épinglée qui échoue car le seul cluster candidat ne comporte pas de ressources disponibles. La Figure 4 illustre le cas de figure où une allocation épinglée échoue parce que le seul cluster candidat ne prend pas en charge la taille de machine virtuelle demandée, bien qu'il puisse libérer des ressources.

![Échec d’allocation épinglée](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Résolution des problèmes d'échec d'allocation pour les services cloud

### <a name="error-message"></a>Message d’erreur

Dans le portail Azure, accédez à votre service cloud puis, dans la barre latérale, sélectionnez *Journaux des opérations (classique)* pour afficher les journaux.

Consultez les autres solutions pour les exceptions ci-dessous :

|Type d’exception  |Message d’erreur  |Solution  |
|---------|---------|---------|
|FabricInternalServerError     |L’opération a échoué avec le code d’erreur « InternalError » et le message d’erreur « Le serveur a rencontré une erreur interne. Relancez la requête. »|[Résoudre les problèmes FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |L’opération a échoué avec le code d’erreur « InternalError » et le message d’erreur « Le serveur a rencontré une erreur interne. Relancez la requête. »|[Résoudre les problèmes ServiceAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |L’opération « `{Operation ID}` » a échoué : « Le niveau de machine virtuelle demandé n’est pas disponible actuellement dans la région (`{Region ID}`) pour cet abonnement. Essayez un autre niveau ou déployez sur un autre emplacement. »|[Résoudre les problèmes LocationNotFoundForRoleSize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |L’opération Azure « `{Operation ID}` » a échoué. Code : Compute.ConstrainedAllocationFailed. Détails : L’allocation a échoué. Impossible de satisfaire aux contraintes spécifiées dans la demande. Le nouveau déploiement de service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou un déploiement existant se trouve sous ce service hébergé. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Veuillez réessayer ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez aussi éventuellement supprimer les contraintes mentionnées précédemment ou essayer de déployer dans une autre région.|[Résoudre les problèmes ConstrainedAllocationFailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverConstrainedAllocationRequest     |La taille de machine virtuelle (ou la combinaison de tailles de machines virtuelles) requise par ce déploiement ne peut pas être provisionnée en raison des contraintes de la demande de déploiement. Si possible, essayez d’assouplir les contraintes telles que les liaisons de réseau virtuel, de déployer vers un service hébergé sans aucun autre déploiement et vers un autre groupe d’affinités ou sans groupe d’affinités, ou essayez de déployer vers une autre région.|[Résoudre les problèmes OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Exemple de message d’erreur :

> « L’opération Azure » {ID d’opération} a échoué. Code : Compute.ConstrainedAllocationFailed. Détails : L’allocation a échoué. Impossible de satisfaire aux contraintes spécifiées dans la demande. Le nouveau déploiement de service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou un déploiement existant se trouve sous ce service hébergé. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Veuillez réessayer ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez aussi éventuellement supprimer les contraintes mentionnées précédemment ou essayer de déployer dans une autre région. »

### <a name="common-issues"></a>Problèmes courants

Voici les scénarios d'allocation courants qui entraînent l'épinglage d'une demande d'allocation à un seul cluster.

* Déploiement sur un emplacement intermédiaire : si un service cloud est déployé dans l'un des deux emplacements, l'ensemble du service cloud est épinglé à un cluster spécifique.  Par conséquent, s'il existe déjà un déploiement dans l'emplacement de production, un nouveau déploiement intermédiaire ne peut être affecté que dans le même cluster que l'emplacement de production. Si le cluster est presque plein, la demande peut échouer.
* Mise à l'échelle : l'ajout de nouvelles instances à un service cloud existant doit être alloué au même cluster.  Les demandes de mise à l'échelle minime peuvent généralement être allouées, mais pas toujours. Si le cluster est presque plein, la demande peut échouer.
* Groupe d'affinités : un nouveau déploiement vers un service cloud vide peut être alloué par l'infrastructure à un cluster de cette région, sauf si le service cloud est épinglé à un groupe d'affinités. Des déploiements vers le même groupe d'affinités seront tentés sur le même cluster. Si le cluster est presque plein, la demande peut échouer.
* Réseau virtuel de groupe d'affinités : des réseaux virtuels plus anciens ont été liés à des groupes d'affinités plutôt qu'à des régions ; des services cloud dans ces réseaux virtuels seraient épinglés au cluster du groupe d'affinités. Des déploiements vers ce type de réseau virtuel seront tentés sur le cluster épinglé. Si le cluster est presque plein, la demande peut échouer.

## <a name="solutions"></a>Solutions

1. Redéployer vers un service cloud : cette solution est la plus susceptible de réussir car elle permet à la plateforme de choisir parmi tous les clusters de cette région.

   * Déployez la charge de travail vers un nouveau service cloud  
   * Mettez à jour l'enregistrement CNAME ou A pour faire pointer le trafic vers le nouveau service cloud
   * Une fois le trafic de l'ancien site nul, vous pouvez supprimer l'ancien service cloud. Cette solution ne devrait pas entraîner de temps d'arrêt.
2. Supprimer les emplacements intermédiaire et de production : cette solution permet de conserver votre nom DNS, mais entraînera un temps d'arrêt de votre application.

   * Supprimez les emplacements de production et intermédiaire d'un service cloud de sorte que le service cloud soit vide, puis
   * Créez un nouveau déploiement dans le service cloud. Cela retentera l'allocation sur tous les clusters de la région. Vérifiez que le service cloud n'est pas lié à un groupe d'affinités.
3. IP réservée : cette solution permet de conserver votre adresse IP existante, mais entraîne un temps d’arrêt de votre application.  

   * Créer une IP réservée pour votre déploiement à l’aide de PowerShell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Suivez la solution 2 mentionnée plus haut, en veillant à spécifier la nouvelle IP réservée dans le .cscfg du service.
4. Supprimer le groupe d'affinités pour les nouveaux déploiements : les groupes d'affinités ne sont plus recommandés. Suivez les étapes de la solution 1 ci-dessus pour déployer un nouveau service cloud. Vérifiez que le service cloud n'est pas dans un groupe d'affinités.
5. Convertir en réseau virtuel régional : consultez [Comment migrer des groupes d'affinités vers un réseau virtuel régional (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
