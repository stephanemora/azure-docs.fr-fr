---
title: Didacticiel relatif à l’intégration d’Azure Functions avec le service Azure SignalR| Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment utiliser Azure Functions avec le service Azure SignalR.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Didacticiel : Intégrer Azure Functions avec le service Azure SignalR

Ce didacticiel s’appuie sur l’application de salle de conversation créée dans les didacticiels précédents. Si vous n’avez pas effectué les exercices de [création de salle de conversation avec le service SignalR](signalr-quickstart-dotnet-core.md) et d’[authentification du service Azure SignalR](./signalr-authenticate-oauth.md), exécutez-les dans un premier temps. 

Bien souvent avec les applications en temps réel, les mises à jour de contenus proviennent d’un serveur et sont publiées sur des clients web. [Azure Functions](../azure-functions/functions-overview.md) est un excellent candidat pour la génération de ces mises à jour de contenus. Entre autres avantages, Azure Functions vous permet notamment d’exécuter votre code à la demande, sans vous soucier de l’architecture de l’application dans son ensemble ni de l’infrastructure qui l’exécute. Vous payez uniquement pour la période pendant laquelle votre code est exécuté.  

En règle générale, ce scénario présente un problème lors des tentatives d’utilisation de SignalR, car SignalR essaie de conserver une connexion entre le client et le serveur pour la transmission des mises à jour de contenu. Dans la mesure où le code s’exécute uniquement à la demande, une connexion ne peut pas être conservée. Cependant, le service Azure SignalR peut prendre en charge ce scénario, car il gère les connexions durant l’exécution.

