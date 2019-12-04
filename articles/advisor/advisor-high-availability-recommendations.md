---
title: Améliorer la disponibilité de votre application avec Azure Advisor | Microsoft Docs
description: Utilisez le conseiller Azure pour améliorer la haute disponibilité de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: dd1b898adf4c4cdff45e05427757d90d5f80bf25
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145331"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Améliorer la disponibilité de votre application avec Azure Advisor

Azure Advisor vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de haute disponibilité auprès du conseiller à partir de l’onglet **Haute disponibilité** du tableau de bord du conseiller.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Assurer la tolérance de panne des machines virtuelles

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Le conseiller identifie les machines virtuelles qui ne font pas partie d’un groupe à haute disponibilité et recommande de les déplacer dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez choisir de créer un groupe à haute disponibilité pour la machine virtuelle ou d’ajouter la machine virtuelle à un groupe à haute disponibilité existant.

> [!NOTE]
> Si vous choisissez de créer un groupe à haute disponibilité, vous devez y ajouter au moins une machine virtuelle supplémentaire. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité pour garantir qu’au moins une d’elles reste disponible en cas de panne.

## <a name="ensure-availability-set-fault-tolerance"></a>Assurer la tolérance de panne d’un groupe à haute disponibilité

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Advisor identifie les groupes à haute disponibilité contenant une seule machine virtuelle et recommande d’y ajouter au moins une machine virtuelle. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez créer une machine virtuelle ou ajouter une machine virtuelle existante au groupe à haute disponibilité.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Utiliser la fonctionnalité Disques managés pour améliorer la fiabilité des données

Les machines virtuelles situées dans un groupe à haute disponibilité avec disques partageant des comptes de stockage ou des unités d'échelle de stockage ne sont pas résistantes aux échecs des unités d'échelle de stockage en cas de pannes. Advisor identifie ces groupes à haute disponibilité et conseille de migrer vers Azure Disques managés. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés pour éviter un point de défaillance unique. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Assurer la tolérance de panne d’une passerelle

Cette recommandation garantit la continuité métier des applications stratégiques qui sont alimentées par des passerelles d’application. Advisor identifie les instances de passerelle d’application qui ne sont pas configurées pour la tolérance de panne, et suggère des mesures de mise à jour que vous pouvez prendre. Advisor identifie les passerelles d’application de moyenne ou grande taille à instance unique, et recommande d’ajouter au moins une instance de plus. Il identifie également les petites passerelles d’application à une ou plusieurs instances et recommande de migrer vers des références de moyenne ou grande taille. Advisor recommande ces actions pour assurer que vos instances de passerelle d’application sont configurées pour satisfaire les exigences actuelles du contrat de niveau de service pour ces ressources.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Protégez les données de vos machines virtuelles d’une suppression accidentelle

La configuration de la sauvegarde de machine virtuelle permet de garantir la disponibilité de vos données métier stratégiques et offre une protection contre une suppression accidentelle ou un endommagement des données. Advisor identifie les machines virtuelles sur lesquelles la sauvegarde n’est pas activée, et recommande l’activation de la sauvegarde. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Assurez-vous d’avoir accès à des experts du cloud Azure chaque fois que vous en avez besoin

Lors de l’exécution d’une charge de travail critique, il est important d’avoir accès au support technique en cas de besoin. Advisor identifie les éventuels abonnements critiques dont le plan de support ne comprend aucun accès au support technique et recommande d’effectuer une mise à niveau pour choisir une option qui inclut le support technique.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Créer des alertes Azure Service Health pour être averti lorsque des problèmes Azure vous concernent

Vous recommandons de configurer des alertes Azure Service Health pour recevoir une notification lorsque des problèmes Azure vous concernent. [Azure Service Health](https://azure.microsoft.com/features/service-health/) est un service gratuit qui offre conseils et support personnalisés lorsque vous êtes concerné par un problème de service Azure. Advisor identifie les abonnements qui n’ont pas d’alertes configurées et recommande de créer une.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurer des points de terminaison Traffic Manager à des fins de résilience

Les profils Traffic Manager avec plusieurs points de terminaison offrent une meilleure disponibilité en cas d’échec d’un point de terminaison donné. Le placement de points de terminaison dans différentes régions améliore la fiabilité du service. Advisor identifie les profils Traffic Manager où il n’existe qu’un seul point de terminaison et recommande d’ajouter au moins un point de terminaison dans une autre région.

Si tous les points de terminaison d’un profil Traffic Manager configuré pour le routage de proximité se trouvent dans la même région, les utilisateurs des autres régions peuvent subir des retards de connexion. L’ajout ou le déplacement d’un point de terminaison vers une autre région améliorera les performances globales et offrira une meilleure disponibilité si tous les points de terminaison d’une région échouent. Advisor identifie les profils Traffic Manager configurés pour le routage de proximité où les points de terminaison se trouvent dans la même région. Il vous recommande d’ajouter ou de déplacer un point de terminaison dans une autre région Azure.

Si un profil Traffic Manager est configuré pour le routage géographique, le trafic est acheminé vers les points de terminaison selon les régions définies. En cas d’échec d’une région, il n’existe aucun basculement prédéfini. Le fait de disposer d’un point de terminaison où le regroupement régional est configuré pour « Tous (monde) » permet d’éviter les pertes de trafic et améliore la disponibilité du service. Advisor identifie les profils Traffic Manager configurés pour le routage géographique où il n’existe aucun point de terminaison configuré avec le regroupement régional « Tous (monde) ». Il recommande cette modification de configuration.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Utilisez la suppression réversible sur votre compte de stockage Azure pour enregistrer et récupérer des données après une suppression ou un remplacement accidentel

Activez la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) sur votre compte de stockage afin que les blobs supprimés passent dans un état de suppression réversible au lieu d’être supprimés de façon permanente. Quand les données sont remplacées, un instantané du blob supprimé de manière réversible est généré pour enregistrer l’état des données remplacées. La suppression réversible vous permet de récupérer les données supprimées en cas de suppression ou d’écrasement accidentel. Advisor identifie les comptes de stockage Azure pour lesquels l’option de suppression réversible n’est pas activée et vous suggère de l’activer.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configurer votre passerelle VPN en mode actif/actif à des fins de résilience de connexion

