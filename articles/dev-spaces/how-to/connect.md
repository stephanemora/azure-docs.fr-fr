---
title: Connecter votre ordinateur de développement à un cluster AKS (préversion)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Découvrez comment connecter votre ordinateur de développement à un cluster AKS avec Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235017"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Connecter votre ordinateur de développement à un cluster AKS (préversion)

Azure Dev Spaces vous permet d’exécuter et de déboguer du code avec ou sans conteneur sur votre ordinateur de développement, tout en étant connecté à votre cluster Kubernetes avec le reste de votre application ou de vos services. La connexion de votre ordinateur de développement à votre cluster vous permet de développer rapidement votre application et d’effectuer des tests de bout en bout sans avoir à créer de configuration de Docker ou de Kubernetes. Vous pouvez également vous connecter à votre cluster AKS sans affecter les autres charges de travail ou utilisateurs qui peuvent utiliser le même cluster.

Azure Dev Spaces redirige le trafic entre votre cluster AKS connecté et votre ordinateur de développement. Cette redirection du trafic permet au code sur votre ordinateur de développement et les services en cours d’exécution dans votre cluster AKS de communiquer comme s’ils se trouvaient dans le même cluster AKS. Étant donné que votre code s’exécute sur votre ordinateur de développement, vous bénéficiez également de la flexibilité dans les outils de développement que vous utilisez pour exécuter et déboguer ce code. Azure Dev Spaces offre également un moyen de répliquer des variables d’environnement et des fichiers montés disponibles pour les pods dans votre cluster AKS sur votre ordinateur de développement.

Dans ce guide, vous allez apprendre à :

* Configurer Azure Dev Spaces sur un cluster Kubernetes managé dans Azure.
* Déployer une vaste application avec plusieurs microservices dans un espace de développement.
* Utilisez Azure Dev Spaces pour rediriger le trafic entre votre cluster AKS et le code qui s’exécute sur votre ordinateur de développement.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

Ce guide utilise l’exemple d’application [Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) pour illustrer la connexion de votre ordinateur de développement à un cluster AKS. Suivez les instructions du fichier [Lisez-moi de l’exemple d’application Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) pour exécuter l’exemple d’application. Sinon, si vous disposez de votre propre application sur un cluster AKS, vous pouvez toujours suivre les étapes ci-dessous et utiliser les noms de vos propres services et pods.

### <a name="limitations"></a>Limites

* UDP n’est pas pris en charge pour le moment.

### <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI][azure-cli].
* [Visual Studio Code][vs-code] avec l’extension [Azure Dev Spaces][azds-vs-code] installée et exécutée sur MacOS ou Windows 10.
* L’exemple d’application [Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) ou votre propre application s’exécutant sur un cluster AKS.

## <a name="connect-your-development-computer"></a>Connecter votre ordinateur de développement

Ouvrez *dev-Spaces/Samples/BikeSharingApp/Bikes* dans Visual Studio Code et utilisez l’extension Azure Dev Spaces pour connecter votre ordinateur de développement à votre cluster AKS.

Pour utiliser l’extension Azure Dev Spaces, ouvrez la palette de commandes dans Visual Studio Code en cliquant sur *Afficher* puis *Palette de commandes*. Commencez à taper `Azure Dev Spaces: Redirect`, puis cliquez sur `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]` ou `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Commandes](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Sélectionner une option de redirection

Si vous exécutez `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, vous êtes invité à choisir un service Kubernetes existant :

![Choisir un service](../media/how-to-connect/connect-choose-service.png)

Cette option redirige tout le trafic du cluster AKS pour ce service vers la version de votre application en cours d’exécution sur votre ordinateur de développement. Si ce service a plusieurs pods en cours d’exécution dans le cluster AKS, tout le trafic pour ce service est acheminé uniquement vers votre ordinateur de développement. Azure Dev Spaces achemine également tout le trafic sortant de l’application vers votre cluster AKS.

Si vous exécutez `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, vous êtes invité à choisir un pod spécifique :

![Choisir un pod](../media/how-to-connect/connect-choose-pod.png)

Cette option permet de se connecter à un pod spécifique. Cette option est utile pour l’interaction avec les pods qui n’envoient pas et ne reçoivent pas de trafic et ne répliquent pas les pods terminés. Si le pod envoie et reçoit du trafic, cette option se comporte de la même façon pour `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` et redirige tout le trafic dans le cluster AKS pour tous les pods relatifs au service du pod sélectionné.

Si vous exécutez `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, vous n’êtes pas invité à sélectionner un pod ou un service existant. Cette option redirige tout le trafic sortant de l’application en cours d’exécution sur votre ordinateur de développement vers le cluster AKS.

