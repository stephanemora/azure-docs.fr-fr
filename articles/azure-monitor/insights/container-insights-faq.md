---
title: Azure Monitor pour conteneurs - Forum Aux Questions (FAQ) | Microsoft Docs
description: Azure Monitor pour conteneurs est une solution qui supervise l'intégrité de vos clusters AKS et de vos instances de conteneur dans Azure. Cet article apporte des réponses aux questions les plus fréquentes.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0984de51221c506bb1824e4dcfd93eef56453a4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405081"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pour conteneurs - Forum Aux Questions (FAQ)

Ce FAQ Microsoft est une liste de questions fréquemment posées au sujet d'Azure Monitor pour conteneurs (préversion). Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Je ne vois pas les valeurs de propriété Image et Name renseignées quand j’interroge la table ContainerLog.

Pour la version de l’agent ciprod12042019 et les versions ultérieures, par défaut, ces deux propriétés ne sont pas remplies pour chaque ligne de journal afin de réduire les coûts liés aux données de journal collectées. Il existe deux options pour interroger la table qui inclut ces propriétés avec leurs valeurs :

### <a name="option-1"></a>Option 1 : 

Joignez d’autres tables pour inclure ces valeurs de propriétés dans les résultats.

Modifiez vos requêtes pour inclure les propriétés Image et ImageTag à partir de la table ```ContainerInventory``` en joignant la propriété ContainerID. Vous pouvez inclure la propriété Name (telle qu’elle apparaissait dans la table ```ContainerLog```) à partir du champ ContaineName de la table KubepodInventory en effectuant une jointure sur la propriété ContainerID. Il s’agit de l’option recommandée.

L’exemple suivant illustre une requête détaillée qui explique comment obtenir ces valeurs de champs avec des jointures.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>Option 2 :

Réactivez la collecte pour ces propriétés pour chaque ligne de journal de conteneur.

Si la première option n’est pas pratique en raison des modifications de requête qu’elle implique, vous pouvez réactiver la collecte de ces champs en activant le paramètre ```log_collection_settings.enrich_container_logs``` dans le mappage de configuration de l’agent, comme décrit dans les [paramètres de configuration de la collecte de données](./container-insights-agent-config.md).

> [!NOTE]
> La seconde option n’est pas recommandée avec les grands clusters qui contiennent plus de 50 nœuds, car elle génère des appels de serveur d’API à partir de chaque nœud du cluster pour effectuer cet enrichissement. En outre, cette option augmente la taille des données pour chaque ligne de journal collectée.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Puis-je consulter les métriques collectées dans Grafana ?

Azure Monitor pour conteneurs prend en charge la consultation des métriques stockées dans votre espace de travail Log Analytics, dans les tableaux de bord Grafana. Nous avons fourni un modèle que vous pouvez télécharger à partir du [référentiel des tableaux de bord](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) de Grafana pour vous aider à démarrer, et une référence permettant de vous familiariser avec l’interrogation des données supplémentaires depuis vos clusters supervisés, pour une visualisation dans des tableaux de bord Grafana personnalisés. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Puis-je surveiller mon cluster AKS-Engine avec Azure Monitor pour les conteneurs ?

