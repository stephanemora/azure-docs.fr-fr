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
ms.openlocfilehash: d3ba7baf79ee972ed7289a1aab93484108da70cf
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724225"
---
Azure Files offre quatre niveaux de stockage, premium, optimisé pour les transactions, chaud et froid, pour vous permettre de personnaliser vos partages en fonction des performances et du budget demandés par votre scénario :

- **Premium** : Les partages de fichiers Premium reposent sur des disques SSD et offrent toujours un niveau de performance élevé et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail les plus gourmandes en E/S. Les partages de fichiers Premium sont adaptés à un vaste éventail de charges de travail, comme les bases de données, l’hébergement de site web et les environnements de développement. Les partages de fichiers Premium peuvent être utilisés avec les protocoles SMB (Server Message Block) et NFS (Network File System).
- **Optimisé pour les transactions** : Les partages de fichiers optimisés pour les transactions permettent d’avoir des charges de travail lourdes en transactions qui n’ont pas besoin de la latence offerte par les partages de fichiers premium. Les partages de fichiers optimisés pour les transactions sont proposés sur le matériel de stockage standard reposant sur des lecteurs de disque dur (HDD). Le niveau Optimisé pour les transactions était auparavant appelé « Standard », mais cela fait référence au type de support de stockage plutôt qu’au niveau lui-même (les niveaux chaud et froid sont également des niveaux « standard », car ils se trouvent sur du matériel de stockage standard).
- **Hot** : Les partages de fichiers chauds offrent un stockage optimisé pour les scénarios de partage de fichiers à usage général, tels que les partages d’équipe. Les partages de fichiers chauds sont proposés sur le matériel de stockage standard reposant sur des HDD.
- **Froid** : Les partages de fichiers froids offrent un stockage abordable optimisé pour les scénarios de stockage d’archive en ligne. Les partages de fichiers froids sont proposés sur le matériel de stockage standard reposant sur des HDD.

Les partages de fichiers Premium sont déployés dans le type de **compte de stockage FileStorage** et sont disponibles uniquement dans un modèle de facturation provisionnée. Pour plus d’informations sur le modèle de facturation avec approvisionnement pour les partages de fichiers Premium, consultez [Comprendre l’approvisionnement des partages de fichiers Premium](../articles/storage/files/understanding-billing.md#provisioned-billing). Les partages de fichiers standard, notamment les partages de fichiers optimisés pour les transactions, chauds et froids, sont déployés dans le type de **compte de stockage à usage générale version 2 (GPv2)** et sont disponibles via une facturation avec paiement à l’utilisation. Les partages de fichiers chauds et froids sont disponibles dans toutes les régions Azure publiques et Azure Government. Les partages de fichiers optimisés pour les transactions sont disponibles dans toutes les régions Azure, y compris les régions Azure Chine et Azure Allemagne.

Au moment de sélectionner un niveau de stockage pour votre charge de travail, tenez compte de vos besoins en matière de performances et d’utilisation. Si votre charge de travail nécessite une latence à un chiffre ou si vous utilisez un média de stockage SSD local, le niveau Premium est probablement le mieux adapté. Si une faible latence ne constitue pas un problème, par exemple si vous avez des partages d’équipe montés localement à partir d’Azure ou mis en mémoire cache localement à l’aide d’Azure File Sync, le stockage standard est peut être plus indiqué du point de vue du coût.

Une fois que vous avez créé un partage de fichiers dans un compte de stockage, vous ne pouvez plus le déplacer dans des niveaux réservés à différents types de compte de stockage. Par exemple, pour déplacer un partage de fichiers optimisé pour les transactions vers le niveau Premium, vous devez créer un nouveau partage de fichiers dans un compte de stockage FileStorage et copier les données de votre partage d’origine vers un nouveau partage de fichiers dans le compte FileStorage. Nous vous recommandons d’utiliser AzCopy pour copier des données entre différents partages de fichiers Azure, mais vous pouvez aussi utiliser des outils comme `robocopy` sur Windows ou `rsync` pour macOS et Linux. 

Les partages de fichiers déployés dans des comptes de stockage GPv2 peuvent être déplacés entre les différents niveaux standard (Optimisé pour les transactions, Chaud et Froid) sans qu’il soit nécessaire de créer un compte de stockage et de migrer les données. En revanche, le changement de niveau engendrera des coûts de transactions. Quand vous déplacez un partage d’un niveau chaud vers un niveau froid, vous vous exposez aux frais de transactions d’écriture du niveau froid pour chaque fichier du partage. Si vous déplacez un partage d’un niveau froid vers un niveau chaud, des frais de transactions de lecture du niveau froid vous seront imputés pour chaque fichier du partage.

Pour plus d’informations, consultez la [présentation de la facturation Azure Files](../articles/storage/files/understanding-billing.md).