---
title: Tâche Azure Pipelines pour un serveur flexible Azure Database pour PostgreSQL
description: Activer la tâche CLI de serveur flexible Azure Database pour PostgreSQL pour l’utilisation avec Azure Pipelines
ms.topic: quickstart
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.service: postgresql
ms.date: 08/09/2021
ms.openlocfilehash: 747a1c59d00ac4127fab4b6c28d02c837e540133
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122516002"
---
# <a name="azure-pipelines-task-for-azure-database-for-postgresql-flexible-server"></a>Tâche Azure Pipelines pour un serveur flexible Azure Database pour PostgreSQL

Avec les **Pipelines Azure**, vous pouvez déployer automatiquement vos mises à jour de base de données sur un serveur flexible Azure Database pour PostgreSQL après chaque build réussi.  Vous pouvez utiliser une tâche Azure CLI pour mettre à jour la base de données avec un fichier SQL ou un script SQL inline sur la base de données. Cette tâche peut être exécutée sur des agents multiplateformes qui, eux-mêmes, s’exécutent sur des systèmes d’exploitation Linux, macOS ou Windows.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/free/).
- [Connexion de service Azure Resource Manager](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) à votre compte Azure
- Azure CLI est préinstallée sur les agents hébergés par Microsoft. Toutefois, si vous utilisez des agents privés, [installez Azure CLI](/cli/azure/install-azure-cli) sur le ou les ordinateurs qui exécutent l’agent de build et de mise en production. Si un agent est déjà en cours d’exécution sur l’ordinateur où Azure CLI est installée, redémarrez-le pour vous assurer que toutes les variables intermédiaires pertinentes sont mises à jour.
- Créer une instance Azure Database pour PostgreSQL - Serveur flexible à l’aide du [portail Azure](./quickstart-create-server-portal.md) ou d’[Azure CLI](./quickstart-create-server-cli.md)


## <a name="use-sql-file"></a>Utiliser un fichier SQL

L’exemple suivant montre comment passer des arguments de base de données et exécuter la commande ```execute```  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az postgres flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>Utiliser un script SQL inline

L’exemple suivant montre comment exécuter un script SQL inline à l’aide de la commande ```execute```.

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription 
      az postgres flexible-server execute --name $(SERVERNAME) --admin-user $(DBUSER) \
      --admin-password '$(DBPASSWORD)'  --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>Entrées de tâche

Vous pouvez voir la liste complète de toutes les entrées de tâche lors de l’utilisation de la tâche Azure CLI avec Azure Pipelines. 

| Paramètre            | Description         | 
| :------------------- | :-------------------|
| azureSubscription| (Obligatoire) Fournissez l’abonnement Azure Resource Manager pour le déploiement. Ce paramètre ne s’affiche que lorsque la version de la tâche sélectionnée est 0.*, car la tâche Azure CLI v1.0 ne prend en charge que les abonnements Azure Resource Manager. |
|scriptType| (Obligatoire) Indiquez le type de script. Les scripts pris en charge sont PowerShell, PowerShell Core, Bat, Shell et script. Lors de l’exécution sur un **agent Linux**, sélectionnez l’une des valeurs suivantes : ```bash``` ou ```pscore```. Lors de l’exécution sur un **agent Windows**, sélectionnez l’une des valeurs suivantes : ```batch```, ```ps``` et ```pscore```. |
|sriptLocation| (Obligatoire) Indiquez le chemin d’accès au script, par exemple le chemin d’accès au fichier réel ou utilisez ```Inline script``` lorsque vous fournissez les scripts inline. La valeur par défaut est ```scriptPath```. |
|scriptPath| (Obligatoire) Chemin d’accès complet au script (.ps1 ou .bat ou. cmd lors de l’utilisation d’un agent basé sur Windows, sinon <code>.ps1 </code> ou <code>.sh </code> lors de l’utilisation d’un agent Linux) ou chemin d’accès relatif au répertoire de travail par défaut. |
|inlineScript|(Obligatoire) Vous pouvez écrire vos scripts inline ici. Quand vous utilisez un agent Windows, utilisez PowerShell ou PowerShell Core ou des scripts par lots. Sinon, utilisez PowerShell Core ou des scripts shell lors de l’utilisation d’un agent basé sur Linux. Pour les fichiers de commandes, utilisez le préfixe \"call\" avant chaque commande Azure. Vous pouvez également passer des variables prédéfinies et personnalisées à ce script à l’aide d’arguments. <br/>Exemple pour PowerShell/PowerShellCore/shell :``` az --version az account show``` <br/>Exemple pour batch : ``` call az --version call az account show```. |
| arguments| (Facultatif) Fournissez tous les arguments passés au script. Par exemple, ```-SERVERNAME mydemoserver```. |
|powerShellErrorActionPreference| (Facultatif) Ajoute la ligne <b>$ErrorActionPreference = ’VALUE’</b> en haut de votre script PowerShell/PowerShell Core. La valeur par défaut est stop. Les valeurs prises en charge sont stop, continue et silentlyContinue. |
|addSpnToEnvironment|(Facultatif) Ajoute la clé et l’ID du principal de service du point de terminaison Azure que vous avez choisi à l’environnement d’exécution du script. Vous pouvez utiliser les variables suivantes : <b>$env:servicePrincipalId, $env:servicePrincipalKey and $env:tenantId</b> dans votre script. Cela est respecté uniquement lorsque le point de terminaison Azure a le schéma d’authentification du principal de service. La valeur par défaut est false.|
|useGlobalConfig|(Facultatif) Si la valeur est false, cette tâche utilise son propre <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">répertoire de configuration Azure CLI</a> distinct. Cela peut être utilisé pour exécuter des tâches Azure CLI dans des versions <b>parallèles</b> <br/>Valeur par défaut : false</td>
|workingDirectory| (Facultatif) Répertoire de travail actuel dans lequel le script est exécuté.  Empty est la racine du référentiel (build) ou des artefacts (mise en production), à savoir $(System.DefaultWorkingDirectory). |
|failOnStandardError|(Facultatif) Si la valeur est true, cette tâche échoue lorsque des erreurs sont écrites dans le flux StandardError. Désactivez la case à cocher pour ignorer les erreurs standard et consultez les codes de sortie pour déterminer l’état. La valeur par défaut est false.|
|powerShellIgnoreLASTEXITCODE| (Facultatif) Si la valeur est false, la ligne <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> est ajoutée à la fin de votre script. Le dernier code de sortie d’une commande externe est alors propagé en tant que code de sortie de PowerShell. Sinon, la ligne n’est pas ajoutée à la fin de votre script. La valeur par défaut est false. |

Si vous avez des problèmes avec la tâche CLI, consultez [Comment résoudre les problèmes de build et de mise en production](/azure/devops/pipelines/troubleshooting/troubleshooting?view=azure-devops).

## <a name="next-steps"></a>Étapes suivantes 
Voici quelques tâches associées qui peuvent être utilisées pour déployer avec Azure Piplelines.

- [Déploiement du groupe de ressources Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)
- [Déploiement d’une application web Azure](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment?view=azure-devops)

