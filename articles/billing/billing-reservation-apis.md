---
title: API dédiées à l’automatisation de la réservation Azure | Microsoft Docs
description: Découvrez les API Azure que vous pouvez utiliser pour obtenir par programmation les informations de réservation.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: banders
ms.openlocfilehash: a71c456f017e80da85f27437738aa963ec967a3c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904155"
---
# <a name="apis-for-azure-reservation-automation"></a>API dédiées à l’automatisation de la réservation Azure

Utilisez des API Azure afin d’obtenir par programmation des informations pour votre organisation sur les réservations de logiciels ou de services Azure.

## <a name="find-reservation-plans-to-buy"></a>Rechercher des plans de réservation à acheter

Utilisez l’API de suggestions de réservation pour obtenir des suggestions sur le plan de réservations à acheter en fonction de l’utilisation au sein de votre organisation. Pour plus d’informations, consultez [Get reservation recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) (Obtenir des suggestions de réservation).

Vous pouvez également analyser votre utilisation des ressources grâce aux détails d’utilisation de l’API de consommation. Pour plus d’informations, consultez [Usage Details - List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount) (Détails d’utilisation : liste pour la période de facturation par compte de facturation). Les ressources Azure que vous utilisez de manière régulière sont généralement la meilleure option de réservation.

## <a name="buy-a-reservation"></a>Acheter une réservation

Actuellement, vous ne pouvez pas acheter de réservation par programmation. Pour acheter une réservation, consultez les articles suivants :

Plans de service :
- [Machine virtuelle](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Base de données SQL](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Abonnements logiciels :
- [Logiciel SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Obtenir des réservations

Si vous êtes un client Azure et si vous disposez d’un Contrat Entreprise (client EA), vous pouvez obtenir les réservations que votre organisation a achetées à l’aide de l’[API d’obtention des frais de transaction des instances réservées](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Pour les autres abonnements, obtenez la liste des réservations que vous avez achetées et que vous êtes autorisé à afficher à l’aide de l’API [de commande de réservation - Liste](/rest/api/reserved-vm-instances/reservationorder/list). Par défaut, le propriétaire du compte ou la personne qui a acheté la réservation dispose des autorisations requises pour afficher la réservation.

## <a name="see-reservation-usage"></a>Voir l’utilisation des réservations

Si vous êtes client EA, vous pouvez afficher par programmation la manière dont les réservations sont utilisées dans votre organisation. Pour plus d’informations, consultez [Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) (Obtenir des informations sur l’utilisation de l’instance réservée pour les clients Entreprise). Pour les autres abonnements, utilisez l’API [de résumés de réservations - Liste par commande de réservation et réservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Si vous constatez que les réservations de votre organisation sont sous-utilisées :

- Assurez-vous que les machines virtuelles créées par votre organisation correspondent à la taille de machine virtuelle qui se trouve sur la réservation.
- Assurez-vous que la flexibilité de taille d’instance est activée. Pour plus d’informations, consultez [Gérer les réservations - Modifier le paramètre d’optimisation pour des instances de machine virtuelle réservées](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Modifiez l’étendue de réservation en partage afin qu’elle s’applique plus largement. Pour plus d’informations, consultez [Gérer les réservations - Modifier l’étendue d’une réservation](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- Échangez la quantité inutilisée. Pour plus d’informations, consultez [Gérer les réservations - Annulations et échanges](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Accorder l’accès aux réservations

Obtenez la liste de toutes les réservations auxquelles un utilisateur a accès grâce à l’[API de réservation - Opération - Liste](/rest/api/reserved-vm-instances/reservationorder/list). Pour accorder l’accès à une réservation par programmation, consultez les articles suivants :

- [Gérer l’accès avec RBAC et l’API REST](../role-based-access-control/role-assignments-rest.md)
- [Gérer l’accès avec RBAC et Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Split or merge reservation (Diviser ou fusionner des réservations)

Après avoir acheté plusieurs instances de ressource dans une réservation, vous pouvez affecter de instances de cette réservation à d’autres abonnements. Vous pouvez modifier l’étendue de réservation afin qu’elle s’applique à tous les abonnements dans le même contexte de facturation. Mais pour faciliter la budgétisation et la gestion des coûts, vous pouvez conserver l’étendue en tant qu’« abonnement unique » et assigner des instances de réservation à un abonnement spécifique. 

Pour diviser une réservation, utilisez l’API [de réservation - Diviser](/rest/api/reserved-vm-instances/reservation/split). Vous pouvez également diviser une réservation à l’aide de PowerShell. Pour plus d’informations, consultez [Manage reservations - Split reservation into two reservations](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations) (Gérer les réservations - Diviser les réservations en deux réservations).

Pour fusionner deux réservations en une seule réservation, utilisez l’API [de réservation - Fusionner](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Modifier l’étendue d’une réservation

L’étendue d’une réservation peut être un abonnement unique ou tous les abonnements de votre contexte de facturation. Si vous définissez l’étendue à un seul abonnement, la réservation est mise en correspondance avec les ressources en cours d’exécution dans l’abonnement sélectionné. Si vous définissez l’étendue pour qu’elle soit partagée, Azure met en correspondance la réservation avec les ressources exécutées dans tous les abonnements du contexte de facturation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter la réservation. Pour plus d’informations, consultez [Manage reservations - Change the scope](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation) (Gérer les réservations - Modifier l’étendue).

Pour modifier l’étendue par programmation, utilisez l’API [de réservation - Mise à jour](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>En savoir plus

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise sur réservation de machine virtuelle est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre comment la remise sur offre logicielle SUSE Linux Enterprise est appliquée](billing-understand-suse-reservation-charges.md)
- [Comprendre comment les autres remises sur réservation sont appliquées](billing-understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)