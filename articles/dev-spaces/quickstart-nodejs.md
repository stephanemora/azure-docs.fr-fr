---
title: Créer un espace de développement Kubernetes dans le cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: quickstart
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 52cca29392f226aa0f33081b9303a8c5d1f41036
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413334"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-nodejs"></a>Démarrage rapide : Créer un espace de développement Kubernetes avec Azure Dev Spaces (Node.js)

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs en utilisant VS Code et la ligne de commande.
- Déboguer le code exécuté dans votre cluster.

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md) ou postez un commentaire sur cette page. Vous pouvez aussi essayer ce [didacticiel](get-started-nodejs.md) plus détaillé.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.43 ou ultérieure.
- Un cluster Kubernetes, qui exécute Kubernetes 1.9.6 ou une version ultérieure dans les régions EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral ou CanadaEast, avec le **routage d’application HTTP** activé.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Configurer Azure Dev Spaces

Il est possible d’installer l’interface de ligne de commande Azure et l’extension Azure Dev Spaces puis de les exécuter sur des ordinateurs Windows, Mac ou Linux. Pour Linux, les distributions prises en charge sont les suivantes : Ubuntu (18.04 16.04 et 14.04), Debian 8 et 9, RHEL 7, Fedora 26 +, CentOS 7, openSUSE 42.2 et SLES 12.

Pour configurer Azure Dev Spaces, procédez comme suit :

1. Configurez Dev Spaces sur votre cluster AKS : `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Téléchargez l’[extension Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) pour VS Code. Cliquez sur Installer une fois sur la page Place de marché de l’extension, puis à nouveau dans VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

1. Téléchargez l’exemple de code à partir de GitHub : [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Remplacez le répertoire par le dossier webfrontend : `cd dev-spaces/samples/nodejs/getting-started/webfrontend`
1. Générez des ressources de graphiques Docker et Helm : `azds prep --public`
1. Générez et exécutez votre code dans AKS. Dans la fenêtre de terminal à partir du **dossier webfrontend**, exécutez la commande suivante : `azds up`
1. Analysez la sortie de la console pour y rechercher les informations concernant l’URL qui a été créée par la commande `up`. Ces informations se présentent sous la forme suivante : 

   ```output
   (pending registration) Service 'webfrontend' port 'http' will be available at <url>
   Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   Ouvrez cette URL dans une fenêtre de navigateur. Vous devriez alors voir l’application web se charger. Lorsque le conteneur s’exécute, les sorties `stdout` et `stderr` sont transmises à la fenêtre de terminal.
   
   > [!Note]
   > À la première exécution, vous devrez peut-être patienter plusieurs minutes avant que le serveur DNS public ne soit prêt. Si l’URL publique n’est pas résolue, vous pouvez utiliser en guise d’alternative l’URL http://localhost: <portnumber> affichée dans la sortie de la console. Si vous utilisez l’URL localhost, le conteneur semble s’exécuter en local. En réalité, il s’exécute dans AKS. Pour des raisons pratiques et pour faciliter l’interaction avec le service à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure. Vous pouvez réessayer l’URL publique plus tard quand l’enregistrement DNS est prêt.

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

1. Recherchez le fichier `./public/index.html` et procédez à une modification du code HTML. Par exemple, remplacez la couleur d’arrière-plan de la page par une nuance de bleu :

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. Enregistrez le fichier . Quelques instants plus tard, la fenêtre de terminal affiche un message indiquant qu’un fichier du conteneur en cours d’exécution a été mis à jour.
1. Accédez à votre navigateur et actualisez la page. La couleur doit avoir été mise à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucun redémarrage du processus Node.js. Par conséquent, une commande `azds up` active synchronise automatiquement tous les fichiers de contenu modifiés directement dans le conteneur en cours d’exécution dans Azure, ce qui vous permet de visualiser rapidement les modifications du contenu.

### <a name="test-from-a-mobile-device"></a>Tester l’application à partir d’un appareil mobile
Ouvrez l’application web sur un appareil mobile à l’aide de l’URL publique pour webfrontend. Nous vous suggérons de copier l’URL sur votre poste de travail et de l’envoyer à votre appareil pour ne pas avoir à entrer la grande adresse. Quand l’application web se charge sur votre appareil mobile, notez que l’interface utilisateur ne s’affiche pas correctement sur un petit appareil.

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

Dans cette section, vous utiliserez VS Code pour déboguer directement notre conteneur exécuté dans Azure. Vous apprendrez aussi à obtenir une boucle modification-exécution-test plus rapide.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initialiser des ressources de débogage avec l’extension VS Code
Vous devez avant tout configurer votre projet de code pour permettre à VS Code de communiquer avec notre espace de développement dans Azure. L’extension VS Code pour Azure Dev Spaces fournit une commande d’assistance pour définir la configuration de débogage. 

Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

Celle-ci ajoute la configuration de débogage pour Azure Dev Spaces sous le dossier `.vscode`. Cette commande ne doit pas être confondue avec la commande `azds prep`, qui configure le projet pour le déploiement.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Sélectionner la configuration de débogage AZDS
1. Pour ouvrir l’affichage de débogage, cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code.
1. Sélectionnez **Lancer le programme (AZDS)** comme configuration de débogage active.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’extension VS Code pour Azure Dev Spaces.

### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’espace de développement lorsque vous démarrez le débogage, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est joint au conteneur distant.

> [!Tip]
> La barre d’état VS Code affichera une URL interactive.

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

L’exemple `webfrontend` projet a été configuré pour utiliser [nodemon](https://nodemon.io/), un outil populaire permettant d’accélérer le développement de Node.js qui est entièrement compatible avec Azure Dev Spaces.

Essayez les étapes suivantes :
1. Arrêtez le débogueur VS Code.
1. Cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code. 
1. Sélectionnez **Attacher (AZDS)** comme configuration de débogage active.
1. Appuyez sur F5.

Dans cette configuration, le conteneur est configuré pour démarrer *nodemon*. Lorsque vous modifiez le code du serveur, *nodemon* redémarre automatiquement le processus Node, exactement comme lors du développement en local. 
1. Modifiez de nouveau le message Hello dans `server.js`, puis enregistrez le fichier.
1. Actualisez le navigateur ou cliquez sur le bouton *Say It Again* (Répéter) pour visualiser la prise en compte de vos modifications.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.**

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](team-development-nodejs.md)
