---
title: Journalisation d’instance de conteneur avec Azure Log Analytics
description: Découvrez comment envoyer la sortie de conteneur (STDOUT et STDERR) vers Azure Log Analytics.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 06/06/2018
ms.author: marsma
ms.openlocfilehash: a0772d1009021ca64b448710c5353407a5492fae
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809863"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Journalisation d’instance de conteneur avec Azure Log Analytics

Les espaces de travail Log Analytics fournissent un emplacement centralisé pour le stockage et l’interrogation des données de journaux, non seulement à partir de ressources Azure, mais également de ressources locales et dans d’autres clouds. Azure Container Instances inclut la prise en charge de l’envoi de données vers Log Analytics.

Pour envoyer des données d’instance de conteneur à Log Analytics, vous devez créer un groupe de conteneurs à l’aide d’interface de ligne de commande Azure (ou Cloud Shell) et d’un fichier YAML. Les sections suivantes décrivent la création d’un groupe de conteneurs dans lequel la journalisation et l’interrogation des journaux sont activées.

## <a name="prerequisites"></a>Prérequis

Pour activer la journalisation dans vos instances de conteneur, vous avez besoin des éléments suivants :

* [Espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obtenir les informations d’identification de Log Analytics

Azure Container Instances doit disposer d’une autorisation pour envoyer des données à votre espace de travail Log Analytics. Pour accorder cette autorisation et activer la journalisation, vous devez fournir l’ID d’espace de travail Log Analytics et une de ses clés (principale ou secondaire) lorsque vous créez le groupe de conteneurs.

Pour obtenir l’ID et la clé primaire de l’espace de travail Log Analytics :

1. Accédez à votre espace de travail Log Analytics dans le portail Azure
1. Sous **PARAMÈTRES**, sélectionnez **Paramètres avancés**
1. Sélectionnez **Sources connectées** > **Serveurs Windows** (ou **Serveurs Linux**, l’ID et les clés sont les mêmes pour les deux)
1. Notez :
   * **ID DE L’ESPACE DE TRAVAIL**
   * **CLÉ PRIMAIRE**

## <a name="create-container-group"></a>Créer un groupe de conteneurs

Maintenant que vous disposez de l’ID d’espace de travail Log Analytics et de la clé primaire, vous êtes prêt à créer un groupe de conteneurs dans lequel la journalisation est activée. L’exemple suivant crée un groupe de conteneurs à un seul conteneur [fluentd][fluentd]. Le conteneur Fluentd produit plusieurs lignes de sortie dans sa configuration par défaut. Cette sortie étant envoyée à votre espace de travail Log Analytics, elle convient parfaitement à l’affichage et à l’interrogation des journaux.

Copiez tout d’abord le fichier YAML suivant, qui définit un groupe de conteneurs avec un seul conteneur, dans un nouveau fichier. Remplacez `LOG_ANALYTICS_WORKSPACE_ID` et `LOG_ANALYTICS_WORKSPACE_KEY` par les valeurs obtenues à l’étape précédente, puis enregistrez le fichier sous **deploy-aci.yaml**.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Exécutez ensuite la commande suivante pour déployer le groupe de conteneurs. Remplacez `myResourceGroup` par un groupe de ressources dans votre abonnement (ou créez tout d’abord un groupe de ressources nommé « myResourceGroup ») :

```azurecli-interactive
az container create -g myResourceGroup -n mycontainergroup001 -f deploy-aci.yaml
```

Vous devriez recevoir une réponse à partir des détails du déploiement de conteneur Azure peu de temps après l’émission de la commande.

## <a name="view-logs-in-log-analytics"></a>Afficher les journaux dans Log Analytics

Une fois que vous avez déployé le groupe de conteneurs, l’affichage dans le portail Azure des premières entrées de journal peut prendre plusieurs minutes (jusqu’à 10). Pour afficher les journaux du groupe de conteneurs, ouvrez votre espace de travail Log Analytics, puis :

1. Dans la vue d’ensemble de l’**espace de travail OMS**, sélectionnez **Recherche dans les journaux**
1. Sous **Quelques requêtes supplémentaires à essayer**, sélectionnez le lien **Toutes les données collectées**

Vous devez voir plusieurs résultats affichés par la requête `search *`. Si vous ne voyez aucun résultat dans un premier temps, patientez quelques minutes, puis sélectionnez le bouton **Exécuter** pour réexécuter la requête. Par défaut, les entrées de journal sont affichées dans la vue « Liste » : sélectionnez **Table** pour afficher les entrées de journal dans un format plus condensé. Vous pouvez ensuite développer une ligne pour afficher le contenu d’une entrée de journal.

![Résultats de recherche dans les journaux dans le portail Azure][log-search-01]

## <a name="query-container-logs"></a>Interroger les journaux d’un conteneur

Log Analytics inclut un [langage de requête][query_lang] étendu pour extraire des informations de milliers de lignes de sortie de journal.

L’agent de journalisation d’Azure Container Instances envoie des entrées vers la table `ContainerInstanceLog_CL` dans votre espace de travail Log Analytics. La structure de base d’une requête est la table source (`ContainerInstanceLog_CL`) suivie d’une série d’opérateurs séparés par une barre verticale (`|`). Vous pouvez chaîner plusieurs opérateurs pour affiner les résultats et effectuer des fonctions avancées.

Pour afficher un exemple de résultats de requête, collez la requête suivante dans la zone de texte de requête (sous « Show legacy language converter » [Afficher le convertisseur de langue hérité], puis sélectionnez le bouton **Exécuter** pour exécuter la requête. Cette requête affiche toutes les entrées de journal dont le champ « Message » contient le mot « Avertissement » :

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Des requêtes plus complexes sont également prises en charge. Par exemple, cette requête n’affiche que les entrées de journal pour le groupe de conteneurs « mycontainergroup001 » générées au cours de la dernière heure :

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="log-analytics"></a>Log Analytics

Pour plus d’informations sur l’interrogation des journaux et la configuration d’alertes dans Azure Log Analytics, consultez :

* [Présentation des recherches dans les journaux dans Log Analytics](../log-analytics/log-analytics-log-search.md)
* [Alertes unifiées dans Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

### <a name="monitor-container-cpu-and-memory"></a>Surveiller le processeur et la mémoire du conteneur

Pour plus d’informations sur la surveillance des ressources processeur et mémoire de l’instance de conteneur, consultez :

* [Surveiller les ressources des conteneurs dans Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://docs.loganalytics.io/

<!-- LINKS - Internal -->