---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377102"
---
Azure Files offre quatre niveaux de stockage, premium, optimisé pour les transactions, chaud et froid, pour vous permettre de personnaliser vos partages en fonction des performances et du budget demandés par votre scénario :

- **Premium** : Les partages de fichiers Premium sont sauvegardés sur des disques SSD et déployés dans le type **Compte de stockage FileStorage**. Les partages de fichiers Premium offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail gourmandes en E/S. Les partages de fichiers Premium sont adaptés à un vaste éventail de charges de travail, comme les bases de données, l’hébergement de site web et les environnements de développement. Les partages de fichiers Premium peuvent être utilisés avec les protocoles SMB (Server Message Block) et NFS (Network File System).
- **Optimisé pour les transactions** : Les partages de fichiers optimisés pour les transactions permettent d’avoir des charges de travail lourdes en transactions qui n’ont pas besoin de la latence offerte par les partages de fichiers premium. Les partages de fichiers optimisés pour les transactions sont offerts sur le matériel de stockage standard fourni par des lecteurs de disque dur (HDD) et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** . Le niveau Optimisé pour les transactions était auparavant appelé « Standard », mais cela fait référence au type de support de stockage plutôt qu’au niveau lui-même (les niveaux chaud et froid sont également des niveaux « standard », car ils se trouvent sur du matériel de stockage standard).
- **Chaud** : Les partages de fichiers chauds offrent un stockage optimisé pour les scénarios de partage de fichiers à usage général, tels que les partages d’équipe et Azure File Sync. Les partages de fichiers chauds sont offerts sur le matériel de stockage standard fourni par des lecteurs HDD et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** .
- **Froid** : Les partages de fichiers froids offrent un stockage abordable optimisé pour les scénarios de stockage d’archive en ligne. Azure File Sync peut également convenir aux charges de travail avec un churn inférieur. Les partages de fichiers froids sont offerts sur le matériel de stockage standard fourni par des lecteurs HDD et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** .

Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement. Pour plus d’informations sur le modèle de facturation avec approvisionnement pour les partages de fichiers Premium, consultez [Comprendre l’approvisionnement des partages de fichiers Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Les partages de fichiers Standard, notamment les partages de fichiers optimisés pour les transactions, chauds et froids, sont disponibles via un paiement à l’utilisation.

Les partages de fichiers chauds et froids sont disponibles dans toutes les régions Azure publiques et Azure Government. Les partages de fichiers optimisés pour les transactions sont disponibles dans toutes les régions Azure, y compris les régions Azure Chine et Azure Allemagne.

> [!Important]  
> Vous pouvez déplacer des partages de fichiers d’un niveau à l’autre, au sein des comptes de stockage de type GPv2 (Transaction optimisée, Accès chaud et Accès froid). Les déplacements d’un niveau à l’autre impliquent des transactions : le passage d’un niveau plus chaud à un niveau plus froid entraînera des frais de transaction d’écriture côté niveau froid pour chaque fichier du partage, tandis que le passage d’un niveau plus froid à un niveau plus chaud entraînera des frais de transaction de lecture côté niveau froid pour chaque fichier du partage.