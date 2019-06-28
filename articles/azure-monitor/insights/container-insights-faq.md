---
title: Azure Monitor pour conteneurs - Forum Aux Questions (FAQ) | Microsoft Docs
description: Azure Monitor pour conteneurs est une solution qui supervise l'intégrité de vos clusters AKS et de vos instances de conteneur dans Azure. Cet article apporte des réponses aux questions les plus fréquentes.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780003"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pour conteneurs - Forum Aux Questions (FAQ)

Ce FAQ Microsoft est une liste de questions fréquemment posées au sujet d'Azure Monitor pour conteneurs (préversion). Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Pourquoi est-ce que je ne vois pas de données dans mon espace de travail Log Analytics ?

Si vous ne voyez pas de données dans l’espace de travail Log Analytics à un moment donné de la journée, il est possible que vous ayez atteint la limite de 500 Mo par défaut ou la limite quotidienne spécifiée pour contrôler la quantité de données à collecter chaque jour. Lorsque la limite est atteinte pour la journée, la collecte de données s’interrompt et ne reprend que sur le jour suivant. Pour vérifier votre utilisation des données et mettre à jour vers un autre niveau tarifaire en fonction de vos modèles d’utilisation anticipée, consultez [Log data usage and cost](../platform/manage-cost-storage.md) (Utilisation et coût de données de journal). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quels sont les états des conteneurs spécifiés dans la table ContainerInventory ?

La table ContainerInventory contient des informations sur les conteneurs arrêtés et en cours d'exécution. La table est renseignée par un flux de travail situé au sein de l’agent qui interroge le docker pour tous les conteneurs (en cours d’exécution et arrêtés) et transmet ces données à l’espace de travail Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Comment puis-je résoudre l’erreur **Missing Subscription registration** (Inscription d’abonnement manquante) ?

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

Pour en savoir plus, utilisez le [lien GitHub](https://github.com/moby/moby/issues/22920) suivant.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Comment puis-je résoudre les erreurs Azure AD lorsque j’active les journaux d’activité dynamiques ? 

L'erreur suivante peut apparaître : **L’URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l’application : « <ID application\> »** . Sa solution se trouve dans l’article [Affichage des journaux d’activité de conteneur en temps réel avec Azure Monitor pour conteneurs](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Pourquoi ne puis-je pas mettre à niveau un cluster après l’intégration ?

Après avoir activé Azure Monitor pour conteneurs pour un cluster AKS, si vous supprimez l’espace de travail Log Analytics auquel le cluster envoyait ses données, le cluster échoue lorsque vous tentez de le mettre à niveau. Pour contourner ce problème, vous devez désactiver la supervision, puis la réactiver en faisant référence à un autre espace de travail valide dans votre abonnement. Lorsque vous réessayez de mettre le cluster à niveau, il doit être traité et s’effectuer correctement.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quels ports et domaines dois-je ouvrir/mettre sur liste verte pour l’agent ?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment intégrer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance. 
