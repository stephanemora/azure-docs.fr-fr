---
title: Dépannage d’Elastic - Solutions partenaires Azure
description: Cet article fournit des informations sur la résolution des problèmes de l’intégration d’Elastic à Azure
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4bc4bc9d9bb5890ed95bbb446f570d25e43d813
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952562"
---
# <a name="troubleshooting-elastic-integration-with-azure"></a>Résolution des problèmes d’intégration d’Elastic à Azure

Ce document contient des informations sur le dépannage de vos solutions qui utilisent Elastic.

## <a name="unable-to-create-an-elastic-resource"></a>Impossible de créer une ressource Elastic

L’intégration d’Elastic avec Azure peut uniquement être configurée par les utilisateurs disposant d’un accès *Propriétaire* sur l’abonnement Azure. [Confirme que vous disposez de l’accès approprié](../../role-based-access-control/check-access.md).

## <a name="logs-not-being-emitted-to-elastic"></a>Journaux non émis vers Elastic

Seules les ressources répertoriées dans les [catégories de journaux de ressources Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md) émettent des journaux vers Elastic. Pour vérifier si la ressource émet des journaux vers Elastic, accédez au [paramètre de diagnostic Azure](../../azure-monitor/essentials/diagnostic-settings.md) pour la ressource spécifique. Vérifiez qu’une option de paramétrage de diagnostic est disponible.

:::image type="content" source="media/troubleshoot/check-diagnostic-setting.png" alt-text="Vérifiez les paramètres de diagnostic":::

## <a name="purchase-errors"></a>Erreurs d’achat

* L’achat échoue car aucune carte de crédit valide n’est connectée à l’abonnement Azure, ou car aucun mode de paiement n’est associé à l’abonnement.

  Utilisez un autre abonnement Azure. Vous pouvez également ajouter ou mettre à jour la carte de crédit ou le mode de paiement de l’abonnement. Pour plus d’informations, consultez [Mise à jour de la carte de crédit et du mode de paiement](../../cost-management-billing/manage/change-credit-card.md).

* L’abonnement EA n’autorise pas les achats de la Place de marché.

  Utilisez un autre abonnement. Ou vérifiez si votre abonnement EA est activé pour les achats de la Place de marché. Pour plus d’informations, voir [Activer les achats de la Place de marché](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).

## <a name="get-support"></a>Obtenir un support

Pour contacter le support technique de l’intégration d’Elastic avec Azure, sélectionnez **Nouvelle demande de support** dans le volet gauche. Sélectionnez **Ouvrir un ticket de support Elastic**.

:::image type="content" source="media/troubleshoot/open-ticket.png" alt-text="Ouvrir un ticket de support":::

Sur le site d’Elastic, ouvrez une demande de support.

:::image type="content" source="media/troubleshoot/elastic-support.png" alt-text="Ouvrir le support d’Elastic":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [gestion de votre instance](manage.md) Elastic.