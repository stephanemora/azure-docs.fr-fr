---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137109"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Que se passe-t-il au niveau de mon application pendant le déploiement ?

Toutes les méthodes de déploiement officiellement prises en charge ont un point commun : elles apportent des modifications aux fichiers dans le dossier `/home/site/wwwroot` de votre application. Ce sont ces mêmes fichiers qui sont exécutés en production. Par conséquent, le déploiement peut échouer en raison de fichiers verrouillés, ou l’application en production peut avoir un comportement imprévisible au cours du déploiement car tous les fichiers ne sont pas mis à jour simultanément. Il existe différentes manières d’éviter ces problèmes :

- Arrêtez votre application ou activez le mode hors connexion pendant le déploiement. Pour plus d’informations, consultez [Gestion des fichiers verrouillés au cours du déploiement](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Effectuez le déploiement sur un [emplacement de préproduction](../articles/app-service/deploy-staging-slots.md) avec [échange automatique](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) activé. 
- Utilisez plutôt [Run From Package](https://github.com/Azure/app-service-announcements/issues/84).
