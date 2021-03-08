---
title: Résoudre les problèmes liés à ConstrainedAllocationFailed lors du déploiement d’un service cloud (classique) sur Azure | Microsoft Docs
description: Cet article explique comment résoudre une exception ConstrainedAllocationFailed lors du déploiement d’un service cloud (classique) sur Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738279"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Résoudre les problèmes liés à ConstrainedAllocationFailed lors du déploiement d’un service cloud (classique) sur Azure

Cet article explique comment résoudre des échecs d’allocation empêchant le déploiement d’Azure Cloud Services (classique) en raison de contraintes d’allocation.

Lorsque vous déployez des instances sur un service cloud (classique) ou ajoutez de nouvelles instances de rôle web ou de rôle de travail, Microsoft Azure alloue des ressources de calcul.

Vous pouvez parfois recevoir des erreurs durant ces opérations avant même d’avoir atteint la limite de votre abonnement Azure.

> [!TIP]
> Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

## <a name="symptom"></a>Symptôme

Dans le portail Azure, accédez à votre service cloud (classique), puis, dans la barre latérale, sélectionnez *Journal des opérations (classique)* pour afficher les journaux.

![Image affichant le panneau Journal des opérations (classique).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Quand vous inspectez les journaux de votre service cloud (classique), vous constatez l’exception suivante :

|Type d’exception  |Message d’erreur  |
|---------|---------|
|ConstrainedAllocationFailed     |L’opération Azure « `{Operation ID}` » a échoué. Code : Compute.ConstrainedAllocationFailed. Détails : L’allocation a échoué. Impossible de satisfaire aux contraintes spécifiées dans la demande. Le nouveau déploiement de service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou un déploiement existant se trouve sous ce service hébergé. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Réessayez ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez aussi éventuellement supprimer les contraintes mentionnées précédemment ou essayer de déployer dans une autre région.|

## <a name="cause"></a>Cause

Lorsque la première instance est déployée sur un service cloud (intermédiaire ou de production), celui-ci est épinglé à un cluster.

Au fil du temps, il arrive que les ressources de ce cluster soient entièrement utilisées. Si un service cloud (classique) demande l’allocation de ressources supplémentaires alors que les ressources disponibles dans le cluster épinglé sont insuffisantes, la demande entraîne un échec d’allocation. Pour plus d’informations, consultez les [problèmes courants d’échec d’allocation](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Solution

Les services cloud existants sont *épinglés* à un cluster. Les déploiements ultérieurs du service cloud (classique) s’effectueront dans le même cluster.

Lorsque vous rencontrez une erreur d’allocation dans ce scénario, la procédure recommandée consiste à opérer un redéploiement vers un nouveau service cloud (classique) (et à mettre à jour l’enregistrement *CNAME*).

> [!TIP]
> Cette solution est celle qui a le plus de chances de réussir car elle permet à la plateforme de choisir parmi tous les clusters de cette région.

> [!NOTE]
> Cette solution ne devrait pas entraîner de temps d'arrêt.

1. Déployez la charge de travail vers un nouveau service cloud (classique).
    - Pour d’autres instructions, consultez le guide [Comment créer et déployer un service cloud (classique)](cloud-services-how-to-create-deploy-portal.md).

    > [!WARNING]
    > Si vous ne souhaitez pas perdre l’adresse IP associée à cet emplacement de déploiement, vous pouvez utiliser la [Solution 3 : Conserver l’adresse IP](cloud-services-allocation-failures.md#solutions).

1. Mettez à jour l’enregistrement *CNAME* ou *A* pour faire pointer le trafic vers le nouveau service cloud (classique).
    - Pour d’autres instructions, consultez le guide [Configuration d’un nom de domaine personnalisé pour un service cloud Azure](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records).

1. Une fois que l’ancien ne reçoit plus de trafic, vous pouvez supprimer l’ancien service cloud (classique).
    - Pour d’autres instructions, consultez le guide [Supprimer des déploiements et un service cloud (classique)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service).
    - Pour voir le trafic réseau dans votre service cloud (classique), consultez [Introduction à la surveillance de service cloud (classique)](cloud-services-how-to-monitor.md).

Pour d’autres étapes de correction, consultez [Résolution des problèmes d’échecs d’allocation de service cloud (classique) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues).

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres solutions en lien avec les échecs d’allocation et des informations générales :

> [!div class="nextstepaction"]
> [Échecs d’allocation – Services cloud (classique)](cloud-services-allocation-failures.md)

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou [@AzureSupport sur Twitter](https://twitter.com/AzureSupport). Vous pouvez également envoyer une demande de support Azure. Pour envoyer une demande de support sur la page [Prise en charge Azure](https://azure.microsoft.com/support/options/), sélectionnez *Obtenir de l’aide*.