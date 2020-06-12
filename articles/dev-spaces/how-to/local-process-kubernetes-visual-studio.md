---
title: Utiliser Processus local avec Kubernetes avec Visual Studio (préversion)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Découvrez comment utiliser Processus local avec Kubernetes avec Visual Studio pour connecter votre ordinateur de développement à un cluster Kubernetes avec Azure Dev Spaces
keywords: Processus local avec Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316550"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Utiliser Processus local avec Kubernetes avec Visual Studio (préversion)

Processus local avec Kubernetes vous permet d’exécuter et de déboguer votre code sur votre ordinateur de développement tout en restant connecté à votre cluster Kubernetes avec le reste de votre application ou de vos services. Par exemple, si vous avez une architecture de microservices importante avec de nombreux services et bases de données interdépendants, la réplication de ces dépendances sur votre ordinateur de développement peut s’avérer difficile. En outre, la création et le déploiement de code sur votre cluster Kubernetes pour chaque changement de code pendant le développement en boucle interne peuvent être lents, fastidieux et difficiles à utiliser avec un débogueur.

Processus local avec Kubernetes vous évite d’avoir à créer et à déployer votre code sur votre cluster en créant à la place une connexion directement entre votre ordinateur de développement et votre cluster. La connexion de votre ordinateur de développement à votre cluster pendant le débogage vous permet de tester et de développer rapidement votre service dans le contexte de l’application complète sans créer une configuration Docker ou Kubernetes.

Processus local avec Kubernetes redirige le trafic entre votre cluster Kubernetes connecté et votre ordinateur de développement. Cette redirection du trafic permet au code sur votre ordinateur de développement et les services en cours d’exécution dans votre cluster Kubernetes de communiquer comme s’ils se trouvaient dans le même cluster Kubernetes. Processus local avec Kubernetes offre également un moyen de répliquer des variables d’environnement et des volumes montés disponibles pour les pods dans votre cluster Kubernetes sur votre ordinateur de développement. L’accès aux variables d’environnement et aux volumes montés sur votre ordinateur de développement vous permet de travailler rapidement sur votre code sans avoir à répliquer ces dépendances manuellement.

Dans ce guide, vous allez apprendre à utiliser Processus local avec Kubernetes pour rediriger le trafic entre votre cluster Kubernetes et le code qui s’exécute sur votre ordinateur de développement. Ce guide fournit également un script pour le déploiement d’un exemple d’application volumineux avec plusieurs microservices sur un cluster Kubernetes.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][preview-terms]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

Ce guide utilise l’exemple d’application [Azure Dev Spaces Bike Sharing][bike-sharing-github] pour illustrer la connexion de votre ordinateur de développement à un cluster Kubernetes. Si vous disposez déjà de votre propre application s’exécutant sur un cluster Kubernetes, vous pouvez toujours suivre les étapes ci-dessous et utiliser les noms de vos propres services.

