---
title: Bien démarrer avec les composants composites du SDK de l’infrastructure d’interface utilisateur d’Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer avec les composants composites de l’infrastructure d’interface utilisateur
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488055"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Démarrage rapide : Bien démarrer avec les composants composites de l’infrastructure d’interface utilisateur

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Commencez à utiliser Azure Communication Services avec l’infrastructure d’interface utilisateur pour intégrer rapidement les expériences de communication à vos applications. Dans ce guide de démarrage rapide, vous allez découvrir comment intégrer des composants composites de l’infrastructure d’interface utilisateur à votre application pour générer des expériences de communication.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (Nœud 12 recommandé).
- Une ressource Communication Services active. [Créez une ressource Communication Services](./../create-communication-resource.md).
- Un jeton d’accès utilisateur pour instancier le composite d’appel. Découvrez comment [créer et gérer des jetons d’accès utilisateur](./../access-tokens.md)

## <a name="setting-up"></a>Configuration

L’infrastructure d’interface utilisateur nécessite qu’un environnement React soit configuré. C’est ce que nous allons faire maintenant. Si vous avez déjà eu une application React, vous pouvez ignorer cette section.

### <a name="set-up-react-app"></a>Configurer une application React

Dans le cadre de ce guide de démarrage rapide, nous allons utiliser le modèle create-react-app. Pour plus d'informations, consultez les pages suivantes : [Bien démarrer avec React](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

À la fin de ce processus, vous devez disposer d’une application complète dans le dossier `my-app`. Pour ce guide de démarrage rapide, nous allons modifier des fichiers se trouvant dans le dossier `src`.

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Calling pour JavaScript. Déplacez le tarball fourni (préversion privée) vers le répertoire my-app.

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="run-create-react-app"></a>Exécuter le modèle de création d’application React

Testons l’installation de l’application React créée en exécutant :

```console

npm run start 

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque cliente d’interface utilisateur Azure Communication Services :

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | Composant composite qui restitue une expérience d’appel de groupe avec les contrôles et la galerie des participants. |
| GroupChat | Composant composite qui restitue une expérience de conversation de groupe avec un thread et une entrée de conversation |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Initialiser les composants composites Appel de groupe et Conversation de groupe

Accédez au dossier `src` se trouvant dans `my-app`, puis recherchez le fichier `app.js`. Ici, nous allons supprimer le code suivant afin d’initialiser nos composants composites pour la conversation et l’appel de groupe. Vous pouvez choisir celui à utiliser en fonction du type d’expérience de communication que vous générez. Si nécessaire, vous pouvez utiliser les deux en même temps. Pour initialiser les composants, vous avez besoin d’un jeton d’accès récupéré à partir d’Azure Communication Services. Pour plus d’informations sur la façon d’obtenir des jetons d’accès, consultez [Créer et gérer des jetons d’accès utilisateur](./../access-tokens.md).

> [!NOTE]
> Les composants ne génèrent pas de jetons d’accès, d’ID de groupe ou d’ID de thread. Ces éléments proviennent de services qui suivent les étapes appropriées permettant de générer ces ID et de les transmettre à l’application cliente. Pour plus d'informations, consultez les pages suivantes : [Architecture du serveur client](./../../concepts/client-and-server-architecture.md).
> 
> Par exemple : Le composite Conversation de groupe s’attend à ce que le `userId` associé au jeton (`token`) utilisé pour l’initialiser ait déjà été joint au `threadId` fourni. Si le jeton n’a pas été joint à l’ID de thread, le composite Conversation de groupe échoue. Pour plus d’informations sur les conversations, consultez : [Bien démarrer avec les conversations](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Exécuter le démarrage rapide

Pour exécuter le code ci-dessus, utilisez la commande :

```console

npm run start 

```

Pour tester entièrement les fonctionnalités, vous devez disposer d’un deuxième client bénéficiant de la fonctionnalité d’appel et de conversation pour joindre l’appel et le thread de conversation. Consultez notre [exemple de bannière d’appel](./../../samples/calling-hero-sample.md) et notre [exemple de bannière de conversation](./../../samples/chat-hero-sample.md) comme options potentielles.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Essayer les composants de base de l’infrastructure d’interface utilisateur](./get-started-with-components.md)

Pour plus d’informations, consultez les ressources suivantes :
- [Vue d’ensemble de l’infrastructure d’interface utilisateur](../../concepts/ui-framework/ui-sdk-overview.md)
- [Fonctionnalités de l’infrastructure d’interface utilisateur](./../../concepts/ui-framework/ui-sdk-features.md)
