---
title: 'Démarrage rapide : Créer un groupe de machines virtuelles identiques à l’aide d’Azure CLI'
description: Commencez vos déploiements en apprenant à créer rapidement un groupe de machines virtuelles identiques avec Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: 172a498b6b92a059c50cac4ad45fee322e12fdd0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76278035"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Démarrage rapide : créer un groupe de machines virtuelles identiques avec Azure CLI
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles de mise à l’échelle automatique en fonction de l’utilisation des ressources telles que l’UC, la demande de mémoire ou le trafic réseau. Un équilibreur de charge Azure distribue ensuite le trafic vers les instances de machine virtuelle du groupe identique. Dans ce guide de démarrage rapide, vous allez créer un groupe de machines virtuelles identiques et déployer un exemple d’application avec Azure CLI.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Créer un groupe identique
Pour pouvoir créer un groupe identique, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Créez à présent un groupe de machines virtuelles identiques avec [az vmss create](/cli/azure/vmss). L’exemple suivant crée un groupe identique nommé *myScaleSet* qui est définit pour une mise à jour automatique lorsque des modifications sont appliquées, et qui génère des clés SSH s’il n’en existe pas dans *~/.ssh/id_rsa*. Ces clés SSH sont utilisées si vous devez vous connecter à des instances de machine virtuelle. Pour utiliser un jeu de clés SSH existant, utilisez plutôt le paramètre `--ssh-key-value` et spécifiez l’emplacement de vos clés.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="deploy-sample-application"></a>Déployer un exemple d’application
Pour tester votre groupe identique, installez une application web de base. L’extension de script personnalisé Azure permet de télécharger et d’exécuter un script qui installe une application sur les instances de machine virtuelle. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](../virtual-machines/linux/extensions-customscript.md).

Utilisez l’extension de script personnalisé pour installer un serveur web NGINX de base. Appliquez l’extension de script personnalisé qui installe NGINX avec [az vmss extension set](/cli/azure/vmss/extension) comme suit :

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Autoriser le trafic vers l’application
Lorsque le groupe identique est créé, un équilibrage de charge Azure est automatiquement déployé. L’équilibreur de charge distribue le trafic vers les instances de machine virtuelle du groupe identique. Pour autoriser le trafic à atteindre l’exemple d’application web, créez une règle d’équilibreur de charge avec [az network lb rule create](/cli/azure/network/lb/rule). L’exemple suivant crée une règle nommée *myLoadBalancerRuleWeb* :

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


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, accédez à l’exemple d’application web dans un navigateur web. Obtenez l’adresse IP publique de votre équilibrage de charge avec [az network public-ip show](/cli/azure/network/public-ip). L’exemple suivant obtient l’adresse IP pour *myScaleSetLBPublicIP* qui a été créée dans le cadre du groupe identique :

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Saisissez l’adresse IP publique de l’équilibreur de charge dans un navigateur web. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Page web par défaut dans NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées, comme suit. Le paramètre `--no-wait` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine. Le paramètre `--yes` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article de démarrage rapide, vous avez créé un groupe identique de base et vous avez utilisé l’extension de script personnalisé afin d’installer un serveur web NGINX de base sur les instances de machine virtuelle. Pour en savoir plus, passez au didacticiel dédié à la création et la gestion des groupes de machines virtuelles identiques Azure.

> [!div class="nextstepaction"]
> [Créer et gérer des groupes de machines virtuelles identiques Azure](tutorial-create-and-manage-cli.md)

