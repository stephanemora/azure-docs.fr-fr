---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: f4600413e05950446db71f988c4c4302f0dcacb3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062957"
---
## <a name="access-the-media-services-api"></a>Accéder à l’API Media Services

Vous utilisez l’authentification de principal de service Azure AD pour vous connecter aux API Azure Media Services. La commande suivante crée une application Azure AD et attache un principal de service pour le compte. Vous devriez utiliser les valeurs renvoyées pour configurer votre application.

Avant d’exécuter le script, vous pouvez remplacer le `amsaccount` et `amsResourceGroup` par les noms que vous avez choisis lors de la création de ces ressources. `amsaccount` est le nom du compte Azure Media Services auquel attacher le principal de service.

La commande suivante renvoie une sortie `json` :

```azurecli-interactive
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

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
