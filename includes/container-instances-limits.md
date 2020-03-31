---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334814"
---
| Ressource | Limite |
| --- | :--- |
| Groupes de conteneurs SKU standard par région et par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Groupes de conteneurs SKU dédiés par région et par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Nombre de conteneurs par groupe de conteneurs | 60 |
| Nombre de volumes par groupe de conteneurs | 20 |
| Ports par adresse IP | 5 |
| Taille de journal d’instance de conteneur - instance en cours d’exécution | 4 Mo |
| Taille de journal d’instance de conteneur - instance arrêtée | 16 Ko ou 1 000 lignes |
| Création de conteneurs par heure |300<sup>1</sup> |
| Création de conteneurs par tranche de 5 minutes | 100<sup>1</sup> |
| Suppression de conteneurs par heure | 300<sup>1</sup> |
| Suppression de conteneurs par tranche de 5 minutes | 100<sup>1</sup> |


<sup>1</sup>Pour réclamer un relèvement de la limite, créez une [demande de support Azure][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
