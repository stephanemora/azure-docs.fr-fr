---
title: Fichier Include
description: Fichier Include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424168"
---
> [!NOTE]
> Une application web peut expirer après 20 minutes d’inactivité. Seules les requêtes à destination de l’application web active réinitialisent le minuteur. L’affichage de la configuration de l’application dans le portail Azure ou l’envoi de requêtes au site d’outils avancés (`https://<app_name>.scm.azurewebsites.net`) ne réinitialise pas le minuteur. Si votre application exécute des tâches web continues ou planifiées (déclenchées par le minuteur), activez l’option **Toujours actif** pour garantir que les tâches web s’exécutent de manière fiable. Cette fonctionnalité est disponible uniquement dans les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) De base, Standard et Premium.
