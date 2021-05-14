---
title: Afficher l’utilisation d’une réservation Azure
description: Découvrez comment obtenir l’utilisation et les détails d’une réservation.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568747"
---
# <a name="view-reservation-utilization-after-purchase"></a>Afficher l’utilisation d’une réservation après achat

Vous pouvez afficher le pourcentage d’utilisation d’une réservation et les ressources qui ont utilisé la réservation dans le portail Azure et dans l’application Power BI Cost Management.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Afficher l’utilisation dans le portail Azure avec l’accès RBAC Azure

Pour afficher l’utilisation d’une réservation, vous devez disposer d’un accès RBAC Azure à la réservation ou vous devez avoir un accès avec élévation de privilèges pour gérer tous les abonnements et groupes d’administration Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à [Réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. La liste répertorie toutes les réservations sur lesquelles vous avez le rôle propriétaire ou lecteur. Chaque réservation affiche le dernier pourcentage d’utilisation connu.
1. Sélectionnez le pourcentage d’utilisation pour voir l’historique et les détails de l’utilisation. La vidéo ci-après présente un exemple.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Afficher l’utilisation en tant qu’administrateur de facturation

Un administrateur Contrat Entreprise (EA) ou un administrateur de facturation Contrat client Microsoft (MCA) peut afficher l’utilisation dans **Gestion des coûts + facturation**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Gestion des coûts + facturation** > **Réservations**.
1. Sélectionnez le pourcentage d’utilisation pour voir l’historique et les détails de l’utilisation.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Obtenir des réservations et leur utilisation à l’aide d’API, de PowerShell et de l’interface CLI

Vous pouvez obtenir l’[utilisation des réservations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) à l’aide de l’API Utilisation des instances réservées.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Voir les réservations et leur utilisation dans Power BI

Les utilisateurs de Power BI ont deux options :

- Connecteur Azure Cost Management pour Power BI Desktop – Les données d’utilisation et de date d’achat de réservation sont disponibles dans le [Connecteur Azure Cost Management pour Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Créez les rapports que vous voulez à l’aide de ce connecteur.
- Application Power BI Azure Cost Management – Utilisez l’[application Power BI Azure Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) pour profiter de rapports précréés à personnaliser ultérieurement.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md).
- [Comprendre l’utilisation d’une réservation pour votre inscription Entreprise](understand-reserved-instance-usage-ea.md).
- [Comprendre l’utilisation d’une réservation pour les abonnements CSP](/partner-center/azure-reservations).