Pour cet exemple, choisissez `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, puis sélectionnez le service *Bikes*.

### <a name="select-a-connection-mode"></a>Sélectionner un mode de connexion

Une fois que vous avez sélectionné l’option de redirection, vous êtes invité à choisir le mode de connexion *Remplacer* ou *Cloner*.

![Remplacer ou cloner](../media/how-to-connect/connect-replace-clone.png)

L’option *Remplacer* remplace le pod ou le service actuel dans le cluster AKS et redirige tout le trafic de ce service vers votre ordinateur de développement. Cette option peut perturber les autres services de votre cluster AKS qui interagissent avec le service que vous redirigez et peuvent ne pas fonctionner tant que vous n’avez pas démarré l’application sur votre ordinateur de développement. L’option *Cloner* vous permet de choisir un espace de développement enfant existant ou de créer un espace de développement enfant pour rediriger le trafic d’un pod ou d’un service vers votre ordinateur de développement. Cette option vous permet de travailler de manière isolée et de ne pas perturber d’autres services, car seul le trafic vers cet espace de développement enfant est redirigé vers votre ordinateur de développement. L’option *Cloner* nécessite que Azure Dev Spaces soit activée sur votre cluster AKS.

Pour cet exemple, sélectionnez *Remplacer*.

> [!NOTE]
> Si le pod de votre service existant comporte plusieurs conteneurs, vous êtes également invité à choisir le conteneur de l’application.

### <a name="select-a-port-for-your-application"></a>Sélectionner un port pour votre application

Après avoir sélectionné votre mode de connexion, vous êtes invité à entrer le port TCP de votre application locale. Si votre application ouvre plusieurs ports, séparez-les par une virgule, par exemple *80,81*. Si votre application n’accepte aucune demande réseau, entrez *0*. Pour cet exemple, entrez *3000*.

![Se connecter au port choisi](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Vérifiez que vous êtes connecté

Après avoir sélectionné le port TCP de votre application, Azure Dev Spaces établit une connexion au cluster AKS. Azure Dev Spaces injecte un agent dans votre cluster AKS pour rediriger le trafic entre le cluster AKS et votre ordinateur de développement. L’établissement de cette connexion peut prendre quelques minutes. Azure Dev Spaces demandera également l’accès administrateur pour modifier le fichier d’*hébergement* sur votre ordinateur de développement.

> [!IMPORTANT]
> Une fois qu’Azure Dev Spaces a établi une connexion avec votre cluster AKS, les autres services de votre cluster AKS peuvent ne pas fonctionner correctement tant que vous n’avez pas démarré le service sur votre ordinateur de développement si vous choisissez le mode de connexion *Remplacer*. Vous pouvez choisir le mode de connexion *Cloner* à la place pour créer un espace de développement enfant pour votre redirection et éviter toute interruption de l’espace parent. En outre, si votre service a une dépendance qui n’est pas disponible sur votre ordinateur de développement, vous devrez peut-être modifier votre application ou fournir une [configuration supplémentaire](#additional-configuration)

Azure Dev Spaces ouvre une fenêtre de terminal intitulée *AZDS Connect-Bikes* après avoir établi une connexion avec votre cluster AKS. Toutes les variables d’environnement et les entrées DNS de cette fenêtre de terminal sont configurées à partir de votre cluster AKS. Tout code que vous exécutez dans cette fenêtre de terminal ou à l’aide du débogueur Visual Studio Code est connecté au cluster AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

En outre, Azure Dev Spaces crée une fenêtre intitulée *Connecter les espaces de développement* avec toute sa sortie.

![Output](../media/how-to-connect/connect-output.png)

Azure Dev Spaces a également un élément de barre d’État qui indique l’état de la connexion.

![Statut](../media/how-to-connect/connect-status.png)

Vérifiez que la barre d’état affiche *Espaces de développement : Connecté à dev/Bikes sur le port local 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Configurer votre application sur votre ordinateur de développement

Ouvrez la fenêtre de terminal *AZDS Connect-Bikes* et exécutez `npm install` :

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Cliquez sur *Déboguer* puis *Ouvrir des configurations*. Si vous êtes invité à sélectionner un environnement, choisissez *Node.js*. Cela crée un fichier `.vscode/launch.json`. Remplacez le contenu de ce fichier par le suivant :

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Ouvrez [package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) et ajoutez un script de débogage :

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Démarrer votre application sur votre ordinateur de développement

Cliquez sur l’icône *Débogage* à gauche et cliquez sur le bouton Démarrer à côté de *Lancer via NPM* en haut.

![Lancer via NPM](../media/how-to-connect/launch-npm.png)

Votre application démarre et Azure Dev Spaces redirige le trafic entre votre cluster AKS et votre ordinateur de développement. Vous verrez des messages similaires à ce qui suit dans la *console de débogage* :

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Accédez au service *bikesharingweb* en cliquant sur la barre d’état d’Azure Dev Spaces et en choisissant l’URL publique de votre application. Vous pouvez également trouver l’URL publique à partir de la commande `azds list-uris` exécutée précédemment. Si vous n’utilisez pas Azure Dev Spaces sur votre cluster, utilisez l’adresse IP ou l’URL de l’application pour l’espace de noms que vous utilisez. Dans l’exemple ci-dessus, l’URL publique pour le service *bikesharingweb* est `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Sélectionnez *Aurelia Briggs (client)* en tant qu’utilisateur, puis sélectionnez un vélo à louer.

