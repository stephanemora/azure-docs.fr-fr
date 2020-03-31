---
title: Fichier include
description: Fichier include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7cfa6e9810057493cc3007eec7fd1668a70c727e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77179044"
---
L’utilisation de machines virtuelles Spot vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot. Les machines virtuelles Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

La capacité disponible dépend de divers facteurs, tels que la taille, la région, l’heure, etc. Quand vous déployez des machines virtuelles Spot, Azure alloue la capacité disponible aux machines virtuelles, le cas échéant. Sachez toutefois qu’il n’y a pas de contrat SLA pour ces machines virtuelles. Une machine virtuelle Spot n’offre aucune garantie de haute disponibilité. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot avec un préavis de 30 secondes. 

> [!IMPORTANT]
> Les instances Spot sont actuellement en préversion publique.
> Cette préversion n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="eviction-policy"></a>Stratégie d’éviction

Les machines virtuelles peuvent être supprimées en fonction de la capacité ou du prix maximal que vous avez défini. Pour les machines virtuelles, la stratégie d’éviction est définie sur *Libérer*. De cette façon, vos machines virtuelles évincées passent à l’état arrêté-libéré, ce qui vous permet de les redéployer ultérieurement. Toutefois, la réallocation des machines virtuelles Spot dépend de la capacité disponible. Les machines virtuelles libérées sont comptabilisées dans votre quota d’instances de processeurs virtuels Spot, et vos disques sous-jacents vous seront facturés. 

Les utilisateurs peuvent s’abonner pour recevoir des notifications dans la machine virtuelle via [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Vous serez ainsi informé si vos machines virtuelles sont en cours d’éviction, et vous aurez 30 secondes pour terminer vos tâches et arrêter la machine virtuelle avant que ne commence l’éviction. 


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

Les machines virtuelles Spot ne peuvent pas utiliser des disques de système d’exploitation éphémères.

Les machines virtuelles Spot peuvent être déployées sur n’importe quelle région, à l’exception de Microsoft Azure Chine 21Vianet.

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


**Q :** Quels sont les canaux qui prennent en charge les machines virtuelles Spot ?

**R :** Consultez le tableau ci-dessous pour connaître la disponibilité des machines virtuelles Spot.

<a name="channel"></a>

| Canaux Azure               | Disponibilité des machines virtuelles Azure Spot       |
|------------------------------|-----------------------------------|
| Contrat Entreprise         | Oui                               |
| Paiement à l’utilisation                | Oui                               |
| Fournisseur de services cloud (CSP) | [Contactez votre partenaire](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Avantages                     | Non disponible                     |
| Sponsorisé                    | Non disponible                     |
| Version d’évaluation gratuite                   | Non disponible                     |


**Q :** Où puis-je poster des questions ?

**R :** Vous pouvez poster et étiqueter vos questions avec `azure-spot` sur [Questions et réponses](https://docs.microsoft.com/answers/topics/azure-spot.html). 



