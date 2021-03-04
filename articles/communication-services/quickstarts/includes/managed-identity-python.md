---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657636"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Ajouter une identité managée à votre solution Communication Services

### <a name="install-the-client-library-packages"></a>Installer les packages de bibliothèques de client

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Utiliser les packages de bibliothèque de client

Ajoutez les directives `import` suivantes à votre code pour utiliser Azure Identity.

```python
from azure.identity import DefaultAzureCredential
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Pour inscrire une application dans l’environnement de développement et configurer des variables d’environnement, consultez [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token"></a>Créer une identité et émettre un jeton

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client afin d’émettre un jeton pour un nouvel utilisateur :

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Envoyer un SMS avec une identité managée Azure

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée Azure, puis comment utiliser le client pour envoyer un SMS :

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’authentification](../../concepts/authentication.md)

Vous souhaiterez peut-être également :

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque Azure Identity pour Python](/net/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../../concepts/telephony-sms/concepts.md)