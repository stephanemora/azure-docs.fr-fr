---
title: Fichier Include
description: Fichier Include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641873"
---
Le déclencheur d’objet blob fournit plusieurs propriétés de métadonnées. Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ces valeurs ont la même sémantique que le type [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet).

|Propriété  |Type  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Chemin de l’objet blob déclencheur.|
|`Uri`|`System.Uri`|URI de l’objet blob pour l’emplacement principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Propriétés système de l’objet blob. |
|`Metadata` |`IDictionary<string,string>`|Métadonnées définies par l’utilisateur pour l’objet blob.|

Par exemple, les exemples JavaScript et Script C# suivants enregistrent le chemin d’accès à l’objet blob déclencheur, notamment le conteneur :

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
