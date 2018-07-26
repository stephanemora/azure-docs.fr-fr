---
title: Déployer une application à partir d’un registre privé sur Azure Service Fabric mesh | Microsoft Docs
description: Découvrez comment déployer une application qui utilise un registre de conteneurs privé sur Service Fabric mesh à l’aide d’Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089484"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Déployer une application Service Fabric mesh à partir d’un registre d’images conteneur privé

Cet article montre comment déployer une application Service Fabric mesh qui utilise un registre d’image conteneur privé.

## <a name="prerequisites"></a>Prérequis

### <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh 
Pour accomplir cette tâche, vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

### <a name="install-docker"></a>Installation de Docker

Installez Docker pour prendre en charge les applications Service Fabric en conteneur utilisées par Service Fabric mesh.

### <a name="windows-10"></a>Windows 10

Téléchargez et installez la dernière version de [Docker Community Edition pour Windows][download-docker]. 

Pendant l’installation, sélectionnez **Utiliser des conteneurs Windows au lieu des conteneurs Linux** lorsque vous y êtes invité. Vous devrez vous déconnecter, puis vous reconnecter. Après vous être reconnecté, si vous n’aviez pas encore activé Hyper-V, vous pouvez être invité à le faire. Activez Hyper-V, puis redémarrez votre ordinateur.

Après redémarrage de votre ordinateur, Docker vous invitera à activer la fonctionnalité **Conteneurs**, à l’activer et à redémarrez votre ordinateur.

### <a name="windows-server-2016"></a>Windows Server 2016

Utilisez les commandes PowerShell suivantes pour installer Docker. Pour plus d’informations, voir [Docker Enterprise Edition pour Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Redémarrez votre ordinateur.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure et définissez l’abonnement actif :

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Créer un registre de conteneurs et lui envoyer une image

Créez un Azure Container Registry en suivant les instructions de la section [Créer un registre Docker privé dans Azure avec Azure CLI](../container-registry/container-registry-get-started-azure-cli.md). Suivez la procédure décrite jusqu’à l’étape [Se connecter à ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr). 

### <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si vous n’avez pas encore toutes les images du conteneur local, exécutez la commande suivante pour tirer (pull) une image existante du hub Docker.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Avant de pousser (push) une image vers le registre, vous devez la marquer avec le nom complet de votre serveur de connexion ACR. Exécutez la commande suivante pour obtenir le nom complet du serveur de connexion de l’instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marquez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Répertorier les images conteneur

L’exemple suivant répertorie les référentiels d’un registre :

```azurecli
az acr repository list --name <acrName> --output table
```

Sortie :

```bash
Result
----------------
azure-mesh-helloworld
```

L’exemple suivant répertorie les étiquettes sur le référentiel **azure-mesh-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Sortie :

```bash
Result
--------
1.1-alpine
```
La sortie précédente confirme la présence de `azure-mesh-helloworld:1.1-alpine` dans le registre de conteneurs privé.

## <a name="retrieve-credentials-for-the-registry"></a>Récupérer les informations d’identification pour le registre

Pour déployer une instance de conteneur à partir du registre créé, des informations d’identification doivent être fournies pendant le déploiement. Activez l’utilisateur administrateur sur votre registre avec la commande suivante :

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Obtenez le nom de serveur, le nom d’utilisateur et le mot de passe du registre en utilisant les commandes suivantes :

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Les valeurs fournies par les commandes précédentes sont référencées comme `<acrLoginServer>`, `<acrUserName>`, et `<acrPassword>` dans la commande suivante.


## <a name="deploy-the-template"></a>Déployer le modèle

Créez l’application et les ressources associées à l’aide de la commande suivante, et fournissez les informations d’identification de l’étape précédente.

Le paramètre `registry-password` dans le modèle est un `securestring`. Il n’apparaîtra pas dans l’état du déploiement et les commandes `az mesh service show`. Assurez-vous qu’il est correctement spécifié dans la commande suivante.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

Dans quelques minutes, votre commande devrait renvoyer :

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Ouvrir l’application
À l’issue du déploiement de l’application, obtenez l’adresse IP publique du point de terminaison de service, et ouvrez-la dans un navigateur. Celui-ci affiche une page web contenant le logo Azure Service Fabric mesh.

La commande de déploiement renvoie l’adresse IP publique du point de terminaison de service. Si vous le souhaitez, vous pouvez également interroger la ressource réseau pour trouver l’adresse IP publique du point de terminaison de service. 
 
Le nom de la ressource réseau pour cette application est `helloWorldPrivateRegistryNetwork`. Extrayez les informations la concernant à l’aide de la commande suivante. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Supprimer les ressources

Pour économiser les ressources limitées affectées au programme en préversion, supprimez les ressources fréquemment. Pour supprimer les ressources liées à cet exemple, supprimez le groupe de ressources dans lequel elles ont été déployées.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes
- Regardez l’exemple d’application Hello World sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/