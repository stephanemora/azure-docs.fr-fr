---
title: 'Déboguer et itérer dans Kubernetes : Visual Studio et .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Ce guide de démarrage rapide vous montre comment utiliser Azure Dev Spaces et Visual Studio pour déboguer et itérer rapidement une application .NET Core sur Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239716"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Démarrage rapide : Déboguer et itérer dans Kubernetes : Visual Studio et .NET Core - Azure Dev Spaces

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs à l’aide de Visual Studio.
- Déboguez du code qui s’exécute dans votre cluster avec Visual Studio.

Azure Dev Spaces vous permet également de déboguer et d’itérer à l’aide de :
- [Java et Visual Studio Code](quickstart-java.md)
- [Node.js et Visual Studio Code](quickstart-nodejs.md)
- [.NET Core et Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
- Visual Studio 2019 sur Windows avec la charge de travail de développement Azure installée. Vous pouvez également utiliser Visual Studio 2017 sur Windows lorsque la charge de travail Développement web et [Visual Studio Tools pour Kubernetes](https://aka.ms/get-vsk8stools) sont installés. Si vous n’avez pas installé Visual Studio, vous pouvez le télécharger [ici](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service

Vous devez créer un cluster AKS dans une [région prise en charge][supported-regions]. Pour créer un cluster :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Sélectionnez *+ Créer une ressource > Service Kubernetes*. 
1. Entrez les informations pour _Abonnement_, _Groupe de ressources_, _Nom du cluster Kubernetes_, _Région_, _Version de Kubernetes_ et _Préfixe du nom DNS_.

    ![Créer AKS dans le portail Azure](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Cliquez sur *Vérifier + créer*.
1. Cliquez sur *Créer*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Activer Azure Dev Spaces sur votre cluster AKS

Accédez à votre cluster AKS dans le portail Azure et cliquez sur *Dev Spaces*. Remplacez *use-dev-spaces* par *Yes*, puis cliquez sur *Enregistrer*.

![Activer Dev Spaces via le portail Azure](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Créer une application web ASP.NET

1. Ouvrez Visual Studio.
1. Créez un projet.
1. Choisissez *Application web ASP.NET Core*, puis cliquez sur *Suivant*.
1. Nommez votre projet *webfrontend*, puis cliquez sur *Créer*.
1. Quand vous y êtes invité, choisissez *Application web (Model-View-Controller)* pour le modèle.
1. Dans la partie supérieure, sélectionnez *.NET Core* et *ASP.NET Core 2.1*.
1. Cliquez sur *Créer*.

## <a name="connect-your-project-to-your-dev-space"></a>Connecter votre projet à votre espace de développement

Dans votre projet, sélectionnez **Azure Dev Spaces** dans la liste déroulante des paramètres de lancement, comme indiqué ci-dessous.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Dans la boîte de dialogue Azure Dev Spaces, sélectionnez votre *abonnement* et votre *cluster Azure Kubernetes*. Laissez *Espace* défini sur *par défaut* et cochez la case *Accessible publiquement*. Cliquez sur *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ce processus déploie votre service sur l’espace de développement *par défaut* avec une URL accessible publiquement. Si vous choisissez un cluster qui n’a pas été configuré pour fonctionner avec Azure Dev Spaces, vous recevez un message vous demandant si vous souhaitez le configurer. Cliquez sur *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

L’URL publique pour le service s’exécutant dans l’espace de développement *par défaut* est affichée dans la fenêtre *Sortie* :

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Dans l’exemple ci-dessus, l’URL publique est http://default.webfrontend.1234567890abcdef1234.eus.azds.io/. 

Sélectionnez **Déboguer**, puis **Démarrer le débogage**. Après quelques secondes, votre service démarre, et Visual Studio ouvre un navigateur avec l’URL publique du service. Si aucun navigateur ne s’ouvre automatiquement, accédez manuellement à l’URL publique de votre service à partir d’un navigateur, puis interagissez avec le service qui s’exécute dans votre espace de développement.

Ce processus peut avoir désactivé l’accès public à votre service. Pour activer l’accès public, vous pouvez mettre à jour la valeur entrée [ dans *values.yaml*][ingress-update].

## <a name="update-code"></a>Mettre à jour le code

Si Visual Studio est toujours connecté à votre espace de développement, cliquez sur le bouton Arrêter. Changez la ligne 20 dans `Controllers/HomeController.cs` en :
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Enregistrez les changements que vous avez apportés, sélectionnez **Déboguer**, puis **Démarrer le débogage**. Après quelques secondes, votre service démarre, et Visual Studio ouvre un navigateur avec l’URL publique du service. Si aucun navigateur ne s’ouvre automatiquement, accédez manuellement à l’URL publique de votre service à partir d’un navigateur, puis cliquez sur *À propos de*. Notez que votre message mis à jour apparaît.

Au lieu de regénérer et de redéployer une nouvelle image conteneur chaque fois que des modifications de code sont effectuées, Azure Dev Spaces recompile le code de façon incrémentielle dans le conteneur existant afin d’accélérer la boucle de modification/débogage.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Définition et utilisation de points d’arrêt pour le débogage

Si Visual Studio est toujours connecté à votre espace de développement, cliquez sur le bouton Arrêter. Ouvrez `Controllers/HomeController.cs`, puis cliquez quelque part sur la ligne 20 pour y placer votre curseur. Pour définir un point d’arrêt, appuyez sur *F9* ou cliquez sur *Déboguer*, puis sur *Activer/désactiver le point d’arrêt*. Pour démarrer votre service en mode de débogage dans votre espace de développement, appuyez sur *F5* ou cliquez sur *Déboguer*, puis sur *Démarrer le débogage*.

Ouvrez votre service dans un navigateur ; notez qu’aucun message n’est affiché. Revenez à Visual Studio et notez que la ligne 20 est mise en surbrillance. Le point d’arrêt que vous avez défini a suspendu le service à la ligne 20. Pour reprendre le service, appuyez sur *F5* ou cliquez sur *Déboguer*, puis sur *Continuer*. Revenez à votre navigateur et notez que le message est maintenant affiché.

Quand vous exécutez votre service dans Kubernetes avec un débogueur attaché, vous avez un accès total aux informations de débogage, comme la pile des appels, les variables locales et les informations sur les exceptions.

Supprimez le point d’arrêt en plaçant votre curseur sur la ligne 20 dans `Controllers/HomeController.cs` et en appuyant sur *F9*.

## <a name="clean-up-your-azure-resources"></a>Nettoyer vos ressources Azure

Accédez à votre groupe de ressources sur le portail Azure, puis cliquez sur *Supprimer le groupe de ressources* . Vous pouvez aussi utiliser la commande [az aks delete](/cli/azure/aks#az-aks-delete) :

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
