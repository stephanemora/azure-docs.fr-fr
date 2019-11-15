---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 3608eb6182f7806c0d4df8b96aa69faac68928f1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590691"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Groupes de conteneurs par région et par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Nombre de conteneurs par groupe de conteneurs | 60 |
| Nombre de volumes par groupe de conteneurs | 20 |
| Ports par adresse IP | 5\. |
| Taille de journal d’instance de conteneur - instance en cours d’exécution | 4 Mo |
| Taille de journal d’instance de conteneur - instance arrêtée | 16 Ko ou 1 000 lignes |
| Création de conteneurs par heure |300<sup>1</sup> |
| Création de conteneurs par tranche de 5 minutes | 100<sup>1</sup> |
| Suppression de conteneurs par heure | 300<sup>1</sup> |
| Suppression de conteneurs par tranche de 5 minutes | 100<sup>1</sup> |


<sup>1</sup>Pour réclamer un relèvement de la limite, créez une [demande de support Azure][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
