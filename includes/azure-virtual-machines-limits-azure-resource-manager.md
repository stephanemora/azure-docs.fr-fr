---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 45c959bc3cb71a965f254867798ae32d86ba1e70
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612651"
---
| Ressource | Limite |
| --- | --- |
| Machines virtuelles par [abonnement](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> par région. |
| Nombre total de cœurs de machine virtuelle par [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| Nombre total de cœurs de machine virtuelle Azure Spot par [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| Machine virtuelle par série, telle que Dv2, et F, cœurs par [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> par région. Contactez le support pour augmenter la limite. |
| [Groupes à haute disponibilité](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) par abonnement |2 500 par région. |
| Machines virtuelles par groupe à haute disponibilité | 200 |
| Certificats par groupe à haute disponibilité | 199<sup>2</sup> |
| Certificats par abonnement |Illimité<sup>3</sup> |

<sup>1</sup> Les limites par défaut varient en fonction du type de catégorie d’offre, comme l’essai gratuit et le paiement à l’utilisation, et en fonction de la série, telle que dv2, F et G. Par exemple, la valeur par défaut pour les abonnements Accord Entreprise est 350.

<sup>2</sup> Les propriétés telles que les clés publiques SSH sont également envoyées en tant que certificats, et sont prises en compte pour cette limite. Pour contourner cette limite, utilisez l’[extension Azure Key Vault pour Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows) ou l’[extension Azure Key Vault pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) pour installer des certificats.

<sup>3</sup> Avec Azure Resource Manager, les certificats sont stockés dans Azure Key Vault. Le nombre de certificats est illimité pour un abonnement. Un déploiement est limité à 1 Mo de certificat, dont une machine virtuelle ou un groupe à haute disponibilité.



> [!NOTE]
> Les cœurs de machines virtuelles sont soumis à une limite totale régionale. Ils ont également une limite pour les séries par taille régionales, telles que Dv2 et F. Ces limites sont appliquées séparément. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement peut déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total. Par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1.  
> <!-- -->
>
