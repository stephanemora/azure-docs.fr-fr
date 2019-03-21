---
title: Fichier Include
description: Fichier Include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553611"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Machines virtuelles par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> par région. |25 000 par région. |
| Nombre total de cœurs de machine virtuelle par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. | Contactez le support technique. |
| Machine virtuelle par série, telle que Dv2 et F, cœurs par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. | Contactez le support technique. |
| [Coadministrateurs](../articles/billing-add-change-azure-subscription-administrator.md) par abonnement |Illimité. |Illimité. |
| [Comptes de stockage](../articles/storage/common/storage-quickstart-create-account.md) par région et par abonnement |200 |200<sup>2</sup> |
| [Groupes de ressources](../articles/azure-resource-manager/resource-group-overview.md) par abonnement |980 |980 |
| [Groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) par abonnement |2 000 par région. |2 000 par région. |
| Taille de la demande Azure API Resource Manager |4,194,304 d’octets. |4,194,304 d’octets. |
| Balises par abonnement<sup>3</sup> |Illimité. |Illimité. |
| Calculs de balise unique par abonnement<sup>3</sup> | 10 000 | 10 000 |
| [Services cloud](../articles/cloud-services/cloud-services-choose-me.md) par abonnement |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Groupes d'affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) par abonnement |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/deploy-to-subscription.md) par emplacement | 800 | 800 |

<sup>1</sup>limites par défaut varient en type de catégorie d’offre, telles que la version d’évaluation gratuite et de paiement à l’utilisation et en série, telle que Dv2, F et G.

<sup>2</sup>les comptes de stockage Premium et Standard sont inclus. Si vous avez besoin de plus de 200 comptes de stockage, sollicitez un [prise en charge Azure](https://azure.microsoft.com/support/faq/). L’équipe de stockage Azure examine votre cas commercial et peut approuver jusqu'à 250 comptes de stockage.

<sup>3</sup>Vous pouvez appliquer un nombre illimité de balises par abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 15. Resource Manager retourne un [liste de valeurs et le nom de balise unique](/rest/api/resources/tags) dans l’abonnement uniquement lorsque le nombre de balises est de 10 000 ou moins. Vous pouvez toujours trouver une ressource par balise lorsque le nombre dépasse 10 000.  

<sup>4</sup>ces fonctionnalités ne sont plus nécessaires avec les groupes de ressources Azure et Resource Manager.

> [!NOTE]
> Cœurs de machines virtuelles ont une limite totale régionale. Ils ont également une limite pour les séries par taille régionales, telles que Dv2 et F. Ces limites sont appliquées séparément. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement peut déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1 ou une combinaison des deux afin de ne pas dépasser 30 cœurs au total. Un exemple d’une combinaison est de 10 machines virtuelles A1 et 20 machines virtuelles D1.  
> <!-- -->
> 
> 

