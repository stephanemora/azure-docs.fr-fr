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
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66136201"
---
> [!NOTE]
> Une application web peut expirer après 20 minutes d’inactivité. Seules les requêtes à destination de l’application web active réinitialisent le minuteur. Affichage de la configuration d’application dans le portail Azure ou d’effectuer des demandes vers le site des outils avancés (`https://<app_name>.scm.azurewebsites.net`) ne pas réinitialiser le minuteur. Si votre application exécute des tâches Web continues ou planifiées, activez l’option **Toujours actif** pour vous assurer que les tâches web s’exécutent de manière fiable. Cette fonctionnalité est disponible uniquement dans les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) De base, Standard et Premium.
