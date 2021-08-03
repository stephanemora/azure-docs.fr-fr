---
title: Utiliser cloud-init dans une machine virtuelle Linux sur Azure
description: Guide pratique pour utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux lors de la création avec l’interface Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/01/2021
ms.author: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: ab44e9710fc4a0a86d3225b11f7755400bca0b79
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853295"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux sur Azure
Cet article montre comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour mettre à jour des packages sur une machine virtuelle Linux ou un groupe de machines virtuelles identiques au moment de l’approvisionnement dans Azure. Ces scripts cloud-init s’exécutent au premier démarrage une fois que les ressources ont été approvisionnées par Azure. Pour plus d’informations sur le fonctionnement de cloud-init en mode natif dans Azure et sur les versions de Linux prises en charge, consultez [Présentation de cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Mettre à jour une machine virtuelle avec cloud-init
Pour des raisons de sécurité, configurez une machine virtuelle pour appliquer les dernières mises à jour au premier démarrage. Étant donné que cloud-init fonctionne sur différentes distributions Linux, il est inutile de spécifier `apt` ou `yum` pour le gestionnaire de package. Vous définissez plutôt `package_upgrade` et laissez le processus cloud-init déterminer le mécanisme approprié pour la distribution en cours d’utilisation. 

Pour cet exemple, nous utilisons Azure Cloud Shell. Pour afficher le processus de mise à niveau en action, créez un fichier nommé *cloud_init_upgrade.txt* et collez-y la configuration suivante. 

Sélectionnez le bouton **Essayer** dans le bloc de code ci-dessous pour ouvrir Cloud Shell. Pour créer le fichier et afficher une liste des éditeurs disponibles dans Cloud Shell, tapez ce qui suit :

```azurecli-interactive
sensible-editor cloud_init_upgrade.txt 
```

Copiez le texte ci-dessous et collez-le dans le fichier `cloud_init_upgrade.txt`. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Avant le déploiement, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm) et spécifiez le fichier cloud-init avec le paramètre `--custom-data` comme suit :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre **publicIpAddress** comme suit :

```bash
ssh <publicIpAddress>
```

Exécutez l’outil de gestion de package et vérifiez s’il existe des mises à jour.

```bash
sudo yum update
```

Étant donné que cloud-init a recherché et installé des mises à jour au démarrage, il ne doit y avoir aucune mise à jour supplémentaire à appliquer.  Vous voyez le processus de mise à jour, le nombre de packages modifiés ainsi que l’installation de `httpd` en exécutant `yum history` et examinez la sortie similaire à celle présentée ci-dessous.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init supplémentaires de modifications de configuration, consultez les rubriques suivantes :
 
- [Ajouter un utilisateur Linux supplémentaire à une machine virtuelle](cloudinit-add-user.md)
- [Exécuter un gestionnaire de package pour mettre à jour les packages existants au premier démarrage](cloudinit-update-vm.md)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Installer un package d’application, mettre à jour des fichiers de configuration et injecter des clés](tutorial-automate-vm-deployment.md)
