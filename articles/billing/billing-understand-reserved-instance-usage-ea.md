---
title: Comprendre l’utilisation des instances réservées Azure pour l’entreprise | Microsoft Docs
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
ms.openlocfilehash: d6e8b2544f919abeb7fde0e37fc12bd29f0171ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064416"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Comprendre l’utilisation des instances réservées Azure pour votre Accord de Mise en Œuvre Entreprise
Voici une présentation de l’utilisation d’une instance réservée avec **l’ID de réservation** qui se trouve dans la [page Réservations](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) et le fichier d’utilisation du [portail EA](https://ea.azure.com). Vous pouvez également consulter l’utilisation de l’instance réservée dans la section du récapitulatif de l’utilisation du [portail EA](https://ea.azure.com).

>[!NOTE]
>Si vous avez acheté l’instance réservée dans un contexte de facturation avec paiement à l’utilisation, consultez [Comprendre l’utilisation de l’instance réservée pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md).

Pour la section suivante, nous supposons que vous exécutez une machine virtuelle Windows Standard_D1_v2 dans la région Est des États-Unis et que les informations de votre instance réservée se présentent comme dans le tableau suivant :

| Champ | Valeur |
|---| --- |
|ID de réservation |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantité |1|
|SKU | D1 standard|
|Région | eastus |

## <a name="reserved-instance-application"></a>Application de l’instance réservée

La partie matérielle de la machine virtuelle est couverte, car la machine virtuelle déployée correspond aux attributs de l’instance réservée. Pour savoir quels logiciels Windows ne sont pas couverts par l’instance réservée, accédez à [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md).


### <a name="reserved-instance-usage-in-csv"></a>Utilisation de l’instance réservée dans un fichier .csv
Vous pouvez télécharger le fichier csv de l’utilisation partagée du Contrat Entreprise à partir du portail EA. Dans le fichier .csv téléchargé, filtrez les données pour n’afficher que les informations supplémentaires, puis tapez votre **ID de réservation**. La capture d’écran suivante montre les champs relatifs à l’instance réservée :

![Fichier .csv du Contrat Entreprise pour l’instance réservée Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. Dans le champ Infos supplémentaires, **ID de réservation** représente l’instance réservée qui a été utilisée pour appliquer la remise à la machine virtuelle.
2. Le Compteur de consommation est l’Id du compteur de la machine virtuelle.
3. Il s’agit du Compteur de la réservation, avec un coût affiché de 0 dollar US, car le coût d’exécution de la machine virtuelle est déjà payé par l’instance réservée. 
4. Standard_D1 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Par conséquent, ce compteur couvre les frais supplémentaires des logiciels Windows. Consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md) pour trouver le compteur correspondant à la machine virtuelle série D 1 cœur. Si Azure Hybrid Benefit est utilisé, ce coût supplémentaire n’est pas appliqué.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Utilisation de l’instance réservée dans le récapitulatif de l’utilisation du portail EA

L’utilisation de l’instance réservée apparaît également dans la section du récapitulatif de l’utilisation du portail EA : ![Récapitulatif de l’utilisation du Contrat Entreprise](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vous n’êtes pas facturé pour le composant matériel de la machine virtuelle, car il est couvert par l’instance réservée. 
2. Vous êtes facturé pour les logiciels Windows puisque Azure Hybrid Benefit n’est pas utilisé. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances réservées Azure, consultez les articles suivants :

- [Présentation des instances de machines virtuelles réservées Azure](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer des instances réservées dans Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.