---
title: Importer l’environnement Postman pour des appels REST Azure Media Services
description: Cette rubrique fournit une définition de l’environnement Postman pour les appels REST Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007837"
---
# <a name="import-the-postman-environment"></a>Importer l’environnement Postman

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Cet article contient une définition des variables d’environnement **Postman** qui sont utilisées dans la [collection Postman](postman-collection.md) contenant les requêtes HTTP groupées qui appellent les API REST Media Services. Les fichiers d’environnement et de la collection sont utilisés par le didacticiel [Configurer Postman pour les appels de l’API REST Media Services](media-rest-apis-with-postman.md).

> [!NOTE]
> Valeur de `AzureADSTSEndpoint ` = `https://login.microsoftonline.com/{{TenantId}}/oauth2/token`. Pour récupérer votre ID de locataire, vous pouvez pointer votre souris sur votre nom d’utilisateur dans le portail (dans le coin supérieur droit) et il se trouvera dans « Directory:Microsoft ({{TENANTID}}) ».

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
