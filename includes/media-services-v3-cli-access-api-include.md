---
title: Fichier include
description: Fichier include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 9a9aaf723b7b89b5b9c5611cea05c22c4003b99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92756099"
---
### <a name="access-the-media-services-api"></a>Accéder à l’API Media Services

Vous utilisez l’authentification de principal de service Azure AD pour vous connecter aux API Azure Media Services. La commande suivante crée une application Azure AD et attache un principal de service pour le compte. Vous devriez utiliser les valeurs renvoyées pour configurer votre application.

Avant d’exécuter le script, vous devez remplacer le `amsaccount` et `amsResourceGroup` par les noms que vous avez choisis lors de la création de ces ressources. `amsaccount` est le nom du compte Azure Media Services auquel attacher le principal de service.

Si vous avez accès à plusieurs abonnements, définissez d’abord comme abonnement actif celui dans lequel le compte Media Services a été créé.

```azurecli
az account set --subscription subscriptionId
```

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
