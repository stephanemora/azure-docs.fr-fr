---
title: Script CLI - Télécharger les journaux de requêtes lentes - Azure Database pour MySQL
description: Cet exemple de script Azure CLI montre comment activer et télécharger les journaux d’activité de serveur pour un seul serveur Azure Database pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 9029856e1d131b9adb2af4437449c43e6a271749
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084639"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Activer et télécharger les journaux d’activité de requête serveur lents d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI

[[!INCLUDE[applies-to-mysql-single-flexible-server](../includes/applies-to-mysql-single-flexible-server.md)]

Cet exemple de script CLI montre comment activer et télécharger les journaux d’activité de serveur lents d’un seul serveur Azure Database pour MySQL.

[!INCLUDE[azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres. Dans les commandes `az monitor`, remplacez &lt;log_file_name&gt; par le nom de votre fichier journal de serveur.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Répertoriez les valeurs de configuration pour un serveur. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Mettez à jour la configuration d’un serveur. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | Répertoriez les fichiers journaux pour un serveur. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Téléchargez les fichiers journaux. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Essayez d’autres scripts : [Exemples de CLI Azure pour Azure Database pour MySQL](../sample-scripts-azure-cli.md)
