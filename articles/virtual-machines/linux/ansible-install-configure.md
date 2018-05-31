---
title: Installer et configurer Ansible pour une utilisation avec des machines virtuelles Azure | Microsoft Docs
description: Découvrez comment installer et configurer Ansible pour la gestion des ressources Azure sur Ubuntu, CentOS et SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: e6fad548eda35d1832cb4ecc2fd9bdabf825f361
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896127"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installer et configurer Ansible pour gérer des machines virtuelles dans Azure

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer vos machines virtuelles dans Azure comme vous le feriez pour toute autre ressource. Cet article explique comment installer Ansible et les modules du SDK Azure Python nécessaires pour quelques-unes des distributions Linux les plus courantes. Vous pouvez installer Ansible sur d’autres distributions en adaptant les packages installés à votre plateforme spécifique. Pour créer des ressources Azure de manière sécurisée, vous découvrez également comment créer et définir des informations d’identification à utiliser par Ansible.

Pour plus d’options d’installation et des étapes pour d’autres plateformes, consultez le [guide d’installation d’Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.30 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Installer Ansible

L’une des méthodes les plus simples d’utiliser Ansible avec Azure est avec Azure Cloud Shell, une expérience d’interpréteur de commandes basée sur un navigateur pour gérer et développer des ressources Azure. Ansible est préinstallé dans Cloud Shell. Vous pouvez donc ignorer les instructions d’installation d’Ansible et accéder à la section [Créer des informations d’identification Azure](#create-azure-credentials). Pour obtenir la liste des outils supplémentaires également disponibles dans Cloud Shell, consultez [Fonctionnalités et outils pour Bash dans Azure Cloud Shell](../../cloud-shell/features.md#tools).

Les instructions suivantes vous montrent comment créer une machine virtuelle Linux pour différentes distributions, puis installer Ansible. Si vous n’avez pas besoin de créer une machine virtuelle Linux, ignorez cette première étape pour créer un groupe de ressources Azure. Si vous avez besoin de créer une machine virtuelle, commencez par créer un groupe de ressource avec [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Sélectionnez maintenant l’une des distributions suivantes pour connaître la procédure permettant de créer une machine virtuelle, le cas échéant, puis installez Ansible :

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Si nécessaire, créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Si nécessaire, créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Si nécessaire, créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Créer des informations d’identification Azure

Ansible communique avec Azure en utilisant un nom d’utilisateur et un mot de passe, ou un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme Ansible. Vous contrôlez et vous définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Sur votre ordinateur hôte ou dans Azure Cloud Shell, créez un principal de service à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac). Les informations d’identification dont Ansible a besoin sont affichées sur l’écran :

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Voici un exemple de la sortie des commandes précédentes :

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Pour s’authentifier auprès d’Azure, vous devez également obtenir votre ID d’abonnement Azure à l’aide de la commande [az account show](/cli/azure/account#az_account_show) :

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Vous utilisez la sortie de ces deux commandes à l’étape suivante.

## <a name="create-ansible-credentials-file"></a>Créer un fichier d’informations d’identification Ansible

Pour fournir des informations d’identification à Ansible, vous définissez des variables d’environnement ou vous créez un fichier d’informations d’identification local. Pour plus d’informations sur la définition des informations d’identification Ansible, consultez [Fourniture d’informations d’identification aux modules Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Pour un environnement de développement, créez un fichier *d’informations d’identification* pour Ansible sur votre machine virtuelle hôte. Créez un fichier d’informations d’identification sur la machine virtuelle sur laquelle vous avez installé Ansible à l’étape précédente :

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Le fichier *d’informations d’identification* combine l’ID d’abonnement avec la sortie de la création d’un principal de service. La sortie de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) précédente est la même que pour *client_id*, *secret* et *locataire*. L’exemple suivant de fichier *d’informations d’identification* montre ces valeurs correspondant à la sortie précédente. Saisissez vos propres valeurs, comme suit :

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Enregistrez et fermez le fichier.

## <a name="use-ansible-environment-variables"></a>Utiliser des variables d’environnement Ansible

Si vous devez utiliser des outils comme Ansible Tower ou Jenkins, vous devez définir des variables d’environnement. Cette étape peut être ignorée si vous allez simplement utiliser le client Ansible et le fichier d’informations d’identification Azure créé à l’étape précédente. Les variables d’environnement définissent les mêmes informations que le fichier d’informations d’identification Azure :

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Étapes suivantes

Ansible et les modules du SDK Azure Python SDK nécessaires sont maintenant installés, et des informations d’identification sont définies pour être utilisées par Ansible. Découvrez comment [créer une machine virtuelle avec Ansible](ansible-create-vm.md). Vous pouvez également découvrir comment [créer une machine virtuelle Azure complète et des ressources de support avec Ansible](ansible-create-complete-vm.md).
