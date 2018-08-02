---
title: Tutoriel - Déployer une application Service Fabric Mesh sur Service Fabric Mesh | Microsoft Docs
description: Découvrez comment publier une application Azure Service Fabric Mesh comprenant un site web ASP.NET Core qui communique avec un service web principal.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/26/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 350749161260768071afbb47b854cb2e9184bd9d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284725"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Tutoriel : Déployer une application web Service Fabric Mesh

Ce tutoriel est la troisième partie d’une série et vous montre comment publier une application web Azure Service Fabric Mesh directement depuis Visual Studio.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Publiez l’application dans Azure.
> * Vérifier l’état du déploiement de l’application
> * Voir toutes les applications actuellement déployées dans votre abonnement
> * Voir les journaux d’application
> * Nettoyez les ressources utilisées par l’application.

Dans cette série de didacticiels, nous allons aborder les points suivants :
> [!div class="checklist"]
> * [Créer une application web Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Déboguer l’application localement](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Publier l’application dans Azure

Vous allez apprendre à créer une application Service Fabric Mesh qui possède un service web ASP.NET frontal et un service principal,d’API web ASP.NET Core. Ensuite, vous allez déboguer l’application sur votre cluster de développement local et publier l’application dans Azure. Une fois terminé, vous disposez d’une application de tâches simple qui montre comment effectuer un appel de service à service dans une application web Service Fabric Mesh.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Assurez-vous que vous avez [configuré votre environnement de développement](service-fabric-mesh-howto-setup-developer-environment-sdk.md) qui inclut l’installation du runtime Service Fabric, du Kit de développement logiciel (SDK), de Docker et de Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Télécharger l’exemple d’application de tâches

Si vous n’avez pas généré l’exemple d’application de tâches dans la [deuxième partie de cette série de tutoriels](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

L’application se trouve dans le répertoire `src\todolistapp`.

## <a name="publish-to-azure"></a>Publication dans Azure

Pour publier votre projet Service Fabric Mesh dans Azure, cliquez avec le bouton droit sur **ServiceFabricMeshApp** dans Visual Studio, puis sélectionnez **Publier...**

Ensuite, une boîte de dialogue **Publier une application Service Fabric** s’affiche.

![Boîte de dialogue de publication d’une application Service Fabric MeshVisual studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Sélectionnez votre compte et votre abonnement Azure. Choisissez un **Emplacement**. Dans cet article, nous utilisons la région **USA Est**.

Sous **Groupe de ressources**, sélectionnez **\<Créer un nouveau groupe de ressources...>**. Une boîte de dialogue s’affiche, dans laquelle vous allez créer un nouveau groupe de ressources. Cet article utilise l’emplacement **USA Est** et nomme le groupe **sfmeshTutorial1RG** (si plusieurs personnes utilisent le même abonnement dans votre organisation, choisissez un nom de groupe unique).  Appuyez sur **Créer** pour créer le groupe de ressources et revenir à la boîte de dialogue Publier.

![Boîte de dialogue du nouveau groupe de ressources Service Fabric Mesh Visual studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Revenez à la boîte de dialogue **Publier une application Service Fabric** sous **Azure Container Registry**, sélectionnez **\<Créer un registre de conteneurs...>**. Dans la boîte de dialogue **Créer un registre de conteneurs**, utilisez un nom unique pour le **nom du registre de conteneurs**. Spécifiez un **emplacement** (ce tutoriel utilise l’emplacement **USA Est**). Sélectionnez le **groupe de ressources** que vous avez créé à l’étape précédente dans la liste déroulante, par exemple, **sfmeshTutorial1RG**. Définissez la **référence (SKU)** sur **De base**, puis appuyez sur **Créer** pour revenir à la boîte de dialogue de publication.

![Boîte de dialogue du nouveau groupe de ressources Service Fabric Mesh Visual studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Si vous recevez une erreur indiquant que le fournisseur de ressources n’a pas été inscrit pour votre abonnement, vous pouvez l’inscrire. Tout d’abord, vérifiez si le fournisseur de ressources est disponible pour votre abonnement :

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Si le fournisseur de registres de conteneurs (`Microsoft.ContainerRegistry`) est disponible, enregistrez-le à partir de Powershell :

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Dans la boîte de dialogue de publication, appuyez sur le bouton **Publier** pour déployer votre application Service Fabric dans Azure.

Lorsque vous publiez sur Azure pour la première fois, l’image docker est envoyée (push) à Azure Container Registry (ACR), ce qui prend un certain temps selon la taille de l’image. Les publications ultérieures du même projet seront plus rapides. Vous pouvez surveiller la progression du processus de déploiement en sélectionnant le volet **Service Fabric Tools** dans la fenêtre de **sortie** de Visual Studio. Une fois le déploiement terminé, la sortie **Service Fabric Tools** affiche l’adresse IP et le port de votre application sous la forme d’une URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Ouvrez un navigateur web et accédez à l’URL pour voir le site web en cours d’exécution dans Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh 
Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale de l’interface Azure CLI pour les étapes restantes. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.


## <a name="check-application-deployment-status"></a>Vérifier l’état du déploiement de l’application

À ce stade, votre application a été déployée. Vous pouvez vérifier son état à l’aide de la commande `app show`. 

Le nom de l’application pour l’application du tutoriel est `ServiceMeshApp`. Collecter les informations relatives à l’application à l’aide de la commande suivante :

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Voir toutes les applications actuellement déployées dans votre abonnement

Vous pouvez utiliser la commande « liste d’applications » pour obtenir une liste des applications que vous avez déployées dans votre abonnement.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Voir les journaux d’application

Examinez les journaux de l’application déployée :

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez toutes les ressources créées. Étant donné que vous avez créé un nouveau groupe de ressources pour héberger les ressources de service ACR et Service Fabric Mesh, vous pouvez supprimer sans risque ce groupe de ressources, ce qui supprimera toutes les ressources associées.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Vous pouvez également supprimer le groupe de ressources [à partir du portail Azure](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Étapes suivantes

Cette partie du didacticiel vous a permis de répondre aux questions suivantes :
> [!div class="checklist"]
> * Publiez l’application dans Azure.
> * Vérifier l’état du déploiement de l’application
> * Voir toutes les applications actuellement déployées dans votre abonnement
> * Voir les journaux d’application
> * Nettoyez les ressources utilisées par l’application.

Maintenant que vous avez terminé la publication d’une application Service Fabric Mesh dans Azure, essayez ce qui suit :

* Explorez l’[exemple d’application de vote](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) pour voir un autre exemple de communication de service à service.
* Lire [Ressources Service Fabric](service-fabric-mesh-service-fabric-resources.md)
* Lire de la documentation relative à [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest