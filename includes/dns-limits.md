---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334599"
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