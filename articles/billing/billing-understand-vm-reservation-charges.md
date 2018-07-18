---
title: Comprendre la remise liée aux instances réservées Azure | Microsoft Docs
description: Découvrez comment la remise d’instance de machine virtuelle réservée Azure est appliquée aux machines virtuelles en cours d’exécution.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064321"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Comprendre comment la remise liée aux instances réservées est appliquée
Quand vous achetez une instance de machine virtuelle réservée Azure, la remise est automatiquement appliquée aux machines virtuelles correspondant aux attributs et à la quantité de l’instance réservée. Une instance réservée couvre les coûts d’infrastructure de vos machines virtuelles. Le tableau suivant montre les coûts de votre machine virtuelle après l’achat d’une instance réservée. Dans tous les cas, vous êtes facturé pour le stockage et la mise en réseau selon les tarifs normaux.

| Type de machine virtuelle  | Frais avec instance réservée |    
|-----------------------|--------------------------------------------|
|Machines virtuelles Linux sans logiciel supplémentaire | L’instance réservée couvre les coûts d’infrastructure de vos machines virtuelles.|
|Machines virtuelles Linux avec frais de logiciel (par exemple, Red Hat) | L’instance réservée couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels supplémentaires.|
|Machines virtuelles Windows sans logiciel supplémentaire |L’instance réservée couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels Windows.|
|Machines virtuelles Windows avec logiciels supplémentaires (par exemple, SQL server) | L’instance réservée couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels Windows et les logiciels supplémentaires.|
|Machines virtuelles Windows avec [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | L’instance réservée couvre les coûts d’infrastructure. Les coûts des logiciels Windows sont couverts par Azure Hybrid Benefit. Tout logiciel supplémentaire est facturé séparément.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Application de la remise liée à l’instance réservée à des machines virtuelles non-Windows
 La remise liée à l’instance réservée Azure est appliquée aux instances de machine virtuelle en cours d’exécution, sur une base horaire. Les instances réservées que vous avez achetées sont mises en correspondance avec l’utilisation générée par les machines virtuelles en cours d’exécution pour appliquer la remise liée à l’instance réservée. Pour les machines virtuelles qui ne s’exécutent pas pendant une heure entière, l’instance réservée est complétée avec d’autres machines virtuelles qui n’utilisent pas d’instance réservée, y compris celles qui s’exécutent simultanément. À la fin de l’heure, l’application de l’instance réservée aux machines virtuelles est verrouillée. Quand une machine virtuelle ne s’exécute pas pendant une heure ou quand des machines virtuelles s’exécutant simultanément durant l’heure ne consomment pas complètement l’heure de l’instance réservée, l’instance réservée est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une instance réservée à l’utilisation de machines virtuelles facturables. L’illustration correspond à l’achat d’une instance réservée et à deux instances de machine virtuelle correspondantes.

![Capture d’écran avec une instance réservée appliquée et deux instances de machine virtuelle correspondantes](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Toute utilisation supérieure à la ligne de l’instance réservée est facturée au tarif standard de paiement à l’utilisation. Aucune facturation n’est appliquée en cas d’utilisation inférieure à la ligne des instances réservées, car elle est déjà incluse dans l’achat de l’instance réservée.
2.  Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3.  Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par l’instance réservée et l’autre est facturée au tarif du paiement à l’utilisation.
4.  Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par l’instance réservée, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos instances réservées Azure dans les rapports d’utilisation de la facturation, consultez [Comprendre l’utilisation des instances réservées](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Application de la remise liée aux instances réservées à des machines virtuelles Windows
Quand vous exécutez des instances de machine virtuelle Windows, l’instance réservée est appliquée pour couvrir les coûts d’infrastructure. L’application de l’instance réservée aux coûts d’infrastructure des machines virtuelles Windows est identique à celle qui est appliquée pour les machines virtuelles non-Windows. Vous payez séparément, par processeur virtuel, pour les logiciels Windows. Consultez [Coûts des logiciels Windows avec les instances réservées](https://go.microsoft.com/fwlink/?linkid=862756). Vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit pour Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances réservées, consultez les articles suivants :

- [Présentation des instances de machines virtuelles réservées Azure](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer des instances réservées dans Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’instance réservée pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation des instances réservées pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
