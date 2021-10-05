---
title: Meilleures pratiques relatives à Azure Functions
description: Découvrez les meilleures pratiques pour la conception, le déploiement et la gestion de code de fonction efficace exécuté dans Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f2efa490a9788f0e52b4dfb19b4359f247a671e9
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057716"
---
# <a name="best-practices-for-reliable-azure-functions"></a>Meilleures pratiques pour des Azure Functions fiables

Azure Functions est une expérience de calcul à la demande pilotée par les événements, qui étend la plateforme Azure App Service existante avec des capacités d’implémenter du code déclenché par des événements se produisant dans d’autres services Azure, dans les services tiers et dans les systèmes locaux. Functions vous permet de créer des solutions en vous connectant à des sources de données ou à des solutions de messagerie, ce qui facilite le traitement et la réaction aux événements. Les fonctions s’exécutent sur des centres de données Azure, qui sont complexes avec de nombreux composants intégrés. Dans un environnement cloud hébergé, les machines virtuelles peuvent parfois redémarrer ou se déplacer, et des mises à niveau système se produisent alors. Vos applications de fonction dépendent également probablement d’API externes, de services Azure et d’autres bases de données, qui peuvent également connaître des problèmes occasionnels de fiabilité. 

Cet article décrit les meilleures pratiques pour la conception et le déploiement d’applications de fonction efficaces qui demeurent saines et fonctionnent bien dans un environnement cloud.

## <a name="choose-the-correct-hosting-plan"></a>Choisir le bon plan d’hébergement 

Lorsque vous créez une Function App dans Azure, vous devez choisir un plan d’hébergement pour votre application. Le plan que vous choisissez a une incidence sur les performances, la fiabilité et les coûts. Trois plans d’hébergement De base sont disponibles pour Functions : 

+ [Plan Consommation](consumption-plan.md)
+ [Plan Premium](functions-premium-plan.md)
+ [Plan dédié (App Service)](dedicated-plan.md)

Tous les plans d’hébergement sont mis à la disposition générale (GA) si vous travaillez avec Linux ou Windows.

Dans le contexte de la plateforme App Service, le plan Premium utilisé pour héberger dynamiquement vos fonctions est le plan Premium élastique (EP). Il existe d’autres plans dédiés (App Service) appelés Premium. Pour en savoir plus, consultez l’article [Plan Premium](functions-premium-plan.md).

Le plan d’hébergement que vous choisissez détermine les comportements suivants :

+   Comment votre application de fonction est mise à l’échelle en fonction de la demande, et la façon dont l’allocation d’instances est gérée.
+   Les ressources disponibles pour chaque instance de Function App.
+   La prise en charge des fonctionnalités avancées, notamment la connectivité du réseau virtuel Azure.

Pour en savoir plus sur le choix du bon plan d’hébergement et pour une comparaison détaillée entre les plans, consultez [Options d’hébergement Azure Functions](functions-scale.md).

Il est important de choisir le plan approprié lorsque vous créez votre application de fonction. Functions offre une capacité limitée de basculer votre plan d’hébergement, principalement entre les plans Consommation et Premium élastique. Pour en savoir plus, consultez [Planifier la migration](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration). 

## <a name="configure-storage-correctly"></a>Configurer le stockage correctement

Functions nécessite qu’un compte de stockage soit associé à votre application de fonction. La connexion au compte de stockage est utilisée par l’hôte Functions pour des opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Elle est également utilisée lors de la mise à l’échelle dynamique des applications de fonction. Pour plus d’informations, consultez la section [Points importants relatifs au stockage pour Azure Functions](storage-considerations.md).

Un système de fichiers ou un compte de stockage mal configuré dans votre application de fonction peut affecter les performances et la disponibilité de vos fonctions. Pour obtenir de l’aide sur le dépannage d’un compte de stockage mal configuré, consultez l’article [Dépannage du stockage](functions-recover-storage-account.md). 

### <a name="storage-connection-settings"></a>Paramètres de connexion au stockage

Les applications de fonction qui évoluent dynamiquement peuvent s’exécuter soit à partir d’un point de terminaison Azure Files dans votre compte de stockage, soit à partir des serveurs de fichiers associés à vos instances scale-out. Ce comportement est contrôlé par les paramètres d’application suivants :

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

Ces paramètres sont uniquement pris en charge lorsque vous travaillez dans un plan Premium ou dans un plan Consommation sur Windows.

Lorsque vous créez votre application de fonction dans le portail Azure ou à l’aide d’Azure CLI ou d’Azure PowerShell, ces paramètres sont créés pour votre application de fonction si nécessaire. Lorsque vous créez vos ressources à partir d’un modèle Azure Resource Manager (modèle ARM), vous devez également inclure `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` dans le modèle. 

