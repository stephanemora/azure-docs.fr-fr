---
title: Démarrage rapide - Exécuter des playbooks Ansible avec Bash dans Azure Cloud Shell
description: Dans ce guide de démarrage rapide, découvrez comment exécuter diverses tâches Ansible avec Bash dans Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 0eb89bcacae1629bdb1f6dcda8f9a25efdb6eedf
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193648"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Démarrage rapide : Exécuter des playbooks Ansible avec Bash dans Azure Cloud Shell

Azure Cloud Shell est un shell interactif, accessible par navigateur pour la gestion des ressources Azure. Cloud Shell vous permet d’utiliser une ligne de commande Bash ou PowerShell. Dans cet article, vous utilisez Bash dans Azure Cloud Shell pour exécuter un playbook Ansible.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurer Azure Cloud Shell** : si vous débutez avec Azure Cloud Shell, consultez [Démarrage rapide de Bash dans Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuration automatique des informations d’identification

Lorsque vous vous connectez à Cloud Shell, Ansible s’authentifie avec Azure pour gérer l’infrastructure sans aucune configuration supplémentaire. 

Quand vous utilisez plusieurs abonnements, spécifiez l’abonnement utilisé par Ansible en exportant la variable d’environnement `AZURE_SUBSCRIPTION_ID`. 

Pour répertorier tous vos abonnements Azure, exécutez la commande suivante :

```azurecli-interactive
az account list
```

À l’aide de votre ID d’abonnement Azure, définissez `AZURE_SUBSCRIPTION_ID` comme suit :

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Vérifier la configuration
Pour vérifier que la configuration a réussi, utilisez Ansible afin de créer un groupe de ressources Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Démarrage rapide : Configurer une machine virtuelle dans Azure avec Ansible](./ansible-create-vm.md)