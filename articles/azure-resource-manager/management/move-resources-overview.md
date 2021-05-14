---
title: Déplacez des ressources Azure entre des groupes de ressources, des abonnements ou des régions.
description: Vue d’ensemble des types de ressources Azure qui peuvent être déplacés entre des groupes de ressources, des abonnements ou des régions.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730277"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Déplacer des ressources Azure entre des groupes de ressources, des abonnements ou des régions

Les ressources Azure peuvent être déplacées vers un nouveau groupe de ressources ou un nouvel abonnement, ou entre différentes régions.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Déplacer des ressources entre des abonnements ou des groupes de ressources

Vous pouvez déplacer les ressources Azure vers un autre abonnement Azure ou un autre groupe de ressources sous le même abonnement. Vous pouvez utiliser le portail Azure, Azure PowerShell, Azure CLI ou l’API REST pour déplacer des ressources. Pour en savoir plus, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](move-resource-group-and-subscription.md).

### <a name="upgrade-a-subscription"></a>Mettre à niveau un abonnement

Vous devez convertir votre abonnement Azure si vous souhaitez le mettre à niveau (par exemple, passer d’une offre gratuite au paiement à l’utilisation).

- Pour mettre à niveau votre essai gratuit, consultez [Passer d’un essai gratuit ou d’un abonnement Azure Microsoft Imagine au paiement à l’utilisation](../../cost-management-billing/manage/upgrade-azure-subscription.md).
- Pour changer un compte de paiement à l’utilisation, consultez [Remplacer votre abonnement Paiement à l’utilisation Azure par une autre offre](../../cost-management-billing/manage/switch-azure-offer.md).

Si vous ne pouvez pas convertir l’abonnement, [créez une demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Sélectionnez **Gestion des abonnements** comme type de problème.

## <a name="move-resources-across-regions"></a>Déplacer des ressources entre les régions

Les zones géographiques, les régions et les zones de disponibilité Azure constituent la base de l’infrastructure mondiale d’Azure. Les [zones géographiques](https://azure.microsoft.com/global-infrastructure/geographies/) d’Azure contiennent généralement au moins deux [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/). Une région est une zone géographique contenant des zones de disponibilité et plusieurs centres de données.

Une fois les ressources déployées dans une région Azure spécifique, il existe plusieurs raisons pour lesquelles vous pouvez souhaiter déplacer des ressources vers une autre région.

- **Aligner sur un lancement d’une région** : Déplacer vos ressources vers une région Azure récemment introduite qui n’était pas disponible auparavant.
- **Aligner en raison des services/fonctionnalités** : Déplacer des ressources pour profiter des services ou fonctionnalités qui sont disponibles dans une région spécifique.
- **Répondre au développement de l’activité** : Déplacer des ressources vers une région en réponse à des changements de l’activité, comme des fusions ou des acquisitions.
- **Aligner pour cause de proximité** : Déplacer des ressources vers une région proche de votre entreprise.
- **Respecter les exigences relatives aux données** : Déplacer des ressources afin de respecter les exigences de résidence des données ou les besoins en matière de classification des données. [Plus d’informations](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)
- **Respecter les exigences de déploiement** : Déplacer des ressources qui ont été déployées par erreur ou déplacez-les en réponse aux besoins de capacité.
- **Respecter la désaffectation** : Déplacer des ressources en raison de la désaffectation des régions.

### <a name="move-resources-with-resource-mover"></a>Déplacer des ressources avec Resource Mover

Vous pouvez déplacer des ressources vers une autre région avec [Azure Resource Mover](../../resource-mover/overview.md). Resource Mover fournit :

- Un hub unique pour déplacer des ressources entre les régions.
- Une réduction de la complexité et du temps de déplacement. Tout ce dont vous avez besoin se trouve à un même emplacement.
- Une expérience simple et cohérente pour le déplacement des différents types de ressources Azure.
- Un moyen simple d’identifier les dépendances des ressources que vous souhaitez déplacer. Cette identification vous permet de déplacer les ressources associées ensemble, pour que tout fonctionne comme prévu dans la région cible après le déplacement.
- Le nettoyage automatique des ressources dans la région source, si vous souhaitez les supprimer après le déplacement
- Tests. Vous pouvez essayer un déplacement, puis l’annuler si vous ne voulez pas effectuer un déplacement complet.

Vous pouvez déplacer des ressources vers une autre région en utilisant deux méthodes différentes :

- **Commencer le déplacement des ressources à partir d’un groupe de ressources** : Avec cette méthode, vous commencez le changement de région à partir d’un groupe de ressources. Une fois que vous avez sélectionné les ressources que vous voulez déplacer, le processus continue dans le hub Resource Mover, où a lieu la vérification des dépendances des ressources et l’orchestration du processus de déplacement. [Plus d’informations](../../resource-mover/move-region-within-resource-group.md)
- **Commencer le déplacement des ressources directement à partir du hub Resource Mover** : Avec cette méthode, vous commencez le changement de région directement dans le hub. [Plus d’informations](../../resource-mover/tutorial-move-region-virtual-machines.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier si un type de ressource prend en charge le déplacement, consultez [Prise en charge des opérations de déplacement pour les ressources](move-support-resources.md).
- Pour en savoir plus sur le processus de déplacement entre régions, consultez [À propos du processus de déplacement](../../resource-mover/about-move-process.md).
