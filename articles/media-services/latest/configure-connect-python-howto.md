---
title: Se connecter à Azure Media Services v3 API - Python
description: Découvrez comment vous connecter à Media Services v3 API avec Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700199"
---
# <a name="connect-to-media-services-v3-api---python"></a>Se connecter à l’API de Media Services v3 - Python

Cet article vous montre comment se connecter au SDK Python Azure Media Services v3 à l’aide de la connexion du principal du service dans la méthode.

## <a name="prerequisites"></a>Conditions préalables

- Téléchargez Python à partir de [python.org](https://www.python.org/downloads/)
- Veillez à définir le `PATH` variable d’environnement
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom de groupe de ressources et le nom du compte Media Services.
- Suivez les étapes de la [accéder aux API](access-api-cli-how-to.md) rubrique. Enregistrez l’ID d’abonnement, ID d’application (ID client), la clé d’authentification (secret) et l’ID de client dont vous avez besoin à l’étape ultérieure.

## <a name="install-the-modules"></a>Installer les modules

Pour travailler avec Azure Media Services à l’aide de Python, vous devez installer ces modules.

* Le `azure-mgmt-resource` module, qui comprend des modules Azure pour Active Directory.
* Le `azure-mgmt-media` module, qui comprend les entités Media Services.

Ouvrez un outil de ligne de commande et utilisez les commandes suivantes pour installer les modules.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Se connecter au client Python

1. Créez un fichier avec un `.py` extension
1. Ouvrez le fichier dans votre éditeur favori
1. Ajoutez le code qui suit au fichier. Le code importe les modules nécessaires et crée l’objet d’informations d’identification Active Directory que vous devez vous connecter à Media Services.

      Définir les valeurs des variables pour les valeurs que vous avez obtenu de [accéder aux API](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Exécutez le fichier

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le [SDK Python](https://aka.ms/ams-v3-python-sdk).
- Passez en revue la documentation [Informations de référence sur Python](https://aka.ms/ams-v3-python-ref) de Media Services.
