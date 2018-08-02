---
title: Recommandations du conseiller Azure en matière de haute disponibilité | Microsoft Docs
description: Utilisez le conseiller Azure pour améliorer la haute disponibilité de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 297a213fe4219b834187f977e3281eb939352f60
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249061"
---
# <a name="advisor-high-availability-recommendations"></a>Recommandations du conseiller en matière de haute disponibilité

Azure Advisor vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de haute disponibilité auprès du conseiller à partir de l’onglet **Haute disponibilité** du tableau de bord du conseiller.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Assurer la tolérance de panne des machines virtuelles

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Le conseiller identifie les machines virtuelles qui ne font pas partie d’un groupe à haute disponibilité et recommande de les déplacer dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez choisir de créer un groupe à haute disponibilité pour la machine virtuelle ou d’ajouter la machine virtuelle à un groupe à haute disponibilité existant.

> [!NOTE]
> Si vous choisissez de créer un groupe à haute disponibilité, vous devez y ajouter au moins une machine virtuelle supplémentaire. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité pour garantir qu’au moins une d’elles reste disponible en cas de panne.

## <a name="ensure-availability-set-fault-tolerance"></a>Assurer la tolérance de panne d’un groupe à haute disponibilité 

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Advisor identifie les groupes à haute disponibilité contenant une seule machine virtuelle et recommande d’y ajouter au moins une machine virtuelle. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez créer une machine virtuelle ou ajouter une machine virtuelle existante au groupe à haute disponibilité.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Assurer la tolérance de panne d’une passerelle
Pour garantir la continuité métier des applications stratégiques qui sont alimentées par des passerelles d’application, Advisor identifie les instances de passerelle d’application qui ne sont pas configurées pour la tolérance de panne, et suggère des mesures de mise à jour que vous pouvez prendre. Advisor identifie les passerelles d’application de moyenne ou grande taille à instance unique, et recommande d’ajouter au moins une instance de plus. Il identifie également les petites passerelles d’application à une ou plusieurs instances et recommande de migrer vers des références de moyenne ou grande taille. Advisor recommande ces actions pour assurer que vos instances de passerelle d’application sont configurées pour satisfaire les exigences actuelles du contrat de niveau de service pour ces ressources.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Améliorer les performances et la fiabilité des disques de machine virtuelle

Advisor identifie les machines virtuelles avec des disques Standard et recommande la mise à niveau vers des disques Premium.
 
Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent des comptes Premium Storage stockent les données sur des disques SSD. Pour que votre application bénéficie de performances optimales, nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium. 

Si votre disque ne nécessite pas une valeur élevée d’E/S par seconde, vous pouvez limiter les coûts en le maintenant en stockage standard. Le stockage standard stocke les données de disque des machines virtuelles sur des disques durs (HDD) au lieu de disques SSD. Vous pouvez choisir de migrer vos disques de machines virtuelles vers des disques Premium. Les disques Premium sont pris en charge sur la plupart des références de machines virtuelles. Toutefois, dans certains cas, si vous souhaitez utiliser des disques Premium, vous devrez également mettre à niveau la référence de votre machine virtuelle.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Protégez les données de vos machines virtuelles d’une suppression accidentelle

La configuration de la sauvegarde de machine virtuelle permet de garantir la disponibilité de vos données métier stratégiques et offre une protection contre une suppression accidentelle ou un endommagement des données.  Advisor identifie les machines virtuelles sur lesquelles la sauvegarde n’est pas activée, et recommande l’activation de la sauvegarde. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Assurez-vous d’avoir accès à des experts du cloud Azure chaque fois que vous en avez besoin

Lors de l’exécution d’une charge de travail critique, il est important d’avoir accès au support technique en cas de besoin. Advisor identifie les éventuels abonnements critiques dont le plan de support ne comprend aucun accès au support technique et recommande d’effectuer une mise à niveau pour choisir une option qui inclut le support technique.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Créer des alertes Azure Service Health pour être averti lorsque des problèmes Azure vous concernent

Vous recommandons de configurer des alertes Azure Service Health pour recevoir une notification lorsque des problèmes Azure vous concernent. [Azure Service Health](https://azure.microsoft.com/features/service-health/) est un service gratuit qui offre conseils et support personnalisés lorsque vous êtes concerné par un problème de service Azure. Advisor identifie les abonnements qui n’ont pas d’alertes configurées et recommande de créer une.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurer des points de terminaison Traffic Manager à des fins de résilience

Les profils Traffic Manager avec plusieurs points de terminaison offrent une meilleure disponibilité en cas d’échec d’un point de terminaison donné. Le placement de points de terminaison dans différentes régions améliore la fiabilité du service. Advisor identifie les profils Traffic Manager où il n’existe qu’un seul point de terminaison et recommande d’ajouter au moins un point de terminaison dans une autre région.

Si tous les points de terminaison d’un profil Traffic Manager configuré pour le routage de proximité se trouvent dans la même région, les utilisateurs des autres régions peuvent subir des retards de connexion. L’ajout ou le déplacement d’un point de terminaison vers une autre région améliorera les performances globales et offrira une meilleure disponibilité si tous les points de terminaison d’une région échouent. Advisor identifie les profils Traffic Manager configurés pour le routage de proximité où les points de terminaison se trouvent dans la même région et vous recommande d’ajouter ou de déplacer un point de terminaison dans une autre région Azure.

Si un profil Traffic Manager est configuré pour le routage géographique, le trafic est acheminé vers les points de terminaison selon les régions définies. En cas d’échec d’une région, il n’existe aucun basculement prédéfini. Le fait de disposer d’un point de terminaison où le regroupement régional est configuré pour « Tous (monde) » permet d’éviter les pertes de trafic et améliore la disponibilité du service. Advisor identifie les profils Traffic Manager configurés pour le routage géographique où il n’existe aucun point de terminaison configuré avec le regroupement régional « Tous (monde) » et recommande cette modification de configuration.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de haute disponibilité dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Haute disponibilité**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)