Azure Monitor pour conteneurs prend en charge la surveillance des charges de travail de conteneur déployées sur un ou plusieurs clusters AKS-Engine (anciennement ACS-Engine) hébergés sur Azure. Pour plus d’informations et une vue d’ensemble des étapes requises pour l’activation de la surveillance dans le cadre de ce scénario, voir [Utilisation d’Azure Monitor pour conteneurs pour AKS-Engine ](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Pourquoi est-ce que je ne vois pas de données dans mon espace de travail Log Analytics ?

Si vous ne voyez pas de données dans l’espace de travail Log Analytics à un moment donné de la journée, il est possible que vous ayez atteint la limite de 500 Mo par défaut ou la limite quotidienne spécifiée pour contrôler la quantité de données à collecter chaque jour. Lorsque la limite est atteinte pour la journée, la collecte de données s’interrompt et ne reprend que sur le jour suivant. Pour vérifier votre utilisation des données et mettre à jour vers un autre niveau tarifaire en fonction de vos modèles d’utilisation anticipée, consultez [Log data usage and cost](../platform/manage-cost-storage.md) (Utilisation et coût de données de journal). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quels sont les états des conteneurs spécifiés dans la table ContainerInventory ?

La table ContainerInventory contient des informations sur les conteneurs arrêtés et en cours d'exécution. La table est renseignée par un flux de travail situé au sein de l’agent qui interroge le docker pour tous les conteneurs (en cours d’exécution et arrêtés) et transmet ces données à l’espace de travail Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Comment puis-je résoudre l’erreur *Missing Subscription registration* (Inscription d’abonnement manquante) ?

Si vous recevez l’erreur **Missing Subscription registration** (Inscription d’abonnement manquante), vous pouvez y remédier en inscrivant le fournisseur de ressources **Microsoft.OperationsManagement** dans l’abonnement où l’espace de travail est défini. La documentation contenant la procédure à suivre est disponible [ici](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Existe-t-il une prise en charge pour les clusters AKS avec RBAC activé ?

La solution Container Monitoring ne prend pas en charge RBAC, mais il est pris en charge avec Azure Monitor pour conteneurs. La page des détails de la solution peut ne pas afficher les bonnes informations sur les panneaux qui présentent les données de ces clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Comment puis-je activer la collecte des journaux pour les conteneurs de l'espace de noms kube-system via Helm ?

Par défaut, la collecte des journaux des conteneurs de l'espace de noms kube-system est désactivée. La collecte des journaux peut être activée en définissant une variable d'environnement sur l'agent omsagent. Pour plus d’informations, consultez la page GitHub [Azure Monitor pour conteneurs](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers). 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Comment mettre à jour l'agent omsagent vers la dernière version disponible ?

Pour savoir comment procéder à la mise à niveau de l'agent, consultez [Gestion de l'agent](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Comment puis-je activer la journalisation multiligne?

Pour le moment, Azure Monitor pour conteneurs ne prend pas en charge la journalisation multiligne, mais il existe des solutions de contournement. Vous pouvez configurer tous les services sur le format JSON. Docker/Moby utilisera ensuite une seule ligne pour l'écriture.

Par exemple, vous pouvez inclure votre journal dans un objet JSON, comme indiqué dans l'exemple d'application node.js ci-dessous :

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Lorsque vous interrogerez les données dans Azure Monitor pour les journaux d’activité, celles-ci se présenteront comme dans l’exemple suivant :

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Pour en savoir plus sur ce problème, utilisez le [lien GitHub](https://github.com/moby/moby/issues/22920) suivant.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Comment puis-je résoudre les erreurs Azure AD lorsque j’active les journaux d’activité dynamiques ? 

L'erreur suivante peut apparaître : **L’URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l’application : « <ID application\> »** . Sa solution se trouve dans l'article [Affichage des données de conteneur en temps réel avec Azure Monitor pour conteneurs](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Pourquoi ne puis-je pas mettre à niveau un cluster après l’intégration ?

Après avoir activé Azure Monitor pour conteneurs pour un cluster AKS, si vous supprimez l’espace de travail Log Analytics auquel le cluster envoyait ses données, le cluster échoue lorsque vous tentez de le mettre à niveau. Pour contourner ce problème, vous devez désactiver la supervision, puis la réactiver en faisant référence à un autre espace de travail valide dans votre abonnement. Lorsque vous réessayez de mettre le cluster à niveau, il doit être traité et s’effectuer correctement.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quels ports et domaines dois-je ouvrir/mettre sur liste verte pour l’agent ?

Pour plus d'informations sur les configurations de proxy et de pare-feu requises pour l'agent conteneurisé avec les clouds Azure, Azure US Government et Azure China, consultez la [Configuration requise pour le pare-feu réseau](container-insights-onboard.md#network-firewall-requirements).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment intégrer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance. 