### <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI][azure-cli].
* [Visual Studio 2019][visual-studio] version 16.7 Preview 2 ou version ultérieure s’exécutant sur Windows 10 avec les charges de travail *ASP.NET et développement web* et *développement Azure* installées.
* [Interface CLI Azure Dev Spaces installée][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Activer la fonctionnalité en préversion Processus local avec Kubernetes dans Visual Studio

Pour activer Processus local avec Kubernetes dans Visual Studio, cliquez sur *Outils* > *Options* > *Environnement* > *Fonctionnalités en préversion*. Sélectionnez *Activer le débogage local des services Kubernetes*.

En outre, pour les applications console .NET, installez le package NuGet *Microsoft.VisualStudio.Azure.Kubernetes.Tools.Targets*.

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

Installez l’exemple d’application sur votre cluster à l’aide du script fourni. Vous pouvez exécuter ce script sur votre ordinateur de développement ou à l’aide [d’Azure Cloud Shell][azure-cloud-shell].

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

Ouvrez *dev-spaces/samples/BikeSharingApp/ReservationEngine/app.csproj* à partir de [l’exemple d’application Azure Dev Spaces Bike Sharing][bike-sharing-github] dans Visual Studio.

Dans votre projet, sélectionnez *Processus local avec Kubernetes* dans la liste déroulante des paramètres de lancement, comme indiqué ci-dessous.

![Choisir Processus local avec Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Cliquez sur le bouton Démarrer en regard de *Processus local avec Kubernetes*. Dans la boîte de dialogue *Processus local avec Kubernetes* :

* Sélectionnez votre abonnement.
* Sélectionnez *MyAKS* pour votre cluster.
* Sélectionnez *dev* pour votre espace de noms.
* Sélectionnez *reservationengine* pour le service à rediriger.
* Sélectionnez *app* pour le profil de lancement.
* Sélectionnez `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` pour l’URL de lancement de votre navigateur.

![Choisir Processus local avec un cluster Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Vous ne pouvez rediriger que les services qui n’ont qu’un seul pod.

Cliquez sur *Enregistrer et commencer le débogage*.

Tout le trafic dans le cluster Kubernetes est redirigé pour le service *reservationengine* vers la version de votre application s’exécutant sur votre ordinateur de développement. Processus local avec Kubernetes achemine également tout le trafic sortant de l’application vers votre cluster Kubernetes.

> [!NOTE]
> Vous êtes invité à autoriser *KubernetesDNSManager* à s’exécuter avec élévation de privilèges et à modifier votre fichier hosts.

Votre ordinateur de développement est connecté lorsque la barre d’état indique que vous êtes connecté au service *reservationengine*.

![Ordinateur de développement connecté](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> Lors des lancements suivants, la boîte de dialogue *Processus local avec Kubernetes* ne s’affiche pas. Vous mettez à jour ces paramètres dans le volet *Débogage* dans les propriétés du projet.

Une fois votre ordinateur de développement connecté, le trafic commence à se rediriger vers votre ordinateur de développement pour le service que vous remplacez.

## <a name="set-a-break-point"></a>Définir un point d’arrêt

Ouvrez [BikesHelper.cs][bikeshelper-cs-breakpoint], puis cliquez quelque part sur la ligne 26 pour y placer votre curseur. Définissez un point d’arrêt en appuyant sur *F9* ou en cliquant sur *Déboguer*, puis sur *Activer/désactiver le point d’arrêt*.

Accédez à l’exemple d’application en ouvrant l’URL publique. Sélectionnez *Aurelia Briggs (client)* en tant qu’utilisateur, puis sélectionnez un vélo à louer. Cliquez sur *Rent Bike*. Revenez à Visual Studio et notez que la ligne 26 est mise en surbrillance. Le point d’arrêt que vous avez défini a suspendu le service à la ligne 26. Pour reprendre le service, appuyez sur *F5* ou cliquez sur *Déboguer*, puis sur *Continuer*. Revenez à votre navigateur et vérifiez que la page indique que vous avez loué le vélo.

Supprimez le point d’arrêt en plaçant votre curseur sur la ligne 26 dans `BikesHelper.cs` et en appuyant sur *F9*.

> [!NOTE]
> Par défaut, l’arrêt de la tâche de débogage déconnecte également votre ordinateur de développement de votre cluster Kubernetes. Vous pouvez modifier ce comportement en définissant *Déconnecter après le débogage* sur *false* dans la section *Outils de débogage Kubernetes* des options de débogage. Après la mise à jour de ce paramètre, votre ordinateur de développement reste connecté lorsque vous arrêtez et démarrez le débogage. Pour déconnecter votre ordinateur de développement de votre cluster, cliquez sur le bouton *Déconnecter* de la barre d’outils.
>
> Si Visual Studio met soudainement fin à la connexion au cluster ou s’arrête, le service que vous redirigez peut ne pas être restauré à son état d’origine avant la connexion avec Processus local avec Kubernetes. Pour résoudre ce problème, consultez le [Guide de résolution des problèmes][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Utilisation de la journalisation et des diagnostics

Les journaux de diagnostic sont disponibles dans le répertoire `Azure Dev Spaces` se trouvant dans le [répertoire *TEMP* de votre ordinateur de développement][azds-tmp-dir].

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
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/