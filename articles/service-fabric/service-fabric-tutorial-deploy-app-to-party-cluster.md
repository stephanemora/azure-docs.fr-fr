---
title: Déployer une application Azure Service Fabric sur un cluster | Microsoft Docs
description: Apprenez à déployer une application sur un cluster à partir de Visual Studio.
services: service-fabric
documentationcenter: .net
-author: rwike77
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f75a05e965a025a3041036679ac06cfe4f1ec8d7
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Didacticiel : déployer une application sur un cluster Service Fabric dans Azure
Deuxième d’une série, ce didacticiel vous montre comment déployer une application Azure Service Fabric sur un nouveau cluster dans Azure directement depuis Visual Studio.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster à partir de Visual Studio
> * Déployer une application sur un cluster distant à l’aide de Visual Studio


Dans cette série de didacticiels, nous allons aborder les points suivants :
> [!div class="checklist"]
> * [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Déployer l’application sur un cluster distant
> * [Ajouter un point de terminaison HTTPS à un service frontal ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote
Si vous n’avez pas généré l’exemple d’application de vote lors de la [première partie de cette série de didacticiels](service-fabric-tutorial-create-dotnet-app.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric
À présent que l’application est prête, vous pouvez la déployer sur un cluster directement à partir de Visual Studio. Un [cluster Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés.

Pour un déploiement sur Visual Studio, vous disposez de deux options :
- Créer un cluster dans Azure à partir de Visual Studio. Cette option vous permet de créer un cluster sécurisé directement depuis Visual Studio avec les configurations que vous voulez. Ce type de cluster est idéal pour des scénarios de test, où vous pouvez créer le cluster puis publier directement au sein de Visual Studio.
- Publier sur un cluster existant dans votre abonnement.  Vous pouvez créer des clusters Service Fabric via le [portail Azure](https://portal.azure.com), à l’aide des scripts [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou [Azure CLI](./scripts/cli-create-cluster.md), ou à partir d’un [modèle Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Ce didacticiel vous permet de créer un cluster à partir de Visual Studio. Si vous avez déjà déployé un cluster, vous pouvez copier et coller votre point de terminaison de connexion ou le sélectionner depuis votre abonnement.
> [!NOTE]
> De nombreux services utilisent le proxy inverse pour communiquer entre eux. Le proxy inverse est activé par défaut pour les clusters créés depuis Visual Studio et les clusters tiers.  Si vous utilisez un cluster existant, vous devez [activer le proxy inverse dans le cluster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Rechercher le point de terminaison de service VotingWeb
Tout d’abord, recherchez le point de terminaison du service web frontal.  Le service web frontal est à l’écoute sur un port spécifique.  Lorsque l’application est déployée sur un cluster dans Azure, le cluster et l’application s’exécutent derrière un équilibreur de charge Azure.  Le port de l’application doit être ouvert dans l’équilibreur de charge Azure afin que le trafic entrant puisse accéder au service web.  Le port (par exemple, 8080) se trouve dans le fichier *VotingWeb/PackageRoot/ServiceManifest.xml* de l’élément **point de terminaison** :

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Dans l’étape suivante, spécifiez ce port dans l’onglet **Avancé** de la boîte de dialogue **Créer le cluster**.  Si vous déployez l’application sur un cluster existant, vous pouvez ouvrir ce port dans l’équilibreur de charge Azure en vous aidant d’un [script PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou en intervenant dans le [portail Azure](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Créer un cluster dans Azure via Visual Studio
Cliquez avec le bouton droit sur le projet de l’application dans l’Explorateur de solutions et choisissez **Publier**.

Connectez-vous avec votre compte Azure afin d’avoir accès à votre ou vos abonnements. Cette étape est facultative si vous utilisez un cluster tiers.

Sélectionnez le menu déroulant correspondant au **point de terminaison de connexion**, puis l’option **<Create New Cluster...>**.
    
![Boîte de dialogue Publier](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
Dans la boîte de dialogue **Créer le cluster**, modifiez les paramètres suivants :

1. Spécifiez le nom de votre cluster dans le champ **Nom du cluster**, ainsi que l’abonnement et l’emplacement à utiliser.
2. Facultatif : vous pouvez modifier le nombre de nœuds. Par défaut, vous disposez de trois nœuds, qui est le minimum requis pour tester des scénarios Service Fabric.
3. Sélectionnez l’onglet **Certificat**. Dans cet onglet, tapez un mot de passe à utiliser pour sécuriser le certificat de votre cluster. Ce certificat aide à sécuriser votre cluster. Vous pouvez aussi modifier le chemin vers l’emplacement où vous voulez enregistrer le certificat. Visual Studio peut aussi importer le certificat pour vous, car il s’agit d’une étape nécessaire à la publication de l’application dans le cluster.
4. Sélectionnez l’onglet **Détail de la machine virtuelle**. Spécifiez le mot de passe que vous voulez utiliser pour les machines virtuelles qui forment le cluster. Le nom d’utilisateur et le mot de passe peuvent être utilisés pour se connecter aux machines virtuelles à distance. Vous devez aussi sélectionner une taille de machine virtuelle et pouvez changer l’image de la machine virtuelle si nécessaire.
5. Dans l’onglet **Avancé**, vous pouvez modifier la liste des ports à ouvrir sur l’équilibreur de charge Azure créé en même temps que le cluster.  Ajoutez le point de terminaison de service VotingWeb que vous avez découvert à l’étape précédente. Vous pouvez également ajouter une clé Application Insights existante et y acheminer les fichiers du journal des applications.
6. Une fois la modification des paramètres terminée, cliquez sur le bouton **Créer**. La création prend quelques minutes, la fenêtre de sortie vous informe lorsque le cluster est complètement créé.

![Boîte de dialogue Créer un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Déployer l’exemple d’application
Une fois que le cluster que vous voulez utiliser est prêt, cliquez avec le bouton droit sur le projet de l’application et choisissez **Publier**.

Une fois la publication terminée, vous devez être en mesure d’envoyer une requête à l’application via un navigateur.

Ouvrez le navigateur de votre choix et saisissez l’adresse du cluster (le point de terminaison de connexion sans les informations de port ; par exemple, win1kw5649s.westus.cloudapp.azure.com).

Vous devez maintenant obtenir le même résultat que lors de l’exécution de l’application en local.

![Réponse de l’API à partir du cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cluster à partir de Visual Studio
> * Déployer une application sur un cluster distant à l’aide de Visual Studio

Passez au didacticiel suivant :
> [!div class="nextstepaction"]
> [Activer HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
