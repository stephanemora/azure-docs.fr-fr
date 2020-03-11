---
title: Déployer un service de synchronisation de stockage
description: Déploiement de la ressource cloud Azure File Sync Service de synchronisation de stockage. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209288"
---
Vous avez besoin des informations d’identification de votre abonnement Azure pour cette étape. L’abonnement Azure que vous utilisez peut être différent de celui utilisé pour StorSimple.

La ressource principale permettant de configurer Azure File Sync est un « service de synchronisation de stockage ».
Nous vous recommandons d’en déployer un seul pour tous les serveurs de l’entreprise, synchronisant le même ensemble de fichiers actuellement ou à l’avenir. Si vous avez plusieurs appliances StorSimple, vous pouvez envisager de créer une ressource de service de synchronisation de stockage pour chacune d’elles. Toutefois, vous devez créer plusieurs services de synchronisation de stockage uniquement si vous avez des ensembles de serveurs distincts qui ne doivent jamais échanger de données. Dans le cas contraire, il est préférable de créer un seul service de synchronisation de stockage.

Choisissez une région Azure proche de l’emplacement de votre bureau pour votre service de synchronisation de stockage. Toutes les autres ressources cloud doivent être déployées dans la même région.
Il est préférable de créer un groupe de ressources dans votre abonnement pour héberger les ressources de synchronisation et de stockage et ainsi simplifier la gestion.

L’article suivant décrit comment déployer un service de synchronisation de stockage. Suivez uniquement cette partie du document. Des liens vers d’autres sous-sections de ce document sont proposés dans des étapes ultérieures.

[Découvrez comment déployer un service de synchronisation de stockage.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
