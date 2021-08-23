---
ms.openlocfilehash: d07614d79410c388c5b93d635564e2720de7e11e
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113659492"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/phone-numbers-quickstart).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou ultérieur.
- Chaîne de connexion et ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

Ouvrez votre terminal ou votre fenêtre de commande, créez un nouveau répertoire pour votre application, puis accédez-y.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Utilisez un éditeur de texte pour créer un fichier appelé phone_numbers_sample.py dans le répertoire racine du projet et ajoutez le code suivant. Nous allons ajouter le code de démarrage rapide restant dans les sections suivantes.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package de la bibliothèque de client Azure Communication Services Administration pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Authentifier le client Numéros de téléphone

Le client `PhoneNumbersClient` est activé pour utiliser l'authentification Azure Active Directory. Pour démarrer avec Azure Active Directory, le plus simple consister à utiliser l'objet `DefaultAzureCredential` ; vous pouvez l'installer à l'aide de la commande `pip install`.

```console
pip install azure-identity
```

Pour créer un objet `DefaultAzureCredential`, les variables d'environnement `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` et `AZURE_TENANT_ID` doivent déjà être définies avec les valeurs correspondantes de votre application Azure AD inscrite.

Pour savoir rapidement comment obtenir ces variables d’environnement, vous pouvez suivre le guide de [démarrage rapide Configurer des principaux de service à partir de CLI](../../identity/service-principal-from-cli.md).

Une fois la bibliothèque `azure-identity` installée, nous pouvons poursuivre l'authentification du client.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Il est également possible d'utiliser le point de terminaison et la clé d'accès de la ressource de communication pour l'authentification.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Fonctions

Une fois le client `PhoneNumbersClient` authentifié, nous pouvons commencer à travailler sur les différentes fonctions qu'il peut remplir.

### <a name="search-for-available-phone-numbers"></a>Rechercher les numéros de téléphone disponibles

Pour pouvoir acheter des numéros de téléphone, vous devez d'abord rechercher les numéros de téléphone disponibles. Pour rechercher des numéros de téléphone, fournissez l'indicatif régional, le type d'affectation, les [fonctionnalités de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), le [type de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) et la quantité (la valeur par défaut est 1). Notez que pour le type de numéro de téléphone gratuit, l'indicatif régional est facultatif.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Acheter des numéros de téléphone

Le résultat de la recherche de numéros de téléphone correspond à `PhoneNumberSearchResult`. Ce résultat contient un élément `searchId` qui peut être transmis à l'API Acheter des numéros pour acquérir les numéros de la recherche. Notez que l'appel de l'API Acheter des numéros de téléphone sera facturé sur votre compte Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Obtenir le ou les numéros de téléphone achetés

Après avoir acheté un numéro, vous pouvez le récupérer à partir du client.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Vous pouvez également récupérer tous les numéros de téléphone achetés.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Mettre à jour les fonctionnalités de numéro de téléphone

Vous pouvez mettre à jour les fonctionnalités d'un numéro de téléphone précédemment acheté.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Libérer un numéro de téléphone

Vous pouvez libérer un numéro de téléphone acheté.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier phone_numbers_sample.py, puis exécutez la commande python suivante pour lancer l'application.

```console
python phone_numbers_sample.py
```