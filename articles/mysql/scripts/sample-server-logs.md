---
title: 'Script Azure CLI : Télécharger des journaux d’activité de serveurs dans Azure Database pour MySQL'
description: Cet exemple de script Azure CLI montre comment activer et télécharger les journaux d’activité de serveur pour un seul serveur Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 7cb28d22dbd1fb7fcefbc16d01f55f4785a5932d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544496"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Activer et télécharger les journaux d’activité de requête serveur lents d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI
Cet exemple de script CLI montre comment activer et télécharger les journaux d’activité de serveur lents d’un seul serveur Azure Database pour MySQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI. 

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
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Répertoriez les valeurs de configuration pour un serveur. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Mettez à jour la configuration d’un serveur. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) | Répertoriez les fichiers journaux pour un serveur. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) | Téléchargez les fichiers journaux. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- Découvrez-en plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Essayez d’autres scripts : [Exemples de CLI Azure pour Azure Database pour MySQL](../sample-scripts-azure-cli.md)
