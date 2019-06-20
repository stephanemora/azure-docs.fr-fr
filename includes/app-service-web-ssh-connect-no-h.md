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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177542"
---
Pour établir une session SSH directe avec votre conteneur, votre application doit être en cours d’exécution.

Collez l’URL suivante dans votre navigateur, puis remplacez \<app-name> par le nom de votre application :

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Si vous n’êtes pas encore authentifié, vous devez le faire avec votre abonnement Azure pour vous connecter. Une fois authentifié, vous voyez un interpréteur de commandes dans le navigateur, vous permettant d’exécuter des commandes à l’intérieur de votre conteneur.

![Connexion SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
