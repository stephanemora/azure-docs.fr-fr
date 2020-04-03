---
title: Déployer un service de synchronisation de stockage
description: Déploiement de la ressource cloud Azure File Sync Service de synchronisation de stockage. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124714"
---
Vous avez besoin des informations d’identification de votre abonnement Azure pour cette étape.

La ressource principale permettant de configurer Azure File Sync est un « service de synchronisation de stockage ».
Nous vous recommandons d’en déployer un seul pour tous les serveurs de l’entreprise, synchronisant le même ensemble de fichiers actuellement ou à l’avenir. Ne créez plusieurs services de synchronisation de stockage que si vous avez des ensembles de serveurs distincts qui ne doivent jamais échanger de données. (Par exemple : synchroniser le même partage de fichiers Azure). Dans le cas contraire, il est préférable de créer un seul service de synchronisation de stockage.

Choisissez une région Azure proche de l’emplacement de votre bureau pour votre service de synchronisation de stockage. Toutes les autres ressources cloud doivent être déployées dans la même région.
Afin de simplifier la gestion, créez un nouveau groupe de ressources dans votre abonnement pour héberger les ressources de synchronisation et de stockage.

L’article suivant décrit comment déployer un service de synchronisation de stockage. Suivez uniquement cette partie du document. Des liens vers d’autres sous-sections de ce document sont proposés dans des étapes ultérieures.

[Découvrez comment déployer un service de synchronisation de stockage.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
