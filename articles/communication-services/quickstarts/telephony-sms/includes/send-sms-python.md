---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: e8424f6b5b7617b00de6dedbece3325f3c5513c8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622223"
---
Commencez avec Azure Communication Services en utilisant la bibliothèque de client Communication Services SMS Python pour envoyer des SMS.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou ultérieur.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un numéro de téléphone permettant de recevoir des SMS. [Obtenez un numéro de téléphone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez la commande `python --version` pour vérifier que Python est installé.
- Pour voir les numéros de téléphone associés à votre ressource Communication Services, connectez-vous au [portail Azure](https://portal.azure.com/), localisez votre ressource Communication Services, puis ouvrez l’onglet **Phone Numbers** (Numéros de téléphone) dans le volet de navigation gauche.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Utilisez un éditeur de texte pour créer un fichier appelé **send-sms.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package de la bibliothèque de client Communication Services SMS pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Azure Communication Services SMS pour Python.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Cette classe est nécessaire pour toutes les fonctionnalités SMS. Vous l’instanciez avec vos informations d’abonnement et vous l’utilisez pour envoyer des SMS.                                                                                                                 |
| SmsSendResult               | Cette classe contient le résultat du service SMS.                                          |

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un **SmsClient** avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion pour la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-a-11-sms-message"></a>Envoi d’un message SMS 1:1

Pour envoyer un message SMS à un seul destinataire, appelez la méthode ```send``` à partir du **SmsClient** avec un numéro de téléphone de destinataire unique. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées. Ajoutez ce code à la fin du bloc `try` dans **send-sms.py** :

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>,
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS associé à votre service de communication et `<to-phone-number>` par le numéro de téléphone auquel vous souhaitez envoyer un message. 

## <a name="send-a-1n-sms-message"></a>Envoyer un message SMS 1:N

Pour envoyer un message SMS à une liste de destinataires, appelez la méthode ```send``` à partir du **SmsClient** avec une liste de numéros de téléphone de destinataire. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées. Ajoutez ce code à la fin du bloc `try` dans **send-sms.py** :

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS associé à votre service de communication et `<to-phone-number-1>` et `<to-phone-number-2>` par les numéros de téléphone auxquels vous souhaitez envoyer un message. 

## <a name="optional-parameters"></a>Paramètres facultatifs

Le paramètre `enable_delivery_report` est un paramètre facultatif que vous pouvez utiliser pour configurer la création de rapports de remise. C’est utile pour les scénarios où vous souhaitez émettre des événements quand des SMS sont remis. Consultez le guide de démarrage rapide [Gérer les événements SMS](../handle-sms-events.md) pour configurer la création de rapports de remise pour vos SMS.

Le paramètre `tag` est un paramètre facultatif que vous pouvez utiliser pour configurer le balisage personnalisé.

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `python`.

```console
python send-sms.py
```
