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
ms.openlocfilehash: 600625f143041eaf983b7ec7e945c5a968b522f5
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022386"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Démarrage rapide : créer un espace de développement Kubernetes avec Azure Dev Spaces (.NET Core et Visual Studio)

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs à l’aide de Visual Studio.
- Déboguer le code exécuté dans votre cluster.

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md) ou postez un commentaire sur cette page. Vous pouvez aussi essayer ce [didacticiel](get-started-netcore-visualstudio.md) plus détaillé.

## <a name="prerequisites"></a>Prérequis

- Un cluster Kubernetes, qui exécute Kubernetes 1.9.6 ou ultérieure dans les régions EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral ou CanadaEast, avec le routage d’application HTTP activé.

  ![Veillez à activer le routage d’application HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio 2017 avec installation de la charge de travail Développement web. Si vous ne l’avez pas installé, vous pouvez le [télécharger ici](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Configurer Azure Dev Spaces

Installez [Visual Studio Tools pour Kubernetes](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Se connecter à un cluster

Ensuite, vous allez créer et configurer un projet pour les espaces de développement Azure.

### <a name="create-an-aspnet-web-app"></a>Créez une application web ASP.NET

Dans Visual Studio 2017, créez un projet. Actuellement, le projet doit correspondre à **Application web ASP.NET Core**. Nommez le projet **webfrontend**.

Sélectionnez le modèle **Application Web (Model-View-Controller)** et assurez-vous que vous ciblez **.NET Core** et **ASP.NET Core 2.0**.

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Activer Dev Spaces pour un cluster AKS

Ouvrez le projet que vous venez de créer, puis sélectionnez **Azure Dev Spaces** dans la liste déroulante des paramètres de lancement, comme indiqué ci-dessous.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Dans la boîte de dialogue qui s’affiche ensuite, assurez-vous que vous êtes connecté avec le compte approprié, puis sélectionnez un cluster existant.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Pour l’instant, laissez la liste déroulante **Espace** définie par défaut sur `default`. Cochez la case **Publicly Accessible** (Accessible publiquement) pour que l’application web soit accessible par le biais d’un point de terminaison public.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Cliquez sur **OK** pour sélectionner ou créer le cluster.

Si vous choisissez un cluster qui n’a pas été configuré pour fonctionner avec Azure Dev Spaces, vous recevez un message vous demandant si vous souhaitez le configurer.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Choisissez **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Examiner les fichiers ajoutés au projet
En attendant la création de l’espace de développement, examinez les fichiers qui ont été ajoutés à votre projet lorsque vous avez choisi d’utiliser Azure Dev Spaces.

- Un dossier nommé `charts` a été ajouté et que dans ce dossier, un [graphique Helm](https://docs.helm.sh) a été structuré pour votre application. Ces fichiers sont utilisés pour déployer votre application dans l’environnement de développement.
- `Dockerfile` contient les informations nécessaires pour empaqueter votre application au format Docker standard.
- `azds.yaml` contient la configuration au moment du développement nécessaire à l’espace de développement.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes
Une fois l’espace de développement créé, vous pouvez déboguer votre application. Définissez un point d’arrêt dans le code, par exemple à la ligne 20 du fichier `HomeController.cs` où la variable `Message` est définie. Cliquez sur **F5** pour démarrer le débogage. 

Visual Studio communique avec l’espace de développement pour générer et déployer l’application, puis ouvre un navigateur avec l’application web en cours d’exécution. Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans l’espace de développement dans Azure. La présence de l’adresse localhost est due à Azure Dev Spaces qui crée un tunnel SSH temporaire jusqu’au conteneur en cours d’exécution dans AKS.

Cliquez sur le lien **À propos de** en haut de la page pour déclencher le point d’arrêt. Vous avez un accès complet aux informations pour le débogage, comme si le code s’exécutait en local, y compris à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.


## <a name="iteratively-develop-code"></a>Développer du code de façon itérative

Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
1. Recherchez le fichier `./Views/Home/Index.cshtml` et procédez à une modification du code HTML. Par exemple, remplacez la ligne 70 indiquant `<h2>Application uses</h2>` par une chaîne telle que : `<h2>Hello k8s in Azure!</h2>`
1. Enregistrez le fichier .
1. Accédez à votre navigateur et actualisez la page. La page web doit afficher le code HTML mis à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucune recompilation dans une application web .NET Core. Une session F5 active synchronise donc automatiquement tous les fichiers de contenu modifiés dans le conteneur en cours d’exécution dans AKS, ce qui vous permet de visualiser immédiatement les modifications du contenu.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code nécessite un peu plus de travail, car une application .NET Core doit régénérer et produire les fichiers binaires d’application mis à jour.

1. Arrêtez le débogueur dans Visual Studio.
1. Ouvrez le fichier de code nommé `Controllers/HomeController.cs`, puis modifiez le message qui doit s’afficher sur la page À propos de : `ViewData["Message"] = "Your application description page.";`
1. Enregistrez le fichier .
1. Appuyez sur **F5** pour redémarrer le débogage. 

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, puis accédez à la page À propos de. Votre message personnalisé doit apparaître dans l’interface utilisateur.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisation de plusieurs conteneurs et développement en équipe](team-development-netcore-visualstudio.md)
