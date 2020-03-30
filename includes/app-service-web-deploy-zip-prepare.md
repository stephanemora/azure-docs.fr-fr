---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945156"
---
## <a name="create-a-project-zip-file"></a>Créer un fichier ZIP de projet

>[!NOTE]
> Si vous avez téléchargé les fichiers dans un fichier ZIP, extrayez tout d’abord les fichiers. Par exemple, si vous avez téléchargé un fichier ZIP à partir de GitHub, vous ne pouvez pas déployer ce fichier tel quel. GitHub ajoute des répertoires imbriqués supplémentaires qui ne fonctionnent pas avec App Service. 
>

Dans une fenêtre de terminal locale, accédez au répertoire racine de votre projet d’application. 

Ce répertoire doit contenir le fichier d’entrée à votre application web, tel que _index.html_, _index.php_ et _app.js_. Il peut également contenir des fichiers de gestion de package comme _project.json_, _composer.json_, _package.json_, _bower.json_ et _requirements.txt_.

Sauf si vous souhaitez qu’App Service exécute l’automatisation du déploiement à votre place, exécutez toutes les tâches de compilation (par exemple, `npm`, `bower`, `gulp`, `composer` et `pip`) et assurez-vous que vous disposez de tous les fichiers nécessaires pour exécuter l'application. Cette étape est requise si vous souhaitez [exécuter votre package directement](../articles/app-service/deploy-run-package.md).

Créez une archive ZIP contenant tous les éléments de votre projet. La commande suivante utilise l’outil par défaut dans votre terminal :

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

