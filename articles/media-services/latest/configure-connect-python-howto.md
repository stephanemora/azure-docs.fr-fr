---
title: Se connecter à l’API Azure Media Services v3 - Python
description: Cet article explique comment se connecter à l’API Azure Media Services v3 avec Python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960688"
---
# <a name="connect-to-media-services-v3-api---python"></a>Se connecter à l’API Media Services v3 - Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article vous montre comment vous connecter à l’API Azure Media Services v3 en utilisant la méthode de connexion au principal du service.

## <a name="prerequisites"></a>Prérequis

- Télécharger Python sur le site [python.org](https://www.python.org/downloads/)
- Définir la variable d’environnement `PATH`
- [Créer un compte Media Services](./account-create-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes de la rubrique [Accéder aux API](./access-api-howto.md), en sélectionnant la méthode d’authentification du principal de service. Notez l’ID d’abonnement (`SubscriptionId`), l’ID client de l’application (`AadClientId`), la clé d’authentification (`AadSecret`) et l’ID de locataire (`AadTenantId`) dont vous aurez besoin au cours des prochaines étapes.

> [!IMPORTANT]
> Examinez les [conventions d’appellation](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Azure Media Services avec Python, vous devez installer ces modules.

* Le module `azure-mgmt-resource`, qui inclut des modules Azure pour Active Directory.
* Le module `azure-mgmt-media`, qui comprend les entités Media Services.

    Procurez-vous la dernière version du [kit de développement logiciel (SDK) Media Services pour Python](https://pypi.org/project/azure-mgmt-media/).

Ouvrez l’outil en ligne de commande et utilisez les commandes suivantes pour installer les modules.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Se connecter au client Python

1. Créer un fichier avec une extension `.py`
1. Ouvrir le fichier dans l’éditeur de votre choix
1. Ajoutez le code suivant au fichier. Le code importe les modules nécessaires et crée l’objet d’informations d’identification Active Directory nécessaire pour vous connecter à Media Services.

      Définissez les valeurs des variables à partir des valeurs que vous avez obtenues à l’étape [Accéder aux API](./access-api-howto.md). Mettez à jour les variables `ACCOUNT_NAME` et `RESOUCE_GROUP_NAME` pour le nom de compte Media Services et les noms de groupes de ressources utilisés au moment de la création des ressources.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Exécuter le fichier

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le [SDK Python](https://aka.ms/ams-v3-python-sdk).
- Passez en revue la documentation [Informations de référence sur Python](/python/api/overview/azure/mediaservices/management) de Media Services.
