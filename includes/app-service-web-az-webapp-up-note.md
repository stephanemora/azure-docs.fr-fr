---
title: fichier descriptif
description: Fichier Include
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577320"
---
> [!NOTE]
> La commande `az webapp up` exécute les actions suivantes :
>
>- Créer un [groupe de ressources](/cli/azure/group#az_group_create) par défaut.
>
>- Créer un [plan App Service](/cli/azure/appservice/plan#az_appservice_plan_create) par défaut.
>
>- [Créer une application](/cli/azure/webapp#az_webapp_create) avec le nom spécifié.
>
>- [Décompresser](../articles/app-service/deploy-zip.md#deploy-a-zip-package) tous les fichiers à partir du répertoire de travail actuel, [avec l’automatisation de build activée](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy).
>
>- Mettre en cache les paramètres localement dans le fichier *.azure/config* afin de ne pas avoir à les spécifier à nouveau lors du déploiement ultérieur avec `az webapp up` ou d’autres commandes Azure CLI. Les valeurs mises en cache sont utilisées automatiquement par défaut.
>