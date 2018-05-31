---
title: Comprendre l’utilisation des instances réservées Azure pour l’entreprise - Facturation Azure | Microsoft Docs
description: Découvrez comment analyser votre utilisation pour comprendre la manière dont est appliqué Azure Reserved VM Instance à votre inscription Entreprise.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301312"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Comprendre l’utilisation des instances réservées Azure pour votre inscription Entreprise
Pour comprendre l’utilisation de l’instance réservée, utilisez **l’ID de réservation** situé dans la [page Réservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), et dans le fichier d’utilisation du [portail EA](https://ea.azure.com). Vous pouvez également consulter l’utilisation de l’instance réservée dans le récapitulatif de l’utilisation du [portail EA](https://ea.azure.com).

>[!NOTE]
>Si vous avez acheté l’instance réservée dans un contexte de facturation avec paiement à l’utilisation, consultez [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md).

Pour la section suivante, supposez que vous exécutez une machine virtuelle Windows Standard_D1_v2 dans la région États-Unis de l’Est, et que les informations de votre instance réservée ressemblent à celles du tableau suivant :

| Champ | Valeur |
|---| --- |
|ID de réservation |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantité |1|
|SKU | D1 standard|
|Région | eastus |

## <a name="reserved-instance-application"></a>Application de l’instance réservée

La partie matérielle de la machine virtuelle est traitée, car la machine virtuelle déployée correspond aux attributs de l’instance réservée. Pour savoir quels logiciels Windows ne sont pas couverts par l’offre d’instance réservée, accédez à [Coûts des logiciels Windows dans Azure Reserved VM Instances](billing-reserved-instance-windows-software-costs.md).


### <a name="reserved-instance-usage-in-csv"></a>Utilisation de l’instance réservée dans un fichier .csv
Vous pouvez télécharger le fichier csv de l’utilisation partagée du Contrat Entreprise à partir du portail EA. Dans le fichier .csv téléchargé, filtrez les données pour n’afficher que les informations supplémentaires, puis tapez votre **ID de réservation**. La capture d’écran suivante montre les champs associés à l’instance réservée :

![Fichier .csv du Contrat Entreprise pour l’instance réservée Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. Dans le champ Infos supplémentaires, **l’ID de réservation** représente l’instance réservée qui a été utilisée pour appliquer la remise à la machine virtuelle.
2. Le Compteur de consommation est l’Id du compteur de la machine virtuelle.
3. Il s’agit du Compteur de la réservation, avec un coût affiché de 0 dollar US, puisque le coût d’exécution de la machine virtuelle est déjà payé par l’instance réservée. 
4. Standard_D1 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Par conséquent, ce compteur couvre les frais supplémentaires des logiciels Windows. Consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md) pour trouver le compteur correspondant à la machine virtuelle série D 1 cœur. Si Azure Hybrid Benefit est utilisé, ce coût supplémentaire n’est pas appliqué.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Utilisation de l’instance réservée dans le récapitulatif de l’utilisation du portail EA

L’utilisation de l’instance réservée apparaît également sur le portail EA, dans le récapitulatif de l’utilisation : ![Récapitulatif de l’utilisation du Contrat Entreprise](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vous n’êtes pas facturé pour le composant matériel de la machine virtuelle, car il est couvert par l’offre d’instance réservée. 
2. Vous êtes facturé pour les logiciels Windows puisque Azure Hybrid Benefit n’est pas utilisé. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances réservées Azure, consultez les articles suivants :

- [Réaliser des économies sur les machines virtuelles avec les instances réservées Azure](billing-save-compute-costs-reservations.md)
- [Prépayer les machines virtuelles avec des instances réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer les instances réservées](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.