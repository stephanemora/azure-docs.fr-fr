---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450538"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Ajouter une identité managée à votre solution Communication Services

### <a name="install-the-sdk-packages"></a>Installer les packages du kit SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Utiliser les packages du kit SDK

Ajoutez les directives `import` suivantes à votre code pour utiliser Azure Identity.

```python
from azure.identity import DefaultAzureCredential
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Pour découvrir facilement l’authentification d’identité managée, consultez la section [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md)

Pour une analyse plus approfondie du fonctionnement de l’objet DefaultAzureCredential et pour savoir comment l’utiliser d’une autre manière que celle spécifiée dans ce guide de démarrage rapide, consultez [Bibliothèque de client Azure Identity pour Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token"></a>Créer une identité et émettre un jeton

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client afin d’émettre un jeton pour un nouvel utilisateur :

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Envoyer un SMS avec une identité managée Azure
L’exemple de code suivant montre comment créer un objet client de service avec une identité managée Azure, puis comment utiliser le client pour envoyer un SMS :

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

### <a name="list-all-your-purchased-phone-numbers"></a>Lister tous vos numéros de téléphone achetés

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée Azure, puis comment utiliser ce client afin de voir tous les numéros de téléphone achetés pour la ressource :

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```