---
ms.openlocfilehash: ad95a54a62c863b3c80cc372d57efbdd2adc49dc
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656961"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

### <a name="install-the-sdk-packages"></a>Installer les packages du kit SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Créer un fichier
Ouvrez et enregistrez un nouveau fichier dans le dossier que vous avez créé, appelé `authentication.py`. Nous placerons notre code dans ce fichier.

### <a name="use-the-sdk-packages"></a>Utiliser les packages du kit SDK

Ajoutez les instructions `import` suivantes au début de votre fichier pour utiliser les Kits de développement logiciel (SDK) que vous avez installés.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Créer un DefaultAzureCredential

Nous allons utiliser le [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement. Comme nous allons les utiliser tout au long de ce démarrage rapide, nous allons les créer au début du fichier.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>Créer une identité et émettre un jeton avec des principaux de service

Nous allons maintenant ajouter du code qui utilise les informations d’identification créées pour émettre un jeton d’accès VoIP. Nous appellerons ce code plus tard :

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-service-principals"></a>Envoyer un SMS avec des principaux de service
Comme autre exemple d’utilisation des principaux de service, nous allons ajouter ce code qui utilise les mêmes informations d’identification pour envoyer un SMS :

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Écrire le code principal

Après avoir créé nos fonctions, nous pouvons maintenant écrire le code principal qui appellera les fonctions que nous avons écrites précédemment.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Le fichier `authentication.py` final doit ressembler à ce qui suit :

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Exécuter le programme

Lorsque tout est terminé, vous pouvez exécuter le fichier en entrant `python authentication.py` dans le répertoire de votre projet. Si tout s’est bien passé, vous devez obtenir un résultat similaire à ce qui suit.

```Bash
    $ python authentication.py
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
