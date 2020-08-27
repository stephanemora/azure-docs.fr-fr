---
title: Utiliser des machines virtuelles Azure Spot
description: Découvrez comment utiliser des machines virtuelles Azure Spot pour réaliser des économies sur les coûts.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/20/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: c0b8f395dde1d94c4c1efa32a2f78707d1456d88
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817119"
---
# <a name="use-spot-vms-in-azure"></a>Utiliser des machines virtuelles Spot dans Azure

L’utilisation de machines virtuelles Spot vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot. Les machines virtuelles Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

La capacité disponible dépend de divers facteurs, tels que la taille, la région, l’heure, etc. Quand vous déployez des machines virtuelles Spot, Azure alloue la capacité disponible aux machines virtuelles, le cas échéant. Sachez toutefois qu’il n’y a pas de contrat SLA pour ces machines virtuelles. Une machine virtuelle Spot n’offre aucune garantie de haute disponibilité. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot avec un préavis de 30 secondes. 


## <a name="eviction-policy"></a>Stratégie d’éviction

Les machines virtuelles peuvent être supprimées en fonction de la capacité ou du prix maximal que vous avez défini. Quand vous créez une VM spot, vous pouvez affecter à la stratégie d’éviction la valeur *Libérer* (par défaut) ou *Supprimer*. 

La stratégie *Libérer* attribue à votre machine virtuelle l’état « arrêté-libéré », ce qui vous permet de la redéployer par la suite. Toutefois, la réussite de l’allocation n’est pas garantie. Les machines virtuelles libérées sont comptabilisées dans votre quota, et les disques sous-jacents engendrent des frais de stockage. 

Si vous souhaitez que la machine virtuelle soit supprimée lorsqu’elle est écartée, définissez la stratégie d’éviction avec la valeur *Supprimer*. Les machines virtuelles évincées sont supprimées en même temps que leurs disques sous-jacents. Le stockage ne vous est donc pas facturé. 

Vous pouvez vous abonner pour recevoir des notifications dans la machine virtuelle avec [Azure Scheduled Events](./linux/scheduled-events.md). Vous serez ainsi informé si vos machines virtuelles sont en cours d’éviction, et vous aurez 30 secondes pour terminer vos tâches et arrêter la machine virtuelle avant que ne commence l’éviction. 


| Option | Résultat |
|--------|---------|
| Le prix maximal doit être supérieur ou égal au prix actuel. | La machine virtuelle est déployée si la capacité et le quota sont disponibles. |
| Le prix maximal doit être supérieur au prix actuel. | La machine virtuelle n’est pas déployée. Vous obtiendrez un message d’erreur indiquant que le prix maximal doit être supérieur ou égal au prix actuel. |
| Redémarrage d’une machine virtuelle à l’état Arrêté/Libéré si le prix maximal est supérieur ou égal au prix actuel | Si la capacité et le quota sont suffisants, la machine virtuelle est déployée. |
| Redémarrage d’une machine virtuelle à l’état Arrêté/Libéré si le prix maximal est inférieur au prix actuel | Vous obtiendrez un message d’erreur indiquant que le prix maximal doit être supérieur ou égal au prix actuel. | 
| Le prix de la machine virtuelle a augmenté et il est désormais supérieur au prix maximal. | La machine virtuelle est évincée. Vous recevez une notification 30 secondes avant l’éviction. | 
| Après éviction, le prix de la machine virtuelle redevient inférieur au prix maximal. | La machine virtuelle ne sera pas redémarrée automatiquement. Vous pouvez redémarrer la machine virtuelle vous-même et celle-ci sera facturée au tarif actuel. |
| Si le prix maximal est défini sur `-1` | La machine virtuelle ne sera pas supprimée pour des raisons de tarif. Le prix maximal sera le prix actuel (au maximum le prix des machines virtuelles standard). Le prix facturé ne dépassera jamais le tarif standard.| 
| Modification du prix maximal | Vous devez libérer la machine virtuelle pour modifier le prix maximal. Libérez la machine virtuelle, définissez un nouveau prix maximal, puis mettez à jour la machine virtuelle. |


## <a name="limitations"></a>Limites

Les tailles de machine virtuelle suivantes ne sont pas prises en charge pour les machines virtuelles Spot :
 - Série B
 - Versions promotionnelles de toutes les tailles (Dv2, NV, NC, H, etc.)

Les machines virtuelles Spot peuvent être déployées sur n’importe quelle région, à l’exception de Microsoft Azure Chine 21Vianet.

<a name="channel"></a>

Les [types d’offres](https://azure.microsoft.com/support/legal/offer-details/) suivants sont pris en charge :

-   Contrat Entreprise
-   Paiement à l’utilisation
-   Sponsorisé
- Pour le fournisseur de services cloud (CSP), contactez votre partenaire.


## <a name="pricing"></a>Tarifs

Les tarifs des machines virtuelles Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


En raison de la variabilité des tarifs, vous avez la possibilité de définir un prix maximal en dollars américains (USD) ayant jusqu’à 5 décimales. Par exemple, la valeur `0.98765` correspond à un prix maximal de 0,98765 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.


##  <a name="frequently-asked-questions"></a>Forum aux questions

**Q :** Une fois créée, la machine virtuelle Spot est-elle identique à la machine virtuelle standard habituelle ?

**R :** Oui, sauf qu’il n’existe aucun contrat SLA pour les machines virtuelles Spot et qu’elles peuvent être supprimées à tout moment.


**Q :** Que faire quand votre machine virtuelle est supprimée alors que vous avez encore besoin de capacité ?

**R :** Nous vous recommandons d’utiliser des machines virtuelles standard au lieu de machines virtuelles Spot si vous avez besoin de capacité immédiatement.


**Q :** Comment le quota de Machines virtuelles Spot est-il géré ?

**R :** Les machines virtuelles Spot auront un pool de quotas distinct. Le quota Spot est partagé entre les machines virtuelles et les instances de groupe identique. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q :** Puis-je demander une augmentation de mon quota pour Spot ?

**R :** Oui, vous pouvez demander une augmentation de votre quota pour les machines virtuelles Spot via la [procédure de demande de quota standard](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Q :** Où puis-je poster des questions ?

**R :** Vous pouvez poster et étiqueter vos questions avec `azure-spot` sur [Questions et réponses](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Étapes suivantes
Utilisez l’[interface CLI](./linux/spot-cli.md), le [portail](./windows/spot-portal.md), le [modèle Resource Manager](./linux/spot-template.md) ou [PowerShell](./windows/spot-powershell.md) pour déployer des machines virtuelles Spot.

Vous pouvez également déployer un [groupe identique avec des instances de machine virtuelle Spot](../virtual-machine-scale-sets/use-spot.md).

Si vous rencontrez une erreur, consultez [Codes d’erreur](./error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
