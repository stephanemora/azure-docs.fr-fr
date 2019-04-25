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
ms.openlocfilehash: 793c881d08e8feb038cc6e7ac82b7e95384e1b55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467725"
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

