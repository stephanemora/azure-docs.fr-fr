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
ms.openlocfilehash: 180b615869579752f9a14aa2dcdd34f1676b577b
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109644878"
---
Pour terminer cette étape, vous avez besoin des informations d’identification de votre abonnement Azure.

La ressource principale permettant de configurer Azure File Sync est un *service de synchronisation de stockage*. Nous vous recommandons d’en déployer un seul pour tous les serveurs synchronisant le même ensemble de fichiers maintenant ou à l’avenir. Ne créez plusieurs services de synchronisation de stockage que si vous avez des ensembles de serveurs distincts qui ne doivent jamais échanger de données. Par exemple, vous pouvez avoir des serveurs qui ne doivent jamais synchroniser le même partage de fichiers Azure. Dans le cas contraire, la meilleure pratique consiste à utiliser un seul service de synchronisation de stockage.

Pour votre service de synchronisation de stockage, choisissez une région Azure proche de votre emplacement. Toutes les autres ressources cloud doivent être déployées dans la même région. Afin de simplifier la gestion, créez un nouveau groupe de ressources dans votre abonnement pour héberger les ressources de synchronisation et de stockage.

Pour plus d’informations, consultez la [section concernant le déploiement du service de synchronisation de stockage](../articles/storage/file-sync/file-sync-deployment-guide.md#deploy-the-storage-sync-service) dans l’article sur le déploiement d’Azure File Sync. Suivez uniquement cette section de l’article. Des liens vers d’autres sections de l’article sont proposés dans des étapes ultérieures.