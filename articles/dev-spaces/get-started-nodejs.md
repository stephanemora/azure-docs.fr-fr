---
title: Créer un environnement de développement Kubernetes Node.js dans le cloud avec Visual Studio Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec les conteneurs et les microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 0507208e58323fd31bb7c6cdb3a293ec0179cabe
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823909"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Prise en main d’Azure Dev Spaces avec Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Vous voici prêt à créer un environnement de développement Kubernetes dans Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
Azure Dev Spaces requiert une configuration d’ordinateur local minimale. La majeure partie de la configuration de votre environnement de développement est stockée dans le cloud et peut être partagée avec d’autres utilisateurs. Commencez par télécharger et exécuter [l’interface de ligne de commande Azure (Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Si vous avez déjà installé Azure CLI, vérifiez que vous utilisez la version 2.0.33 ou une version ultérieure.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Pendant que vous attendez la création du cluster, vous pouvez commencer à écrire du code.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Créer un conteneur Node.js dans Kubernetes

Dans cette section, vous allez créer une application web Node.js et l’exécuter dans un conteneur dans Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Créer une application web Node.js
Téléchargez du code à partir de GitHub en accédant à https://github.com/Azure/dev-spaces, puis sélectionnez **Cloner ou télécharger** pour télécharger le référentiel GitHub dans votre environnement local. Le code de ce guide se trouve dans `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

1. Recherchez le fichier `./public/index.html` et procédez à une modification du code HTML. Par exemple, remplacez la couleur d’arrière-plan de la page par une nuance de bleu :

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Enregistrez le fichier . Quelques instants plus tard, la fenêtre de terminal affiche un message indiquant qu’un fichier du conteneur en cours d’exécution a été mis à jour.
1. Accédez à votre navigateur et actualisez la page. La couleur doit avoir été mise à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucun redémarrage du processus Node.js. Par conséquent, une commande `azds up` active synchronise automatiquement tous les fichiers de contenu modifiés directement dans le conteneur en cours d’exécution dans Azure, ce qui vous permet de visualiser rapidement les modifications du contenu.

### <a name="test-from-a-mobile-device"></a>Tester l’application à partir d’un appareil mobile
Si vous ouvrez l’application web sur un appareil mobile, vous remarquerez que l’interface utilisateur ne s’affiche pas correctement sur un appareil de petite taille.

Pour résoudre ce problème, vous allez ajouter une balise META `viewport` :
1. Ouvrez le fichier `./public/index.html`.
1. Ajoutez une balise META `viewport` dans l’élément `head` existant :

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Enregistrez le fichier .
1. Actualisez le navigateur de votre appareil. L’application web doit désormais s’afficher correctement. 

Cet exemple illustre le fait que certains problèmes sont indécelables tant que vous ne testez pas vos applications sur les appareils auxquels elles sont destinées. Grâce à Azure Dev Spaces, vous pouvez rapidement itérer sur votre code et valider les modifications sur les appareils cibles.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code côté serveur nécessite un peu plus de travail, car elle requiert le redémarrage d’une application Node.js.

1. Dans la fenêtre de terminal, appuyez sur `Ctrl+C` (pour arrêter `azds up`).
1. Ouvrez le fichier de code nommé `server.js`, puis modifiez le message Hello du service : 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Enregistrez le fichier .
1. Exécutez `azds up` dans la fenêtre de terminal. 

Cette procédure régénère l’image conteneur et redéploie le graphique Helm. Rechargez la page du navigateur pour visualiser la prise en compte de vos modifications de code.

Toutefois, vous allez découvrir à la section suivante une *méthode encore plus rapide* pour développer du code. 

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Sélectionner la configuration de débogage AZDS
1. Pour ouvrir l’affichage de débogage, cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code.
1. Sélectionnez **Lancer le programme (AZDS)** comme configuration de débogage active.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez [installé l’extension VS Code pour Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’environnement de développement lorsque vous démarrez le débogage, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est joint au conteneur distant.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Définissez un point d’arrêt dans un fichier de code côté serveur, par exemple dans la fonction `app.get('/api'...` du fichier `server.js`. Actualisez la page de navigateur ou appuyez sur le bouton « Say It Again » (Répéter), ce qui doit vous permettre d’atteindre le point d’arrêt et d’exécuter le code pas à pas.

Vous disposez d’un accès complet aux informations de débogage, exactement comme si le code s’exécutait en local, notamment à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

### <a name="edit-code-and-refresh-the-debug-session"></a>Modifier le code et actualiser la session de débogage
Lorsque le débogueur est actif, apportez une modification au code ; par exemple, modifiez de nouveau le message Hello :

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Enregistrez le fichier, puis dans le **volet Actions de débogage**, cliquez sur le bouton **Actualiser**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Plutôt que de régénérer et redéployer une nouvelle image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces redémarre le processus Node.js entre les sessions de débogage afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, ou appuyez sur le bouton *Say It Again* (Répéter). Votre message personnalisé doit apparaître dans l’interface utilisateur.

### <a name="use-nodemon-to-develop-even-faster"></a>Accélérer le développement grâce à nodemon
Les développeurs Node.js ont fréquemment recours à l’outil *nodemon* pour développer des applications rapidement. Plutôt que de redémarrer manuellement le processus Node chaque fois qu’une modification de code côté serveur est effectuée, les développeurs configurent souvent leur projet Node pour que l’outil *nodemon* surveille les modifications de fichier et redémarre automatiquement le processus serveur. Dans ce type d’approche, le développeur se contente d’actualiser son navigateur après avoir apporté une modification au code.

Avec Azure Dev Spaces, vous pouvez exploiter la plupart des workflows de développement que vous utilisez dans le cadre de vos opérations de développement en local. Pour illustrer cet aspect, l’exemple de projet `webfrontend` a été configuré de manière à utiliser *nodemon* (configuré en tant que dépendance de développement dans `package.json`).

Essayez les étapes suivantes :
1. Arrêtez le débogueur VS Code.
1. Cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code. 
1. Sélectionnez **Attacher (AZDS)** comme configuration de débogage active.
1. Appuyez sur F5.

Dans cette configuration, le conteneur est configuré pour démarrer *nodemon*. Lorsque vous modifiez le code du serveur, *nodemon* redémarre automatiquement le processus Node, exactement comme lors du développement en local. 
1. Modifiez de nouveau le message Hello dans `server.js`, puis enregistrez le fichier.
1. Actualisez le navigateur ou cliquez sur le bouton *Say It Again* (Répéter) pour visualiser la prise en compte de vos modifications.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.** Ensuite, vous verrez comment créer et appeler un second conteneur.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous allez créer un deuxième service, `mywebapi`, et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous effectuerez le débogage dans les deux conteneurs.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Ouvrir l’exemple de code pour *mywebapi*
Vous devez déjà avoir l’exemple de code de `mywebapi` pour ce guide dans un dossier nommé `samples` (dans le cas contraire, accédez à https://github.com/Azure/dev-spaces et sélectionnez **Cloner ou télécharger** pour télécharger le référentiel GitHub.). Le code de cette section se trouve dans `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Ce dernier sera prêt lorsque vous verrez la barre de débogage VS Code s’afficher.
1. Notez l’URL du point de terminaison qui doit ressembler à http://localhost:\<portnumber\>. **Conseil : la barre d’état VS Code affichera une URL interactive.** Le conteneur semble s’exécuter en local, mais en réalité, il s’exécute dans votre environnement de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost. Vous devez voir une réponse du service `mywebapi` («Hello from mywebapi »).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une demande de *webfrontend* à *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`.
1. Basculer vers la fenêtre VS Code pour `webfrontend`.
1. Ajoutez ces lignes de code en haut de `server.js` :
    ```javascript
    var request = require('request');
    ```

3. *Remplacez* le code par le gestionnaire GET `/api`. Lors du traitement d’une demande, cette dernière effectue ensuite un appel à `mywebapi`, puis retourne les résultats des deux services.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez ultérieurement de quelle façon cette fonction aide les équipes au niveau du développement collaboratif.

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans le gestionnaire GET `/` par défaut.
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une demande GET à `http://mywebapi`.
1. Appuyez sur F5 dans le projet `webfrontend`.
1. Ouvrez l’application web et parcourez le code dans les deux services. L’application web devrait afficher un message concaténé provenant des deux services : « Hello from webfrontend and Hello from mywebapi ».

C’est terminé ! Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="learn-about-team-development"></a>Découvrir le développement en équipe

[!INCLUDE[](includes/team-development-1.md)]

Découvrez-le maintenant en action :
1. Accédez à la fenêtre VS Code pour `mywebapi` et modifiez le code du gestionnaire GET `/` par défaut, par exemple :

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




