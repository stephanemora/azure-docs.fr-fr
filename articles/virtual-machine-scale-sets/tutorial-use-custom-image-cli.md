---
title: Tutoriel - Utiliser une image de machine virtuelle personnalisée dans un groupe identique avec Azure CLI
description: Découvrez comment utiliser Azure CLI pour créer une image de machine virtuelle personnalisée que vous pouvez utiliser pour déployer un groupe de machines virtuelles identiques
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 95e22b40b56d3ac3129573958c77b8643c0e72dc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276132"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutoriel : Créer et utiliser une image personnalisée pour des groupes de machines virtuelles identiques avec Azure CLI
Lorsque vous créez un groupe identique, vous spécifiez une image à utiliser lors du déploiement des instances de machine virtuelle. Pour réduire le nombre de tâches une fois que les instances de machine virtuelle sont déployées, vous pouvez utiliser une image de machine virtuelle personnalisée. Cette image de machine virtuelle personnalisée inclut les configurations ou installations des applications requises. Toutes les instances de machine virtuelle créées dans le groupe identique utilisent l’image de machine virtuelle personnalisée et sont prêtes à répondre au trafic des applications. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Créer et configurer une machine virtuelle source

>[!NOTE]
> Ce tutoriel vous sert de guide pour créer et utiliser une image de machine virtuelle généralisée. La création d’un groupe identique depuis une image de machine virtuelle généralisée n’est pas prise en charge.

Commencez par créer un groupe de ressource avec [az group create](/cli/azure/group), puis créez une machine virtuelle avec [az vm create](/cli/azure/vm). Cette machine virtuelle est ensuite utilisée comme source d’une image de machine virtuelle personnalisée. L’exemple suivant crée une machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

L’adresse IP publique de votre machine virtuelle est affichée dans la sortie de la commande [az vm create](/cli/azure/vm). La connexion SSH à l’adresse IP publique de votre machine virtuelle se présente comme suit :

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Pour personnaliser votre machine virtuelle, nous allons installer un serveur web de base. Lorsque l’instance de machine virtuelle dans le groupe identique est déployée, elle doit avoir tous les packages nécessaires pour exécuter une application web. Utilisez `apt-get` pour installer *NGINX* comme suit :

```bash
sudo apt-get install -y nginx
```

La dernière étape de préparation de votre machine virtuelle pour une utilisation en tant qu’image personnalisée consiste à déprovisionner votre machine virtuelle. Cette étape supprime les informations spécifiques à l’ordinateur de la machine virtuelle et permet de déployer plusieurs machines virtuelles à partir d’une seule image. Quand la machine virtuelle est déprovisionnée, le nom d’hôte est réinitialisé sur *localhost.localdomain*. Les clés d’hôte SSH, les configurations de serveur de noms, le mot de passe racine et les baux DHCP mis en cache sont également supprimés.

Pour déprovisionner la machine virtuelle, utilisez l’agent de machine virtuelle Azure (*waagent*). L’agent de machine virtuelle Azure est installé sur chaque machine virtuelle et est utilisé pour communiquer avec la plateforme Azure. Le paramètre `-force` indique à l’agent d’accepter les invites pour réinitialiser les informations spécifiques à l’ordinateur.

```bash
sudo waagent -deprovision+user -force
```

Fermez votre connexion SSH à la machine virtuelle :

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Créer une image de machine virtuelle personnalisée à partir de la machine virtuelle source
La machine virtuelle source est à présent personnalisée avec le serveur web Nginx installé. Nous allons créer l’image de machine virtuelle personnalisée à utiliser avec un groupe identique.

Pour créer une image, la machine virtuelle doit être libérée. Libérez la machine virtuelle avec la commande [az vm deallocate](/cli//azure/vm). Définissez ensuite l’état de la machine virtuelle comme généralisé avec [az vm generalize](/cli//azure/vm), afin que la plateforme Azure sache que la machine virtuelle est prête pour utiliser une image personnalisée. Vous pouvez uniquement créer une image à partir d’une machine virtuelle généralisée :


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Quelques minutes peuvent être nécessaires pour libérer et généraliser la machine virtuelle.

Créez à présent une image de la machine virtuelle à l’aide de [az image create](/cli//azure/image). L’exemple suivant crée une image nommée *myImage* à partir de votre machine virtuelle :

> [REMARQUE] Si le groupe de ressources et la machine virtuelle sont situés à des emplacements différents, vous pouvez ajouter le paramètre `--location` aux commandes ci-dessous pour définir l’emplacement de la machine virtuelle source utilisée pour créer l’image. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Créer un groupe identique à partir de l’image de machine virtuelle personnalisée
Créez un groupe identique avec [az vmss create](/cli/azure/vmss#az-vmss-create). Au lieu d’une image de plateforme, telle que *UbuntuLTS* ou *CentOS*, spécifiez le nom de votre image de machine virtuelle personnalisée. L’exemple suivant crée un groupe identique nommé *myScaleSet* qui utilise l’image personnalisée nommée *myImage* de l’étape précédente :

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour autoriser le trafic à atteindre votre groupe identique, vérifiez que le serveur web fonctionne correctement et créez une règle d’équilibreur de charge avec [az network lb rule create](/cli/azure/network/lb/rule). L’exemple suivant crée une règle nommée *myLoadBalancerRuleWeb* qui autorise le trafic sur le port *TCP* *80* :

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Pour voir votre groupe identique en action, obtenez l’adresse IP publique de votre équilibreur de charge avec [az network public-ip show](/cli/azure/network/public-ip). L’exemple suivant obtient l’adresse IP pour *myScaleSetLBPublicIP* qui a été créée dans le cadre du groupe identique :

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Entrez l’adresse IP publique dans votre navigateur web. La page web NGINX par défaut s’affiche comme dans l’exemple suivant :

![Nginx s’exécutant à partir de l’image de machine virtuelle personnalisée](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources
Pour supprimer votre groupe identique et les ressources supplémentaires, supprimez le groupe de ressources et toutes ses ressources avec [az group delete](/cli/azure/group). Le paramètre `--no-wait` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine. Le paramètre `--yes` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à créer et utiliser une image de machine virtuelle personnalisée pour vos groupes identiques avec Azure CLI :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Passez au didacticiel suivant pour apprendre à déployer des applications dans votre groupe identique.

> [!div class="nextstepaction"]
> [Déployer des applications dans vos groupes identiques](tutorial-install-apps-cli.md)
