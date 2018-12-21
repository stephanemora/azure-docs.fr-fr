---
title: Créer un espace de développement Kubernetes dans le cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: stepro
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: quickstart
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: mmontwil
ms.openlocfilehash: d7f9609fa9eef7d478604db4543862af57826218
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437161"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-java-and-vs-code"></a>Démarrage rapide : Créer un espace de développement Kubernetes avec Azure Dev Spaces (Java et VS Code)

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs en utilisant VS Code et la ligne de commande.
- Déboguer le code dans votre espace de développement à partir de VS Code.

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md) ou postez un commentaire sur cette page. Vous pouvez aussi essayer ce [didacticiel](get-started-netcore.md) plus détaillé.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.43 ou ultérieure.
- Un cluster Kubernetes, qui exécute Kubernetes 1.10.3 ou une version ultérieure dans les régions EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral ou CanadaEast, avec le **routage d’application HTTP** activé.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Configurer Azure Dev Spaces

1. Configurez Dev Spaces sur votre cluster AKS : `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Téléchargez l’[extension Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) pour VS Code. Cliquez sur Installer une fois sur la page Place de marché de l’extension, puis à nouveau dans VS Code.
1. Téléchargez l’extension [débogueur Java pour Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) pour VS Code. Cliquez sur Installer une fois sur la page Place de marché de l’extension, puis à nouveau dans VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

1. Téléchargez l’exemple de code à partir de GitHub : [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Remplacez le répertoire par le dossier webfrontend : `cd dev-spaces/samples/java/getting-started/webfrontend`
1. Générez des ressources de graphiques Docker et Helm : `azds prep --public`
1. Générez et exécutez votre code dans AKS. Dans la fenêtre de terminal à partir du **dossier webfrontend**, exécutez la commande suivante : `azds up`
1. Analysez la sortie de la console pour y rechercher les informations concernant l’URL qui a été créée par la commande `up`. Ces informations se présentent sous la forme suivante :

   ```output
    (pending registration) Service 'webfrontend' port 'http' will be available at <url>
    Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   Ouvrez cette URL dans une fenêtre de navigateur. Vous devriez alors voir l’application web se charger.

   > [!Note]
   > À la première exécution, vous devrez peut-être patienter plusieurs minutes avant que le serveur DNS public ne soit prêt. Si l’URL publique n’est pas résolue, vous pouvez utiliser en guise d’alternative l’URL http://localhost: <portnumber> affichée dans la sortie de la console. Si vous utilisez l’URL localhost, le conteneur semble s’exécuter en local. En réalité, il s’exécute dans AKS. Pour des raisons pratiques et pour faciliter l’interaction avec le service à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure. Vous pouvez réessayer l’URL publique plus tard quand l’enregistrement DNS est prêt.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code

1. Dans la fenêtre de terminal, appuyez sur `Ctrl+C` (pour arrêter `azds up`).
1. Ouvrez le fichier de code nommé `src/main/java/com/ms/sample/webfrontend/Application.java`, puis modifiez le message d’accueil : `return "Hello from webfrontend in Azure!";`
1. Enregistrez le fichier .
1. Exécutez `azds up` dans la fenêtre de terminal.

Cette commande régénère l’image conteneur et redéploie le graphique Helm. Pour visualiser la prise en compte de vos modifications de code dans l’application en cours d’exécution, actualisez simplement le navigateur.

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
1. Sélectionnez **Launch Java Program (AZDS)** (Lancer le programme Java (AZDS)) comme configuration de débogage active.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’extension VS Code pour Azure Dev Spaces. Assurez-vous que l’espace de travail que vous avez ouvert dans VS Code correspond au dossier contenant azds.yaml.

### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’espace de développement, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est bien entendu joint au conteneur distant.

> [!Tip]
> La barre d’état VS Code affichera une URL interactive.

Définissez un point d’arrêt dans un fichier de code côté serveur, par exemple dans la fonction `greeting()` du fichier source `src/main/java/com/ms/sample/webfrontend/Application.java`. L’actualisation de la page du navigateur entraîne l’atteinte du point d’arrêt.

Vous disposez d’un accès complet aux informations de débogage, exactement comme si le code s’exécutait en local, notamment à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

### <a name="edit-code-and-refresh"></a>Modifier du code et actualiser la page
Lorsque le débogueur est actif, apportez une modification au code. Par exemple, modifier le message d’accueil dans `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Enregistrez le fichier, puis dans le **volet Actions de débogage**, cliquez sur le bouton **Actualiser**. 

![](media/get-started-java/debug-action-refresh.png)

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur. Votre message personnalisé doit apparaître dans l’interface utilisateur.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.**

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur des conteneurs multiples, et comment il vous est possible de simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](team-development-java.md)
