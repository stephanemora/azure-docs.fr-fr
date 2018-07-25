---
title: Déployer une application Service Fabric sur un cluster dans Azure | Microsoft Docs
description: Apprenez à déployer une application sur un cluster à partir de Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 58b7dc532511ae25c7db2bf021a42fecc3dd9bb5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056679"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutoriel : Déployer une application Service Fabric sur un cluster dans Azure

Deuxième d’une série, ce didacticiel vous montre comment déployer une application Azure Service Fabric sur un nouveau cluster dans Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster Party.
> * Déployer une application sur un cluster distant à l’aide de Visual Studio.

Dans cette série de didacticiels, nous allons aborder les points suivants :
> [!div class="checklist"]
> * [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Déployer l’application sur un cluster distant
> * [Ajouter un point de terminaison HTTPS à un service frontal ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
* [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote

Si vous n’avez pas généré l’exemple d’application de vote lors de la [première partie de cette série de tutoriels](service-fabric-tutorial-create-dotnet-app.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="publish-to-a-service-fabric-cluster"></a>Publier sur un cluster Service Fabric

À présent que l’application est prête, vous pouvez la déployer sur un cluster directement à partir de Visual Studio. Un [cluster Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés.

Pour ce didacticiel, vous avez deux options pour le déploiement de l’application de vote vers un cluster Service Fabric à l’aide de Visual Studio :

* Publiez sur un cluster d’essai (partie).
* Publier sur un cluster existant dans votre abonnement.  Vous pouvez créer des clusters Service Fabric via le [portail Azure](https://portal.azure.com), à l’aide des scripts [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou [Azure CLI](./scripts/cli-create-cluster.md), ou à partir d’un [modèle Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> De nombreux services utilisent le proxy inverse pour communiquer entre eux. Le proxy inverse est activé par défaut pour les clusters créés depuis Visual Studio et les clusters tiers.  Si vous utilisez un cluster existant, vous devez [activer le proxy inverse dans le cluster](service-fabric-reverseproxy.md#setup-and-configuration).


### <a name="find-the-votingweb-service-endpoint-for-your-azure-subscription"></a>Rechercher le point de terminaison de service VotingWeb pour votre abonnement Azure

Si vous vous apprêtez à publier l’application de vote sur votre propre abonnement Azure, recherchez le point de terminaison du service web frontal. Si vous utilisez un cluster tiers, le port 8080 utilisé par l’exemple de vote est automatiquement ouvert et vous n’avez pas besoin de le configurer dans l’équilibreur de charge du cluster tiers.

Le service web frontal est à l’écoute sur un port spécifique.  Lorsque l’application est déployée sur un cluster dans Azure, le cluster et l’application s’exécutent derrière un équilibreur de charge Azure.  Le port de l’application doit être ouvert en utilisant une règle dans l’équilibreur de charge Azure pour ce cluster afin que le trafic entrant puisse accéder au service web.  Le port (par exemple, 8080) se trouve dans le fichier *VotingWeb/PackageRoot/ServiceManifest.xml* de l’élément **point de terminaison** :

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Pour votre abonnement Azure, ouvrez ce port à l’aide d’une règle d’équilibrage de charge dans Azure par le biais d’un [script PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou via l’équilibreur de charge pour ce cluster dans le [portail Azure](https://portal.azure.com).

### <a name="join-a-party-cluster"></a>Rejoindre un cluster Party

> [!NOTE]
> Accédez directement au déploiement de l’application à l’aide de Visual Studio dans la section suivante si vous vous apprêtez à publier l’application sur votre propre cluster dans un abonnement Azure.

Les clusters tiers sont des clusters Service Fabric gratuits et limités dans le temps, hébergés sur Azure et gérés par l’équipe Service Fabric, où chacun peut déployer des applications et découvrir la plateforme. Le cluster utilise un seul certificat auto-signé pour la sécurité de nœud à nœud et de client à nœud.

Connectez-vous et [rejoignez un cluster Windows](http://aka.ms/tryservicefabric). Téléchargez le certificat PFX sur votre ordinateur en cliquant sur le lien **PFX**. Cliquez sur le lien **Comment se connecter à un cluster tiers sécurisé ?** et copiez le mot de passe du certificat. Le certificat, le mot de passe du certificat et la valeur **Point de terminaison de connexion** sont utilisés dans les étapes suivantes.

![Certificat PFX et point de terminaison de connexion](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> Le nombre de clusters tiers disponibles par heure est limité. Si vous obtenez une erreur lors de votre inscription à un cluster tiers, attendez un peu et réessayez, ou suivez ces étapes dans le didacticiel [Déployer une application .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) pour créer un cluster Service Fabric dans votre abonnement Azure et déployez-y l’application. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
>

Sur votre ordinateur Windows, installez le certificat PFX dans le magasin de certificats *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

N’oubliez pas l’empreinte numérique, elle sera utilisée dans une étape suivante.

> [!Note]
> Par défaut, le service frontend web est configuré pour écouter le trafic entrant sur le port 8080. Le port 8080 est ouvert dans le cluster Party.  Si vous devez changer le port de l’application, remplacez-le par l’un des ports ouverts dans le cluster Party.
>

### <a name="publish-the-application-using-visual-studio"></a>Publier l’application à l’aide de Visual Studio

À présent que l’application est prête, vous pouvez la déployer sur un cluster directement à partir de Visual Studio.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Voting**, puis choisissez **Publier**. La boîte de dialogue Publier s’affiche.

2. Copiez le **Point de terminaison de connexion** depuis la page du cluster tiers ou de votre abonnement Azure dans le champ **Point de terminaison de connexion**. Par exemple : `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Cliquez sur **Paramètres de connexion avancés** et veillez à ce que les valeurs *FindValue* et *ServerCertThumbprint* correspondent à l’empreinte numérique du certificat installé lors d’une étape précédente pour un cluster tiers ou du certificat correspondant à votre abonnement Azure.

    ![Boîte de dialogue Publier](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Chaque application du cluster doit avoir un nom unique.  Toutefois, les clusters Party constituent un environnement public partagé et un conflit avec une application existante peut se présenter.  S’il existe un conflit de noms, renommez le projet Visual Studio et recommencez le déploiement.

3. Cliquez sur **Publier**.

4. Ouvrez un navigateur et tapez l’adresse du cluster suivie de « :8080 » (ou un autre port configuré) pour accéder à votre application de vote dans le cluster, par exemple, `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Vous devez à présent voir l’application en cours d’exécution dans le cluster dans Azure. Dans la page web de vote, essayez d’ajouter ou de supprimer des options de vote et de voter pour une ou plusieurs de ces options.

    ![Service frontal de l’application](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cluster tiers.
> * Déployer une application sur un cluster distant à l’aide de Visual Studio.

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Activer HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
