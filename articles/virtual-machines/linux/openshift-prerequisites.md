---
title: Conditions préalables à l’utilisation d’OpenShift | Microsoft Docs
description: Conditions préalables requises au déploiement d’OpenShift dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: fd20fe880ae77992e5eadb5f2b581d3f5b53f86e
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085860"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Prérequis courants pour déployer OpenShift dans Azure

Cet article décrit les prérequis courants pour déployer OpenShift Container Platform ou OKD dans Azure.

L’installation d’OpenShift utilise des playbooks Ansible. Ansible utilise Secure Shell (SSH) pour se connecter à tous les hôtes de cluster pour effectuer les étapes d’installation.

Quand Ansible démarre la connexion SSH aux hôtes distants, il ne peut pas entrer un mot de passe. Pour cette raison, aucun mot de passe (phrase secrète) ne doit être associé à la clé privée, sous peine de provoquer l’échec du déploiement.

Puisque les machines virtuelles sont déployées via des modèles Azure Resource Manager, la même clé publique est utilisée pour accéder à l’ensemble des machines virtuelles. Vous devez injecter la clé privée correspondante dans la machine virtuelle qui exécute également tous les playbooks. Pour y parvenir en toute sécurité, nous utilisons un coffre de clés Azure pour transmettre la clé privée à la machine virtuelle.

Si un stockage persistant est nécessaire pour les conteneurs, alors des volumes persistants sont nécessaires. OpenShift prend en charge les disques durs virtuels (VHD) Azure pour cette fonctionnalité, mais Azure doit d’abord être configuré en tant que fournisseur cloud.

Dans ce modèle, OpenShift :

- Crée un objet VHD dans un compte Stockage Azure ou un disque managé.
- Monte le disque dur virtuel (VHD) sur une machine virtuelle et formate le volume.
- Monte le volume sur le pod.

Pour que cette configuration fonctionne, OpenShift doit être autorisé à effectuer ces tâches dans Azure. Pour cela, utilisez un principal de service. Le principal de service est un compte de sécurité dans Azure Active Directory qui dispose d’autorisations sur les ressources.

Le principal de service a besoin d’accéder aux comptes de stockage et aux machines virtuelles qui composent le cluster. Si toutes les ressources de cluster OpenShift sont déployées sur un seul groupe de ressources, des autorisations pour ce groupe de ressources peuvent être affectées au principal de service.

Ce guide décrit comment créer les artefacts associés aux prérequis.

> [!div class="checklist"]
> * Créez un coffre de clés pour gérer les clés SSH pour le cluster OpenShift.
> * Créez un principal de service, qui sera utilisé par le fournisseur de cloud Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure 
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/reference-index#az_login) et suivez les instructions à l’écran ou cliquez sur **Essayer** pour utiliser Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Nous vous recommandons d’utiliser un groupe de ressources dédié pour y héberger le coffre de clés. Ce groupe est différent du groupe de ressources dans lequel se déploient les ressources de cluster OpenShift.

L’exemple suivant crée un groupe de ressources nommé *keyvaultrg* à l’emplacement *eastus* :

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Création d’un coffre de clés
Créez un coffre de clés pour stocker les clés SSH du cluster avec la commande [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Le nom du coffre de clés doit être globalement unique.

L’exemple suivant crée un coffre de clés nommé *keyvault* dans le groupe de ressources *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Création d’une clé SSH 
Une clé SSH est nécessaire pour sécuriser l’accès au cluster OpenShift. Créez une paire de clés SSH à l’aide de la commande `ssh-keygen` (sur Linux ou macOS) :
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Votre paire de clés SSH ne peut pas avoir de mot de passe/phrase secrète.

Pour plus d’informations sur les clés SSH sur Windows, consultez [Guide pratique pour créer des clés SSH sur Windows](/azure/virtual-machines/linux/ssh-from-windows). Assurez-vous d’exporter la clé privée au format OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Stocker la clé privée SSH dans Azure Key Vault
Le déploiement OpenShift utilise la clé SSH que vous avez créée pour sécuriser l’accès au maître OpenShift. Pour permettre au déploiement de récupérer la clé SSH en toute sécurité, stockez la clé dans Key Vault à l’aide de la commande suivante :

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Créer un principal du service 
OpenShift communique avec Azure à l’aide d’un nom d’utilisateur et d’un mot de passe ou d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme OpenShift. Vous contrôlez et vous définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) et affichez les informations d’identification requises par OpenShift.

L’exemple suivant crée un principal de service et lui assigne des autorisations de contributeur sur un groupe de ressources nommé openshiftrg.
séparément et utilisez la sortie pour remplir l’option --scopes.

Commencez par créer le groupe de ressources appelé openshiftrg :

```azurecli
az group create -l eastus -n openshiftrg
```

Créez un principal de service :

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Si vous utilisez Windows, exécutez ```az group show --name openshiftrg --query id``` et utilisez la sortie à la place de $scope.

Prenez note de la propriété appId renvoyée par la commande :
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Veillez à créer un mot de passe sécurisé. Suivez les conseils en matière de [Stratégies et restrictions de mot de passe dans Azure Active Directory](/azure/active-directory/active-directory-passwords-policy).

Pour plus d’informations sur les principaux de service, consultez [Créer un principal du service avec Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

Cet article a abordé les thèmes suivants :
> [!div class="checklist"]
> * Créez un coffre de clés pour gérer les clés SSH pour le cluster OpenShift.
> * Créez un principal de service pour que le fournisseur de solution cloud Azure l’utilise.

Ensuite, déployez un cluster OpenShift :

- [Déployer OpenShift Container Platform](./openshift-container-platform.md)
- [Déployer OKD](./openshift-okd.md)