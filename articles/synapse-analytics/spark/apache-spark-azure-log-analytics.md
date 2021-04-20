---
title: Utiliser Azure Log Analytics pour collecter et visualiser les métriques et les journaux (préversion)
description: Découvrez comment activer le connecteur Azure Log Analytics intégré avec Synapse pour collecter et envoyer les métriques et les journaux de l’application Apache Spark à votre espace de travail Azure Log Analytics.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107727"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Tutoriel : Utiliser Azure Log Analytics pour collecter et visualiser les métriques et les journaux (préversion)

Dans ce tutoriel, vous allez découvrir comment activer le connecteur Azure Log Analytics intégré avec Synapse pour collecter et envoyer les métriques et les journaux de l’application Apache Spark à votre [espace de travail Azure Log Analytics](/azure/azure-monitor/logs/quick-create-workspace). Vous pouvez ensuite tirer profit d’un classeur Azure Monitor pour visualiser les métriques et les journaux.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Configurer les informations de l’espace de travail Azure Log Analytics dans Synapse Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Étape 1 : Créer un espace de travail Azure Log Analytics

Vous pouvez suivre les documents ci-dessous pour créer un espace de travail Log Analytics :
- [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Créer un espace de travail Log Analytics avec Azure CLI](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Créer et configurer un espace de travail Log Analytics dans Azure Monitor à l’aide de PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Étape 2 : Préparer un fichier config Spark

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Option 1. Effectuer la configuration avec l’ID et la clé d’espace de travail Azure Log Analytics 

Copiez la configuration Spark suivante, enregistrez-la sous **« spark_loganalytics_conf.txt »** , puis indiquez les paramètres :

   - `<LOG_ANALYTICS_WORKSPACE_ID>` : ID d’espace de travail Azure Log Analytics.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>` : Clé Azure Log Analytics : **Portail Azure > Espace de travail Azure Log Analytics > Gestion des agents > Clé primaire**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Option 2. Effectuer la configuration avec un coffre Azure Key Vault

> [!NOTE]
>
> Vous devez octroyer une autorisation de lecture des secrets aux utilisateurs qui soumettent des applications Spark. Consultez [Donner accès aux clés, certificats et secrets du coffre de clés avec un contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Pour configurer un coffre Azure Key Vault afin de stocker la clé d’espace de travail, effectuez les étapes suivantes :

1. Créez et accédez à votre coffre de clés dans le portail Azure
2. Dans les pages des paramètres de coffre de clés, sélectionnez **Secrets**.
3. Cliquez sur **Generate/Import (Générer/Importer)** .
4. Dans l’écran **Create a secret (Créer un secret)** , choisissez les valeurs suivantes :
   - **Nom** : tapez le nom du secret. Tapez `"SparkLogAnalyticsSecret"` en tant que valeur par défaut.
   - **Valeur** : tapez la **<CLÉ_ESPACE_TRAVAIL_LOG_ANALYTICS>** pour le secret.
   - Conservez les valeurs par défaut des autres options. Cliquez sur **Créer**.
5. Copiez la configuration Spark suivante, enregistrez-la sous **« spark_loganalytics_conf.txt »** , puis indiquez les paramètres :

   - `<LOG_ANALYTICS_WORKSPACE_ID>` : ID d’espace de travail Azure Log Analytics.
   - `<AZURE_KEY_VAULT_NAME>` : nom du coffre Azure Key Vault que vous avez configuré.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (facultatif) : nom du secret dans le coffre Azure Key Vault pour la clé d’espace de travail. Valeur par défaut : « SparkLogAnalyticsSecret ».

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Vous pouvez également stocker l’ID d’espace de travail Log Analytics dans Azure Key Vault. Consultez les étapes ci-dessus pour stocker l’ID d’espace de travail avec le nom de secret `"SparkLogAnalyticsWorkspaceId"`. Ou utilisez la configuration `spark.synapse.logAnalytics.keyVault.key.workspaceId` pour spécifier le nom du secret de l’ID d’espace de travail dans Azure Key Vault.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Option 3. Effectuer la configuration avec un service lié à Azure Key Vault

> [!NOTE]
>
> Vous devez octroyer une autorisation de lecture des secrets pour l’espace de travail Synapse. Consultez [Donner accès aux clés, certificats et secrets du coffre de clés avec un contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Pour configurer un service lié à Azure Key Vault dans Synapse Studio afin de stocker la clé d’espace de travail, effectuez les étapes suivantes :

1. Suivez toutes les étapes de la section `Option 2. Configure with an Azure Key Vault`.
2. Créez un service lié à Azure Key Vault dans Synapse Studio :

    a. Accédez à **Synapse Studio > Gérer > Services liés**, puis cliquez sur le bouton **Nouveau**.

    b. Recherchez **Azure Key Vault** dans la zone de recherche.

    c. Tapez le nom du service lié.

    d. Choisissez votre coffre de clés Azure. Cliquez sur **Créer**.

3. Ajoutez un élément `spark.synapse.logAnalytics.keyVault.linkedServiceName` à la configuration Spark.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Configuration Spark disponible

| Nom de la configuration                                  | Valeur par défaut                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | Pour activer le récepteur Azure Log Analytics des applications Spark, affectez la valeur true. Sinon, False.                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | ID d’espace de travail Azure Log Analytics de destination                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | Secret de l’espace de travail Azure Log Analytics de destination.                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Nom du service lié à Azure Key Vault pour l’ID et la clé d’espace de travail Azure Log Analytics                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Nom du coffre Azure Key Vault pour l’ID et la clé Azure Log Analytics                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Nom du secret dans Azure Key Vault pour l’ID d’espace de travail Azure Log Analytics                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Nom du secret dans Azure Key Vault pour la clé d’espace de travail Azure Log Analytics                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.uriSuffix       | ods.opinsights.azure.com     | [Suffixe d’URI][uri_suffix] d’espace de travail Azure Log Analytics de destination. Si votre espace de travail Azure Log Analytics n’est pas dans Azure global, vous devez mettre à jour le suffixe d’URI en fonction du cloud correspondant. |

> [!NOTE]  
> - Pour les clouds Azure Chine, le paramètre « spark.synapse.logAnalytics.keyVault.uriSuffix » doit être « ods.opinsights.azure.cn ». 
> - Pour les clouds Azure Government, le paramètre « spark.synapse.logAnalytics.keyVault.uriSuffix » doit être « ods.opinsights.azure.us ». 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Étape 3 : Charger votre configuration Spark dans un pool Spark
Vous pouvez charger le fichier config vers votre pool Synapse Spark dans Synapse Studio.

   1. Accédez à votre pool Apache Spark dans Azure Synapse Studio (Gérer -> Pools Apache Spark)
   2. Cliquez sur le bouton «  **...**  » à droite de votre pool Apache Spark
   3. Sélectionnez la configuration Apache Spark 
   4. Cliquez sur **Charger**, puis choisissez le fichier « **spark_loganalytics_conf.txt** » créé.
   5. Cliquez sur **Charger**, puis sur **Appliquer**.

      > [!div class="mx-imgBorder"]
      > ![configuration du pool Spark](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Toute application Spark soumise au pool Spark ci-dessus utilise le paramètre de configuration pour envoyer les métriques et les journaux de l’application Spark vers l’espace de travail Azure Log Analytics que vous avez spécifié.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Envoyer une application Spark et voir les journaux et les métriques dans Azure Log Analytics

 1. Vous pouvez soumettre une application Spark au pool Spark configuré à l’étape précédente, en utilisant l’une des méthodes suivantes :
    - Exécutez un notebook Synapse Studio. 
    - Soumettez un traitement par lots Synapse Apache Spark via la définition de tâche Spark.
    - Exécutez un pipeline qui contient une activité Spark.

 2. Accédez à l’espace de travail Azure Log Analytics spécifié, puis visualisez les métriques et les journaux de l’application quand l’application Spark commence à s’exécuter.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Utiliser l’exemple de classeur Azure Log Analytics pour visualiser les métriques et les journaux

1. [Téléchargez le classeur](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) ici.
2. Ouvrez et **copiez** le contenu du fichier de classeur.
3. Accédez au classeur Azure Log Analytics ([Portail Azure](https://portal.azure.com/) > Espace de travail Log Analytics > Classeurs)
4. Ouvrez le classeur Azure Log Analytics **Vide** en mode **Éditeur avancé** (appuyez sur l’icône </>).
5. **Collez** tout fichier JSON existant.
6. Appuyez ensuite sur **Appliquer**, puis sur **Fin de l’édition**.

    > [!div class="mx-imgBorder"]
    > ![nouveau classeur](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![importer un classeur](./media/apache-spark-azure-log-analytics/import-workbook.png)

Soumettez ensuite votre application Apache Spark au pool Spark configuré. Une fois l’application passée à l’état en cours d’exécution, choisissez-la dans la liste déroulante relative aux classeurs.

> [!div class="mx-imgBorder"]
> ![image de classeur](./media/apache-spark-azure-log-analytics/workbook.png)

Vous pouvez personnaliser le classeur à l’aide d’une requête Kusto et configurer des alertes.

> [!div class="mx-imgBorder"]
> ![alertes et requêtes Kusto](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

1. Exemple illustrant l’interrogation d’événements Spark.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Exemple illustrant l’interrogation de journaux d’exécuteurs et de pilotes d’application Spark.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Exemple illustrant l’interrogation de métriques Spark.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Créer et gérer des alertes à l’aide d’Azure Log Analytics

Les alertes Azure Monitor permettent aux utilisateurs d’exécuter une requête Log Analytics pour évaluer les métriques et les journaux à une fréquence définie, et de déclencher une alerte en fonction des résultats.

Pour plus d’informations, consultez [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Limitation

 - L’espace de travail Azure Synapse Analytics avec activation d’un [réseau virtuel managé](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) n’est pas pris en charge.
 - Les régions suivantes ne sont pas prises en charge :
   - USA Est 2
   - Norvège Est
   - Émirats arabes unis Nord

## <a name="next-steps"></a>Étapes suivantes

 - Découvrez comment [utiliser le pool Apache Spark serverless dans Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Découvrez comment [exécuter une application Spark dans un notebook](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Découvrez comment [créer une définition de tâche Apache Spark dans Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).