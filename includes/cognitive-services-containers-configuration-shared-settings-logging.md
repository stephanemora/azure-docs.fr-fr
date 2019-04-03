---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886829"
---
Les paramètres `Logging` gèrent la prise en charge de la journalisation ASP.NET Core pour votre conteneur. Vous pouvez utiliser pour votre conteneur les mêmes paramètres et valeurs de configuration que ceux d’une application ASP.NET Core. 

Le conteneur prend en charge les fournisseurs de journalisation suivants :

|Fournisseur|Objectif|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Fournisseur de journalisation `Console` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Déboguer](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Fournisseur de journalisation `Debug` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Disque](#disk-logging)|Fournisseur de journalisation JSON. Ce fournisseur de journalisation écrit les données de journal dans le montage de sortie.|

Cette commande conteneur stocke les informations de journalisation dans le format JSON pour le montage de sortie :

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Cette commande de conteneur affiche des informations de débogage, le préfixe `dbug`, tandis que le conteneur est en cours d’exécution :

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Journalisation Disk

Le fournisseur de journalisation `Disk` prend en charge les paramètres de configuration suivants :  

| Nom | Type de données | Description |
|------|-----------|-------------|
| `Format` | Chaîne | Format de sortie des fichiers journaux.<br/> **Remarque :** Cette valeur doit être `json` pour activer le fournisseur de journalisation. Si cette valeur est spécifiée sans que le montage de sortie soit aussi spécifié pendant l’instanciation d’un conteneur, une erreur se produit. |
| `MaxFileSize` | Entier  | Taille maximale, en mégaoctets (Mo), d’un fichier journal. Dès que la taille du fichier journal actif atteint ou dépasse cette valeur, un nouveau fichier journal est commencé par le fournisseur de journalisation. Si la valeur -1 est spécifiée, la taille du fichier journal est limitée uniquement par la taille de fichier maximale, le cas échéant, pour le montage de sortie. La valeur par défaut est 1. |

Pour plus d’informations sur la configuration de la prise en charge de la journalisation ASP.NET Core, consultez [Configuration d’un fichier de paramètres](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

