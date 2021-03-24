---
title: Résolution des exceptions FabricInternalServerError et ServiceAllocationFailure lors du déploiement d’un service cloud (classique) sur Azure | Microsoft Docs
description: Cet article explique comment résoudre une exception FabricInternalServerError ou ServiceAllocationFailure lors du déploiement d’un service cloud (classique) sur Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743193"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Résolution des exceptions FabricInternalServerError et ServiceAllocationFailure lors du déploiement d’un service cloud (classique) sur Azure

Dans cet article, vous allez résoudre les échecs d’allocation selon lesquels l’allocation effectuée par le contrôleur de structure n’aboutit pas lors du déploiement d’un service cloud Azure (classique).

Lorsque vous déployez des instances sur un service cloud ou ajoutez de nouvelles instances de rôle Web ou de rôle de travail, Microsoft Azure alloue des ressources de calcul.

Vous pouvez parfois recevoir des erreurs durant ces opérations avant même d’avoir atteint la limite de votre abonnement Azure.

> [!TIP]
> Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

## <a name="symptom"></a>Symptôme

Dans le portail Azure, accédez à votre service cloud (classique), puis, dans la barre latérale, sélectionnez *Journal des opérations (classique)* pour afficher les journaux.

![Image affichant le panneau Journal des opérations (classique).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Quand vous inspectez les journaux de votre service cloud (classique), vous constatez l’exception suivante :

|Exception  |Message d’erreur  |
|---------|---------|
FabricInternalServerError     |L’opération a échoué avec le code d’erreur « InternalError » et le message d’erreur « Le serveur a rencontré une erreur interne. Réessayez la demande. »|
|ServiceAllocationFailure     |L’opération a échoué avec le code d’erreur « InternalError » et le message d’erreur « Le serveur a rencontré une erreur interne. Réessayez la demande. »|

## <a name="cause"></a>Cause

*FabricInternalServerError* et *ServiceAllocationFailure* sont des exceptions qui peuvent se produire lorsque le contrôleur de structure ne parvient pas à allouer des instances dans le cluster. La cause racine varie selon que le service cloud est **épinglé** ou **non épinglé**.

- [**Non épinglé :** Échecs d’un premier déploiement d’un nouveau service cloud](#not-pinned-to-a-cluster)
- [**Épinglé :** Échecs d’un service cloud existant](#pinned-to-a-cluster)

> [!NOTE]
> Lorsque la première instance est déployée sur un service cloud (de préproduction ou de production), celui-ci est épinglé à un cluster.
>
> Au fil du temps, les ressources de ce pool de ressources peuvent devenir entièrement utilisées. Si un service cloud effectue une demande d’allocation pour des ressources supplémentaires alors que les ressources disponibles dans le pool de ressources épinglé sont insuffisantes, la demande entraîne un [échec d’allocation](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solution

Suivez les instructions relatives aux échecs d’allocation dans les scénarios suivants.

### <a name="not-pinned-to-a-cluster"></a>Non épinglé à un cluster

La première fois que vous déployez un service cloud (classique), le cluster n’a pas encore été sélectionné. Le service cloud n’est donc pas *épinglé*. Azure risque de présenter un échec de déploiement pour les raisons suivantes :

- Vous avez sélectionné une taille particulière qui n’est pas disponible dans la région.
- La combinaison des tailles nécessaires à travers différents rôles n’est pas disponible dans la région.

En cas d’erreur d’allocation dans ce scénario, la procédure recommandée consiste à vérifier les tailles disponibles dans la région et à modifier la taille spécifiée précédemment.

1. Pour connaître les tailles disponibles dans une région, consultez la page [Produits de service cloud (classique)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services).

    > [!NOTE]
    > La page *Produits* n’affiche pas la capacité disponible. Pour toute nouvelle allocation, Azure doit être en mesure de sélectionner le cluster optimal dans votre région à ce moment précis.

1. Mettez à jour le fichier de définition de votre service cloud (classique) pour spécifier une autre [taille de produit](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) de votre région.

### <a name="pinned-to-a-cluster"></a>Épinglé à un cluster

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
