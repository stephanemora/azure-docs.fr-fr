---
title: Fichier Include
description: Fichier Include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060941"
---
**Name** | **URL commerciale**  | **URL du gouvernement** | **Description** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Élément utilisé pour la gestion des identités et le contrôle d’accès avec AAD 
Sauvegarde | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Élément utilisé pour la coordination et le transfert des données de réplication 
Réplication | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication 
Stockage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Élément utilisé pour l’accès au compte de stockage qui stocke les données répliquées 
Données de télémétrie (facultatif) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Élément utilisé pour la télémétrie 
Synchronisation temporelle | ``time.windows.com`` | ``time.nist.gov`` | Élément utilisé pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale lors de tous les déploiements.


