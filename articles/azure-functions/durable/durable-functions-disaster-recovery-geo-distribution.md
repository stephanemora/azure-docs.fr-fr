---
title: Récupération d’urgence et géodistribution - Azure Durable Functions
description: En savoir plus sur la récupération d’urgence et la géodistribution dans Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1bddb6af777f36d04bae4a8b68c476789048be1b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081778"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Récupération d’urgence et géodistribution dans Azure Durable Functions

Dans Durable Functions, tous les états sont rendu persistants dans le stockage Azure. Un [hub de tâches](durable-functions-task-hubs.md) est un conteneur logique réservé aux ressources du stockage Azure qui sont utilisées pour les orchestrations. Les fonctions d’orchestrateur et d’activité peuvent interagir entre elles uniquement si elles appartiennent au même hub de tâches.
Les scénarios décrits proposent des options de déploiement pour augmenter la disponibilité et réduire les temps d’arrêt lors des opérations de récupération d’urgence.

Il est important de noter que ces scénarios sont basés sur des configurations du type actif/passif, puisqu’ils sont guidés par l’utilisation du stockage Azure. Ce modèle consiste à déployer une application de fonction de secours (passive) vers une autre région. Traffic Manager surveille la disponibilité de l’application de fonction principale (active). Il bascule vers l’application de fonction de secours si l’application principale échoue. Pour plus d’informations, voir [Méthode de routage du trafic basé sur la priorité](https://azure.microsoft.com/services/traffic-manager/) de [Traffic Manager.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Grâce à la configuration actif/passif proposée, un client est toujours en mesure de déclencher de nouvelles orchestrations via HTTP. Mais comme deux applications de fonction partagent le même stockage, le traitement en arrière-plan sera distribué entre les deux et les messages seront gérés simultanément à partir des mêmes files d’attente. Cette configuration entraîne des coûts de sortie supplémentaires pour l’application de fonction secondaire.
> - Le compte de stockage et le hub de tâches sous-jacents sont créés dans la région principale et sont partagés par les deux applications de fonction.
> - Toutes les applications de fonction qui sont déployés de manière redondante doivent donc partager les mêmes clés d’accès de fonction si elles sont activées via le protocole HTTP. Le Functions Runtime expose une [API de gestion](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) qui permet aux consommateurs d’ajouter, supprimer et mettre à jour des clés de fonction par programmation.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénario 1 : calcul avec équilibrage de charge et stockage partagé

En cas de problème au niveau de l’infrastructure de calcul dans Azure, l’application de fonction peut devenir indisponible. Pour réduire l’éventualité de ce type de temps d’arrêt, ce scénario utilise deux applications de fonction déployées dans des régions différentes.
Traffic Manager est configuré pour détecter les problèmes dans l’application de fonction principale et pour rediriger automatiquement le trafic vers l’application de fonction dans la région secondaire. Cette application de fonction partage le même compte de stockage Azure et le même hub de tâches. Il n’y a donc aucune perte de l’état des applications de fonction et le travail peut reprendre normalement. Une fois que la région principale est de nouveau opérationnelle, Azure Traffic Manager commence automatiquement à acheminer les demandes vers cette application de fonction.

![Schéma illustrant le scénario n°1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

L’utilisation de ce scénario de déploiement présente plusieurs avantages :

- En cas de panne de l’infrastructure de calcul, le travail peut reprendre dans la région de basculement sans perte d’état.
- Traffic Manager bascule automatiquement vers l’application de fonction saine.
- Traffic Manager rétablit automatiquement le trafic vers l’application de fonction principale une fois la panne résolue.

Si vous utilisez ce scénario, tenez cependant compte des remarques suivantes :

- Si l’application de fonction est déployée à l’aide d’un plan App Service dédié, le fait de répliquer l’infrastructure de calcul dans le centre de données de basculement entraîne des coûts supplémentaires.
- Ce scénario décrit des pannes au niveau de l’infrastructure de calcul, mais le compte de stockage demeure le point de défaillance unique pour l’application de fonction. En cas de panne de stockage, l’application subit un temps d’arrêt.
- Un basculement de l’application de fonction augmentera la latence car celle-ci accèdera à son compte de stockage d’une région à une autre.
- L’accès au service de stockage à partir d’une autre région implique un coût plus élevé lié au trafic de sortie du réseau.
- Ce scénario dépend de Traffic Manager. Compte tenu du [mode de fonctionnement de Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md), il peut être nécessaire d’attendre un certain temps avant qu’une application cliente qui utilise une fonction durable ait besoin de redemander à Traffic Manager l’adresse de l’application de fonction.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénario 2 : calcul avec équilibrage de charge et stockage régional

Le scénario précédent s’appliquait uniquement à une situation de panne au niveau de l’infrastructure de calcul. Un échec du service de stockage entraîne donc un arrêt de l’application de fonction.
Pour garantir un fonctionnement continu des fonctions durables, ce scénario utilise un compte de stockage local dans chaque région où les applications de fonction sont déployées.

![Schéma illustrant le scénario n°2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Cette approche apporte certaines améliorations au scénario précédent :

- En cas d’échec de l’application de fonction, Traffic Manager assure le basculement vers la région secondaire. Mais puisque l’application de fonction repose sur son propre compte de stockage, les fonctions durables continuent de fonctionner.
- Pendant un basculement, aucune latence supplémentaire n’est observée dans la région de basculement étant donné que l’application de fonction se trouve au même emplacement que le compte de stockage.
- Un problème au niveau de la couche de stockage entraînera l’échec des fonctions durables, ce qui déclenchera une redirection du trafic vers la région de basculement. Là encore, dans la mesure où l’application de fonction et le compte de stockage sont isolés par région, les fonctions durables continueront de fonctionner.

Points importants à prendre en compte pour ce scénario :

- Si l’application de fonction est déployée à l’aide d’un plan App Service dédié, le fait de répliquer l’infrastructure de calcul dans le centre de données de basculement entraîne des coûts supplémentaires.
- L’état actuel n’est pas basculé, ce qui signifie que les fonctions en cours d’exécution et passées au point de contrôle échoueront. C’est à l’application cliente de réessayer/redémarrer le travail.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénario 3 : calcul avec équilibrage de charge et stockage GRS partagé

Ce scénario modifie le premier scénario en implémentant un compte de stockage partagé. La principale différence réside dans l’activation de la géoréplication au moment de la création du compte de stockage.
D’un point de vue fonctionnel, ce scénario offre les mêmes avantages que le premier scénario, mais introduit d’autres avantages sur le plan de la récupération des données :

- Le stockage géoredondant (GRS) et le GRS avec accès en lecture (RA-GRS) optimisent la disponibilité de votre compte de stockage.
- En cas de panne du service de stockage dans une région, les opérations du centre de données peuvent estimer qu’il est nécessaire de basculer le stockage vers la région secondaire. Dans ce cas, l’accès au compte de stockage sera redirigé en toute transparence vers la copie géorépliquée du compte de stockage, sans la moindre intervention de l’utilisateur.
- Dans ce cas, l’état des fonctions durables est conservé jusqu'à la dernière réplication du compte de stockage, qui intervient à intervalles de quelques minutes.

Comme pour les autres scénarios, quelques aspects sont à prendre en compte :

- Le basculement vers le réplica est assuré par les opérateurs du centre de données et cette opération peut prendre un certain temps. Dans l’intervalle, l’application de fonction est bloquée.
- L’utilisation de comptes de stockage géorépliqués entraîne des coûts supplémentaires.
- Le stockage géoredondant s’exécute de façon asynchrone. La latence de la réplication peut provoquer la perte de certaines transactions plus récentes.

![Schéma illustrant le scénario n°3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../../storage/common/geo-redundant-design.md)
