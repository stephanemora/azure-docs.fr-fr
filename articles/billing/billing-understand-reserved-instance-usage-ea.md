---
title: Comprendre l’utilisation des réservations Azure pour Entreprise | Microsoft Docs
description: Découvrez comment analyser votre utilisation pour comprendre l’application de la réservation Azure pour votre Accord de Mise en Œuvre Entreprise.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 5ce0103315f297996ed3f3bd88b5e53558e22e14
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628230"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Comprendre l’utilisation d’une réservation Azure pour votre Accord de Mise en Œuvre Entreprise

Pour évaluer l’utilisation d’une réservation, utilisez **l’ID de réservation** situé dans la [page Réservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) et le fichier d’utilisation du [portail EA](https://ea.azure.com). Vous pouvez également consulter l’utilisation de la réservation dans la section récapitulative de l’utilisation du [portail EA](https://ea.azure.com).

Si vous avez acheté la réservation dans un contexte de facturation de paiement à l’utilisation, consultez [Comprendre l’utilisation de la réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Utilisation d’instances de machine virtuelle réservées Azure

Les sections suivantes supposent que vous exécutez une machine virtuelle Windows Standard_D1_v2 dans la région USA Est et que vos informations de réservation ressemblent au contenu du tableau suivant :

| Champ | Valeur |
|---| --- |
|ID de réservation |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantité |1|
|SKU | D1 standard|
|Région | eastus |

La partie matérielle de la machine virtuelle est traitée, car la machine virtuelle déployée correspond aux attributs de la réservation. Pour savoir quels logiciels Windows ne sont pas couverts par la réservation, consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Utilisation dans un fichier CSV pour les instances de machine virtuelle réservées

Vous pouvez télécharger le fichier CSV d’utilisation Enterprise à partir d’Enterprise Portal. Dans le fichier CSV, filtrez les données sur **Informations supplémentaires**, puis tapez votre **ID de réservation**. La capture d’écran suivante affiche les champs associés à la réservation :

![Fichier CSV du Contrat Entreprise pour la réservation Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **L’ID de réservation** dans le champ **Infos supplémentaires** représente la réservation appliquée à la machine virtuelle.
2. Le **compteur de consommation** est l’ID du compteur de la machine virtuelle.
3. **L’ID du compteur** est le compteur de réservation avec un coût de 0 dollar US. Le coût de la machine virtuelle en cours d’exécution est payé par l’instance de machine virtuelle réservée.
4. Standard_D1 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Ce compteur couvre donc les frais supplémentaires des logiciels Windows. Pour trouver le compteur correspondant à la machine virtuelle à 1 cœur de la série D, consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md).  Si vous avez Azure Hybrid Benefit, ces frais supplémentaires ne sont pas appliqués.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>Utilisation pour les réservations de capacité réservée SQL Database

Dans les sections suivantes, supposez que vous exécutez une machine virtuelle SQL Database de 4e génération dans la région USA Est, et que vos informations de réservation ressemblent au contenu du tableau suivant :

| Champ | Valeur |
|---| --- |
|ID de réservation |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Quantité |2|
|Produit| SQL Database 4e génération (2 cœurs)|
|Région | eastus |

### <a name="usage-in-csv-file-for-sql-database-reserved-capacity"></a>Utilisation dans un fichier CSV pour la capacité réservée SQL Database

Filtrez les données sur **Informations supplémentaires**, puis tapez votre **ID de réservation**. La capture d’écran suivante affiche les champs associés à la réservation.

![Fichier CSV du Contrat Entreprise pour la capacité réservée SQL Database](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **L’ID de réservation** dans le champ **Infos supplémentaires** représente la réservation appliquée à la ressource SQL Database.
2. Le **compteur de consommation** représente l’ID du compteur de la ressource SQL Database.
3. **L’ID du compteur** est le compteur de réservation avec un coût de 0 dollar US. Cet ID de compteur est indiqué dans le fichier CSV pour toute ressource SQL Database répondant aux critères de la réservation.

## <a name="usage-summary-page-in-enterprise-portal"></a>Page récapitulative de l’utilisation dans Enterprise Portal

L’utilisation de la réservation Azure apparaît également dans la section récapitulative de l’utilisation d’Enterprise Portal : ![Récapitulatif de l’utilisation du Contrat Entreprise](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png).

1. Le composant matériel de la machine virtuelle ne vous est pas facturé, car il est couvert par la réservation. Pour une réservation SQL Database, une ligne avec **Nom du service** indique l’utilisation de la capacité réservée Azure SQL Database.
2. Dans cet exemple, vous n’avez pas Azure Hybrid Benefit. Les logiciels Windows utilisés avec la machine virtuelle vous sont donc facturés.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md) 
- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de réservation est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.