Lors de votre premier déploiement utilisant un modèle ARM, n’incluez pas `WEBSITE_CONTENTSHARE`, qui est généré pour vous.   

Vous pouvez utiliser les exemples de modèles ARM suivants pour vous aider à configurer correctement ces paramètres :

+ [Plan Consommation](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [Plan dédié](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [Plan Premium avec intégration au réseau virtuel](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [Plan de consommation avec un emplacement de déploiement](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>Configuration du compte de stockage

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Functions s’appuie sur Stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. La chaîne de connexion du compte de stockage pour votre application de fonction se trouve dans les paramètres d’application `AzureWebJobsStorage` et `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`.

Gardez à l’esprit les points suivants lors de la création de ce compte de stockage : 

+ Pour réduire la latence, créez le compte de stockage dans la même région que l’application de fonction.

+ Pour améliorer les performances en production, utilisez un compte de stockage distinct pour chaque application de fonction. Cela est particulièrement vrai avec les fonctions déclenchées par Durable Functions et Event Hub. 

+ Pour les fonctions déclenchées par Event Hub, n’utilisez pas un compte avec [Data Lake Storage activé](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81).

### <a name="handling-large-data-sets"></a>Gérer de grands jeux de données

En cas d’exécution sur Linux, vous pouvez ajouter du stockage supplémentaire en montant un partage de fichiers. Le montage d’un partage est un moyen pratique pour une fonction de traiter un grand jeu de données existant. Pour en savoir plus, consultez [Monter des partages de fichiers](storage-considerations.md#mount-file-shares).

## <a name="organize-your-functions"></a>Organiser vos fonctions 

Dans le cadre de votre solution, vous pouvez développer et publier plusieurs fonctions. Ces fonctions sont souvent combinées en une application de fonction unique, mais elles peuvent également être exécutées dans des applications de fonction distinctes. Dans les plans d’hébergement Premium et dédié (App Service), plusieurs applications de fonction peuvent également partager les mêmes ressources en s’exécutant dans le même plan. La façon dont vous regroupez vos fonctions et vos applications de fonction peut impacter les performances, la mise à l’échelle, la configuration, le déploiement et la sécurité de votre solution globale. 

Pour les plans Consommation et Premium, toutes les fonctions d’une application de fonction sont mises à l’échelle de manière dynamique.

Pour plus d’informations sur la façon d’organiser vos fonctions, consultez [Meilleures pratiques d’organisation des fonctions](performance-reliability.md#function-organization-best-practices).

## <a name="optimize-deployments"></a>Optimiser les déploiements

Lors du déploiement d’une application de fonction, il est important de garder à l’esprit que l’unité de déploiement pour les fonctions dans Azure est l’application de fonction. Toutes les fonctions d’une application de fonction sont déployées en même temps, généralement à partir du même package de déploiement.  

Envisagez les options suivantes pour un déploiement réussi :

+  Exécutez vos fonctions à partir du package de déploiement. Cette approche [d’exécution à partir du package](run-functions-from-deployment-package.md) offre les avantages suivants :

    + Réduction des risques de verrouillage lors de la copie de fichiers 
    + Possibilité de déployer directement dans une application de production, ce qui déclenche un redémarrage. 
    + Savoir que tous les fichiers du package sont disponibles pour votre application. 
    + Amélioration des performances des déploiements de modèle ARM.
    + Peut réduire les temps de démarrage à froid, en particulier pour les fonctions JavaScript avec des arborescences de package npm de grande taille.

+ Envisagez d’utiliser le [déploiement continu](functions-continuous-deployment.md) pour connecter les déploiements à votre solution de contrôle de code source. Les déploiements continus vous permettent également d’exécuter à partir du package de déploiement.

+ Pour l’[hébergement du plan Premium](functions-premium-plan.md), envisagez d’ajouter un déclencheur de préchauffage pour réduire la latence lorsque de nouvelles instances sont ajoutées. Pour en savoir plus, consultez [Déclencheur de préchauffage Azure Functions](functions-bindings-warmup.md). 

## <a name="write-robust-functions"></a>Écrire des fonctions robustes

Il existe plusieurs principes de conception que vous pouvez suivre lors de l’écriture de votre code de fonction, afin de vous aider à améliorer les performances générales et la disponibilité de vos fonctions. Ces principes sont les suivants :
 
+ [Évitez les fonctions dont l’exécution prend beaucoup de temps.](performance-reliability.md#avoid-long-running-functions) 
+ [Planifiez la communication entre fonctions.](performance-reliability.md#cross-function-communication) 
+ [Écrivez des fonctions sans état.](performance-reliability.md#write-functions-to-be-stateless)
+ [Écrivez des fonctions défensives.](performance-reliability.md#write-defensive-functions)

Les défaillances transitoires étant courantes dans le cloud computing, vous devez utiliser un [modèle de nouvelle tentative](/azure/architecture/patterns/retry) lorsque vous accédez à des ressources cloud. De nombreux déclencheurs et liaisons implémentent déjà la nouvelle tentative. 

## <a name="design-for-security"></a>Conception dans l’optique de la sécurité

La sécurité est à envisager de préférence pendant la phase de planification, et non une fois que vos fonctions sont prêtes à l’emploi. Pour savoir comment développer et déployer des fonctions de façon sécurisée, consultez [Sécuriser Azure Functions](security-concepts.md).  

## <a name="consider-concurrency"></a>Prendre en compte la concurrence

À mesure que la demande grandit sur votre application de fonction suite à des événements entrants, les applications de fonction en cours d’exécution et les plans Premium sont mis à l’échelle. Il est important de comprendre comment votre application de fonction répond à la charge et comment des déclencheurs peuvent être configurés pour gérer les événements entrants. Pour une présentation générale, consultez [Mise à l’échelle pilotée par les événements dans Azure Functions](event-driven-scaling.md).

Les plans dédiés (App Service) nécessitent que vous fournissiez la montée en charge de vos applications de fonction. 

### <a name="worker-process-count"></a>Nombre de processus Worker

Dans certains cas, il est plus efficace de gérer la charge en créant plusieurs processus, appelés processus Worker de langage, dans l’instance avant le scale-out. Le nombre maximal de processus Worker de langage autorisés est contrôlé par le paramètre [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). La valeur par défaut de ce paramètre est `1`, ce qui signifie que les processus multiples ne sont pas utilisés. Une fois le nombre maximal de processus atteint, l’application de fonction est mise à l’échelle vers plus d’instances pour gérer la charge. Ce paramètre ne s’applique pas aux [fonctions de la bibliothèque de classes C#](functions-dotnet-class-library.md), qui s’exécutent dans le processus hôte.

Si vous utilisez `FUNCTIONS_WORKER_PROCESS_COUNT` sur un plan Premium ou dédié (App Service), gardez à l’esprit le nombre de cœurs fournis par votre plan. Par exemple, le plan Premium `EP2` fournit deux cœurs. Vous devez donc commencer par une valeur de `2` et une augmentation de deux au nécessaire, jusqu’à la limite maximale.

### <a name="trigger-configuration"></a>Configuration de déclencheur

Lors de la planification du débit et de la mise à l’échelle, il est important de comprendre comment les différents types de déclencheurs traitent les événements. Certains déclencheurs vous permettent de contrôler les comportements de traitement par lot et de gérer l’accès concurrentiel. Souvent l’ajustement des valeurs de ces options peut permettre la mise à l’échelle adéquate de chaque instance face aux demandes des fonctions appelées. Ces options de configuration sont appliquées à tous les déclencheurs dans une application de fonction et sont conservées dans le fichier host.json de l’application. Pour plus d’informations sur les paramètres, consultez la section Configuration de la référence du déclencheur spécifique.

Pour en savoir plus sur la façon dont Functions traite les flux de messages, consultez [Traitement des événements fiables d’Azure Functions](functions-reliable-event-processing.md).

### <a name="plan-for-connections"></a>Planifier les connexions

Les applications de fonction qui s’exécutent dans le [plan Consommation](consumption-plan.md) sont soumises aux limites de connexion. Ces limites sont appliquées au niveau de chaque instance. En raison de ces limites et à titre meilleure pratique générale, vous devez optimiser vos connexions sortantes à partir de votre code de fonction. Pour plus d’informations, consultez la rubrique [Gérer les connexions dans Azure Functions](manage-connections.md). 

### <a name="language-specific-considerations"></a>Considérations spécifiques au langage

Pour le langage de votre choix, gardez à l’esprit les points suivants :

# <a name="c"></a>[C#](#tab/csharp)

+ [Utilisez du code asynchrone tout en évitant de bloquer les appels](performance-reliability.md#use-async-code-but-avoid-blocking-calls).

+ [Utilisez des jetons d’annulation](functions-dotnet-class-library.md?#cancellation-tokens) (in-process uniquement).

# <a name="java"></a>[Java](#tab/java)

+ Pour les applications qui combinent des opérations liées à l’UC et aux E/S, envisagez d’utiliser [davantage de processus Worker](functions-app-settings.md#functions_worker_process_count).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [Utilisez `async` et `await`](functions-reference-node.md#use-async-and-await).

+ [Utilisez plusieurs processus Worker pour les applications liées à l’UC](functions-reference-node.md?tabs=v2#scaling-and-concurrency).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Passez en revue les considérations relatives à l’accès concurrentiel](functions-reference-powershell.md#concurrency).

# <a name="python"></a>[Python](#tab/python)

+ [Améliorer les performances de débit des applications Python dans Azure Functions](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>Optimiser la disponibilité

Le démarrage à froid est un facteur clé pour les architectures serverless. Pour plus d’informations, consultez [Démarrage à froid](event-driven-scaling.md#cold-start). Si le démarrage à froid est un problème pour votre scénario, vous pouvez trouver une présentation approfondie sur le [Démarrage à froid serverless](https://azure.microsoft.com/blog/understanding-serverless-cold-start/). 

Le plan Premium est recommandé pour réduire le démarrage à froid, tout en maintenant la mise à l’échelle dynamique. Vous pouvez utiliser les conseils suivants pour réduire le démarrage à froid et améliorer la disponibilité dans les trois plans d’hébergement. 

| Planifier | Guidance |
| --- | --- | 
| **Plan Premium** | • [Implémenter un déclencheur de préchauffage dans votre application fonctionnelle](functions-bindings-warmup.md)<br/>• [Définir les valeurs des instances toujours prêtes et le débit maximum de rafale](functions-premium-plan.md#plan-and-sku-settings)<br/>• [Utiliser la prise en charge des déclencheurs de réseau virtuel lors de l’utilisation de déclencheurs non-HTTP sur un réseau virtuel](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **Plans dédiés** | • [Exécuter sur au moins deux instances avec Azure App Service Health Check activé](../app-service/monitor-instances-health-check.md)<br/>• [Implémenter la mise à l’échelle automatique](/azure/architecture/best-practices/auto-scaling)|
| **Plan Consommation** | • Passez en revue l’utilisation des modèles Singleton et des paramètres de concurrence pour les liaisons et les déclencheurs afin d’éviter de placer artificiellement des limites sur la mise à l’échelle de votre application de fonction.<br/>• [Vérifiez le paramètre `functionAppScaleLimit`, qui peut limiter la mise à l’échelle](event-driven-scaling.md#limit-scale-out)<br/>• Vérifiez la limite du quota d’utilisation quotidienne (en Go/s) définie lors du développement et du test. Envisagez de supprimer cette limite dans les environnements de production. |

## <a name="monitor-effectively"></a>Surveiller efficacement

Azure Functions offre une intégration avec Azure Application Insights pour surveiller l’exécution de vos fonctions et les traces écrites par votre code. Pour en savoir plus, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). Azure Monitor fournit également des fonctionnalités permettant de surveiller l’intégrité de l’application de fonction elle-même. Pour en savoir plus, consultez [Utilisation d’Azure Monitor Metrics avec Azure Functions](monitor-metrics.md).

Tenez compte des points suivants lors de l’utilisation de l’intégration Application Insights pour surveiller vos fonctions :

+ Assurez-vous que le paramètre d’application [AzureWebJobsDashboard](functions-app-settings.md#azurewebjobsdashboard) est supprimé. Ce paramètre était pris en charge dans les versions antérieures de Functions. S’il existe, la suppression de `AzureWebJobsDashboard` améliore les performances de vos fonctions. 

+  Consultez les [journaux Application Insights](analyze-telemetry-data.md). Si les données que vous souhaitez trouver sont manquantes, envisagez d’ajuster les paramètres d’échantillonnage pour mieux capturer votre scénario de surveillance. Vous pouvez utiliser le paramètre `excludedTypes` pour exclure certains types de l’échantillonnage, comme `Request` ou `Exception`. Pour plus d’informations, consultez [Configurer l’échantillonnage](configure-monitoring.md?tabs=v2#configure-sampling).

Azure Functions vous permet également d’[envoyer les journaux générés par le système et par l’utilisateur dans les journaux Azure Monitor](functions-monitor-log-analytics.md). L’intégration avec les journaux de Azure Monitor est actuellement en préversion. 

## <a name="build-in-redundancy"></a>Intégrer la redondance

Les besoins de votre entreprise peuvent nécessiter que vos fonctions soient toujours disponibles, même pendant une panne du centre de données. Pour savoir comment utiliser une approche à plusieurs régions pour maintenir les fonctions critiques en état d’exécution, consultez [Géo-reprise d’activité après sinistre dans Azure Functions](functions-geo-disaster-recovery.md).

## <a name="next-steps"></a>Étapes suivantes

[Gérer votre application de fonction](functions-how-to-use-azure-function-app-settings.md)
