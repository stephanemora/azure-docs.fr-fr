---
title: Déplacer des abonnements Azure VMware Solution d’EA et de CSP
description: Découvrez comment déplacer le cloud privé d’un abonnement à un autre. Le déplacement peut être effectué pour diverses raisons, telles que la facturation.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555049"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Déplacer des abonnements Azure VMware Solution d’EA et de CSP

Dans cet article, vous allez apprendre à déplacer le cloud privé d’un abonnement à un autre. Le déplacement peut être effectué pour diverses raisons, telles que la facturation. 

>[!IMPORTANT]
>Vous devez disposer au moins des droits de contributeur sur les abonnements source et cible. Le réseau virtuel et la passerelle de réseau virtuel ne peuvent pas être déplacés d’un abonnement à un autre. En outre, le déplacement de vos abonnements n’a aucun impact sur la gestion et les charges de travail, comme les machines virtuelles vCenter, NSX et de charge de travail.

1. Connectez-vous au portail Azure et sélectionnez le cloud privé que vous souhaitez déplacer.

1. Sélectionnez le lien **Abonnement (modifier)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Capture d’écran montrant les détails du cloud privé.":::

1. Fournissez les détails de l’abonnement pour la **cible**, puis sélectionnez **Suivant**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Capture d’écran de la ressource cible." lightbox="media/move-resources-subscription-target.png":::

1. Confirmez la validation des ressources que vous avez sélectionnées pour le déplacement, puis sélectionnez **Suivant**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Capture d’écran montrant le déplacement de la ressource." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Cochez la case indiquant que vous comprenez que les outils et les scripts associés ne fonctionneront pas tant que vous ne les aurez pas mis à jour pour utiliser les nouveaux ID de ressource. Sélectionnez ensuite **Déplacer**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Capture d’écran montrant le résumé de la ressource sélectionnée qui est déplacée." lightbox="media/review-move-resources-subscription-target.png":::

   Une notification s’affiche une fois le déplacement de la ressource terminé. Le nouvel abonnement s’affiche dans l’onglet Vue d’ensemble du cloud privé.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Capture d’écran montrant un nouvel abonnement." lightbox="media/moved-subscription-target.png":::

