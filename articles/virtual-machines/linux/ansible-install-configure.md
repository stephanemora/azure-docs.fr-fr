---
title: Installer Ansible sur des machines virtuelles Azure
description: Découvrez comment installer et configurer Ansible pour la gestion des ressources Azure sur Ubuntu, CentOS et SLES
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 9ad18397a3463fc845692c79b5e1f817d0912a8e
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43663665"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Installer Ansible sur des machines virtuelles Azure

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer vos machines virtuelles dans Azure comme vous le feriez pour toute autre ressource. Cet article explique comment installer Ansible et les modules du SDK Azure Python nécessaires pour quelques-unes des distributions Linux les plus courantes. Vous pouvez installer Ansible sur d’autres distributions en adaptant les packages installés à votre plateforme spécifique. Pour créer des ressources Azure de manière sécurisée, vous découvrez également comment créer et définir des informations d’identification à utiliser par Ansible. Pour obtenir la liste des outils supplémentaires disponibles dans Cloud Shell, consultez l’article [Fonctionnalités et outils pour Bash dans Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Accès à Linux ou à une machine virtuelle Linux** : si vous n’avez pas de machine Linux, créez une [machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Principal du service Azure** : suivez les instructions de la section **Créer le principal du service** de l’article [Créer un principal du service Azure avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Notez les valeurs des éléments **appId**, **displayName**, **password** et **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installer Ansible sur une machine virtuelle Linux Azure

Connectez-vous à votre machine Linux, puis sélectionnez l’une des distributions ci-après pour découvrir la procédure d’installation d’Ansible correspondante :

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Installez les packages requis pour les modules du Kit de développement logiciel (SDK) Python Azure et Ansible en entrant les commandes ci-après dans une fenêtre de terminal ou Bash :

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Suivez les instructions de la section [Créer des informations d’identification Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Installez les packages requis pour les modules du Kit de développement logiciel (SDK) Python Azure et Ansible en entrant les commandes ci-après dans une fenêtre de terminal ou Bash :


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Suivez les instructions de la section [Créer des informations d’identification Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Installez les packages requis pour les modules du Kit de développement logiciel (SDK) Python Azure et Ansible en entrant les commandes ci-après dans une fenêtre de terminal ou Bash :

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Suivez les instructions de la section [Créer des informations d’identification Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Créer des informations d’identification Azure

La combinaison de l’ID d’abonnement et des informations renvoyées par la création du principal de service permet de configurer les informations d’identification Ansible de l’une des deux manières suivantes :

- [Créer un fichier d’informations d’identification Ansible](#file-credentials)
- [Utiliser des variables d’environnement Ansible](#env-credentials)

Si vous prévoyez d’utiliser des outils tels qu’Ansible Tower ou Jenkins, vous devez utiliser l’option de déclaration des valeurs de principal de service en tant que variables d’environnement.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Créer un fichier d’informations d’identification Ansible

Cette section vous explique comment créer un fichier d’informations d’identification local pour fournir les informations d’identification à Ansible. Pour plus d’informations sur la définition des informations d’identification Ansible, consultez [Fourniture d’informations d’identification aux modules Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Pour un environnement de développement, créez un fichier *credentials* pour Ansible sur votre machine virtuelle hôte comme suit :

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Insérez les lignes ci-après dans le fichier *credentials* en remplaçant les espaces réservés par les informations issues de la création du principal de service.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Enregistrez et fermez le fichier.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utiliser des variables d’environnement Ansible

Cette section vous explique comment configurer vos informations d’identification Ansible en les exportant en tant que variables d’environnement.

Dans une fenêtre de terminal ou Bash, entrez les commandes suivantes :

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Vérifier la configuration
Pour vous assurer que la configuration a réussi, vous pouvez à présent utiliser Ansible pour créer un groupe de ressources.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Créer une machine virtuelle Linux dans Azure à l’aide d’Ansible](./ansible-create-vm.md)