En configuration actif/actif, les deux instances d’une passerelle VPN établissent des tunnels S2S VPN vers votre appareil VPN local. Lorsqu'un événement de maintenant planifié ou non planifié se produit sur une instance de passerelle, le trafic bascule automatiquement vers l'autre tunnel IPsec actif. Azure Advisor identifie les passerelles VPN non configurées en mode actif/actif et vous suggère que les configurer pour la haute disponibilité.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Utiliser des passerelles VPN de production pour exécuter vos charges de travail de production

Azure Advisor recherchera des passerelles VPN qui sont une référence SKU de base et vous recommandera d’utiliser une référence SKU de production à la place. Les références SKU de base sont conçues à des fins de développement et de test. Les références SKU de production offrent plus de tunnels, une prise en charge du protocole BGP, des options de configuration actif-actif, une stratégie Ipsec/IKE personnalisée ainsi qu’une stabilité et une disponibilité plus élevées.

## <a name="repair-invalid-log-alert-rules"></a>Réparer les règles d’alerte de journal invalides

Azure Advisor détectera les règles d’alerte comportant des requêtes non valides spécifiées dans leur section des conditions. Les règles d’alerte de journal sont créées dans Azure Monitor et sont utilisés pour exécuter des requêtes d’analytique à des intervalles spécifiés. Les résultats de la requête déterminent si une alerte doit être déclenchée. Les requêtes d’analytique peuvent devenir non valides au fil du temps en raison de modifications dans les ressources, les tables ou les commandes référencées. Advisor vous recommandera de corriger la requête dans la règle d’alerte pour éviter qu’elle ne se désactive automatiquement, ce qui garantit aussi une couverture de la supervision de vos ressources dans Azure. [En savoir plus sur la résolution des problèmes liés aux règles d’alerte](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurer un mode d’indexation cohérent sur votre collection Cosmos DB

Les conteneurs Azure Cosmos DB configurés avec le mode Indexation en différé peuvent affecter l’actualisation des résultats de requête. Advisor détecte les conteneurs configurés de cette façon et vous recommande de basculer en mode Cohérent. [En savoir plus sur les stratégies d’indexation Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurer vos conteneurs Azure Cosmos DB avec une clé de partition

Azure Advisor identifiera les collections Azure Cosmos DB non partitionnées qui ont presque atteint leur quota de stockage approvisionné. Il vous recommandera de migrer ces collections vers de nouvelles collections avec une définition de clé de partition afin que le service les mette à l’échelle automatiquement. [En savoir plus sur le choix d’une clé de partition](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Mettre à niveau votre SDK .NET Azure Cosmos DB avec la dernière version de Nuget

Azure Advisor identifiera les comptes Azure Cosmos DB qui utilisent des anciennes versions du kit de développement logiciel (SDK) .NET et vous recommandera de mettre à niveau avec la dernière version de Nuget pour obtenir les derniers correctifs, des améliorations de performances et de nouvelles fonctionnalités. [En savoir plus sur le kit de développement logiciel (SDK) .NET de Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Mettre à niveau votre SDK Java Azure Cosmos DB avec la dernière version de Maven

Azure Advisor identifiera les comptes Azure Cosmos DB qui utilisent des anciennes versions du kit de développement logiciel (SDK) Java et vous recommandera de mettre à niveau avec la dernière version de Maven pour obtenir les derniers correctifs, des améliorations de performances et de nouvelles fonctionnalités. [En savoir plus sur le kit de développement logiciel (SDK) Java de Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Mettre à niveau votre connecteur Spark Azure Cosmos DB avec la dernière version de Maven

Azure Advisor identifiera les comptes Azure Cosmos DB qui utilisent des anciennes versions du connecteur Spark Cosmos DB et vous recommandera de mettre à niveau avec la dernière version de Maven pour obtenir les derniers correctifs, des améliorations de performances et de nouvelles fonctionnalités. [Pour en savoir plus sur le connecteur Spark Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Activer la réplication des machines virtuelles
Les machines virtuelles pour lesquelles la réplication n’est pas activée vers une autre région ne sont pas résilientes face aux pannes régionales. La réplication des machines virtuelles réduit les impacts négatifs sur l’activité pendant la durée de la panne d’une région Azure. Advisor détectera les machines virtuelles pour lesquelles la réplication n’est pas activée et vous recommandera de l’activer afin d’afficher rapidement vos machines virtuelles dans une région Azure distante en cas de panne. [En savoir plus sur la réplication de machines virtuelles](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de haute disponibilité dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Haute disponibilité**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’excellence opérationnelle Advisor](advisor-operational-excellence-recommendations.md)
