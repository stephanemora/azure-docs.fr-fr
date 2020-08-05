---
title: Prérequis Azure CLI à l’utilisation d’Azure HPC Cache
description: Étapes de configuration avant de pouvoir utiliser Azure CLI pour créer ou modifier un cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094502"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurer Azure CLI pour Azure HPC Cache

Suivez ces étapes pour préparer votre environnement avant d’utiliser Azure CLI pour créer ou gérer un cache Azure HPC Cache.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Installation de l’interface de ligne de commande Azure

Azure HPC Cache nécessite la version 2.7 ou ultérieure d’Azure CLI. Exécutez `az --version` pour rechercher la version et les bibliothèques dépendantes installées. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Installer l’extension Azure HPC Cache

Les fonctions Azure HPC Cache ne faisant pas partie de la base de code principale, vous devez également installer la référence d’extension. Procédez comme suit.

1. Se connecter

   Connectez-vous à l’aide de la commande [az login](/cli/azure/reference-index#az-login) si vous utilisez une version localement installée de l’interface CLI.

    ```azurecli
    az login
    ```

    Suivez les étapes affichées dans votre terminal pour effectuer le processus d’authentification.

   > [!TIP]
   > Si vous avez plusieurs abonnements, vous devez en choisir un. Sélectionnez-le lorsque vous démarrez une session Cloud Shell dans le portail Azure ou suivez les instructions de [prise en main d’Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) pour définir votre abonnement à partir de la ligne de commande.

2. Installer l’extension Azure CLI

   Les fonctions Azure HPC Cache sont fournies en tant qu’extension Azure CLI. Elles ne font pas encore partie du package principal de l’interface CLI. Vous devez installer la référence d’extension avant de pouvoir l’utiliser.

   Les extensions Azure CLI vous donnent accès à des commandes expérimentales et disponibles en préversion. Pour en savoir plus sur les extensions, notamment sur la mise à jour et la désinstallation, consultez [Utiliser des extensions avec Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Installez l’extension pour Azure HPC Cache en exécutant la commande suivante :

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Définir le groupe de ressources par défaut (facultatif)

La plupart des commandes hpc-cache nécessitent que vous transmettiez le groupe de ressources du cache. Vous pouvez définir le groupe de ressources par défaut en utilisant [az configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Étapes suivantes

Après avoir installé l’extension Azure CLI et vous être connecté, vous pouvez utiliser Azure CLI pour créer et gérer des systèmes Azure HPC Cache.

* [Créer un cache Azure HPC Cache](hpc-cache-create.md)
* [Documentation Azure CLI sur hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
