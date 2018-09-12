---
title: Comprendre la remise Azure Reserved VM Instances | Microsoft Docs
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: de4a771672b45f930385c2c923b3935c65b6c045
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301644"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Comprendre comment la remise de réservation Azure est appliquée aux machines virtuelles

Quand vous achetez une instance de machine virtuelle réservée Azure, la remise de réservation est automatiquement appliquée aux machines virtuelles qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre les coûts de calcul de vos machines virtuelles.

Pour la capacité réservée SQL Database, consultez [Comprendre la remise des instances réservées Azure](billing-understand-reservation-charges.md).

Le tableau suivant montre les coûts de votre machine virtuelle après l’achat d’une instance de machine virtuelle réservée. Dans tous les cas, les frais de stockage et de mise en réseau vous sont facturés aux tarifs normaux.

| Type de machine virtuelle  | Frais avec instance de machine virtuelle réservée |
|-----------------------|--------------------------------------------|
|Machines virtuelles Linux sans logiciel supplémentaire | La réservation couvre les coûts d’infrastructure des machines virtuelles.|
|Machines virtuelles Linux avec frais de logiciel (par exemple, Red Hat) | La réservation couvre les coûts d’infrastructure. Les logiciels supplémentaires vous sont facturés.|
|Machines virtuelles Windows sans logiciel supplémentaire |La réservation couvre les coûts d’infrastructure. Les logiciels Windows vous sont facturés.|
|Machines virtuelles Windows avec logiciels supplémentaires (par exemple, SQL server) | La réservation couvre les coûts d’infrastructure. Les logiciels Windows et les autres logiciels vous sont facturés.|
|Machines virtuelles Windows avec [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | La réservation couvre les coûts d’infrastructure. Les coûts des logiciels Windows sont couverts par Azure Hybrid Benefit. Tout logiciel supplémentaire est facturé séparément.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Application de la remise de réservation aux machines virtuelles non Windows

 La remise de réservation Azure est appliquée aux instances de machine virtuelle en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les machines virtuelles en cours d’exécution pour appliquer la remise de réservation. Pour les machines virtuelles qui ne peuvent pas s’exécuter pendant une heure entière, la réservation est remplie à partir d’autres machines virtuelles n’utilisant pas de réservation, incluant des machines virtuelles s’exécutant simultanément. À la fin de l’heure, l’application de réservation pour les machines virtuelles dans l’heure est verrouillée. Quand une machine virtuelle ne s’exécute pas pendant une heure ou quand des machines virtuelles s’exécutant simultanément dans l’heure ne remplissent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une réservation à l’utilisation de machines virtuelles facturables. La représentation est basée sur l’achat d’une réservation et sur deux instances de machine virtuelle correspondantes.

![Capture d’écran montrant une réservation appliquée et deux instances de machine virtuelle correspondantes](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Toute utilisation supérieure à la ligne de réservation est facturée au tarif standard de paiement à l’utilisation. Toute utilisation sous la ligne de réservation n’est pas facturée, car elle a déjà été payée dans le cadre de l’achat de la réservation.
2. Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3. Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par la réservation, et l’autre est facturée au tarif du paiement à l’utilisation.
4. Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par la réservation, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports d’utilisation pour la facturation, voir [Comprendre l’utilisation des réservations](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Application de la remise de réservation aux machines virtuelles Windows

Quand vous exécutez des instances de machine virtuelle Windows, la réservation est appliquée pour couvrir les coûts d’infrastructure. L’application de la réservation aux coûts d’infrastructure de machine virtuelle pour les machines virtuelles Windows est identique à celle qui est appliquée pour les machines virtuelles non Windows. Les logiciels Windows vous sont facturés séparément, par processeur virtuel. Vous [Coûts des logiciels Windows avec les réservations](https://go.microsoft.com/fwlink/?linkid=862756). Vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit pour Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, voir les articles suivants :

- [Que sont les réservations Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
