---
title: Rechercher un acheteur de réservation à partir des journaux Azure Monitor
description: Cet article aide à rechercher un acheteur de réservation avec des informations issues des journaux Azure Monitor.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: baf7fe1968f2ba2a72e27f1caf85d02c65a3e93f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122566136"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Rechercher un acheteur de réservation à partir des journaux Azure

Cet article aide à rechercher un acheteur de réservation avec des informations issues de vos journaux d’annuaire. Les journaux d’annuaire d’Azure Monitor affichent les ID d’e-mail des utilisateurs qui ont effectué des achats de réservation.

## <a name="find-the-purchaser"></a>Rechercher l’acheteur

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Surveiller** > **Journal d’activité** > **Activité**.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Capture d’écran montrant la navigation dans Journal d’activité – Activité" lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Sélectionnez **Activité de l’annuaire**. Si vous voyez un message indiquant que *vous avez besoin de l’autorisation d’afficher les journaux au niveau de l’annuaire*, sélectionnez le [lien](../../role-based-access-control/elevate-access-global-admin.md) pour apprendre à obtenir des autorisations.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Capture d’écran montrant l’activité de l’annuaire sans l’autorisation d’afficher le journal" lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Une fois que vous avez l’autorisation, filtrez le **fournisseur de ressources de locataire** avec **Microsoft.Capacity**. Vous devez voir tous les événements liés à une réservation pour la période sélectionnée. Si nécessaire, modifiez la période.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Capture d’écran montrant l’utilisateur qui a acheté la réservation" lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Si nécessaire, vous devrez peut-être **modifier les colonnes** pour sélectionner **Événement lancé par**.
   L’utilisateur qui a effectué l’achat de la réservation est indiqué sous **Événement lancé par**.

## <a name="next-steps"></a>Étapes suivantes

- Si nécessaire, les administrateurs de facturation peuvent [prendre possession d’une réservation](view-reservations.md#view-and-manage-reservations).
