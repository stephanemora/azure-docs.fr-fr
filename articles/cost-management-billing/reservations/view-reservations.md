---
title: Afficher les réservations pour les ressources Azure| Microsoft Docs
description: Découvrez comment afficher les réservations Azure dans le portail Azure. Affichez les réservations et leur utilisation à l’aide d’API, de PowerShell, de l’interface CLI et de Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 04c38f7445dc0405add88fd61cc6e4943a4fda96
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459264"
---
# <a name="view-azure-reservations"></a>Voir les réservations Azure

Vous pouvez voir et gérer la réservation achetée dans le portail Azure.

## <a name="permissions-to-view-a-reservation"></a>Autorisations pour voir une réservation

Pour voir ou gérer une réservation, vous devez disposer d’une autorisation de lecteur ou de propriétaire. Par défaut, quand vous achetez une réservation, vous et l’administrateur de compte recevez automatiquement le rôle Propriétaire sur la réservation et l’ordre de réservation. Pour autoriser d’autres personnes à afficher la réservation, vous devez les ajouter en tant que **propriétaire** ou **lecteur** sur celle-ci. Le fait d’ajouter une personne à l’abonnement fourni pour la facturation de la réservation n’ajoute pas automatiquement cette personne à la réservation. 

Pour plus d’informations, consultez la rubrique [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Afficher la réservation et l’utilisation dans le portail Azure

Pour afficher une réservation en tant que propriétaire ou que lecteur

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à [Réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. La liste montre toutes les réservations pour lesquelles vous disposez du rôle Propriétaire ou Lecteur. Chaque réservation affiche le dernier pourcentage d’utilisation connu.
4. Cliquez sur le pourcentage d’utilisation pour voir l’historique et les détails de l’utilisation. Consultez les détails dans la vidéo ci-dessous.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Obtenir des réservations et leur utilisation à l’aide d’API, de PowerShell et de l’interface CLI

Obtenir la liste de toutes les réservations à l’aide des ressources suivantes
- [API : Ordre de réservation - Liste](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell : Ordre de réservation - Liste](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI : Ordre de réservation - Liste](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Vous pouvez également obtenir l’[utilisation des réservations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) à l’aide de l’API d’utilisation d’instances réservées. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Voir les réservations et leur utilisation dans Power BI

Les utilisateurs de Power BI ont deux options.
- Pack de contenu : Les achats de réservation et les données d’utilisation sont disponibles dans le [pack de contenu Power BI Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Créez les rapports souhaités à l’aide de ce pack de contenu. 
- Application Cost Management : Utilisez l’[application Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) pour accéder à des rapports précréés que vous pouvez personnaliser.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
