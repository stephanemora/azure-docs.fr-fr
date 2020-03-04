---
title: Récupérer des paires clé-valeur à un point dans le temps
titleSuffix: Azure App Configuration
description: Récupérer les anciennes paires clé-valeur à l’aide de captures instantanées à un point dans le temps dans Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523654"
---
# <a name="point-in-time-snapshot"></a>Capture instantanée à un point dans le temps

Azure App Configuration conserve un enregistrement des modifications apportées aux paires clé-valeur. Cet enregistrement fournit une chronologie des modifications apportées aux paires clé-valeur. Vous pouvez retracer l’historique de n’importe quelle paire clé-valeur et fournir la valeur qu’avait la clé dans les sept jours qui ont précédé sa modification. Avec cette fonctionnalité, vous pouvez « voyager dans le passé » pour récupérer une ancienne valeur de clé. Par exemple, vous pouvez récupérer les paramètres de configuration qui étaient utilisés avant le dernier déploiement en date, afin de restaurer la précédente configuration de l’application.

## <a name="key-value-retrieval"></a>Récupération de paires clé-valeur

Vous pouvez utiliser Azure PowerShell pour récupérer les valeurs de clé précédentes.  Utilisez `az appconfig revision list` en ajoutant les paramètres permettant de récupérer les valeurs nécessaires.  Spécifiez l’instance Azure App Configuration en fournissant le nom du magasin (`--name {app-config-store-name}`) ou en utilisant une chaîne de connexion (`--connection-string {your-connection-string}`). Limitez la sortie en spécifiant un point dans le temps (`--datetime`) et en spécifiant le nombre maximal d’éléments à retourner (`--top`).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Récupérez toutes les modifications enregistrées qui ont été apportées à vos paires clé-valeur.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Récupérez toutes les modifications enregistrées qui ont été apportées à la clé `environment` et aux étiquettes `test` et `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Récupérez toutes les modifications enregistrées qui ont été apportées à l’espace de clé hiérarchique `environment:prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Récupérez toutes les modifications enregistrées qui ont été apportées à la clé `color` à un point dans le temps.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Récupérez les 10 dernières modifications enregistrées qui ont été apportées à vos paires clé-valeur, et retournez uniquement les valeurs avec `key`, `label` et l’horodatage `last-modified`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET Core](./quickstart-aspnet-core-app.md)  
