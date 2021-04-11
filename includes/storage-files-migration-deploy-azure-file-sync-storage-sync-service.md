---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075565"
---
Vous avez besoin des informations d’identification de votre abonnement Azure pour cette étape.

La ressource principale permettant de configurer Azure File Sync est un *service de synchronisation de stockage*. Nous vous recommandons d’en déployer un seul pour tous les serveurs synchronisant le même ensemble de fichiers maintenant ou à l’avenir. Ne créez plusieurs services de synchronisation de stockage que si vous avez des ensembles de serveurs distincts qui ne doivent jamais échanger de données. Par exemple, vous pouvez avoir des serveurs qui ne doivent jamais synchroniser le même partage de fichiers Azure. Dans le cas contraire, la meilleure pratique consiste à créer un seul service de synchronisation de stockage.

Pour votre service de synchronisation de stockage, choisissez une région Azure proche de votre emplacement. Toutes les autres ressources cloud doivent être déployées dans la même région. Afin de simplifier la gestion, créez un nouveau groupe de ressources dans votre abonnement pour héberger les ressources de synchronisation et de stockage.

Pour plus d’informations, consultez la [section concernant le déploiement du service de synchronisation de stockage](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) dans l’article sur le déploiement d’Azure File Sync. Suivez uniquement cette partie de l’article. Des liens vers d’autres sections de l’article sont proposés dans des étapes ultérieures.