---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: dc527a4e1bdf9648ddfc9f582b0c146197214f26
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741372"
---
Les paramètres `Logging` gèrent la prise en charge de la journalisation ASP.NET Core pour votre conteneur. Vous pouvez utiliser pour votre conteneur les mêmes paramètres et valeurs de configuration que ceux d’une application ASP.NET Core. 

Le conteneur prend en charge les fournisseurs de journalisation suivants :

|Fournisseur|Objectif|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Fournisseur de journalisation `Console` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Déboguer](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Fournisseur de journalisation `Debug` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Disque](#disk-logging)|Fournisseur de journalisation JSON. Ce fournisseur de journalisation écrit les données de journal dans le montage de sortie.|

### <a name="disk-logging"></a>Journalisation Disk

Le fournisseur de journalisation `Disk` prend en charge les paramètres de configuration suivants :  

| Nom | Type de données | Description |
|------|-----------|-------------|
| `Format` | Chaîne | Format de sortie des fichiers journaux.<br/> **Remarque :** Cette valeur doit être `json` pour activer le fournisseur de journalisation. Si cette valeur est spécifiée sans que le montage de sortie soit aussi spécifié pendant l’instanciation d’un conteneur, une erreur se produit. |
| `MaxFileSize` | Entier  | Taille maximale, en mégaoctets (Mo), d’un fichier journal. Dès que la taille du fichier journal actif atteint ou dépasse cette valeur, un nouveau fichier journal est commencé par le fournisseur de journalisation. Si la valeur -1 est spécifiée, la taille du fichier journal est limitée uniquement par la taille de fichier maximale, le cas échéant, pour le montage de sortie. La valeur par défaut est 1. |

Pour plus d’informations sur la configuration de la prise en charge de la journalisation ASP.NET Core, consultez [Configuration d’un fichier de paramètres](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
