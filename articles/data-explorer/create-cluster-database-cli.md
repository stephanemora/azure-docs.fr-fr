---
title: 'Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer à l’aide de CLI'
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un cluster et une base de données Azure Data Explorer à l’aide d’Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881114"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Créer un cluster et une base de données Azure Data Explorer à l’aide de CLI

Ce guide de démarrage rapide montre comment créer un cluster et une base de données Azure Data Explorer à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour vérifier votre version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Configurer les paramètres CLI

Les étapes suivantes ne sont pas obligatoires si vous exécutez des commandes dans Cloud Shell. Si vous utilisez l’interface CLI localement, effectuez les étapes suivantes pour vous connecter à Azure et définir votre abonnement actuel :

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurecli-interactive
    az login
    ```

2. Définissez l’abonnement dans lequel vous souhaitez créer le cluster. Remplacez `MyAzureSub` par le nom de l’abonnement Azure que vous souhaitez utiliser :

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster avec la commande suivante :

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | Nom | *azureclitest* | Nom souhaité de votre cluster.|
   | sku | *D13_v2* | Référence SKU utilisée pour votre cluster. |
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

    Vous pouvez définir d’autres paramètres facultatifs, comme la capacité du cluster.

2. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Si le résultat contient « provisioningState » avec la valeur « Succeeded » (Réussi), le cluster a bien été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données avec la commande suivante :

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nom du cluster dans lequel la base de données doit être créée.|
   | Nom | *clidatabase* | Nom souhaité de votre base de données.|
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | soft-delete-period | *3650:00:00:00* | Durée pendant laquelle les données doivent être conservées afin qu’elles soient disponibles et puissent être interrogées. |
   | hot-cache-period | *3650:00:00:00* | Durée pendant laquelle les données doivent être conservées dans le cache. |

2. Exécutez la commande suivante pour voir votre nouvelle base de données :

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

* Si vous envisagez de suivre nos autres tutoriels et guides de démarrage rapide, gardez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande ci-dessous pour supprimer votre cluster :

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python d’Azure Data Explorer](python-ingest-data.md)
