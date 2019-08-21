---
title: Déployer une application sur Kubernetes à l’aide d’Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Déployer un microservice sur AKS à l’aide d’Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67710672"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Démarrage rapide : Développer une application sur Kubernetes à l’aide d’Azure Dev Spaces
Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développez et exécutez du code dans des conteneurs à l’aide de la ligne de commande.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service

Vous devez créer un cluster AKS dans une [région prise en charge][supported-regions]. Les commandes ci-dessous créent un groupe de ressources nommé *MyResourceGroup* et un cluster AKS nommé *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
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

Dans cet article, vous utilisez l’[exemple d’application Azure Dev Spaces](https://github.com/Azure/dev-spaces) pour illustrer l’utilisation d’Azure Dev Spaces.

Clonez l’application à partir de GitHub et accédez au répertoire *dev-spaces/samples/nodejs/getting-started/webfrontend* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Préparer l’application

Pour exécuter votre application sur Azure Dev Spaces, vous avez besoin d’un fichier Dockerfile et d’un chart Helm. Pour certains langages, tels que [Java][java-quickstart], [.NET Core][netcore-quickstart], et [Node.js][nodejs-quickstart], les outils du client Azure Dev Spaces peuvent générer toutes les ressources dont vous avez besoin. Pour de nombreux autres langages, tels que Go, PHP et Python, les outils du client peuvent générer le chart Helm si vous fournissez un fichier Dockerfile valide.

Générez les ressources Docker et chart Helm pour l’exécution de l’application dans Kubernetes avec la commande `azds prep` :

```cmd
azds prep --public
```

Vous devez exécuter la commande `prep` à partir du répertoire *dev-spaces/samples/nodejs/getting-started/webfrontend* pour générer correctement les ressources Docker et chart Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

Générez et exécutez votre code dans AKS avec la commande `azds up` :

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Vous pouvez voir le service en cours d’exécution en ouvrant l’URL publique qui est affichée dans la sortie de la commande `azds up`. Dans cet exemple, l’URL publique est *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Si vous arrêtez la commande `azds up` avec *Ctrl+C*, le service continue de s’exécuter dans AKS et l’URL publique reste disponible.

## <a name="update-code"></a>Mettre à jour le code

Pour déployer une version mise à jour de votre service, vous pouvez mettre à jour n’importe quel fichier de votre projet, puis réexécuter la commande `azds up`. Par exemple :

1. Si `azds up` est toujours en cours d’exécution, appuyez sur *Ctrl+C*.
1. Mettez à jour la [ligne 13 dans `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) de la façon suivante :
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
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

## <a name="clean-up-your-azure-resources"></a>Nettoyer vos ressources Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations