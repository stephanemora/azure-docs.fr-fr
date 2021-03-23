---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563235"
---
| Modèle d’utilisation | Mode de mise en cache | Vérification backend | Délai maximal d’écriture différée |
|--|--|--|--|
| Lire les écritures lourdes et peu fréquentes | Lire | Jamais | None |
| Opérations d’écriture supérieures à 15 % | Lecture/écriture | 8 heures | 20 minutes |
| Les clients ignorent le cache | Lire | 30 secondes | None |
| Écritures supérieures à 15 %, vérification fréquente du serveur principal (30 secondes) | Lecture/écriture | 30 secondes | 20 minutes |
| Écritures supérieures à 15 %, vérification fréquente du serveur principal (60 secondes) | Lecture/écriture | 60 secondes | 20 minutes |
| Écritures supérieures à 15 %, écritures différées fréquentes | Lecture/écriture | 30 secondes | 30 secondes |
| Lectures intensives, vérification du serveur de stockage toutes les 3 heures | Lire | 3 heures | None |
