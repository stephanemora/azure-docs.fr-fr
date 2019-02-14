---
title: Créer un cluster et une base de données Azure Data Explorer à l’aide de CLI
description: Cet article montre comment créer un cluster et une base de données pour Azure Data Explorer à l’aide d’Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812682"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Créer un cluster et une base de données Azure Data Explorer à l’aide de CLI

Cet article montre comment créer un cluster et une base de données pour Azure Data Explorer à l’aide d’Azure CLI.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Différence entre les API de plan de gestion et de plan de données

Il existe deux bibliothèques d’API différentes : les API de gestion et de plan de données.
Les API de gestion servent à gérer les ressources, par exemple créer un cluster, créer une base de données, supprimer une connexion de données, modifier le nombre d’instances, etc. Les API de plan de données sont utilisées pour interagir avec les données, exécuter des requêtes, recevoir des données, etc.

## <a name="configure-the-cli-parameters"></a>Configurer les paramètres CLI

Se connecter à son compte

```Bash
az login
```

Définissez l’abonnement dans lequel vous souhaitez créer le cluster.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

Créez votre cluster avec la commande suivante.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Renseignez les valeurs suivantes :

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | Nom | *azureclitest* | Nom souhaité de votre cluster.|
   | sku | *D13_v2* | Référence SKU utilisée pour votre cluster. |
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

Si vous le souhaitez, il existe plusieurs paramètres facultatifs que vous pouvez utiliser, tels que la capacité du cluster, etc.

Pour vérifier si votre cluster a été créé avec succès, vous pouvez exécuter

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Si le résultat contient « provisioningState » avec la valeur Succeeded (Réussi), cela signifie que le cluster a été créé avec succès.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

Créez votre base de données avec la commande suivante.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Renseignez les valeurs suivantes :

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nom de votre cluster dans lequel la base de données doit être créée.|
   | Nom | *clidatabase* | Nom souhaité de votre base de données.|
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | soft-delete-period | *3650:00:00:00* | Durée pendant laquelle les données doivent être conservées afin qu’elles soient disponibles et puissent être interrogées. |
   | hot-cache-period | *3650:00:00:00* | Durée pendant laquelle les données doivent être conservées dans le cache. |

Vous pouvez voir la base de données que vous avez créé en exécutant

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Voilà, vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de suivre nos autres tutoriels et guides de démarrage rapide, gardez les ressources que vous avez créées.

Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande ci-dessous pour supprimer votre cluster.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)
