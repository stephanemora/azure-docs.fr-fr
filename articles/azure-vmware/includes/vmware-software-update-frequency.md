---
title: Fréquence des mises à jour de logiciel VMware
description: Fréquence des mises à jour de logiciel VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869860"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft est responsable de la gestion du cycle de vie des logiciels VMware (ESXi, vCenter, PSC et NXS) dans le cloud privé Azure VMware Solution.

Les logiciels du cloud privé sont mis à niveau selon une planification qui suit la version de l’offre logicielle groupée de VMware. Votre cloud privé ne nécessite pas de temps d’arrêt pour les mises à niveau.

Les mises à niveau de l’offre logicielle groupée de cloud privé conservent dans la version de l’offre logicielle groupée la plus récente de VMware. Les versions de logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, vSAN).

Il existe plusieurs mises à jour de logiciels :

- **Correctifs** : correctifs de sécurité ou correctifs logiciels publiés par VMware
- **Mises à jour** : changement de version mineur d’un composant de la pile VMware
- **Mises à niveau** : changement de version majeur d’un composant de la pile VMware

Microsoft teste les mises à jour de sécurité critiques dès qu’elles sont mises à disposition par VMware.

Les solutions de contournement VMware documentées sont implémentées à la place de l’installation d’un patch correspondant jusqu’au déploiement des prochaines mises à jour planifiées. 
