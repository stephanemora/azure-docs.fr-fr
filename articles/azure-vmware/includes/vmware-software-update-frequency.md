---
title: Fréquence des mises à jour de logiciel VMware
description: Fréquence des mises à jour de logiciel VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 3295ab9da93b1e1ec1cace62653670de366c880d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324313"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

La maintenance et la gestion du cycle de vie de l’hôte n’ont aucune incidence sur la capacité ou les performances des clusters du cloud privé. Les mises à niveau des logiciels du cloud privé s’effectuent selon une planification qui suit la version du bundle de logiciels de VMware.  Ainsi, votre cloud privé ne nécessite pas de temps d’arrêt pour les mises à niveau.

Les mises à niveau de l’offre logicielle groupée de cloud privé conservent dans la version de l’offre logicielle groupée la plus récente de VMware. Les versions de logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, vSAN). Les mises à jour concernent également les pilotes, les logiciels sur les commutateurs réseau et les micrologiciels sur les nœuds nus.

Vous serez averti avant et après l’application des correctifs sur vos clouds privés. Nous travaillerons également avec vous pour planifier une fenêtre de maintenance avant d’appliquer des mises à jour ou des mises à niveau à votre cloud privé. 

Il existe plusieurs mises à jour de logiciels :

- **Correctifs** : correctifs de sécurité ou correctifs logiciels publiés par VMware
- **Mises à jour** : changement de version mineur d’un composant de la pile VMware
- **Mises à niveau** : changement de version majeur d’un composant de la pile VMware

>[!NOTE]
>Microsoft teste les mises à jour de sécurité critiques dès qu’elles sont mises à disposition par VMware.

Les solutions de contournement VMware documentées sont implémentées à la place de l’installation d’un patch correspondant jusqu’au déploiement des prochaines mises à jour planifiées.
