---
title: Utiliser Local Process with Kubernetes avec Visual Studio Code (préversion)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Découvrez comment utiliser Local Process with Kubernetes pour connecter votre ordinateur de développement à un cluster Kubernetes avec Azure Dev Spaces.
keywords: Local Process with Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316460"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Utiliser Local Process with Kubernetes avec Visual Studio Code (préversion)

Local Process with Kubernetes vous permet d’exécuter et de déboguer du code sur votre ordinateur de développement tout en restant connecté à votre cluster Kubernetes avec le reste de votre application ou de vos services. Par exemple, si vous avez une architecture de microservices importante avec de nombreux services et bases de données interdépendants, la réplication de ces dépendances sur votre ordinateur de développement peut s’avérer difficile. En outre, la création et le déploiement de code sur votre cluster Kubernetes pour chaque changement de code pendant le développement en boucle interne peuvent être lents, fastidieux et difficiles à effectuer avec un débogueur.

Local Process with Kubernetes vous évite d’avoir à créer et à déployer votre code sur votre cluster en créant une connexion directement entre votre ordinateur de développement et votre cluster. La connexion de votre ordinateur de développement à votre cluster pendant le débogage vous permet de tester et de développer rapidement votre service dans le contexte de l’application complète sans créer une configuration Docker ou Kubernetes.

Local Process with Kubernetes redirige le trafic entre votre cluster Kubernetes connecté et votre ordinateur de développement. Grâce à cette redirection du trafic, le code sur votre ordinateur de développement et les services en cours d’exécution dans votre cluster Kubernetes peuvent communiquer comme s’ils se trouvaient dans le même cluster Kubernetes. Local Process with Kubernetes offre également un moyen de répliquer des variables d’environnement et des volumes montés disponibles pour les pods dans votre cluster Kubernetes sur votre ordinateur de développement. L’accès aux variables d’environnement et aux volumes montés sur votre ordinateur de développement vous permet de travailler rapidement sur votre code sans avoir à répliquer ces dépendances manuellement.

Ce guide vous explique comment utiliser Local Process with Kubernetes pour rediriger le trafic entre votre cluster Kubernetes et le code qui s’exécute sur votre ordinateur de développement. Ce guide fournit également un script pour le déploiement d’un exemple d’application volumineux avec plusieurs microservices sur un cluster Kubernetes.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][preview-terms]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

Ce guide utilise l’[exemple d’application Azure Dev Spaces Bike Sharing][bike-sharing-github] pour illustrer la connexion de votre ordinateur de développement à un cluster Kubernetes. Si vous disposez déjà de votre propre application s’exécutant sur un cluster Kubernetes, vous pouvez toujours suivre les étapes ci-dessous et utiliser les noms de vos propres services.

