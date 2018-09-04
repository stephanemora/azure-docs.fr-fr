---
title: 'Démarrage rapide : Déployer Hello World vers Azure Service Fabric Mesh | Microsoft Docs'
description: Ce démarrage rapide montre comment déployer une application Service Fabric Mesh vers Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO.
author: rwike77
ms.author: ryanwi
ms.date: 08/24/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f5b834f92b2a126f68780a7647fda4d8b35dfe43
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886442"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Démarrage rapide : Déployer Hello World vers Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) facilite la création et la gestion des applications de microservices dans Azure, sans avoir à approvisionner des machines virtuelles. Dans ce démarrage rapide, vous allez créer une application Hello World dans Azure et l’exposer à Internet. Cette opération s’effectue en une seule commande. Vous voyez la vue indiquée ci-dessous dans le navigateur en quelques minutes seulement :

![Application Hello world dans le navigateur][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Si vous ne disposez pas d’un compte Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/) avant de commencer.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh 
Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale de l’interface Azure CLI pour procéder à ce démarrage rapide. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous à Azure et définissez votre abonnement.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources dans lequel déployer l’application. Vous pouvez utiliser un groupe de ressources existant et ignorer cette étape. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Déployer l’application
Créez votre application dans le groupe de ressources à l’aide de la commande `az mesh deployment create`.  Si vous utilisez une console Bash, exécutez la commande suivante :

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}" 
```

Si vous utilisez une console PowerShell, exécutez la commande suivante :

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{'location': {'value': 'eastus'}}"
```

La commande précédente déploie une application Linux à l’aide du [modèle mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json). Si vous souhaitez déployer une application Windows, utilisez le [modèle mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.windows.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

En quelques minutes, la commande retourne :

`helloWorldApp has been deployed successfully on helloWorldNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Ouvrir l’application
À l’issue du déploiement de l’application, copiez l’adresse IP publique du point de terminaison de service à partir de la sortie de l’interface CLI. Ouvrez l’adresse IP dans un navigateur web. Une page web contenant le logo Azure Service Fabric Mesh s’affiche.

## <a name="check-the-application-details"></a>Vérifier les détails de l’application
Vous pouvez vérifier l’état de l’application à l’aide de la commande `az mesh app show`. Cette commande fournit des informations utiles dont vous pouvez effectuer le suivi.

Le nom de l’application utilisée pour ce démarrage rapide est `helloWorldApp`. Pour collecter les détails sur l’application, exécutez la commande suivante :

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Voir les journaux d’application

Examinez les journaux de l’application déployée en utilisant la commande `az mesh code-package-log get` :

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous êtes prêt à supprimer l’application, exécutez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources ainsi que les ressources réseau et l’application qu’il contient.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création et le déploiement d’applications Service Fabric mesh, passez au didacticiel.
> [!div class="nextstepaction"]
> [Créer et déployer une application web multiservice](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
