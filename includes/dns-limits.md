---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805650"
---
**Zones DNS publiques**

| Ressource | Limite |
| --- | --- |
| Zones DNS publiques par abonnement |250 <sup>1</sup> |
| Jeux d’enregistrements par zone DNS publique |10 000 <sup>1</sup> |
| Enregistrements par jeu d’enregistrements dans la zone DNS publique |20 |
| Nombre d’enregistrements d’alias pour une seule ressource Azure |20|
| Zones DNS privées par abonnement |1 000|
| Jeux d’enregistrements par zone DNS privée |25000|
| Enregistrements par jeu d’enregistrements pour les zones DNS privées |20|
| Liens de réseau virtuel par zone DNS privée |1 000|
| Liens de réseau virtuel par zone DNS privée avec l’inscription automatique activée |100|
| Nombre de zones DNS privées auxquelles un réseau virtuel peut être lié avec l’inscription automatique activée |1|
| Nombre de zones DNS privées auxquelles un réseau virtuel peut être lié |1 000|
| Nombre de requêtes DNS qu'une machine virtuelle peut envoyer au résolveur Azure DNS, par seconde |500 <sup>2</sup> |
| Nombre maximum de requêtes DNS mises en file d'attente (réponse en attente) par machine virtuelle |200 <sup>2</sup> |

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.

<sup>2</sup>Ces limites sont appliquées à chaque machine virtuelle individuelle, et non au niveau du réseau virtuel. Les requêtes DNS dépassant ces limites sont supprimées.
