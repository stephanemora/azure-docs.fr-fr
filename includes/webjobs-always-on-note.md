---
title: Fichier Include
description: Fichier Include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c3a653d66ddfb00833f2b90d61f08a51697fd46
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113131781"
---
> [!NOTE]
> Une application Web peut expirer après 20 minutes d’inactivité, et seules les demandes adressées à l’application Web réelle peuvent réinitialiser le minuteur. L’affichage de la configuration de l’application dans le portail Azure ou l’envoi de requêtes au site d’outils avancés (`https://<app_name>.scm.azurewebsites.net`) ne réinitialise pas le minuteur. Si vous configurez votre application web pour s’exécuter continuellement, selon une planification ou pour utiliser des déclencheurs en fonction des événements, activez le paramètre **Toujours activé** dans la page Azure **Configuration** de votre application web. Le paramètre Always on permet de s’assurer que ces types de tâches Web s’exécutent de manière fiable. Cette fonctionnalité est disponible uniquement dans les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) De base, Standard et Premium.
