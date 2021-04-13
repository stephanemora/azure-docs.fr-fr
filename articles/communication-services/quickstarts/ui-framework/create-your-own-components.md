---
title: Créer votre propre composant Infrastructure d’interface utilisateur
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez apprendre à générer un composant personnalisé compatible avec l’infrastructure d’interface utilisateur
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 798b898f6e2367e3096bcf985715fd48de3d394f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930215"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Démarrage rapide : Créer votre propre composant de l’infrastructure d’interface utilisateur

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Commencez à utiliser Azure Communication Services avec l’infrastructure d’interface utilisateur pour intégrer rapidement les expériences de communication à vos applications.

Dans ce guide de démarrage rapide, vous allez apprendre à créer vos propres composants à l’aide de l’interface à l’état prédéfini proposée par l’infrastructure d’interface utilisateur. Cette approche est idéale pour les développeurs nécessitant plus de capacités de personnalisation et qui veulent utiliser leurs propres ressources de conception pour l’expérience. 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (Nœud 12 recommandé).
- Une ressource Communication Services active. [Créez une ressource Communication Services](./../create-communication-resource.md).
- Un jeton d’accès utilisateur pour instancier le client d’appel. Découvrez comment [créer et gérer des jetons d’accès utilisateur](./../access-tokens.md)

L’infrastructure d’interface utilisateur nécessite qu’un environnement React soit configuré. C’est ce que nous allons faire maintenant. Si vous avez déjà eu une application React, vous pouvez ignorer cette section.

### <a name="set-up-react-app"></a>Configurer une application React

Dans le cadre de ce guide de démarrage rapide, nous allons utiliser le modèle create-react-app. Pour plus d'informations, consultez les pages suivantes : [Bien démarrer avec React](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

À la fin de ce processus, vous devez disposer d’une application complète dans le dossier `my-app`. Pour ce guide de démarrage rapide, nous allons modifier des fichiers se trouvant dans le dossier `src`.

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer le SDK Azure Communication Services Calling pour JavaScript. Déplacez le tarball fourni (préversion privée) vers le répertoire my-app.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="run-create-react-app"></a>Exécuter le modèle de création d’application React

Testons l’installation de l’application React créée en exécutant :

```console

npm run start   

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK d’interface utilisateur Azure Communication Services :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Fournisseur| Fournisseur d’interface utilisateur Fluent qui permet aux développeurs de modifier les composants d’interface utilisateur Fluent sous-jacents|
| CallingProvider| Fournisseur appelant utilisé pour instancier un appel. Nécessaire pour ajouter des composants de base|
| ChatProvider | Fournisseur de conversation utilisé pour instancier un thread de conversation. Nécessaire pour ajouter des composants de base|
| connectFuncsToContext | Méthode pour connecter des composants de l’infrastructure d’interface utilisateur avec des fournisseurs sous-jacents à l’aide de mappeurs |
| MapToChatMessageProps | Mappeur de la couche de données du message de conversation qui fournit aux composants des propriétés de message de conversation |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Initialiser des Fournisseurs de conversation avec des informations d’identification Azure Communication Services

Dans le cadre de ce guide de démarrage rapide, nous allons utiliser les conversions comme exemple. Pour plus d’informations sur les appels, consultez [Démarrage rapide avec les composants de base](./get-started-with-components.md) et [Démarrage rapide avec les composants composites](./get-started-with-composites.md).

Accédez au dossier `src` se trouvant dans `my-app`, puis recherchez le fichier `app.js`. Ici, nous allons supprimer le code suivant pour initialiser notre Fournisseur de conversation. Ce fournisseur est chargé de la maintenance du contexte des expériences d’appel et de conversation. Pour initialiser les composants, vous avez besoin d’un jeton d’accès récupéré à partir d’Azure Communication Services. Pour plus d’informations sur la façon d’obtenir un jeton d’accès, consultez [Créer et gérer des jetons d’accès](./../access-tokens.md).

Les composants de l’infrastructure d’interface utilisateur suivent la même architecture générale pour le reste du service. Les composants ne génèrent pas de jetons d’accès, d’ID de groupe ou d’ID de thread. Ces éléments proviennent de services qui suivent les étapes appropriées permettant de générer ces ID et de les transmettre à l’application cliente. Pour plus d’informations, consultez [Architecture du serveur client](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Une fois initialisé, ce fournisseur vous permet de générer votre propre disposition à l’aide d’un composant de l’infrastructure d’interface utilisateur et de toute logique de disposition supplémentaire. Le fournisseur s’occupe de l’initialisation de toute la logique sous-jacente et de la connexion correcte des différents composants entre eux. Ensuite, nous allons créer un composant personnalisé à l’aide de mappeurs de l’infrastructure d’interface utilisateur pour vous connecter à notre fournisseur de conversation.


## <a name="create-a-custom-component-using-mappers"></a>Créer un composant personnalisé à l’aide de mappeurs

Nous allons commencer par créer un fichier appelé `SimpleChatThread.js` dans lequel nous allons créer le composant. Nous allons commencer par importer les composants de l’infrastructure d’interface utilisateur dont nous aurons besoin. Ici, nous allons utiliser le code HTML et React prêt à l’emploi afin de créer un composant entièrement personnalisé pour un thread de conversation simple. À l’aide de la méthode `connectFuncsToContext`, nous allons utiliser le mappeur `MapToChatMessageProps` pour mapper des propriétés aux composants personnalisés `SimpleChatThread`. Ces propriétés nous permettront d’accéder aux messages de conversation envoyés et reçus pour les renseigner sur notre thread simple.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Ajouter votre composant personnalisé à votre application

Maintenant que notre composant personnalisé est prêt, nous allons l’importer et l’ajouter à notre disposition.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Exécuter le démarrage rapide

Pour exécuter le code ci-dessus, utilisez la commande :

```console

npm run start   

```

Pour tester entièrement les fonctionnalités, vous devez disposer d’un deuxième client bénéficiant de la fonctionnalité de conversation pour envoyer des messages qui seront reçus par notre thread de conversation simple. Consultez notre [exemple de bannière d’appel](./../../samples/calling-hero-sample.md) et notre [exemple de bannière de conversation](./../../samples/chat-hero-sample.md) comme options potentielles.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Essayer les composants composites de l’infrastructure d’interface utilisateur](./get-started-with-composites.md)

Pour plus d’informations, consultez les ressources suivantes :
- [Vue d’ensemble de l’infrastructure d’interface utilisateur](../../concepts/ui-framework/ui-sdk-overview.md)
- [Fonctionnalités de l’infrastructure d’interface utilisateur](./../../concepts/ui-framework/ui-sdk-features.md)
- [Démarrage rapide avec les composants de base de l’infrastructure d’interface utilisateur](./get-started-with-components.md)
