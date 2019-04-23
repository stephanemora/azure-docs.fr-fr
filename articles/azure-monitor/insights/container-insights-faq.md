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
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999720"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pour conteneurs - Forum Aux Questions (FAQ)

Ce FAQ Microsoft est une liste de questions fréquemment posées au sujet d'Azure Monitor pour conteneurs (préversion). Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Pourquoi ne pas voir les données dans mon espace de travail Analytique de journal ?

Si vous ne pouvez pas voir toutes les données dans l’espace de travail Analytique de journal à une certaine tous les jours de temps, vous avez peut-être atteint la limite de 500 Mo par défaut ou la limite quotidienne spécifié pour contrôler la quantité de données à collecter tous les jours. Lorsque la limite est atteinte pour la journée, la collecte de données s’interrompt et reprend uniquement sur le jour suivant. Pour vérifier votre utilisation des données et mettre à jour vers un autre niveau tarifaire en fonction de vos modèles d’utilisation anticipée, consultez [consigner l’utilisation des données et coût](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quelles sont les États de conteneur spécifiés dans la table ContainerInventory ?

La table ContainerInventory contient des informations sur les conteneurs arrêtés et en cours d'exécution. La table est renseignée par un flux de travail situé au sein de l’agent qui interroge le docker pour tous les conteneurs (en cours d’exécution et arrêtés) et transmet ces données à l’espace de travail Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Comment résoudre **inscription de l’abonnement manquant** erreur ?

Si vous recevez l’erreur **l’enregistrement d’abonnement manquant pour Microsoft.OperationsManagement**, vous pouvez y remédier en inscrivant le fournisseur de ressources **Microsoft.OperationsManagement** dans le abonnement dans lequel l’espace de travail est défini. La documentation contenant la procédure à suivre est disponible [ici](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Existe-t-il une prise en charge pour les clusters AKS compatibles avec le RBAC ?

La solution Container Monitoring ne prend pas en charge RBAC, mais il est pris en charge avec Azure Monitor pour les conteneurs. La page des détails de la solution peut ne pas afficher les bonnes informations sur les panneaux qui présentent les données de ces clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Comment puis-je activer la collecte des journaux pour les conteneurs de l'espace de noms kube-system via Helm ?

Par défaut, la collecte des journaux des conteneurs de l'espace de noms kube-system est désactivée. La collecte des journaux peut être activée en définissant une variable d'environnement sur l'agent omsagent. Pour plus d’informations, consultez le [Azure Monitor pour les conteneurs](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) page GitHub. 

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

Ces données ressemblera à l’exemple suivant dans Azure Monitor pour les journaux lorsque vous recherchez :

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Pour en savoir plus, utilisez le [lien GitHub](https://github.com/moby/moby/issues/22920) suivant.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Comment résoudre des erreurs d’Azure AD quand activer les journaux en direct ? 

L'erreur suivante peut apparaître : **La réponse à l’url spécifiée dans la demande ne correspond pas à l’URL de réponse configurées pour l’application : « < ID d’application\>'**. Vous trouverez la solution pour résoudre le problème dans l’article [comment afficher le conteneur journaux en temps réel avec Azure Monitor pour les conteneurs](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Pourquoi ne puis-je pas passer cluster après l’intégration ?

Si après avoir activé Azure Monitor pour les conteneurs d’un cluster AKS, vous supprimez l’espace de travail Analytique de journal du cluster a été envoyant ses données, lorsque vous tentez de mettre à niveau le cluster échouera. Pour contourner ce problème, vous devrez désactiver l’analyse, puis réactiver faisant référence à un autre espace de travail valide dans votre abonnement. Lorsque vous essayez d’effectuer la mise à niveau de cluster à nouveau, il doit traiter et terminer avec succès.  

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment intégrer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance. 