---
title: Chiffrer des disques sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: Comment chiffrer des disques virtuels sur une machine virtuelle Linux pour renforcer la sécurité à l’aide d’Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731615"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Chiffrement d’une machine virtuelle Linux dans Azure

Pour renforcer la sécurité et la conformité de la machine virtuelle, les disques virtuels et la machine virtuelle elle-même peuvent être chiffrés. Les machines virtuelles sont chiffrées à l’aide de clés de chiffrement sécurisées dans un coffre de clés Azure. Vous contrôlez ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une machine virtuelle Linux à l’aide d’Azure CLI. 

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.30 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Présentation du chiffrement de disque
Les disques virtuels sur des machines virtuelles Linux sont chiffrés au repos à l’aide de la commande [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Le chiffrement de disques virtuels dans Azure n’entraîne aucun frais. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. 

Le processus de chiffrement d’une machine virtuelle est le suivant :

1. Créez une clé de chiffrement dans un coffre de clés Azure.
1. Configurez la clé de chiffrement afin de la rendre utilisable pour le chiffrement des disques.
1. Activez le chiffrement de vos disques virtuels.
1. Les clés de chiffrement requises sont demandées à Azure Key Vault.
1. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.


## <a name="requirements-and-limitations"></a>Spécifications et limitations
Configuration requise et scénarios pris en charge pour le chiffrement de disque :

* Les versions serveur Linux suivantes : Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.
* Toutes les ressources (par exemple, le coffre de clés, le compte de stockage, la machine virtuelle, etc.) doivent appartenir à la même région et au même abonnement Azure.
* Machines virtuelles standard des séries A, D, DS, G, GS, etc.
* Mise à jour des clés de chiffrement sur une machine virtuelle Linux déjà chiffrée.

Le chiffrement de disque n’est actuellement pas pris en charge dans les scénarios suivants :

* Machines virtuelles de niveau de base.
* Machines virtuelles créées à l’aide du modèle de déploiement Classic.
* Désactivation du chiffrement de disque du système d’exploitation sur les machines virtuelles Linux.
* Utilisation d’images personnalisées Linux.

Pour plus d’informations sur les scénarios pris en charge et les limitations, consultez [Chiffrement de disque Azure pour des machines virtuelles IaaS](../../security/azure-security-disk-encryption.md).


## <a name="create-an-azure-key-vault-and-keys"></a>Créer un coffre Azure Key Vault et des clés
Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myKey* et *myVM*.

La première étape consiste à créer un coffre de clés Azure pour y stocker les clés de chiffrement. Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Pour le chiffrement de disque virtuel, vous utilisez le coffre de clés afin de stocker une clé de chiffrement pour chiffrer ou déchiffrer vos disques virtuels.

Activez le fournisseur Azure Key Vault au sein de votre abonnement Azure avec [az provider register](/cli/azure/provider#az-provider-register) puis créez un groupe de ressources avec [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement `eastus` :

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Le coffre de clés Azure contenant les clés de chiffrement et les ressources de calcul associées tels que le stockage et la machine virtuelle elle-même doit résider dans la même région. Créez un coffre Azure Key Vault avec [az keyvault create](/cli/azure/keyvault#az-keyvault-create) et activez Key Vault pour une utilisation avec le chiffrement de disque. Spécifiez un nom de Key Vault unique pour *keyvault_name* comme suit :

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Vous pouvez stocker des clés de chiffrement à l’aide d’une protection logicielle ou HSM (modèle de sécurité matériel). L’utilisation d’une protection HSM nécessite un coffre de clés Premium. Contrairement à l’utilisation d’un coffre de clés standard qui stocke les clés protégées par logiciel, la création d’un coffre de clés Premium entraîne des frais. Pour créer un coffre de clés Premium, ajoutez `--sku Premium` à la commande de l’étape précédente. L’exemple suivant utilise des clés protégées par logiciel, car vous avez créé un coffre de clés standard.

Pour les deux modèles de protection, la plateforme Azure doit être autorisée à demander les clés de chiffrement lorsque la machine virtuelle démarre pour déchiffrer les disques virtuels. Créez une clé de chiffrement dans le coffre Key Vault avec [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). L’exemple qui suit permet de créer une clé nommée *myKey* :

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az-vm-create) et attachez un disque de données de 5 Go. Seules certaines images Marketplace prennent en charge le chiffrement de disque. L’exemple qui suit permet de créer une machine virtuelle nommée *myVM* qui utilise une image *Ubuntu 16.04 LTS* :

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Connectez-vous avec SSH à votre machine virtuelle à l’aide de la valeur *publicIpAddress* indiquée dans la sortie de la commande précédente. Créez une partition et un système de fichiers, puis montez le disque de données. Pour plus d’informations, consultez [Connexion à la machine virtuelle Linux pour monter le nouveau disque](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Fermez votre session SSH.


## <a name="encrypt-the-virtual-machine"></a>Chiffrer la machine virtuelle


Chiffrez votre machine virtuelle avec [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) :

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Il faut un certain temps pour que le processus de chiffrement de disque se termine. Surveillez l’état du processus avec [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) :

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Lorsque vous avez terminé, le résultat ressemble à l’exemple qui suit :

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Ajouter des disques de données supplémentaires
Une fois que vous avez chiffré vos disques de données, vous pouvez ajouter d’autres disques virtuels à votre machine virtuelle et les chiffrer. 

Une fois que le disque de données a été ajouté à la machine virtuelle, réexécutez la commande pour chiffrer les disques virtuels comme suit :

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la gestion du coffre de clés Azure, y compris la suppression des clés de chiffrement et des coffres, consultez [Gérer le coffre de clés à l’aide de l’interface de ligne de commande](../../key-vault/key-vault-manage-with-cli2.md).
* Pour plus d’informations sur le chiffrement de disque, notamment la préparation d’une machine virtuelle personnalisée chiffrée à télécharger vers Azure, consultez [Chiffrement de disque Azure](../../security/azure-security-disk-encryption.md).
