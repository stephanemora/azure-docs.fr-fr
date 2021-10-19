---
title: 'Démarrage rapide : Lanceur de dés'
description: Créer rapidement une application de lancer de dés à l’aide du service Relais Azure Fluid
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662084"
---
# <a name="quickstart-dice-roller"></a>Démarrage rapide : Lanceur de dés

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Dans ce guide de démarrage rapide, nous allons parcourir le processus de création d’une application de lancer de dés qui utilise le service Relais Azure Fluid. Ce guide de démarrage rapide se divise en deux parties. Dans la première partie, nous allons créer l’application elle-même et l’exécuter sur un serveur Fluid local. Dans la deuxième partie, nous allons reconfigurer l’application pour l’exécuter sur le service Relais Azure Fluid au lieu du serveur de développement local.

L’exemple de code utilisé dans ce guide de démarrage rapide est disponible [ici](https://github.com/microsoft/FluidHelloWorld/tree/main-azure).

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Pour suivre ce guide de démarrage rapide, vous devez avoir un compte Azure [configuré par le service Relais Azure Fluid](../how-tos/provision-fluid-azure-portal.md). Si vous n’avez pas de compte, vous pouvez [essayer Azure gratuitement](https://azure.com/free).

Vous devez aussi avoir les logiciels suivants installés sur votre ordinateur.

- Un éditeur de code -- Nous recommandons [Visual Studio Code](https://code.visualstudio.com/).
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download) version 12.17 ou ultérieure

## <a name="getting-started-locally"></a>Bien démarrer en local

Pour commencer, vous devez télécharger l’exemple d’application depuis GitHub. Ouvrez une nouvelle fenêtre de commande et accédez au dossier dans lequel vous souhaitez télécharger le code et utilisez Git pour cloner le [dépôt FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld). Le processus de clonage crée un sous-dossier appelé FluidHelloWorld avec les fichiers projet qu’il contient.

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

Accédez au dossier nouvellement créé, installez les dépendances et démarrez l’application.

```cli
cd FluidHelloWorld
npm install
...
npm start
```


Lorsque vous exécutez la commande `npm start`, deux événements se produisent. Tout d’abord, un serveur Fluid est lancé dans un processus local. Ce serveur est conçu uniquement pour le développement. Vous effectuerez une mise à niveau vers un serveur de production hébergé par Azure plus tard. Ensuite, un nouvel onglet de navigateur s’ouvre sur une page contenant une nouvelle instance de l’application de lanceur de dés. Vous pouvez ouvrir de nouveaux onglets avec la même URL pour créer des instances supplémentaires de l’application de lanceur de dés. Chaque instance de l’application est configurée par défaut pour utiliser votre service Fluid local. Cliquez sur le bouton **Roll** (Lancer) dans une instance de l’application et notez que l’état des dés change dans chaque client.

## <a name="upgrading-to-azure-fluid-relay"></a>Mise à niveau vers Relais Azure Fluid

Pour une exécution sur le service Relais Azure Fluid, vous devez mettre à jour la configuration de votre application pour qu’elle se connecte à votre service Azure au lieu de votre serveur local.

### <a name="configure-and-create-an-azure-client"></a>Configurer et créer un client Azure

Pour configurer le client Azure, remplacez les valeurs de l’objet `serviceConfig` dans `app.js` par vos valeurs de configuration du service Relais Azure Fluid. Ces valeurs se trouvent dans la section « Clé d’accès » de la ressource Relais Fluid dans le portail Azure.

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> Lors du développement, vous pouvez utiliser `InsecureTokenProvider` pour générer et signer des jetons d’authentification que le service Relais Azure Fluid acceptera. Toutefois, comme son nom l’indique, il n’est pas sécurisé et ne doit pas être utilisé dans des environnements de production. Le processus de création de ressource Relais Azure Fluid vous fournit une clé secrète qui peut être utilisée pour signer des demandes sécurisées. **Pour vous assurer que cette clé secrète n’est pas exposée, elle doit être remplacée par une autre implémentation d’ITokenProvider qui extrait le jeton à partir d’un service back-end sécurisé fourni par le développeur avant de le mettre en production.**

### <a name="build-and-run-the-client-only"></a>Créer et exécuter le client uniquement

Maintenant que vous avez configuré votre application pour qu’elle utilise Azure au lieu d’un serveur local, vous n’avez pas besoin de lancer le serveur Fluid local avec votre application cliente. Vous pouvez lancer le client sans avoir à lancer aussi le serveur avec cette commande. 

```bash
npm run start:client
```

🥳**Félicitations**🎉 Vous avez réussi la première étape permettant d’accéder à l’univers de la collaboration Fluid.
