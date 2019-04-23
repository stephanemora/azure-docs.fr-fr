---
title: Développer avec Java sur Kubernetes en utilisant Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Développement Kubernetes rapide avec des conteneurs, des microservices et Java sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Java, Helm, service Mesh, routage du service Mesh, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: c1c039ba8696baff11abed3930998983647f4356
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425744"
---
# <a name="quickstart-develop-with-java-on-kubernetes-using-azure-dev-spaces"></a>Démarrage rapide : Développer avec Java sur Kubernetes en utilisant Azure Dev Spaces

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développez du code de façon itérative dans des conteneurs en utilisant Visual Studio Code et la ligne de commande.
- Déboguez le code dans votre espace de développement à partir de Visual Studio Code.


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- [Visual Studio Code installé](https://code.visualstudio.com/download).
- Les extensions [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) et [Débogueur Java pour Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) pour Visual Studio Code installées.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Maven installé et configuré](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service

Vous devez créer un cluster AKS dans une [région prise en charge](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Les commandes ci-dessous créent un groupe de ressources nommé *MyResourceGroup* et un cluster AKS nommé *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-count 1 --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Activer Azure Dev Spaces sur votre cluster AKS

Utilisez la commande `use-dev-spaces` pour activer Dev Spaces sur votre cluster AKS et suivez les invites. La commande ci-dessous active Dev Spaces sur le cluster *MyAKS* dans le groupe *MyResourceGroup* et crée un espace de développement *par défaut*.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Obtenir l’exemple de code d’application

Dans cet article, vous utilisez l[exemple d’application Azure Dev Spaces](https://github.com/Azure/dev-spaces) pour illustrer l’utilisation d’Azure Dev Spaces.

Clonez l’application à partir de GitHub et accédez au répertoire *dev-spaces/samples/java/getting-started/webfrontend* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/java/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Préparer l’application

Générez les ressources Docker et chart Helm pour l’exécution de l’application dans Kubernetes avec la commande `azds prep` :

```cmd
azds prep --public
```

Vous devez exécuter la commande `prep` à partir du répertoire *dev-spaces/samples/java/getting-started/webfrontend* pour générer correctement les ressources Docker et chart Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

Générez et exécutez votre code dans AKS avec la commande `azds up` :

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...8s
Waiting for container image build...28s
Building container image...
Step 1/8 : FROM maven:3.5-jdk-8-slim
Step 2/8 : EXPOSE 8080
Step 3/8 : WORKDIR /usr/src/app
Step 4/8 : COPY pom.xml ./
Step 5/8 : RUN /usr/local/bin/mvn-entrypoint.sh     mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true --fail-never
Step 6/8 : COPY . .
Step 7/8 : RUN mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true
Step 8/8 : ENTRYPOINT ["java","-jar","target/webfrontend-0.1.0.jar"]
Built container image in 37s
Waiting for container...57s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Vous pouvez voir le service en cours d’exécution en ouvrant l’URL publique qui est affichée dans la sortie de la commande `azds up`. Dans cet exemple, l’URL publique est *http://webfrontend.1234567890abcdef1234.eus.azds.io/*.

Si vous arrêtez la commande `azds up` avec *Ctrl+C*, le service continue de s’exécuter dans AKS et l’URL publique reste disponible.

## <a name="update-code"></a>Mettre à jour le code

Pour déployer une version mise à jour de votre service, vous pouvez mettre à jour n’importe quel fichier de votre projet, puis réexécuter la commande `azds up`. Par exemple : 

1. Si `azds up` est toujours en cours d’exécution, appuyez sur *Ctrl+C*.
1. Changez la [ligne 16 dans `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L16) en :
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Enregistrez vos modifications.
1. Réexécutez la commande `azds up` :

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Accédez à votre service en cours d’exécution et observez vos modifications.
1. Appuyez sur *Ctrl+C* pour arrêter la commande `azds up`.

## <a name="enable-visual-studio-code-to-debug-in-kubernetes"></a>Activer Visual Studio Code pour déboguer dans Kubernetes

Ouvrez Visual Studio Code, cliquez sur *Fichier* puis sur *Ouvrir...*, accédez au répertoire *dev-spaces/samples/java/getting-started/webfrontend*, puis cliquez sur *Ouvrir*.

Vous avez maintenant le projet *webfrontend* ouvert dans Visual Studio Code, qui est le même service que celui que vous avez exécuté avec la commande `azds up`. Pour déboguer ce service dans AKS avec Visual Studio Code, au lieu d’utiliser `azds up` directement, vous devez préparer ce projet à utiliser Visual Studio Code pour communiquer avec votre espace de développement.

Pour ouvrir la palette de commandes dans Visual Studio Code, cliquez sur *Afficher*, puis sur *Palette de commandes*. Commencez à taper `Azure Dev Spaces`, puis cliquez sur `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Préparer les fichiers de configuration pour Azure Dev Spaces](./media/common/command-palette.png)

Quand Visual Studio Code vous demande aussi de configurer vos images de base et le port exposé, choisissez `Azul Zulu OpenJDK for Azure (Free LTS)` pour l’image de base et `8080` pour le port exposé.

![Sélectionner une image de base](media/get-started-java/select-base-image.png)

![Sélectionner le port exposé](media/get-started-java/select-exposed-port.png)

Cette commande prépare votre projet à s’exécuter dans Azure Dev Spaces à partir de Visual Studio Code. Il génère également un répertoire *.vscode* avec la configuration de débogage à la racine de votre projet.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Générer et exécuter du code dans Kubernetes à partir de Visual Studio

Cliquez sur l’icône *Déboguer* sur la gauche, puis sur *Lancer le programme Java (AZDS)* en haut.

![Lancer le programme Java](media/get-started-java/debug-configuration.png)

Cette commande génère et exécute votre service dans Azure Dev Spaces en mode débogage. La fenêtre *Terminal* dans le bas montre la sortie de la génération et les URL pour votre service exécutant Azure Dev Spaces. La *Console de débogage* montre la sortie du journal.

> [!Note]
> Si la *palette de commandes* ne montre aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’[extension Visual Studio Code pour Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Vérifiez également que vous avez ouvert le répertoire *dev-spaces/samples/java/getting-started/webfrontend* dans Visual Studio Code.

Cliquez sur *Déboguer*, puis sur *Arrêter le débogage* pour arrêter le débogueur.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Définition et utilisation de points d’arrêt pour le débogage

Démarrez votre service en mode de débogage avec *Lancer le programme Java (AZDS)*.

Revenez à la vue *Explorer* en cliquant sur *Afficher*, puis sur *Explorer*. Ouvrez `src/main/java/com/ms/sample/webfrontend/Application.java`, puis cliquez quelque part sur la ligne 16 pour y placer votre curseur. Pour définir un point d’arrêt, appuyez sur *F9* ou cliquez sur *Déboguer*, puis sur *Activer/désactiver le point d’arrêt*.

Ouvrez votre service dans un navigateur ; notez qu’aucun message n’est affiché. Revenez à Visual Studio Code et notez que la ligne 16 est mise en surbrillance. Le point d’arrêt que vous avez défini a suspendu le service à la ligne 16. Pour reprendre le service, appuyez sur *F5* ou cliquez sur *Déboguer*, puis sur *Continuer*. Revenez à votre navigateur et notez que le message est maintenant affiché.

Quand vous exécutez votre service dans Kubernetes avec un débogueur attaché, vous avez un accès complet aux informations de débogage, comme la pile des appels, les variables locales et les informations sur les exceptions.

Supprimez le point d’arrêt en plaçant votre curseur sur la ligne 16 dans `src/main/java/com/ms/sample/webfrontend/Application.java` et en appuyant sur *F9*.

## <a name="update-code-from-visual-studio-code"></a>Mettre à jour le code à partir de Visual Studio Code

Avec le service en cours d’exécution en mode de débogage, mettez à jour la ligne 16 dans `src/main/java/com/ms/sample/webfrontend/Application.java`. Par exemple : 
```java
return "Hello from webfrontend in Azure while debugging!";
```

Enregistrez le fichier . Cliquez sur *Déboguer*, puis sur *Redémarrer le débogage* ou, dans la *barre d’outils de débogage*, cliquez sur le bouton *Redémarrer le débogage*.

![Actualiser le débogage](media/get-started-java/debug-action-refresh.png)

Ouvrez votre service dans un navigateur et notez que votre message mis à jour est affiché.

Au lieu de regénérer et de redéployer une nouvelle image conteneur chaque fois que des modifications de code sont effectuées, Azure Dev Spaces recompile le code de façon incrémentielle dans le conteneur existant afin d’accélérer la boucle de modification/débogage.

## <a name="clean-up-your-azure-resources"></a>Nettoyer vos ressources Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](multi-service-java.md)
