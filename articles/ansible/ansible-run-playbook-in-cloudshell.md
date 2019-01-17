---
title: Exécuter Ansible avec Bash dans Azure Cloud Shell
description: Découvrir comment effectuer diverses tâches Ansible avec Bash dans Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 61b23b5bc2620c82051b0ba1de4bb54b44a495e0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359185"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Exécuter Ansible avec Bash dans Azure Cloud Shell

Dans ce didacticiel, vous allez apprendre à utiliser Bash dans Cloud Shell pour configurer un abonnement Azure en tant qu’espace de travail Ansible. 

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Configurez Azure Cloud Shell** : si vous ne connaissez pas encore Azure Cloud Shell, l’article [Démarrage rapide de Bash dans Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) vous indique comment démarrer et configurer Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuration automatique des informations d’identification

Lorsque vous vous connectez à Cloud Shell, Ansible s’authentifie avec Azure pour gérer l’infrastructure sans aucune configuration supplémentaire. Si vous avez plusieurs abonnements, vous pouvez choisir l’abonnement à utiliser par Ansible en exportant la variable d’environnement `AZURE_SUBSCRIPTION_ID`. Pour répertorier tous vos abonnements Azure, exécutez la commande suivante :

```azurecli-interactive
az account list
```

À l’aide de l’**id** de l’abonnement avec lequel vous souhaitez travailler, définissez l’**AZURE_SUBSCRIPTION_ID** comme suit :

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Vérifier la configuration
Pour vous assurer que la configuration a réussi, utilisez Ansible pour créer un groupe de ressources.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Créer une machine virtuelle Linux de base dans Azure avec Ansible](/azure/virtual-machines/linux/ansible-create-vm)