### <a name="set-a-break-point"></a>Définir un point d’arrêt

Ouvrez [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233), puis cliquez quelque part sur la ligne 233 pour y placer votre curseur. Définissez un point d’arrêt en appuyant sur *F9* ou en cliquant sur *Déboguer*, puis sur *Activer/désactiver le point d’arrêt*.

Accédez au service *bikesharingweb* en ouvrant l’URL publique. Sélectionnez *Aurelia Briggs (client)* en tant qu’utilisateur, puis sélectionnez un vélo à louer. Notez que l’image du vélo ne se charge pas. Revenez à Visual Studio Code et notez que la ligne 233 est mise en surbrillance. Le point d’arrêt que vous avez défini a suspendu le service à la ligne 233. Pour reprendre le service, appuyez sur *F5* ou cliquez sur *Déboguer*, puis sur *Continuer*. Revenez à votre navigateur et vérifiez que vous voyez une image d’espace réservé pour le vélo.

Supprimez le point d’arrêt en plaçant votre curseur sur la ligne 233 dans `server.js` et en appuyant sur *F9*.

### <a name="update-your-application"></a>Mettre à jour votre application

Modifiez `server.js` pour supprimer les lignes 232 et 233 :

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La section doit maintenant se présenter comme suit :

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Enregistrez vos modifications, cliquez sur *Débogage* puis *Redémarrer le débogage*. Rafraichissez votre navigateur et vérifiez que vous ne voyez plus une image d’espace réservé pour le vélo.

Cliquez sur *Déboguer*, puis sur *Arrêter le débogage* pour arrêter le débogueur. Cliquez sur la barre d’état Azure Dev Spaces pour vous déconnecter du cluster AKS.

## <a name="additional-configuration"></a>Configuration supplémentaire

Azure Dev Spaces peut gérer le trafic de routage et répliquer les variables d’environnement sans aucune configuration supplémentaire. Si vous devez télécharger des fichiers montés sur le conteneur de votre cluster AKS, tel qu’un fichier ConfigMap, vous pouvez créer un `azds-local.env` pour télécharger ces fichiers sur votre ordinateur de développement.

Voici un exemple `azds-local.env` :

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Utilisation de la journalisation et des diagnostics

La sortie de la journalisation est écrite dans la fenêtre *Dev Spaces Connect* après avoir connecté votre ordinateur de développement à votre cluster AKS.

![Output](../media/how-to-connect/connect-output.png)

Cliquez sur la barre d’état Azure Dev Spaces, puis choisissez *Afficher les informations de diagnostic*. Cette commande imprime les variables d’environnement actuelles et les entrées DNS dans la sortie de journalisation.

![Sortie avec des diagnostics](../media/how-to-connect/connect-output-diagnostics.png)

De plus, les journaux de diagnostic sont disponibles dans le répertoire `Azure Dev Spaces` se trouvant dans le [répertoire *TEMP* de votre ordinateur de développement][azds-tmp-dir].

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser les actions Azure Dev Spaces et GitHub pour tester les modifications d’une demande de tirage (pull request) directement dans AKS avant de fusionner la requête de tirage dans la branche principale de votre référentiel.

> [!div class="nextstepaction"]
> [Actions GitHub & Azure Kubernetes Service][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download