---
title: 'Script Azure CLI : Mettre à l’échelle et superviser Azure Database pour PostgreSQL'
description: Exemple de script Azure CLI - Mettre à l’échelle le serveur Azure Database pour PostgreSQL vers un autre niveau de performances après interrogation des métriques.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: b0a17b12c51c08cd39d0c4fb7860021b8c68e606
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606400"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Surveiller et mettre à l’échelle d’un seul serveur PostgreSQL à l’aide de la CLI Azure
Cet exemple de script CLI met à l’échelle le calcul et le stockage d’un serveur Azure Database pour PostgreSQL après interrogation des métriques. Le calcul peut effectuer une opération d’augmentation ou de réduction d'échelle. Le stockage peut uniquement effectuer une opération d’augmentation d'échelle. 

> [!IMPORTANT] 
> Le stockage peut seulement monter en puissance.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article demande la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="sample-script"></a>Exemple de script
Mettez à jour le script avec votre ID d’abonnement.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Crée un serveur PostgreSQL qui héberge les bases de données. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Met à jour les propriétés du serveur PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Affiche la valeur de métrique pour les ressources. |
| [az group delete](/cli/azure/group) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [le calcul et le stockage Azure Database pour PostgreSQL](../concepts-pricing-tiers.md)
- Essayez d’autres scripts : [Exemples Azure CLI pour base de données pour PostgreSQL](../sample-scripts-azure-cli.md)
- En savoir plus sur [Azure CLI](/cli/azure)
