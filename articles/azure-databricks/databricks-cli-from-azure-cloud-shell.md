---
title: 'Utiliser la CLI Databricks à partir d’Azure Cloud Shell '
description: Découvrez comment utiliser la CLI Databricks à partir d’Azure Cloud Shell pour effectuer des opérations sur Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605719"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Utiliser la CLI Databricks à partir d’Azure Cloud Shell

Découvrez comment utiliser la CLI Databricks à partir d’Azure Cloud Shell pour effectuer des opérations sur Databricks.

## <a name="prerequisites"></a>Conditions préalables requises

* Un cluster et un espace de travail Azure Databricks. Pour obtenir des instructions, consultez [Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure](quickstart-create-databricks-workspace-portal.md). 

* Configurez un jeton d’accès personnel dans Databricks. Pour obtenir des instructions, consultez [Generate token](/azure/databricks/dev-tools/api/latest/authentication) (Générer un jeton).

## <a name="use-the-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

1. Connectez-vous au [portail Azure](https://portal.azure.com).
 
2. Dans l’angle supérieur droit, cliquez sur l’icône **Cloud Shell**.

   ![Lancer Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Lancement d’Azure Cloud Shell")

3. Veillez à sélectionner **Bash** comme environnement Cloud Shell. Vous pouvez effectuer la sélection à partir de l’option de liste déroulante, comme indiqué dans la capture d’écran suivante.

   ![Sélectionner Bash comme environnement Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Sélectionnez Bash") 

4. Créez un environnement virtuel dans lequel vous pouvez installer la CLI Databricks. Dans l’extrait de code ci-dessous, vous créez un environnement virtuel appelé `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Basculez vers l’environnement virtuel que vous avez créé.

       source databrickscli/bin/activate

6. Installez la CLI Databricks.

       pip install databricks-cli

7. Configurez l’authentification avec Databricks en utilisant le jeton d’accès que vous devez avoir créé, répertorié comme faisant partie des composants requis. Utilisez la commande suivante :

       databricks configure --token

    Les invites suivantes s’affichent :

    * Tout d’abord, vous êtes invité à saisir l’hôte Databricks. Saisissez la valeur au format `https://eastus2.azuredatabricks.net`. Ici, **USA Est 2** correspond à la région Azure où vous avez créé votre espace de travail Azure Databricks.

    * Ensuite, vous êtes invité à saisir un jeton. Entrez le jeton que vous avez créé précédemment.

Après avoir terminé ces étapes, vous pouvez commencer à utiliser la CLI Databricks à partir d’Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Utiliser la CLI Databricks

Vous pouvez maintenant démarrer à l’aide de la CLI Databricks. Par exemple, exécutez la commande suivante pour répertorier tous les clusters Databricks que vous avez dans votre espace de travail.

    databricks clusters list

Vous pouvez également utiliser la commande suivante pour accéder au système de fichiers Databricks.

    databricks fs ls


Pour obtenir une référence complète sur les commandes, consultez [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (CLI Databricks).


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Azure CLI, consultez [Azure CLI overview](../cloud-shell/overview.md) (Vue d’ensemble d’Azure CLI)
* Pour obtenir une liste de commandes pour Azure CLI, consultez la [référence sur Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Pour obtenir une liste de commandes pour Databricks CLI, consultez [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (CLI Databricks)


