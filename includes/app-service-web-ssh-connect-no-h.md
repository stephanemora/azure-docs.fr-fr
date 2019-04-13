---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550000"
---
Pour ouvrir une session SSH directe avec votre conteneur, votre application doit être en cours d’exécution.

Collez l’URL suivante dans votre navigateur, puis remplacez \<application-name > par votre nom d’application :

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Si vous n’êtes pas encore authentifié, vous devez vous authentifier avec votre abonnement Azure pour vous connecter. Une fois authentifié, vous voyez un interpréteur de commandes dans le navigateur, vous permettant d’exécuter des commandes à l’intérieur de votre conteneur.

![Connexion SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
