---
title: Script CLI - Mettre à l’échelle un serveur - Azure Database pour MySQL
description: Cet exemple de script CLI met à l’échelle un serveur Azure Database pour MySQL vers un nouveau niveau de performance après l’analyse des métriques.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 54082daeeee2d5cc894aee49e2c3456e377637f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771309"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL à l’aide de la CLI Azure
Cet exemple de script CLI met à l’échelle le calcul et le stockage d’un serveur Azure Database pour MySQL après interrogation des métriques. Le calcul peut effectuer une opération d’augmentation ou de réduction d'échelle. Le stockage peut uniquement effectuer une opération d’augmentation d'échelle.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI. 

## <a name="sample-script"></a>Exemple de script
Mettez à jour le script avec votre ID d’abonnement.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | Met à jour les propriétés du serveur MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Affiche la valeur de métrique pour les ressources. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [le calcul et le stockage Azure Database pour MySQL](../concepts-pricing-tiers.md)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour MySQL](../sample-scripts-azure-cli.md)
- En savoir plus sur [Azure CLI](/cli/azure)