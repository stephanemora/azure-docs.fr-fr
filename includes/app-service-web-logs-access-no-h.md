---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551216"
---
Vous pouvez accéder aux journaux console générés à partir de l’intérieur du conteneur. Tout d’abord, activer la journalisation de conteneur en exécutant la commande suivante dans Cloud Shell :

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Une fois la journalisation du conteneur activée, exécutez la commande suivante pour voir le flux de journal :

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Si vous ne voyez pas les journaux d’activité de la console, attendez 30 secondes et vérifiez à nouveau.

> [!NOTE]
> Vous pouvez également inspecter les fichiers journaux à partir du navigateur à `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pour arrêter la diffusion de journaux à tout moment, tapez `Ctrl` + `C`.
