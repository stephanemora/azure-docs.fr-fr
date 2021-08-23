---
ms.service: hpc-cache
ms.topic: include
ms.date: 06/17/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 37f2895ed476cc0dbe0bdb3def034a17448d8dca
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587218"
---
| Modèle d’utilisation | Mode de mise en cache | Vérification backend | Délai maximal d’écriture différée |
|--|--|--|--|
| Lire les écritures lourdes et peu fréquentes <!--read_heavy_infreq-->| Lire | Jamais | None |
| Opérations d’écriture supérieures à 15 % <!--write_workload_15-->| Lecture/écriture | 8 heures | 1 heure |
| Les clients ignorent le cache <!--write_around-->| Lire | 30 secondes | None |
| Écritures supérieures à 15 %, vérification fréquente du serveur principal (30 secondes) <!--write_workload_check_30-->| Lecture/écriture | 30 secondes | 1 heure |
| Écritures supérieures à 15 %, vérification fréquente du serveur principal (60 secondes) <!--write_workload_check_60-->| Lecture/écriture | 60 secondes | 1 heure |
| Écritures supérieures à 15 %, écritures différées fréquentes <!--write_workload_cloudws-->| Lecture/écriture | 30 secondes | 30 secondes |
| Lectures intensives, vérification du serveur de stockage toutes les 3 heures <!--read_heavy_check_180-->| Lire | 3 heures | None |
