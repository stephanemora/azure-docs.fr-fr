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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418427"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pour conteneurs - Forum Aux Questions (FAQ)
Ce FAQ Microsoft est une liste de questions fréquemment posées au sujet d'Azure Monitor pour conteneurs (préversion). Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Pourquoi les données n'apparaissent-elles pas lorsque l'espace de travail Log Analytics est configuré sur le niveau tarifaire Gratuit ? 

Vous avez peut-être atteint la limite par défaut de 500 Mo ou spécifié un plafond quotidien pour contrôler le volume de données à collecter. Pour vérifier et gérer l'utilisation des données, consultez [Consigner l'utilisation et le coût des données](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Quels sont les états des conteneurs spécifiés dans la table ContainerInventory ?
La table ContainerInventory contient des informations sur les conteneurs arrêtés et en cours d'exécution. La table est renseignée par un flux de travail situé au sein de l'agent qui interroge le docker pour tous les conteneurs (en cours d'exécution et arrêtés) et transmet ces données à l'espace de travail Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Comment puis-je résoudre les erreurs de type **Inscription d'abonnement manquante pour Microsoft.OperationsManagement** ?
Pour résoudre ce type d'erreur, inscrivez le fournisseur de ressources **Microsoft.OperationsManagement** dans l'abonnement où l'espace de travail est défini. La documentation contenant la procédure à suivre est disponible [ici](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Azure Monitor pour conteneurs inclut-il la prise en charge des clusters AKS avec activation du contrôle RBAC ?
Pour le moment, la solution ne prend pas en charge les clusters AKS avec activation du contrôle RBAC. La page des détails de la solution peut ne pas afficher les bonnes informations sur les panneaux qui présentent les données de ces clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Comment puis-je activer la collecte des journaux pour les conteneurs de l'espace de noms kube-system via Helm ?
Par défaut, la collecte des journaux des conteneurs de l'espace de noms kube-system est désactivée. La collecte des journaux peut être activée en définissant une variable d'environnement sur l'agent omsagent. Pour plus d'informations, consultez la page GitHub [Azure Monitor pour conteneurs](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers). 

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

Lorsque vous interrogerez les données dans Azure Monitor pour les journaux, celles-ci se présenteront comme suit :

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Pour en savoir plus, utilisez le [lien GitHub](https://github.com/moby/moby/issues/22920) suivant.

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Comment puis-je résoudre les erreurs Azure Active Directory lorsque j'active les journaux dynamiques ? 
L'erreur suivante peut apparaître : **L'URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l'application : '60b4dec7-5a69-4165-a211-12c40b5c0435'**. Vous trouverez le correctif correspondant dans l'article [Affichage des journaux de conteneur en temps réel avec Azure Monitor pour conteneurs](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Étapes suivantes
Pour commencer la surveillance de votre cluster AKS, consultez [Comment intégrer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance. 