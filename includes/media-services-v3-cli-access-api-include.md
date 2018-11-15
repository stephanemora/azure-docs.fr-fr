---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616637"
---
## <a name="access-the-media-services-api"></a>Accéder à l’API Media Services

Vous utilisez l’authentification de principal de service Azure AD pour vous connecter aux API Azure Media Services. La commande suivante crée une application Azure AD et attache un principal de service pour le compte. Vous devriez utiliser les valeurs renvoyées pour configurer votre application.

Avant d’exécuter le script, vous pouvez remplacer le `amsaccount` et `amsResourceGroup` par les noms que vous avez choisis lors de la création de ces ressources. `amsaccount` est le nom du compte Azure Media Services auquel attacher le principal de service.

La commande suivante renvoie une sortie `json` :

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Cette commande génère une réponse semblable à celle-ci :

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Si vous souhaitez obtenir un `xml` dans la réponse, utilisez la commande suivante :

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
