---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 8607fd6f01ea02841017d09dac7236812f2330b9
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133731"
---
| Ressource | Limite par défaut |
| --- | --- |
| Machines virtuelles par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> par région. |
| Nombre total de cœurs de machine virtuelle par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| Nombre total de cœurs de machine virtuelle Azure Spot par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| Machine virtuelle par série, telle que Dv2, et F, cœurs par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| [Groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) par abonnement |2 000 par région. |
| Machines virtuelles par groupe à haute disponibilité | 200 |
| Certificats par abonnement |Illimité<sup>2</sup> |

<sup>1</sup>Les limites par défaut varient selon le type de catégorie d’offre, comme la version d’évaluation gratuite et le paiement à l’utilisation, et selon la série, par exemple dv2, F et G. Par exemple, la valeur par défaut pour les abonnements Accord Entreprise est 350.

<sup>2</sup> Avec Azure Resource Manager, les certificats sont stockés dans Azure Key Vault. Le nombre de certificats est illimité pour un abonnement. Un déploiement est limité à 1 Mo de certificat, dont une machine virtuelle ou un groupe à haute disponibilité.

> [!NOTE]
> Les cœurs de machines virtuelles sont soumis à une limite totale régionale. Ils ont également une limite pour les séries par taille régionales, telles que Dv2 et F. Ces limites sont appliquées séparément. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement peut déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total. Par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1.  
> <!-- -->
> 
