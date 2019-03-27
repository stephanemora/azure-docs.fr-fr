---
title: Bien démarrer avec Azure Service Fabric à l’aide de VS Code | Microsoft Docs
description: Cet article est une vue d’ensemble de la création d’applications Service Fabric à l’aide de Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1053bde5eb76a9e2731d9663dec97d51c321e907
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500217"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric pour Visual Studio Code

[L’extension Service Fabric Reliable Services pour VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fournit les outils nécessaires pour créer, générer et déboguer des applications Service Fabric sur les systèmes d’exploitation Windows, Linux et macOS.

Cet article fournit une vue d’ensemble des exigences et de la configuration de l’extension, ainsi que de l’utilisation des différentes commandes qu’elle fournit. 

> [!IMPORTANT]
> Vous pouvez développer des applications Service Fabric en Java sur des machines Windows, mais ne pouvez les déployer que sur des clusters Azure Linux. Le débogage des applications Java n’est pas pris en charge sur Windows.

## <a name="prerequisites"></a>Conditions préalables

Les composants requis suivants doivent être installés sur tous les environnements.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.JS](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Générateurs Yeoman : installez les générateurs appropriés pour votre application

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Les composants requis suivants doivent être installés pour le développement Java :

* [SDK Java](https://aka.ms/azure-jdks) (version 1.8)
* [Gradle](https://gradle.org/install/)
* [Débogueur pour l’extension Java VS Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nécessaire pour déboguer les services Java. Le débogage des services Java n’est pris en charge que sur Linux. Vous pouvez effectuer l’installation en cliquant sur l’icône Extensions dans la **barre d’activités** de VS Code et en recherchant l’extension, ou bien à partir de la place de marché VS Code.

Les composants requis suivants doivent être installés pour le développement .NET Core/C# :

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (version 2.0.0 ou ultérieure)
* [Extension VS Code C# pour Visual Studio Code (technologie OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Nécessaire pour déboguer les services C#. Vous pouvez effectuer l’installation en cliquant sur l’icône Extensions dans la **barre d’activités** de VS Code et en recherchant l’extension, ou bien à partir de la place de marché VS Code.

## <a name="setup"></a>Paramétrage

1. Ouvrez Visual Studio Code.
2. Cliquez sur l’icône Extensions dans la **barre d’activités** située sur le côté gauche de VS Code. Recherchez « Service Fabric ». Cliquez sur **installer** pour l’extension Service Fabric Reliable Services.

## <a name="commands"></a>Commandes
L’extension Service Fabric Reliable Services pour VS Code fournit de nombreuses commandes qui permettent aux développeurs de créer et de déployer des projets Service Fabric. Vous pouvez appeler une commande à partir de la **Palette de commandes** en appuyant sur `(Ctrl + Shift + p)`, en tapant le nom de la commande dans la barre d’entrée et en sélectionnant la commande souhaitée à partir de la liste de choix. 

* Service Fabric : Créer une application 
* Service Fabric : Publication d’Application 
* Service Fabric : Déployer l’Application 
* Service Fabric : Supprimer l'application  
* Service Fabric : Création d’Application 
* Service Fabric : Nettoyer l’Application 

### <a name="service-fabric-create-application"></a>Service Fabric : Créer une application

Le **Service Fabric : Créer des Application** commande crée une nouvelle application de Service Fabric dans votre espace de travail actuel. Selon les générateurs yeoman installés sur votre machine de développement, vous pouvez créer plusieurs types d’application Service Fabric, y compris des projets Java, C#, conteneur et invité. 

1.  Sélectionnez le **Service Fabric : Ajouter un Service** commande
2.  Sélectionnez le type de votre nouvelle application Service Fabric. 
3.  Entrez le nom de l’application à créer.
3.  Sélectionnez le type de service que vous souhaitez ajouter à votre application Service Fabric. 
4.  Suivez les invites pour nommer le service. 
5.  La nouvelle application Service Fabric s’affiche dans l’espace de travail.
6.  Ouvrez le dossier de la nouvelle application afin qu’il devienne le dossier racine dans l’espace de travail. Vous pouvez continuer à exécuter des commandes à partir de cet emplacement.

### <a name="service-fabric-add-service"></a>Service Fabric : Ajouter un service
Le **Service Fabric : Ajouter un Service** commande ajoute un nouveau service à une application de Service Fabric existante. L’application à laquelle est ajouté le service doit être le répertoire racine de l’espace de travail. 

1.  Sélectionnez le **Service Fabric : Ajouter un Service** commande.
2.  Sélectionnez le type de votre application Service Fabric actuelle. 
3.  Sélectionnez le type de service que vous souhaitez ajouter à votre application Service Fabric. 
4.  Suivez les invites pour nommer le service. 
5.  Le nouveau service s’affiche dans le répertoire de votre projet. 

### <a name="service-fabric-publish-application"></a>Service Fabric : Publication d’Application
Le **Service Fabric : Publier l’Application** commande déploie votre application Service Fabric sur un cluster distant. Le cluster cible peut être un cluster sécurisé ou non sécurisé. Si les paramètres ne sont pas définis dans Cloud.json, l’application est déployée sur le cluster local.

1.  La première fois que l’application est générée, un fichier Cloud.json est généré dans le répertoire du projet.
2.  Entrez les valeurs du cluster auquel vous souhaitez vous connecter dans le fichier Cloud.json.
3.  Sélectionnez le **Service Fabric : Publier l’Application** commande.
4.  Affichez le cluster cible avec Service Fabric Explorer pour vérifier que l’application a été installée. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric : Déployer l’Application (Localhost)
Le **Service Fabric : Déployer l’Application** commande déploie votre application de Service Fabric sur votre cluster local. Vérifiez que votre cluster local est en cours d’exécution avant d’utiliser la commande. 

1. Sélectionnez le **Service Fabric : Déployer l’Application** commande
2. Afficher le cluster local avec Service Fabric Explorer (http :\//localhost:19080 / Explorer) pour confirmer que l’application a été installée. Cette opération pouvant prendre un certain temps, soyez patient.
3. Vous pouvez également utiliser **Service Fabric : Publier l’Application** commande sans aucun paramètre défini dans le fichier Cloud.json à déployer sur un cluster local.

> [!NOTE]
> Le déploiement d’applications Java sur le cluster local n’est pas pris en charge sur les machines Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric : Supprimer l'application
Le **Service Fabric : Supprimer Application** commande supprime une application Service Fabric à partir du cluster qu’il a été déployé précédemment à l’aide de l’extension VS Code. 

1.  Sélectionnez le **Service Fabric : Supprimer Application** commande.
2.  Affichez le cluster avec Service Fabric Explorer pour vérifier que l’application a été supprimée. Cette opération pouvant prendre un certain temps, soyez patient.

### <a name="service-fabric-build-application"></a>Service Fabric : Création d’Application
Le **Service Fabric : Supprimer Application** commande peut générer soit Java ou C# applications Service Fabric. 

1.  Vérifiez que vous vous trouvez dans le dossier racine de l’application avant d’exécuter cette commande. La commande identifie le type d’application (C# ou Java) et génère votre application en conséquence.
2.  Sélectionnez le **Service Fabric : Générer une application**.
3.  La sortie du processus de génération est écrite dans le terminal intégré.

### <a name="service-fabric-clean-application"></a>Service Fabric : Nettoyer l’Application
Le **Service Fabric : Nettoyer l’Application** commande supprime tous les fichiers jar et des bibliothèques natives qui ont été générées par la build. Valide pour les applications Java uniquement. 

1.  Vérifiez que vous vous trouvez dans le dossier racine de l’application avant d’exécuter cette commande. 
2.  Sélectionnez le **Service Fabric : Nettoyer l’Application** commande.
3.  La sortie du processus de nettoyage est écrite dans le terminal intégré.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [développer et déboguer des applications Service Fabric en C# avec VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Découvrez comment [développer et déboguer des applications Service Fabric en Java avec VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
