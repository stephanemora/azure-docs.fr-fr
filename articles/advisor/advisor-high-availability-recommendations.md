---
title: Améliorer la disponibilité de votre application avec Azure Advisor | Microsoft Docs
description: Utilisez le conseiller Azure pour améliorer la haute disponibilité de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdba3f135f852312af1692f77643095d865f1d06
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254665"
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

En configuration actif-actif, les deux instances d’une passerelle VPN établira tunnels S2S VPN pour votre périphérique VPN sur site. Lorsqu'un événement de maintenant planifié ou non planifié se produit sur une instance de passerelle, le trafic bascule automatiquement vers l'autre tunnel IPsec actif. Azure Advisor identifie les passerelles VPN non configurées en mode actif/actif et vous suggère que les configurer pour la haute disponibilité.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Utiliser des passerelles VPN de production pour exécuter vos charges de travail de production

Azure Advisor vérifie pour les passerelles VPN qui sont une référence SKU de base et vous recommandons d’utiliser une référence (SKU) de production à la place. La référence SKU de base est conçu pour le développement et à des fins de tests. Références SKU de production offre un nombre plus élevé de tunnels, prise en charge du protocole BGP, les options de configuration actif-actif, personnalisé stratégie Ipsec/IKE et plus élevé la stabilité et de disponibilité.

## <a name="repair-invalid-log-alert-rules"></a>Réparer des règles d’alerte de journal non valide

Azure Advisor détectera les règles d’alerte qui ont des requêtes non valides spécifiés dans la section de la condition. Les règles d’alerte sont créés dans Azure Monitor et sont utilisés pour exécuter des requêtes d’analytique à des intervalles spécifiés. Les résultats de la requête déterminent si une alerte doit être déclenchée. Requêtes d’Analytique peuvent devenir des heures supplémentaires non valide en raison de modifications dans les ressources référencées, les tables ou les commandes. Assistant recommandera que vous corrigez la requête dans la règle d’alerte pour empêcher son obtention automatique-désactivé et vous assurer de la couverture de surveillance de vos ressources dans Azure. [En savoir plus sur la résolution des problèmes de règles d’alerte](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurer le mode d’indexation cohérent sur votre collection Cosmos DB

Les conteneurs Azure Cosmos DB configurés avec le mode d’indexation différé peuvent avoir une incidence sur l’actualisation des résultats de la requête. L’Assistant détecte conteneurs configurées de cette façon et est recommandé de basculer en mode cohérent. [En savoir plus sur l’indexation de stratégies dans Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurez vos conteneurs Azure Cosmos DB avec une clé de partition

Azure Advisor identifie les collections Azure Cosmos DB non partitionnées qui sont approchent de leur quota de stockage approvisionné. Il vous recommande de migrer ces collections pour les nouvelles collections avec une définition de clé de partition afin qu’ils peuvent être automatiquement montés en charge par le service. [En savoir plus sur le choix d’une clé de partition](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Mise à niveau de votre SDK .NET Azure Cosmos DB vers la dernière version à partir de Nuget

Azure Advisor identifie les comptes Azure Cosmos DB qui sont à l’aide des anciennes versions du SDK .NET et recommande la mise à niveau vers la dernière version à partir de Nuget pour les derniers correctifs, les améliorations de performances et les nouvelles fonctionnalités. [En savoir plus sur Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Mise à niveau de votre SDK Java Azure Cosmos DB vers la dernière version à partir de Maven

Azure Advisor identifie les comptes Azure Cosmos DB qui utilisent des versions anciennes du kit SDK Java et recommande la mise à niveau vers la dernière version à partir de Maven pour les derniers correctifs, les améliorations de performances et les nouvelles fonctionnalités. [En savoir plus sur Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Mettre à niveau votre connecteur Spark Azure Cosmos DB vers la dernière version à partir de Maven

Azure Advisor identifie les comptes Azure Cosmos DB qui utilisent des versions anciennes du connecteur Spark Cosmos DB et recommande la mise à niveau vers la dernière version à partir de Maven pour les derniers correctifs, les améliorations de performances et les nouvelles fonctionnalités. [En savoir plus sur le connecteur Spark Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

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

