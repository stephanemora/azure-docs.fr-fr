---
title: Fichier Include
description: Fichier Include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765536"
---
| NOM | URL commerciale | URL du gouvernement | Description |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Élément utilisé pour la gestion des identités et le contrôle d’accès avec AAD |
| Sauvegarde | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Élément utilisé pour la coordination et le transfert des données de réplication |
| Réplication | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication |
| Stockage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Élément utilisé pour l’accès au compte de stockage qui stocke les données répliquées |
| Données de télémétrie (facultatif) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Élément utilisé pour la télémétrie |

Les éléments ``time.nist.gov`` et ``time.windows.com`` sont utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale lors de tous les déploiements.


