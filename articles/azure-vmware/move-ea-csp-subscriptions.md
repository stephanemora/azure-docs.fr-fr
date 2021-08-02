---
title: Déplacer l’abonnement Azure VMware Solution vers un autre abonnement
description: Cet article explique comment déplacer un abonnement Azure VMware Solution vers un autre abonnement. Vous pouvez déplacer vos ressources pour diverses raisons, telles que la facturation.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 04/26/2021
ms.openlocfilehash: 02f35efe90d4670c4ab38155f129ec0d61c1b03d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966703"
---
# <a name="move-azure-vmware-solution-subscription-to-another-subscription"></a>Déplacer l’abonnement Azure VMware Solution vers un autre abonnement

Cet article explique comment déplacer un abonnement Azure VMware Solution vers un autre abonnement. Vous pouvez déplacer votre abonnement pour diverses raisons, telles que la facturation.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer au moins des droits de contributeur sur les abonnements **source** et **cible**. 

>[!IMPORTANT]
>Le réseau virtuel et la passerelle de réseau virtuel ne peuvent pas être déplacés d’un abonnement à un autre. En outre, le déplacement de vos abonnements n’a aucun impact sur la gestion et les charges de travail, comme les machines virtuelles vCenter, NSX et de charge de travail.

## <a name="prepare-and-move"></a>Préparer et déplacer 

1. Dans le portail Azure, sélectionnez le cloud privé que vous souhaitez déplacer.

   :::image type="content" source="media/move-subscriptions/source-subscription-id.png" alt-text="Capture d’écran montrant les détails de la vue d’ensemble du cloud privé sélectionné.":::

1. À partir d’une invite de commandes, effectuez un test ping sur les composants et les charges de travail pour vérifier qu’ils effectuent un test ping à partir du même abonnement.  

   :::image type="content" source="media/move-subscriptions/verify-components-workloads.png" alt-text="Capture d’écran montrant la commande ping et les résultats du test ping.":::

1. Sélectionnez le lien **Abonnement (modifier)** .

   :::image type="content" source="media/move-subscriptions/private-cloud-overview-subscription-id.png" alt-text="Capture d’écran montrant les détails du cloud privé."::: 

1. Fournissez les détails de l’abonnement pour la **cible**, puis sélectionnez **Suivant**.

   :::image type="content" source="media/move-subscriptions/move-resources-subscription-target.png" alt-text="Capture d’écran de la ressource cible.":::

1. Confirmez la validation des ressources que vous avez sélectionnées pour être déplacées.  Cette opération permet de valider toutes les ressources sélectionnées pour être déplacées. Pendant la validation des ressources sélectionnées, vous verrez l’état **Validation en attente**. 

   :::image type="content" source="media/move-subscriptions/pending-move-resources-subscription-target.png" alt-text="Capture d’écran montrant le déplacement de la ressource.":::

1. Une fois la validation terminée, sélectionnez **Suivant** pour démarrer la migration de votre cloud privé.

   :::image type="content" source="media/move-subscriptions/move-resources-succeeded.png" alt-text="Capture d’écran montrant l’état de validation Opération réussie.":::

1. Cochez la case indiquant que vous comprenez que les outils et les scripts associés ne fonctionneront pas tant que vous ne les aurez pas mis à jour pour utiliser les nouveaux ID de ressource. Sélectionnez ensuite **Déplacer**.

   :::image type="content" source="media/move-subscriptions/review-move-resources-subscription-target.png" alt-text="Capture d’écran montrant le résumé de la ressource sélectionnée qui est déplacée.":::

## <a name="verify-the-move"></a>Vérifier le déplacement

Une notification s’affiche une fois le déplacement de la ressource terminé. 

:::image type="content" source="media/move-subscriptions/notification-move-resources-subscription-target.png" alt-text="Capture d’écran de la notification une fois le déplacement des ressources terminé.":::

Le nouvel abonnement s’affiche dans l’onglet Vue d’ensemble du cloud privé.

:::image type="content" source="media/move-subscriptions/moved-subscription-target.png" alt-text="Capture d’écran montrant un nouvel abonnement.":::

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus :

- [Déplacer Azure VMware Solution entre les régions](move-azure-vmware-solution-across-regions.md)
- [Conseils sur le déplacement de ressources réseau](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [Conseils pour le déplacement de machines virtuelles](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [Conseils de déplacement de ressources App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)