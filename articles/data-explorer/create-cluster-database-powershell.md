---
title: 'Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer à l’aide de PowerShell'
description: Apprendre à créer un cluster et une base de données Azure Data Explorer à l’aide de PowerShell
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 553564be494b4175cba937b583d49ad84a8d0e66
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526518"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Créer un cluster et une base de données Azure Data Explorer à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Dans ce guide de démarrage rapide, vous créez un cluster et une base de données en utilisant PowerShell. Vous pouvez exécuter des scripts et des applets de commande PowerShell sur Windows, sur Linux ou dans [Azure Cloud Shell](../cloud-shell/overview.md) avec [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) pour créer et configurer des clusters et des bases de données Azure Data Explorer.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite au minimum la version 2.0.4 d’Azure CLI. Exécutez `az --version` pour vérifier votre version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurer les paramètres

Les étapes suivantes ne sont pas obligatoires si vous exécutez des commandes dans Azure Cloud Shell. Si vous utilisez l’interface CLI localement, suivez les 1 et 2 étapes pour vous connecter à Azure et définir votre abonnement actuel :

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Définissez l’abonnement où vous voulez créer le cluster :

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Quand vous exécutez Azure CLI localement ou dans Azure Cloud Shell, vous devez installer le module Az.Kusto sur votre appareil :
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster avec la commande suivante :

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | Nom | *mykustocluster* | Nom souhaité de votre cluster.|
   | Sku | *D13_v2* | Référence SKU utilisée pour votre cluster. |
   | ResourceGroupName | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

    Vous pouvez définir d’autres paramètres facultatifs, comme la capacité du cluster.

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Si le résultat contient `provisioningState` avec la valeur `Succeeded`, alors le cluster a correctement été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données avec la commande suivante :

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Nom du cluster dans lequel la base de données est créée.|
   | Nom | *mykustodatabase* | Nom de votre base de données.|
   | ResourceGroupName | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | SoftDeletePeriod | *3650:00:00:00* | Durée pendant laquelle les données restent disponibles pour les requêtes. |
   | HotCachePeriod | *3650:00:00:00* | Durée pendant laquelle les données sont conservées dans le cache. |

1. Exécutez la commande suivante pour voir la base de données que vous avez créée :

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

* Si vous envisagez de suivre nos autres tutoriels et guides de démarrage rapide, gardez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande suivante pour supprimer votre cluster :

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Commandes Az.Kusto supplémentaires](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Démarrage rapide : Ingérer des données avec le SDK .NET Standard dans Azure Data Explorer (préversion)](net-standard-ingest-data.md)
