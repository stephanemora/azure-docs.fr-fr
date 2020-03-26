---
title: Démarrage rapide – Installer Ansible sur des machines virtuelles Linux dans Azure
description: Guide de démarrage rapide expliquant comment installer et configurer Ansible pour la gestion des ressources Azure sur Ubuntu, CentOS et SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202441"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Démarrage rapide : Installer Ansible sur des machines virtuelles Linux dans Azure

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article explique comment configurer Ansible pour certaines des distributions Linux les plus courantes. Pour installer Ansible sur d’autres distributions, ajustez les packages installés à votre plateforme spécifique. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Accès à Linux ou à une machine virtuelle Linux** : si vous n’avez pas de machine Linux, créez une [machine virtuelle Linux](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installer Ansible sur une machine virtuelle Linux Azure

Connectez-vous à votre machine Linux, puis sélectionnez l’une des distributions ci-après pour découvrir la procédure d’installation d’Ansible correspondante :

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Dans cette section, vous configurez CentOS pour qu’il utilise Ansible.

1. Ouvrez une fenêtre de terminal.

1. Entrez la commande suivante afin d’installer les packages obligatoires pour les modules du SDK Azure Python :

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Entrez la commande suivante pour installer les packages Ansible obligatoires :

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Créer les informations d’identification Azure](#create-azure-credentials)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Dans cette section, vous configurez Ubuntu pour qu’il utilise Ansible.

1. Ouvrez une fenêtre de terminal.

1. Entrez la commande suivante afin d’installer les packages obligatoires pour les modules du SDK Azure Python :

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Entrez la commande suivante pour installer les packages Ansible obligatoires :

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Créer les informations d’identification Azure](#create-azure-credentials)

### <a name="sles-12-sp2"></a>SLES 12 SP2

Dans cette section, vous configurez SLES pour qu’il utilise Ansible.

1. Ouvrez une fenêtre de terminal.

1. Entrez la commande suivante afin d’installer les packages obligatoires pour les modules du SDK Azure Python :

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Entrez la commande suivante pour installer les packages Ansible obligatoires :

    ```bash
    sudo pip install ansible[azure]
    ```

1. Entrez la commande suivante pour supprimer le package de chiffrement de Python en conflit :

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Créer les informations d’identification Azure](#create-azure-credentials)

## <a name="create-azure-credentials"></a>Créer des informations d’identification Azure

Pour configurer les informations d’identification Ansible, vous avez besoin des informations suivantes :

* L'identifiant de votre abonnement Azure. 
* Les valeurs du principal de service

Si vous utilisez Ansible Tower ou Jenkins, déclarez les valeurs du principal de service en tant que variables d’environnement.

Configurez les informations d’identification Ansible à l’aide de l’une des techniques suivantes :

- [Créer un fichier d’informations d’identification Ansible](#file-credentials)
- [Utiliser des variables d’environnement Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Créer un fichier d’informations d’identification Ansible

Dans cette section, vous créez un fichier d’informations d’identification local pour fournir ces informations à Ansible. 

Pour plus d’informations sur la définition des informations d’identification Ansible, consultez [Fourniture d’informations d’identification aux modules Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Pour un environnement de développement, créez un fichier nommé `credentials` sur la machine virtuelle hôte :

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Insérez les lignes suivantes dans le fichier. Remplacez les espaces réservés par les valeurs du principal de service.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Enregistrez et fermez le fichier.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utiliser des variables d’environnement Ansible

Dans cette section, vous exportez les valeurs du principal de service pour configurer vos informations d’identification Ansible.

1. Ouvrez une fenêtre de terminal.

1. Exportez les valeurs du principal de service :

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Vérifier la configuration

Pour vérifier que la configuration a réussi, utilisez Ansible afin de créer un groupe de ressources Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Démarrage rapide : Configurer une machine virtuelle Linux dans Azure avec Ansible](./ansible-create-vm.md)