Dans ce didacticiel, vous allez utiliser Azure Functions pour générer des messages à l’aide d’une fonction de minuteur au début de chaque minute. La fonctions publiera les messages à destination de l’ensemble des clients de la salle de conversation créés dans les didacticiels précédents. Pour plus d’informations sur les fonctions de minuteur, consultez la section [dédiée](../azure-functions/functions-create-scheduled-function.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce démarrage rapide. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

Le code de ce didacticiel est disponible au téléchargement dans le [référentiel AzureSignalR-samples GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Application de conversation avec les messages du serveur](./media/signalr-integrate-functions/signalr-functions-complete.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une fonction de minuteur avec Azure Functions à l’aide de l’interface Azure CLI
> * Configurez la fonction de minuteur pour le déploiement du référentiel Git local.
> * Connecter le minuteur à votre service SignalR afin de transmettre les mises à jour chaque minute

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous devez disposer des éléments suivants :

* [Git](https://git-scm.com/)
* [Kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell configuré](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Référentiel [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub téléchargé ou cloné.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez créer une application de fonction pour définir l’environnement d’exécution dédié à vos fonctions. Une application de fonction vous permet également de grouper plusieurs fonctions en tant qu’unité logique pour simplifier les activités de gestion, de déploiement et de partage des ressources. For more information, see [Créer votre première fonction à l’aide d’Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md).

Dans cette section, vous allez utiliser Azure Cloud Shell pour créer une application Azure Function configurée pour le déploiement à partir d’un référentiel Git local. 

Lorsque vous créez les ressources de l’application de fonction, placez-les dans le groupe de ressources utilisé dans les didacticiels précédents. Cette approche simplifie la gestion de l’ensemble des ressources de didacticiel.

Copiez le script ci-dessous dans votre éditeur de texte, puis remplacez les valeurs des paramètres de variables par les valeurs à associer à vos ressources. Copiez-collez le script mis à jour dans votre instance Azure Cloud Shell, puis appuyez sur **Entrée** pour créer un compte de stockage et une application de fonction.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Paramètre | Description |
| -------------------- | --------------- |
| ResourceGroupName | Ce nom de groupe de ressources a été conseillé dans les didacticiels précédents. Il est judicieux de grouper l’ensemble des ressources de didacticiels. Utilisez le groupe de ressources employé dans les didacticiels précédents. | 
| location | Mettez à jour cette variable vers l’emplacement utilisé pour créer le groupe de ressources dans les didacticiels précédents. | 
| functionappName | Mettez à jour cette variable avec un nom unique pour votre nouvelle application de fonction. Par exemple, signalrfunctionapp22665120. | 
| storageAccountName | Saisissez un nom à associer au nouveau compte de stockage destiné à héberger le code et les paramètres de l’application de fonction. | 



## <a name="configure-the-function-app"></a>Configurer l’application de fonction

Dans cette section, vous allez configurer l’application de fonction avec un paramètre d’application contenant la chaîne de connexion associée à votre ressource du service Azure SignalR. Votre code de fonction utilisera ce paramètre pour la connexion et la publication des messages sur la salle de conversation. Vous allez également configurer l’application de fonction pour le déploiement à partir d’un référentiel Git local.

Copiez le script ci-dessous et remplacez les valeurs des paramètres. Collez le script mis à jour dans votre instance Azure Cloud Shell, puis appuyez sur **Entrée**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Paramètre | Description |
| -------------------- | --------------- |
| ResourceGroupName | Ce nom de groupe de ressources a été conseillé dans les didacticiels précédents. Il est judicieux de grouper l’ensemble des ressources de didacticiels. Utilisez le groupe de ressources employé dans les didacticiels précédents. | 
| functionappName | Mettez à jour cette variable avec un nom unique pour votre nouvelle application de fonction. Par exemple, signalrfunctionapp22665120. | 
| connstring | Saisissez la chaîne de connexion pour votre ressource du service SignalR. Pour récupérer cette chaîne de connexion, accédez à la page des ressources du service SignalR du portail Azure, en cliquant sur **Clés**, sous **PARAMÈTRES**. | 



Notez l’URL du déploiement Git renvoyée par la dernière commande. Vous allez utiliser cette URL pour le déploiement du code de fonction.


## <a name="the-timer-function"></a>La fonction de minuteur

L’exemple de fonction de minuteur est situé dans le répertoire */samples/Timer* de votre téléchargement, ou clone du référentiel github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). Le code de la fonction de minuteur est hébergé dans *TimerFunction.cs*. Ce code utilise la chaîne de connexion stockée avec la clé de configuration par défaut (*Azure:SignalR:ConnectionString*) afin de créer un proxy vers le concentrateur. Étant donné que le code de fonction est exécuté du côté serveur, il n’est aucunement nécessaire d’exiger une authentification de ce dernier en tant que client régulier. Le code est autorisé à utiliser la chaîne de connexion. Avec ce proxy de concentrateur, le code de fonction peut appeler toutes les méthodes définies sur votre concentrateur. Le code appelle la méthode `BroadcastMessage` pour publier un message contenant l’horodatage d’activation du déclencheur.

Le déclencheur de ce code de fonction est *timerTrigger*, défini dans les liaisons de *TimerFunction/function.json*. Il inclut une [expression CRON](https://wikipedia.org/wiki/Cron#CRON_expression) utilisée pour définir le déclenchement du minuteur au début de chaque minute.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Développement de la fonction de minuteur

Utilisez [l’interface de ligne de commande (CLI).NET Core](https://docs.microsoft.com/dotnet/core/tools/) dans les étapes suivantes pour développer la fonction et la préparer pour le déploiement :

1. Accédez au sous-répertoire */samples/Timer* de votre téléchargement, ou clone du référentiel github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Restaurez les packages NuGet à l’aide de la commande suivante :

        dotnet restore

3. Générer l’application de fonction *Timer* à l’aide de la commande suivante :

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Créer et déployer le référentiel Git local

1. Dans un interpréteur de commande Git, accédez au sous-répertoire de build, */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Initialisez le répertoire en tant que nouveau référentiel Git à l’aide de la commande suivante :

        git init

3. Ajoutez une nouvelle validation pour l’ensemble des fichiers dans le répertoire de build.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Ajoutez un point de terminaison distant pour l’URL de déploiement Git consignée durant la configuration de votre application de fonction :

        git remote add Azure <enter your Git deployment URL>

5. Déployer l’application de fonction

        git push Azure master

   Une fois le code déployé, le minuteur amorce immédiatement son cycle de déclenchement d’exécution du code toutes les minutes.

## <a name="test-the-chat-app"></a>Tester l’application de conversation

Accédez à l’application de conversation, et la fonction de minuteur que vous venez de créer commencera à consigner l’heure au début de chaque minute.

![Application de conversation avec les messages du serveur](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez continuer à tester l’application, vous pouvez conserver les ressources créées.

Sinon, si vous en avez terminé avec l’exemple d’application, vous pouvez supprimer les ressources Azure afin d’éviter les frais supplémentaires. 

> [!IMPORTANT]
> La suppression d’un groupe de ressources est définitive ; le groupe de ressources et l’ensemble des ressources qu’il contient sont supprimés de manière permanente. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé les ressources pour l’hébergement de cet exemple dans un groupe de ressources existant contenant des ressources que vous souhaitez conserver, vous pouvez supprimer chaque ressource individuellement à partir de son panneau respectif, au lieu de supprimer l’intégralité du groupe de ressources.
> 
> 

Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

Dans la zone de texte **Filtrer par nom..**, saisissez le nom de votre groupe de ressources. Les instructions relatives à cet article utilisaient un groupe de ressources nommé *SignalRTestResources*. Sur votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

   
![Supprimer](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**.
   
Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à procéder à l’intégration avec Azure Functions pour transmettre les mises à jour aux clients à l’aide de déclencheurs Azure Functions. Pour en savoir plus sur l’utilisation d’Azure SignalR Server, accédez aux exemples Azure CLI pour le service SignalR.

> [!div class="nextstepaction"]
> [Exemples CLI Azure SignalR](./signalr-cli-samples.md)



