---
title: Démarrage rapide - API REST Service Azure SignalR | Microsoft Docs
description: Un guide de démarrage rapide pour l’utilisation de l’API REST Service Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972757"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Démarrage rapide : Diffuser des messages en temps réel à partir de l’application de console

Le service Azure SignalR fournit une [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) permettant de prendre en charge les scénarios de communication du serveur vers le client, par exemple la diffusion. Vous pouvez choisir n’importe quel langage de programmation qui peut appeler l’API REST. Vous pouvez publier des messages pour tous les clients connectés, un client spécifique par nom ou un groupe de clients.

Dans ce démarrage rapide, vous allez apprendre à envoyer des messages à partir d’une application de ligne de commande vers les applications clientes connectées en C#.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.
* [Kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/core)
* Un éditeur de texte ou un éditeur de code de votre choix.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Pendant le déploiement du service, passons à la préparation du code. Clonez l’[exemple d’application à partir de GitHub](https://github.com/aspnet/AzureSignalR-samples.git), définissez la chaîne de connexion du service SignalR, et exécutez l’application en local.

1. Ouvrez une fenêtre de terminal git. Passez à un dossier dans lequel vous souhaitez cloner l’exemple de projet.

1. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Cet exemple est une application de console montrant l’utilisation du service Azure SignalR. Il propose deux modes :

- Mode serveur : utilisez des commandes simples pour appeler l’API REST Service Azure SignalR.
- Mode client : connectez-vous au service Azure SignalR et recevez des messages à partir du serveur.

Vous pouvez également découvrir comment générer un jeton d’accès pour vous authentifier avec le service Azure SignalR.

### <a name="build-the-executable-file"></a>Générer le fichier exécutable

Nous utilisons macOS osx.10.13-x64 comme exemple. Vous trouverez des [références](https://docs.microsoft.com/dotnet/core/rid-catalog) sur la création sur d’autres plateformes.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Démarrer un client

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Démarrer un serveur

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Exécuter l’exemple sans le publier

Vous pouvez également exécuter la commande ci-dessous pour démarrer un serveur ou un client

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Utiliser les clés secrètes de l’utilisateur pour spécifier la chaîne de connexion

Vous pouvez exécuter `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` dans le répertoire racine de l’exemple. Après cela, vous n’avez plus besoin de l’option `-c "<ConnectionString>"`.

## <a name="usage"></a>Usage

Une fois le serveur démarré, utilisez la commande pour envoyer un message.

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Vous pouvez démarrer plusieurs clients avec des noms différents.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
