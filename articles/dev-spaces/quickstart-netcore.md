---
title: Créer un espace de développement Kubernetes dans le cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: quickstart
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 3bb190570cbdf795668dd6e9f9d36630b6f3201b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949864"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-vs-code"></a>Guide de démarrage rapide : créer un espace de développement Kubernetes avec Azure Dev Spaces (.NET Core et Visual Studio Code)

Dans ce guide, vous allez apprendre à :

- Configurez Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs en utilisant VS Code et la ligne de commande.
- Déboguer le code dans votre espace de développement à partir de VS Code

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md) ou postez un commentaire sur cette page. Vous pouvez aussi essayer ce [didacticiel](get-started-netcore.md) plus détaillé.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- Un [cluster Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks), qui exécute Kubernetes 1.10.3 dans les régions EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral ou CanadaEast, avec le **routage d’application HTTP** activé.

  ![Veillez à activer le routage d’application HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="set-up-azure-dev-spaces"></a>Configurer Azure Dev Spaces

1. Installez [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (version 2.0.38 ou ultérieure).
1. Configurez Dev Spaces sur votre cluster AKS : `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Téléchargez l’[extension Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) pour VS Code. Cliquez sur Installer une fois sur la page Place de marché de l’extension, puis à nouveau dans VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

1. Téléchargez l’exemple de code à partir de GitHub : [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Remplacez le répertoire par le dossier webfrontend : `cd dev-spaces/samples/dotnetcore/getting-started/webfrontend`
1. Générez des ressources de graphiques Docker et Helm : `azds prep --public`
1. Générez et exécutez votre code dans AKS. Dans la fenêtre de terminal à partir du **dossier webfrontend**, exécutez la commande suivante : `azds up`
1. Analysez la sortie de la console pour y rechercher les informations concernant l’URL qui a été créée par la commande `up`. Ces informations se présentent sous la forme suivante : 

   `Service 'webfrontend' port 'http' is available at <url>` 

   Ouvrez cette URL dans une fenêtre de navigateur. Vous devriez alors voir l’application web se charger. 
   
   > [!Note]
   > À la première exécution, vous devrez peut-être patienter plusieurs minutes avant que le serveur DNS public ne soit prêt. Si l’URL publique n’est pas résolue, vous pouvez utiliser en guise d’alternative l’URL http://localhost: <portnumber> affichée dans la sortie de la console. Si vous utilisez l’URL localhost, le conteneur semble s’exécuter en local. En réalité, il s’exécute dans AKS. Pour des raisons pratiques et pour faciliter l’interaction avec le service à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure. Vous pouvez réessayer l’URL publique plus tard quand l’enregistrement DNS est prêt.

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu

1. Recherchez un fichier, par exemple `./Views/Home/Index.cshtml`, et procédez à une modification du code HTML. Par exemple, remplacez la ligne 70 indiquant `<h2>Application uses</h2>` par une chaîne telle que : `<h2>Hello k8s in Azure!</h2>`
1. Enregistrez le fichier . Quelques instants plus tard, la fenêtre de terminal affiche un message indiquant qu’un fichier du conteneur en cours d’exécution a été mis à jour.
1. Accédez à votre navigateur et actualisez la page. La page web doit afficher le code HTML mis à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucune recompilation dans une application web .NET Core. Par conséquent, une commande `azds up` active synchronise automatiquement tous les fichiers de contenu modifiés dans le conteneur en cours d’exécution dans Azure, ce qui vous permet de visualiser immédiatement les modifications du contenu.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code nécessite un peu plus de travail, car une application .NET Core doit régénérer et produire les fichiers binaires d’application mis à jour.

1. Dans la fenêtre de terminal, appuyez sur `Ctrl+C` (pour arrêter `azds up`).
1. Ouvrez le fichier de code nommé `Controllers/HomeController.cs`, puis modifiez le message qui doit s’afficher sur la page À propos de : `ViewData["Message"] = "Your application description page.";`
1. Enregistrez le fichier .
1. Exécutez `azds up` dans la fenêtre de terminal. 

Cette commande régénère l’image conteneur et redéploie le graphique Helm. Pour visualiser la prise en compte de vos modifications de code dans l’application en cours d’exécution, accédez au menu À propos de figurant dans l’application web.

Toutefois, vous allez découvrir à la section suivante une *méthode encore plus rapide* pour développer du code. 

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes

Dans cette section, vous utiliserez VS Code pour déboguer directement votre conteneur exécuté dans Azure. Vous apprendrez aussi à obtenir une boucle modification-exécution-test plus rapide.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initialiser des ressources de débogage avec l’extension VS Code
Vous devez avant tout configurer votre projet de code pour permettre à VS Code de communiquer avec l’espace de développement dans Azure. L’extension VS Code pour Azure Dev Spaces fournit une commande d’assistance pour définir la configuration de débogage. 

Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Celle-ci ajoute la configuration de débogage pour Azure Dev Spaces sous le dossier `.vscode`.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Sélectionner la configuration de débogage AZDS
1. Pour ouvrir l’affichage de débogage, cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code.
1. Sélectionnez **Lancer .NET Core (AZDS)** comme configuration de débogage active.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’extension VS Code pour Azure Dev Spaces. Assurez-vous que l’espace de travail que vous avez ouvert dans VS Code correspond au dossier contenant azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’espace de développement, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est bien entendu joint au conteneur distant.

> [!Tip]
> La barre d’état VS Code affichera une URL interactive.

Définissez un point d’arrêt dans un fichier de code côté serveur, par exemple dans la fonction `Index()` du fichier source `Controllers/HomeController.cs`. L’actualisation de la page du navigateur entraîne l’atteinte du point d’arrêt.

Vous disposez d’un accès complet aux informations de débogage, exactement comme si le code s’exécutait en local, notamment à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

### <a name="edit-code-and-refresh"></a>Modifier du code et actualiser la page
Lorsque le débogueur est actif, apportez une modification au code. Par exemple, modifiez le message de la page À propos de dans `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Enregistrez le fichier, puis dans le **volet Actions de débogage**, cliquez sur le bouton **Actualiser**. 

![](media/get-started-netcore/debug-action-refresh.png)

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, puis accédez à la page À propos de. Votre message personnalisé doit apparaître dans l’interface utilisateur.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.**

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur des conteneurs multiples, et comment il vous est possible de simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces. 

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](team-development-netcore.md)