---
title: Développer une application avec un graphique Helm existant sur Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Ce guide de démarrage rapide montre comment utiliser Azure Dev Spaces et la ligne de commande pour développer une application avec un graphique Helm existant sur Azure Kubernetes Service.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c37ea0b04e99cf1bba555e098bdf33b8a8558cfa
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996676"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Démarrage rapide : Développer une application avec un graphique Helm existant sur Kubernetes - Azure Dev Spaces
Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Exécuter une application avec un graphique Helm existant dans AKS à l’aide d’Azure Dev Spaces sur la ligne de commande.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service

Vous devez créer un cluster AKS dans une [région prise en charge][supported-regions]. Les commandes ci-dessous créent un groupe de ressources nommé *MyResourceGroup* et un cluster AKS nommé *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Activer Azure Dev Spaces sur votre cluster AKS

Utilisez la commande `use-dev-spaces` pour activer Dev Spaces sur votre cluster AKS et suivez les invites. La commande ci-dessous active Dev Spaces sur le cluster *MyAKS* dans le groupe *MyResourceGroup* et crée un espace de développement appelé *dev*.

> [!NOTE]
> La commande `use-dev-spaces` installe également l’interface CLI Azure Dev Spaces si celle-ci n’est pas déjà installée. Vous ne pouvez pas installer l’interface CLI d’Azure Dev Spaces dans Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obtenir l’exemple de code d’application

Dans cet article, vous utilisez l’[exemple d’application Azure Dev Spaces](https://github.com/Azure/dev-spaces) pour illustrer l’utilisation d’Azure Dev Spaces.

Clonez l’application à partir de GitHub et accédez au répertoire *dev-spaces/samples/python/getting-started/webfrontend* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Préparer l’application

Pour exécuter votre application sur Azure Dev Spaces, vous avez besoin d’un fichier Dockerfile et d’un chart Helm. Pour certains langages, tels que [Java][java-quickstart], [.NET Core][netcore-quickstart], et [Node.js][nodejs-quickstart], les outils du client Azure Dev Spaces peuvent générer toutes les ressources dont vous avez besoin. Pour de nombreux autres langages, tels que Go, PHP et Python, les outils du client peuvent générer le chart Helm si vous fournissez un fichier Dockerfile valide. Dans le cas présent, l’exemple d’application a un fichier Dockerfile et un graphique Helm existants.

Générez la configuration pour l’exécution de l’application avec Azure Dev Spaces avec le fichier Dockerfile et le graphique Helm existants à l’aide de la commande `azds prep` :

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Vous devez exécuter la commande `prep` à partir du répertoire *dev-spaces/samples/python/getting-started/webfrontend* et spécifier l’emplacement du graphique Helm à l’aide de `--chart`.

> [!NOTE]
> L’avertissement suivant peut s’afficher : *WARNING: Impossible de générer Dockerfile en raison d’un langage non pris en charge.* lors de l’exécution de `azds prep`. La commande `azds prep` tente de générer [un fichier Dockerfile et un graphique Helm](how-dev-spaces-works-prep.md#prepare-your-code) pour votre projet, mais ne remplace pas les fichiers Dockerfile ou graphiques Helm existants.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes

Générez et exécutez votre code dans AKS avec la commande `azds up` :

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Vous pouvez voir le service en cours d’exécution en ouvrant l’URL publique qui est affichée dans la sortie de la commande `azds up`. Dans cet exemple, l’URL publique est `http://dev.service.1234567890abcdef1234.eus.azds.io/`.

> [!NOTE]
> Quand vous accédez à votre service pendant l’exécution de `azds up`, les traces des requêtes HTTP sont également affichées dans la sortie de la commande `azds up`. Ces traces peuvent vous aider à résoudre les problèmes liés à votre service et à le déboguer. Vous pouvez désactiver ces traces à l’aide de `--disable-http-traces` lors de l’exécution de `azds up`.

Si vous arrêtez la commande `azds up` avec *Ctrl+C*, le service continue de s’exécuter dans AKS et l’URL publique reste disponible.

## <a name="update-code"></a>Mettre à jour le code

Pour déployer une version mise à jour de votre service, vous pouvez mettre à jour n’importe quel fichier de votre projet, puis réexécuter la commande `azds up`. Par exemple :

1. Si `azds up` est toujours en cours d’exécution, appuyez sur *Ctrl+C*.
1. Changez la [ligne 13 de `webfrontend.py`](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) en :
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Enregistrez vos modifications.
1. Réexécutez la commande `azds up` :

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Accédez à votre service en cours d’exécution et observez vos modifications.
1. Appuyez sur *Ctrl+C* pour arrêter la commande `azds up`.

## <a name="clean-up-your-azure-resources"></a>Nettoyer vos ressources Azure

```azurecli
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
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service