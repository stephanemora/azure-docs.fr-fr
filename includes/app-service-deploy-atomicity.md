---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836851"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Que se passe-t-il au niveau de mon application pendant le déploiement ?

Toutes les méthodes de déploiement officiellement prises en charge apportent des modifications aux fichiers dans le dossier `/home/site/wwwroot` de votre application. Ce sont ces mêmes fichiers qui sont exécutés en production. Par conséquent, le déploiement peut échouer si des fichiers sont verrouillés. L’application en production peut également se comporter de façon imprévisible pendant le déploiement, car tous les fichiers ne sont pas tous mis à jour en même temps. Il existe différentes manières d’éviter ces problèmes :

- Arrêtez votre application ou activez le mode hors connexion pendant le déploiement. Pour plus d’informations, consultez [Gérer les fichiers verrouillés au cours du déploiement](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Effectuez le déploiement sur un [emplacement de préproduction](../articles/app-service/deploy-staging-slots.md) avec [échange automatique](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) activé. 
- Utilisez [Exécuter à partir du package](https://github.com/Azure/app-service-announcements/issues/84) au lieu d’effectuer un déploiement continu.
