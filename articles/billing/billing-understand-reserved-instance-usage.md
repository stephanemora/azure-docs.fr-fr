---
title: Comprendre l’utilisation des instances réservées Azure sur votre abonnement avec paiement à l’utilisation | Microsoft Docs
description: Découvrez comment analyser votre utilisation pour comprendre la manière dont est appliqué Azure Reserved VM Instance à votre abonnement avec paiement à l’utilisation.
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
ms.openlocfilehash: 7e303f3e5ce0e618d941be4190f6fadb40f2e09d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064329"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Comprendre l’utilisation des instances réservées Azure sur votre abonnement avec paiement à l’utilisation

Voici une présentation de l’utilisation d’une instance réservée avec l’ID de réservation de page [Réservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) et le fichier d’utilisation du [portail des comptes Azure](https://account.azure.com).


>[!NOTE]
>Cet article ne s’applique pas aux clients Contrat Entreprise. Si vous êtes un client Contrat Entreprise, consultez [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise.](billing-understand-reserved-instance-usage-ea.md) Cet article suppose également que l’instance réservée ne s’applique qu’à un seul abonnement. Si l’instance réservée s’applique à plusieurs abonnements, la remise peut s’étendre à plusieurs fichiers .csv d’utilisation. 

Pour la section suivante, supposez que vous exécutez une machine virtuelle Windows Standard_DS1_v2 dans la région Est des États-Unis, et que les informations de votre instance réservée se présentent comme dans le tableau suivant :

| Champ | Valeur |
|---| :---: |
|ID de réservation |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantité |1|
|SKU | Standard_DS1_v2|
|Région | eastus |

## <a name="reserved-instance-application"></a>Application de l’instance réservée

La partie matérielle de la machine virtuelle est couverte, car la machine virtuelle déployée correspond aux attributs de l’instance réservée. Pour savoir quels logiciels Windows ne sont pas couverts par l’offre d’instance réservée, accédez à [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Section Relevés du fichier csv
Cette section de votre fichier .csv montre l’utilisation totale de votre instance réservée. Appliquez le filtre sur le champ Sous-catégorie du compteur qui contient « Réservation » pour que vos données se présentent comme dans la capture d’écran suivante : ![Capture d’écran des informations détaillées et des frais liés à l’utilisation de l’instance réservée](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

La ligne « Instances réservées-Machine virtuelle de base » indique le nombre total d’heures qui sont couvertes par l’instance réservée. Cette ligne affiche 0,00 dollars US, car l’instance réservée la couvre. La ligne Réservation-Windows Server (1 cœur) couvre le coût des logiciels Windows.

### <a name="daily-usage-section-of-csv"></a>Section Utilisation quotidienne du fichier csv
Filtrez les données pour n’afficher que les informations supplémentaires, puis tapez votre **ID de réservation**. La capture d’écran suivante montre les champs relatifs à l’instance réservée. 

![Capture d’écran de l’utilisation quotidienne et des frais](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. Dans le champ Infos supplémentaires, **ReservationId** représente l’instance réservée qui a été utilisée pour appliquer la remise à la machine virtuelle.
2. Le Compteur de consommation est l’Id du compteur de la machine virtuelle.
3. La ligne « Instances réservées-Machine virtuelle de base » dans Sous-catégorie du compteur correspond à la ligne de coût de 0 dollar US dans la section du relevé. Le coût d’exécution de cette machine virtuelle est déjà payé par l’instance réservée.
4. Il s’agit de l’ID du compteur de l’instance réservée. Le coût de ce compteur est de 0 dollars US. Cet ID de compteur est présent dans le fichier .csv pour chaque machine virtuelle bénéficiant d’une offre d’instance réservée et est pris en compte pour son coût. 
5. Standard_DS1_v2 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Par conséquent, ce compteur couvre les frais supplémentaires des logiciels Windows. Consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md) pour trouver le compteur correspondant à la machine virtuelle série D 1 cœur. Si Azure Hybrid Benefit est utilisé, ce coût supplémentaire n’est pas appliqué. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances réservées, consultez les articles suivants :

- [Présentation des instances de machines virtuelles réservées Azure](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer des instances réservées dans Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’instance réservée pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
