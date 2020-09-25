---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944586"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou ultérieur.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

1. Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Utilisez un éditeur de texte pour créer un fichier appelé **issue-tokens.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package de la bibliothèque de client Azure Communication Services Administration pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez ce code dans le bloc `try` :

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Créer un utilisateur

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `create_user` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Vous devez tenir à jour un mappage entre les utilisateurs de votre application et les identités générées par Communication Services (par exemple, en les stockant dans la base de données de votre serveur d’applications).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Émettre des jetons d’accès utilisateur

Avec la méthode `issue_token`, émettez un jeton d’accès pour un utilisateur de Communication Services. Si vous ne spécifiez pas le paramètre facultatif `user`, un nouvel utilisateur sera créé et retourné avec le jeton.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Les jetons d’accès utilisateur sont des informations d’identification de courte durée qui doivent être réémises afin d’éviter que les utilisateurs rencontrent des interruptions de service. La propriété de réponse `expires_on` indique la durée de vie du jeton.

## <a name="revoke-user-access-tokens"></a>Révoquer des jetons d’accès utilisateur

Dans certains cas, il est nécessaire de révoquer explicitement des jetons d’accès utilisateur, par exemple, quand des utilisateurs changent leur mot de passe pour s’authentifier auprès de votre service. Cette fonctionnalité est disponible à partir de la bibliothèque de client Azure Communication Services Administration.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Supprimer un utilisateur

Quand vous supprimez une identité, vous supprimez aussi tous les jetons actifs et vous ne pouvez plus ensuite émettre de jetons pour les identités. De plus, tout le contenu persistant associé à l’utilisateur est également supprimé.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-token.py*, puis exécutez la commande `python` suivante pour exécuter l’application.

```console
python ./issue-token.py
```
