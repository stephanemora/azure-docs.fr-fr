---
title: Tutoriel – Connecter et surveiller les métriques de l’application Azure Synapse Spark
description: Tutoriel – Découvrez comment intégrer votre serveur Prometheus local à l’espace de travail Azure Synapse pour obtenir les métriques de l’application Azure Spark en quasi-temps réel grâce au connecteur Synapse Prometheus.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 45ccced6f083e0d304651a0cea7df90c6396fd88
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143248"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Tutoriel : Connecter et surveiller les métriques de l’application Azure Synapse Spark

## <a name="overview"></a>Vue d’ensemble

Dans ce tutoriel, vous découvrirez comment intégrer votre serveur Prometheus local à l’espace de travail Azure Synapse pour obtenir les métriques de l’application Azure Spark en quasi-temps réel grâce au connecteur Synapse Prometheus. 

Ce tutoriel présente également les API de métriques REST Azure Synapse. Vous pouvez extraire les données de métriques de l’application Spark grâce aux API REST pour créer votre propre kit de ressources de surveillance et de diagnostic ou les intégrer à vos systèmes de surveillance.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Utiliser le connecteur Azure Synapse Prometheus pour vos serveurs Prometheus locaux

Le [connecteur Azure Synapse Prometheus](https://github.com/microsoft/azure-synapse-spark-metrics) est un projet open source. Le connecteur Synapse Prometheus utilise une méthode de découverte de service basée sur les fichiers pour vous permettre de :
 - Vous authentifier auprès de l’espace de travail Synapse à l’aide d’un principal du service AAD.
 - Récupérer la liste des applications Apache Spark de l’espace de travail. 
 - Extraire les métriques de l’application Spark à l’aide de la configuration basée sur les fichiers Prometheus. 

### <a name="1-prerequisite"></a>1. Configuration requise

Vous devez avoir un serveur Prometheus déployé sur une machine virtuelle Linux.

### <a name="2-create-a-service-principal"></a>2. Créer un principal du service

Pour utiliser le connecteur Azure Synapse Prometheus sur votre serveur Prometheus local, créez un principal de service en suivant les étapes ci-dessous.

#### <a name="21-create-a-service-principal"></a>2.1 Créer un principal de service :

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Le résultat doit avoir l’aspect suivant :

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Notez l’appId, le mot de passe et l’ID de locataire.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 Ajouter les autorisations correspondantes au principal de service créé lors de l’étape ci-dessus.

![capture d’écran octroi d’autorisation srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Connectez-vous à votre [espace de travail Azure Synapse Analytics](https://web.azuresynapse.net/) en tant qu’administrateur Synapse

2. Dans Synapse Studio, sélectionnez **Gérer > Contrôle d’accès** dans le volet de gauche

3. Cliquez sur le bouton Ajouter situé en haut à gauche pour **ajouter une attribution de rôle**

4. Pour Étendue, choisissez **Espace de travail**

5. Pour Rôle, choisissez **Opérateur de capacité de calcul Synapse**

6. Pour Sélectionner un utilisateur, entrez votre **<service_principal_name>** et cliquez sur votre principal de service

7. Cliquez sur **Appliquer** (attendez 3 minutes pour que l’autorisation prenne effet.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Télécharger le connecteur Azure Synapse Prometheus

Utilisez les commandes pour installer le connecteur Azure Synapse Prometheus.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Créer un fichier config pour les espaces de travail Azure Synapse

Créez un fichier config.yaml dans le dossier config et remplissez les champs suivants : workspace_name, tenant_id, service_principal_name et service_principal_password.
Vous pouvez ajouter plusieurs espaces de travail dans la configuration yaml.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Mettre à jour la configuration Prometheus

Ajoutez la section de configuration suivante dans votre scrape_config Prometheus et remplacez <your_workspace_name> par le nom de votre espace de travail et <path_to_synapse_connector> par votre dossier synapse-prometheus-connector cloné

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Démarrer le connecteur dans la machine virtuelle du serveur Prometheus

Procédez comme suit pour démarrer un serveur de connecteur dans la machine virtuelle du serveur Prometheus.

```
python main.py
```

Le connecteur devrait fonctionner au bout de quelques secondes. Vous pouvez voir « synapse-prometheus-connector » dans la page de découverte du service Prometheus.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Utilisez Azure Synapse Prometheus ou les API de métriques REST pour collecter les données de métriques

### <a name="1-authentication"></a>1. Authentification
Vous pouvez utiliser le flux des informations d’identification du client pour obtenir un jeton d’accès. Pour accéder à l’API de métriques, vous devez obtenir un jeton d’accès Azure AD pour le principal de service, qui dispose de l’autorisation nécessaire pour accéder aux API.

| Paramètre     | Obligatoire | Description                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | True     | L’ID de locataire de votre principal de service (application) Azure                                                          |
| grant_type    | True     | Spécifie le type d’autorisation demandée. Dans un flux d'octroi d'informations d'identification de client, la valeur doit être client_credentials. |
| client_id     | True     | L’ID de l’application (principal de service) que vous avez inscrite dans le Portail Azure ou Azure CLI.        |
| client_secret | True     | Le secret généré pour l’application (principal de service)                                                  |
| resource      | True     | L’URI de ressource Synapse doit être « https://dev.azuresynapse.net  »                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

La réponse se présente ainsi :

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Liste des applications en cours d’exécution dans l’espace de travail Azure Synapse

Si vous souhaitez obtenir la liste des applications Spark pour un espace de travail Synapse, consultez ce document [Surveillance – Obtenir la liste des travaux Spark](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Collecter les métriques de l’application Spark avec les API REST ou Prometheus


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Collectez les métriques de l’application Spark avec les API Prometheus

Obtenez les dernières métriques de l’application Spark spécifiée grâce à l’API Prometheus

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Paramètre          | Obligatoire | Description                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | Point de terminaison de développement de l’espace de travail, par exemple, `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | Version valide de l’API pour la requête. Il s’agit actuellement de la préversion 2019-11-01                      |
| sparkPoolName      | True     | Nom du pool Spark.                                                                     |
| sessionID          | True     | Identificateur de la session.                                                                 |
| sparkApplicationId | True     | ID de l’application Spark                                                                        |

Exemple de demande : 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Exemple de réponse :

Code d’état : la réponse 200 se présente ainsi :

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Collecter les métriques de l’application Spark avec l’API REST

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Paramètre          | Obligatoire | Description                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | Point de terminaison de développement de l’espace de travail, par exemple, `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | Version valide de l’API pour la requête. Il s’agit actuellement de la préversion 2019-11-01                      |
| sparkPoolName      | True     | Nom du pool Spark.                                                                     |
| sessionID          | True     | Identificateur de la session.                                                                 |
| sparkApplicationId | True     | ID de l’application Spark                                                                        |

Exemple de demande

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Exemple du code d’état de la réponse : 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Créer vos propres outils de diagnostic et de surveillance

L’API Prometheus et les API REST fournissent des données de métriques enrichies sur les informations d’exécution de l’application Spark. Vous pouvez collecter les données de métriques relatives à l’application grâce à l’API Prometheus et aux API REST. Vous pouvez aussi créer vos propres outils de diagnostic et de surveillance qui sont plus adaptés à vos besoins.
