---
title: Fréquence des mises à jour de logiciel VMware
description: Fréquence des mises à jour de logiciel VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 08/24/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 20286e2363001a49905715f2274d94bd23351e9c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867097"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Un avantage des clouds privés Azure VMware Solution est que la plateforme est maintenue pour vous.  Microsoft est responsable de la gestion du cycle de vie des logiciels VMware (ESXi vCenter et vSAN). Microsoft est également responsable de la gestion du cycle de vie des appliances NSX-T et du démarrage de la configuration du réseau, par exemple la création de la passerelle de niveau 0 et l’activation du routage Nord-Sud. Vous êtes responsable de la configuration du SDN NSX-T : segments réseau, règles de pare-feu distribuées, passerelles de niveau 1 et équilibreurs de charge. 

Microsoft est responsable de l’application des correctifs, des mises à jour ou des mises à niveau vers ESXi, vCenter, vSAN et NSX-T dans votre cloud privé. L’impact des correctifs, des mises à jour et des mises à niveau sur ESXi, vCenter et NSX-T est différent. 

- **ESXi** : il n’y a aucun impact sur les charges de travail exécutées dans votre cloud privé. L’accès à vCenter et à NSX-T n’est pas bloqué pendant cette période.  Pendant cette période, il est recommandé de ne pas planifier d’autres activités telles que le scale-up du cloud privé dans votre cloud privé.

- **vCenter** : il n’y a aucun impact sur les charges de travail exécutées dans votre cloud privé. Pendant ce temps, vCenter est indisponible et vous ne pouvez pas gérer les machines virtuelles (arrêter, démarrer, créer ou supprimer). Pendant cette période, il est recommandé de ne pas planifier d’autres activités telles que le scale-up du cloud privé ou la création de réseaux dans votre cloud privé.

- **NSX-T** : il y a un impact sur la charge de travail et quand un hôte particulier est en cours de mise à niveau, les machines virtuelles sur cet hôte peuvent perdre leur connectivité entre 2 secondes et 1 minute maximum avec une partie ou la totalité des symptômes suivants :

   - Erreurs de test Ping

   - Perte de paquets

   - Messages d’erreur (par exemple, *Hôte de destination inaccessible* et *Réseau inaccessible*)

   Au cours de cette fenêtre de mise à niveau, tous les accès au plan de gestion NSX-T sont bloqués. Vous ne pouvez pas changer la configuration de l’environnement NSX-T pendant cette période.  Toutefois, vos charges de travail continuent de s’exécuter normalement, sous réserve de l’impact de la mise à niveau détaillé ci-dessus.
 
   Pendant cette période de mise à niveau, il est recommandé de ne pas planifier d’autres activités telles que le scale-up du cloud privé dans votre cloud privé. Celles-ci peuvent empêcher le démarrage de la mise à niveau ou peuvent avoir un impact négatif sur la mise à niveau et l’environnement.
 
Vous êtes averti avant l’application des correctifs/mises à jour ou mises à niveau à vos clouds privés. Nous travaillerons également avec vous pour planifier une fenêtre de maintenance avant d’appliquer des mises à jour ou des mises à niveau à votre cloud privé.


Il existe plusieurs mises à jour de logiciels :

- **Correctifs** : correctifs de sécurité ou correctifs logiciels publiés par VMware

- **Mises à jour** : changement de version mineur d’un composant de la pile VMware

- **Mises à niveau** : changement de version majeur d’un composant de la pile VMware

>[!NOTE]
>Microsoft teste les mises à jour de sécurité critiques dès qu’elles sont mises à disposition par VMware.

Les solutions de contournement VMware documentées sont implémentées à la place de l’installation d’un patch correspondant jusqu’au déploiement des prochaines mises à jour planifiées.
