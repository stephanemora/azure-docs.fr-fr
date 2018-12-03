---
title: 'Script Azure CLI : Créer une instance d’Azure Database for MariaDB'
description: Cet exemple de script CLI crée un serveur Azure Database for MariaDB et configure une règle de pare-feu au niveau du serveur.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 80609035933e45ea9b7cc911a908f6ed24b9f6a7
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585241"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer un serveur MariaDB et configurer une règle de pare-feu à l’aide d’Azure CLI
Cet exemple de script CLI crée un serveur Azure Database for MariaDB et configure une règle de pare-feu au niveau du serveur. Une fois que le script s’exécute correctement, le serveur MariaDB est accessible par tous les services Azure et par l’adresse IP configurée.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI.

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Crée un serveur MariaDB qui héberge les bases de données. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | Crée une règle de pare-feu pour autoriser l’accès au serveur et aux bases de données qui s’y trouvent à partir de la plage d’adresses IP entrée. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database for MariaDB](../sample-scripts-azure-cli.md)
