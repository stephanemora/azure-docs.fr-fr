---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: c232875715253b3601c7f9a5ecc289705e3dcf40
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108212827"
---
| Ressource | Limite |
| --- | :--- |
| Groupes de conteneurs de la référence SKU Standard par région et par abonnement | 100<sup>1</sup> |
| Groupes de conteneurs de la référence SKU dédiée par région et par abonnement | 0<sup>1</sup> |
| Nombre de conteneurs par groupe de conteneurs | 60 |
| Nombre de volumes par groupe de conteneurs | 20 |
| Cœurs de la référence SKU Standard (CPU) par région par abonnement | 10<sup>1,2</sup> | 
| Cœurs de la référence SKU Standard (CPU) pour GPU K80 par région par abonnement | 18<sup>1,2</sup> |
| Cœurs de la référence SKU Standard (CPU) pour GPU P100 ou V100 par région par abonnement | 0<sup>1,2</sup> |
| Ports par adresse IP | 5 |
| Taille de journal d’instance de conteneur - instance en cours d’exécution | 4 Mo |
| Taille de journal d’instance de conteneur - instance arrêtée | 16 Ko ou 1 000 lignes |
| Création de groupes de conteneurs par heure |300<sup>1</sup> |
| Création de groupes de conteneurs toutes les 5 minutes | 100<sup>1</sup> |
| Suppression de groupes de conteneurs par heure | 300<sup>1</sup> |
| Suppression de groupes de conteneurs toutes les 5 minutes | 100<sup>1</sup> |


<sup>1</sup>Pour réclamer un relèvement de la limite, créez une [demande de support Azure][azure-support]. Les abonnements gratuits, notamment le [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) et [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) ne sont pas éligibles à des augmentations de limite ou de quota. Si vous avez un abonnement gratuit, vous pouvez [passer](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) à un abonnement Paiement à l’utilisation.<br />
<sup>2</sup>Limite par défaut pour l’abonnement [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). La limite peut être différente pour les autres types de catégorie.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