### <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI][azure-cli].
* [Visual Studio Code][vs-code] exécuté sur macOS ou Windows 10.
* L’extension [Azure Dev Spaces][azds-vs-code] version 2.0.220200601 ou supérieure installée dans Visual Studio Code.
* [Interface CLI Azure Dev Spaces installée][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Créer un cluster Kubernetes

Créez un cluster AKS dans une [région prise en charge][supported-regions]. Les commandes ci-dessous créent un groupe de ressources nommé *MyResourceGroup* et un cluster AKS nommé *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Installation de l’exemple d’application

Installez l’exemple d’application sur votre cluster à l’aide du script fourni. Vous pouvez exécuter ce script sur votre ordinateur de développement ou à l’aide d’[Azure Cloud Shell][azure-cloud-shell].

> [!IMPORTANT]
> Pour exécuter le script, vous devez avoir un accès *Propriétaire* ou *Contributeur* à votre cluster.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Accédez à l’exemple d’application s’exécutant sur votre cluster en ouvrant son URL publique, qui est affichée dans la sortie du script d’installation.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

Dans l’exemple ci-dessus, l’URL publique est `dev.bikesharingweb.EXTERNAL_IP.nip.io`.

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Connexion à votre cluster et débogage d’un service

Sur votre ordinateur de développement, téléchargez et configurez la CLI Kubernetes pour vous connecter à votre cluster Kubernetes avec [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Ouvrez *dev-spaces/samples/BikeSharingApp/Bikes* à partir de l’[exemple d’application Azure Dev Spaces Bike Sharing][bike-sharing-github] dans Visual Studio Code. Ouvrez l’extension Azure Kubernetes Service et sélectionnez l’espace de noms *dev* dans le cluster *MyAKS*.

![Sélection de l’espace de noms](../media/local-process-kubernetes-vs-code/select-namespace.png)

Utilisez la commande `npm install` pour installer les dépendances de l’application.

```console
npm install
```

Sélectionnez l’icône *Débogage* sur la gauche, puis sélectionnez *Local Process with Kubernetes (Preview)* en haut.

![Sélection de Local Process with Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Cliquez sur le bouton de démarrage en regard de *Local Process with Kubernetes (Preview)* . Quand vous exécutez cette configuration de lancement pour la première fois, vous êtes invité à configurer le service que vous souhaitez remplacer, le port à transférer à partir de votre ordinateur de développement et la tâche de lancement à utiliser.

Choisissez le service *bikes*.

![Choisir un service](../media/local-process-kubernetes-vs-code/choose-service.png)

Tout le trafic dans le cluster Kubernetes est redirigé pour le service *bikes* vers la version de votre application s’exécutant sur votre ordinateur de développement. Local Process with Kubernetes route également tout le trafic sortant de l’application vers votre cluster Kubernetes.

> [!IMPORTANT]
> Vous ne pouvez rediriger que les services qui n’ont qu’un seul pod.

Après avoir sélectionné votre service, vous êtes invité à entrer le port TCP de votre application locale. Pour cet exemple, entrez *3000*.

![Se connecter au port choisi](../media/local-process-kubernetes-vs-code/choose-port.png)

Choisissez la tâche de lancement *Lancer via NPM*.

![Connexion - Choix de la tâche de lancement](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Vous êtes invité à autoriser *KubernetesDNSManager* à s’exécuter avec élévation de privilèges et à modifier votre fichier hosts.

Quand la barre d’état devient orange et que l’extension Dev Spaces indique que vous êtes connecté, cela signifie que votre ordinateur de développement est connecté.

![Ordinateur de développement connecté](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> Pour les lancements suivants, vous ne serez pas invité à entrer le nom du service, le port ou la tâche de lancement. Ces valeurs sont enregistrées dans *.vscode/tasks.json*.

Une fois votre ordinateur de développement connecté, le trafic commence à être redirigé vers votre ordinateur de développement pour le service que vous remplacez.

## <a name="set-a-break-point"></a>Définir un point d’arrêt

Ouvrez [server.js][server-js-breakpoint], puis cliquez quelque part sur la ligne 233 pour y placer votre curseur. Définissez un point d’arrêt en appuyant sur *F9* ou en cliquant sur *Exécuter*, puis sur *Basculer le point d’arrêt*.

Accédez à l’exemple d’application en ouvrant l’URL publique. Sélectionnez *Aurelia Briggs (client)* en tant qu’utilisateur, puis sélectionnez un vélo à louer. Notez que l’image du vélo ne se charge pas. Revenez à Visual Studio Code et notez que la ligne 233 est mise en surbrillance. Le point d’arrêt que vous avez défini a suspendu le service à la ligne 233. Pour reprendre le service, appuyez sur *F5* ou cliquez sur *Exécuter*, puis sur *Continuer*. Revenez à votre navigateur et vérifiez que vous voyez une image d’espace réservé pour le vélo.

Supprimez le point d’arrêt en plaçant votre curseur sur la ligne 233 dans `server.js` et en appuyant sur *F9*.

### <a name="update-your-application"></a>Mettre à jour votre application

Modifiez `server.js` pour supprimer les lignes 234 et 235 :

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

Enregistrez vos modifications. Cliquez sur *Exécutez*, puis sur *Redémarrer le débogage*. Quand vous êtes reconnecté, actualisez votre navigateur et vérifiez que vous ne voyez plus une image d’espace réservé pour le vélo.

Cliquez sur *Exécuter*, puis sur *Arrêter le débogage* pour arrêter le débogueur.

> [!NOTE]
> Par défaut, l’arrêt de la tâche de débogage déconnecte également votre ordinateur de développement de votre cluster Kubernetes. Vous pouvez modifier ce comportement en recherchant *Local Process With Kubernetes: Déconnecter après le débogage* dans les paramètres Visual Studio Code et en désactivant la case à cocher en regard de *Déconnecter automatiquement à la fin du débogage*. Après la mise à jour de ce paramètre, votre ordinateur de développement reste connecté quand vous arrêtez et démarrez le débogage. Pour déconnecter votre ordinateur de développement de votre cluster, cliquez sur l’extension Azure Dev Spaces dans la barre d’état, puis choisissez *Déconnecter la session active*.
>
> Si Visual Studio Code met soudainement fin à la connexion au cluster ou s’arrête, il se peut que le service que vous redirigez ne soit pas restauré à son état d’origine (avant votre connexion à Local Process with Kubernetes). Pour résoudre ce problème, consultez le [Guide de résolution des problèmes][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Utilisation de la journalisation et des diagnostics

La sortie de la journalisation est écrite dans la fenêtre *Dev Spaces* après la connexion de votre ordinateur de développement à votre cluster Kubernetes.

![Output](../media/local-process-kubernetes-vs-code/output.png)

Cliquez sur la barre d’état Azure Dev Spaces, puis choisissez *Afficher les informations de diagnostic de connexion*. Cette commande imprime les variables d’environnement actuelles et les entrées DNS dans la sortie de journalisation.

![Sortie avec des diagnostics](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

De plus, les journaux de diagnostic sont disponibles dans le répertoire `Azure Dev Spaces` se trouvant dans le [répertoire *TEMP* de votre ordinateur de développement][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Supprimer l’exemple d’application de votre cluster

Utilisez le script fourni pour supprimer l’exemple d’application de votre cluster.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser les actions Azure Dev Spaces et GitHub pour tester les modifications d’une demande de tirage (pull request) directement dans AKS avant de fusionner la requête de tirage dans la branche principale de votre référentiel.

> [!div class="nextstepaction"]
> [Actions GitHub & Azure Kubernetes Service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download