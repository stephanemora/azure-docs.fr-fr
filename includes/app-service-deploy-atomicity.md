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
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236465"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Que se passe-t-il au niveau de mon application pendant le déploiement ?

Toutes les méthodes de déploiement officiellement prises en charge ont un point commun : elles apportent des modifications aux fichiers dans le dossier `/site/home/wwwroot` de votre application. Ce sont ces mêmes fichiers qui sont exécutés en production. Par conséquent, le déploiement peut échouer en raison de fichiers verrouillés, ou l’application en production peut avoir un comportement imprévisible au cours du déploiement car tous les fichiers ne sont pas mis à jour simultanément. Il existe différentes manières d’éviter ces problèmes :

- Arrêtez votre application ou activez le mode hors connexion pendant le déploiement. Pour plus d’informations, consultez [Gestion des fichiers verrouillés au cours du déploiement](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Effectuez le déploiement sur un [emplacement de préproduction](../articles/app-service/web-sites-staged-publishing.md) avec [échange automatique](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) activé. 
- Utilisez à la place [Run-from-Zip](https://github.com/Azure/app-service-announcements/issues/84).
