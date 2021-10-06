---
title: fichier descriptif
description: Fichier Include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 09/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0d6363861a8412c7732a697f7e869504ddb106c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558504"
---
> [!NOTE]
> Une application Web peut expirer après 20 minutes d’inactivité, et seules les demandes adressées à l’application Web réelle peuvent réinitialiser le minuteur. L’affichage de la configuration de l’application dans le portail Azure ou l’envoi de requêtes au site d’outils avancés (`https://<app_name>.scm.azurewebsites.net`) ne réinitialise pas le minuteur. Si vous configurez l’application web qui héberge votre travail pour s’exécuter en continu, selon une planification ou pour utiliser des déclencheurs en fonction des événements, activez le paramètre **Toujours activé** dans la page Azure **Configuration** de votre application web. Le paramètre Always on permet de s’assurer que ces types de tâches Web s’exécutent de manière fiable. Cette fonctionnalité est disponible uniquement dans les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) De base, Standard et Premium.
