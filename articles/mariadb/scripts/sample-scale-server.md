---
title: Script CLI - Mettre à l’échelle un serveur - Azure Database for MariaDB
description: Cet exemple de script CLI met à l’échelle un serveur Azure Database for MariaDB pour obtenir un autre niveau de performances, après analyse des métriques.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 562f265cccf444740c177a41e516f9066188613e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771633"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Superviser et mettre à l’échelle une base de données Azure Database for MariaDB à l’aide d’Azure CLI
Cet exemple de script CLI met à l’échelle le calcul et le stockage d’un serveur Azure Database for MariaDB après interrogation des métriques. Le calcul peut effectuer une opération d’augmentation ou de réduction d'échelle. Le stockage peut uniquement effectuer une opération d’augmentation d'échelle.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI. 

## <a name="sample-script"></a>Exemple de script
Mettez à jour le script avec votre ID d’abonnement.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Crée un serveur MariaDB qui héberge les bases de données. |
| [az mariadb server update](/cli/azure/mariadb/server#az-mariadb-server-update) | Met à jour les propriétés du serveur MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Affiche la valeur de métrique pour les ressources. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [le calcul et le stockage Azure Database for MariaDB](../concepts-pricing-tiers.md)
- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- En savoir plus sur [Azure CLI](/cli/azure)
