---
title: Démarrage rapide - Créer un registre Docker privé dans Azure avec PowerShell
description: Apprenez à créer rapidement un registre de conteneurs Docker privé dans Azure avec PowerShell.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: e6c3330519692eb829803af2582b711be2fb3efe
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092876"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Démarrage rapide : Créer un registre Azure Container Registry à l’aide de PowerShell

Azure Container Registry est un service de registre de conteneurs Docker géré et privé que vous utilisez pour créer, stocker et dépanner des images conteneurs Docker. Dans ce démarrage rapide, vous apprenez à créer un registre de conteneurs Azure à l’aide de PowerShell. Après avoir créé le registre, vous lui envoyez une image conteneur, puis vous déployez le conteneur à partir du registre dans Azure Container Instances (ACI).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide requiert le module Azure PowerShell version 5.7.0 ou supérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour déterminer la version installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Docker doit également être installé en local. Docker fournit des packages pour [macOS][docker-mac], [Windows][docker-windows] et [Linux][docker-linux].

Étant donné qu’Azure Cloud Shell n’inclut pas tous les composants Docker requis (par exemple, le démon `dockerd`), vous ne pouvez pas l’utiliser pour ce démarrage rapide.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Connect-AzureRmAccount][Connect-AzureRmAccount] et suivez les instructions indiquées à l’écran.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Une fois votre authentification auprès d’Azure effectuée, créez un groupe de ressources avec [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un groupe de ressources est un conteneur logique dans lequel vous déployez et gérez vos ressources Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Créer un registre de conteneurs

Ensuite, créez un registre de conteneurs dans votre nouveau groupe de ressources avec la commande [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. L’exemple suivant crée un registre appelé « myContainerRegistry007 ». Remplacez *myContainerRegistry007* dans la commande suivante, puis exécutez-la pour créer le registre :

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Se connecter au registre

Avant d’extraire et d’envoyer des images conteneurs, vous devez vous connecter au registre. Utilisez tout d’abord la commande [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] pour obtenir les informations d’identification de l’administrateur du registre :

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Ensuite, exécutez [connexion docker][docker-login] pour vous connecter :

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Une connexion réussie renvoie `Login Succeeded` :

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Envoyer l’image au registre

Maintenant que vous êtes connecté au registre, vous pouvez lui envoyer les images conteneurs. Pour obtenir une image à envoyer au registre, extrayez l’image [aci-helloworld][aci-helloworld-image] du Hub Docker. L’image [aci-helloworld][aci-helloworld-github] est une petite application Node.js qui sert de page HTML statique et qui affiche le logo Azure Container Instances.

```powershell
docker pull microsoft/aci-helloworld
```

Une fois l’image extraite, le résultat ressemble à la sortie suivante :

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Avant d’envoyer une image vers votre registre de conteneurs Azure, vous devez la marquer avec le nom de domaine complet du registre. Les noms de domaine complets des registres de conteneurs Azure ont pour format *\<registry-name\>.azurecr.io*.

Remplissez une variable avec la balise d’image complète. Insérez le serveur de connexion, le nom du référentiel (« aci-helloworld ») et la version de l’image (« v1 ») :

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Maintenant, étiquetez l’image avec la [balise docker][docker-tag] :

```powershell
docker tag microsoft/aci-helloworld $image
```

Enfin, effectuez un [envoi (push) Docker][docker-push] vers votre registre :

```powershell
docker push $image
```

À mesure que le client Docker envoie (push) votre image, la sortie ressemble à ce qui suit :

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Félicitations ! Vous venez d’envoyer votre première image conteneur au registre.

## <a name="deploy-image-to-aci"></a>Déployer l’image dans ACI

L’image figurant maintenant dans votre registre, déployez un conteneur directement sur Azure Container Instances pour voir son exécution dans Azure.

Tout d’abord, donnez aux informations d’identification du registre la valeur *PSCredential*. La commande `New-AzureRmContainerGroup`, qui vous permet de créer l’instance de conteneur, nécessite des informations dans ce format.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

En outre, l’étiquette de nom DNS de votre conteneur doit être unique dans la région Azure dans laquelle vous l’avez créée. Exécutez la commande suivante pour remplir une variable avec un nom généré :

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Enfin, exécutez [New-AzureRmContainerGroup][New-AzureRmContainerGroup] pour déployer un conteneur à partir de l’image de votre registre avec 1 cœur UC et 1 Go de mémoire :

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Vous devriez obtenir une réponse initiale d’Azure avec des détails concernant le conteneur. Dans un premier temps, l’état est « en attente ».

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Pour surveiller l’état et déterminer sa mise en exécution, exécutez plusieurs fois la commande [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Le conteneur doit démarrer en moins d’une minute.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Ici, vous pouvez voir que la valeur ProvisioningState du conteneur est dans un premier temps *Creating*, puis *Succeeded* une fois qu’il est en cours d’exécution :

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Visualiser une application en cours d'exécution

Une fois le déploiement vers ACI effectué et votre conteneur en cours d’exécution, accédez à son nom de domaine complet (FQDN) dans votre navigateur pour voir l’exécution de l’application dans Azure.

Obtenez le nom de domaine complet de votre conteneur avec [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] :


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

La sortie de la commande correspond au nom de domaine complet de votre instance de conteneur :

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Avec le nom de domaine complet, accédez à ce dernier dans votre navigateur :

![Application Hello world dans le navigateur][qs-psh-01-running-app]

Félicitations ! Vous avez créé un conteneur exécutant une application dans Azure, lequel est déployé directement à partir d’une image conteneur dans le registre de conteneurs Azure privé.

## <a name="clean-up-resources"></a>Supprimer les ressources

Une fois que vous avez terminé d’utiliser les ressources créées dans ce démarrage rapide, utilisez la commande [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] pour supprimer le groupe de ressources, le registre de conteneurs et l’instance de conteneur :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un registre de conteneur Azure Container Registry avec Azure CLI, et lancé une instance dans Azure Container Instances. Poursuivez avec le didacticiel sur Azure Container Instances pour approfondir vos connaissances sur ACI.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
