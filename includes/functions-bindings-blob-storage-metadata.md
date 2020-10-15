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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
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
