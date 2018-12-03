---
title: 'Script Azure CLI : Modifier les configurations de serveur'
description: Cet exemple de script CLI répertorie toutes les configurations de serveur disponibles et met à jour la valeur de innodb_lock_wait_timeout.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: a2104b4a2a80dc7ca3f76edb6757a59fcc6c7a4f
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585161"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Lister et mettre à jour les configurations d’un serveur Azure Database for MariaDB à l’aide d’Azure CLI
Cet exemple de script CLI liste tous les paramètres de configuration disponibles ainsi que les valeurs autorisées pour un serveur Azure Database for MariaDB. En outre, il définit *innodb_lock_wait_timeout* sur une valeur autre que celle par défaut.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI. 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Crée un serveur MariaDB qui héberge les bases de données. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Liste les configurations d’un serveur Azure Database for MariaDB. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Met à jour la configuration d’un serveur Azure Database for MariaDB. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Affiche la configuration d’un serveur Azure Database for MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur les paramètres de serveur, consultez [Guide pratique pour configurer les paramètres de serveur dans Azure Database for MariaDB](../howto-server-parameters.md).
