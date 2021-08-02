---
title: Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory
description: Utiliser différentes méthodes pour résoudre des problèmes de déclencheurs de pipeline dans Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 04/01/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: aaaa9f2e82bb8db0ce4851359d7fb97d475f4e98
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111812732"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Une exécution du pipeline dans Azure Data Factory définit une instance d’une exécution du pipeline. Supposons par exemple que vous disposez d’un pipeline qui s’exécute à 8h00, 9h00 et 10h00. Dans ce cas, il y aura trois exécutions de pipeline distinctes. Chaque exécution de pipeline possède un ID d’exécution de pipeline unique. Un ID d’exécution est un identificateur global unique (GUID) qui définit cette exécution de pipeline spécifique.

Les exécutions de pipeline sont généralement instanciées en transmettant des arguments aux paramètres que vous définissez dans les pipelines. Vous pouvez exécuter un pipeline manuellement ou via un déclencheur. Pour plus de détails, consultez [Exécution et déclencheurs de pipeline dans Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="common-issues-causes-and-solutions"></a>Problèmes, causes et solutions courants

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Un pipeline de l’application Azure Functions lève une erreur liée à une connexion de point de terminaison privé
 
Data Factory et l’application de fonction s’exécutent sur un point de terminaison privé dans Azure. Vous essayez d’exécuter un pipeline qui interagit avec l’application de fonction. Vous avez essayé trois méthodes différentes, mais l’une retourne l’erreur « Requête incorrecte » et les deux autres méthodes retournent l’erreur « 103 Erreur Interdit ».

**Cause**

Data Factory ne prend actuellement pas en charge un connecteur de point de terminaison privé pour les applications de fonction. Azure Functions rejette les appels, car il est configuré pour autoriser uniquement les connexions à partir d’une liaison privée.

**Résolution :**

Créez un point de terminaison **PrivateLinkService** et fournissez le DNS de votre application de fonction.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>L’exécution du pipeline est annulée mais le moniteur continue d’afficher l’état de progression

**Cause**

Quand vous annulez l’exécution d’un pipeline, la surveillance de celui-ci continue souvent d’afficher l’état de progression. Cela est dû à un problème de cache du navigateur. Vous ne disposez peut-être pas des filtres appropriés pour la surveillance.

**Résolution :**

Actualisez le navigateur et appliquez les filtres appropriés pour la surveillance.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Une erreur « DelimitedTextMoreColumnsThanDefined » s’affiche lors de la copie d’un pipeline
 
 **Cause**
 
Si un dossier que vous copiez contient des fichiers avec des schémas différents, comme un nombre variable de colonnes, des délimiteurs ou paramètres de guillemets différents, ou des problèmes quelconques liés aux données, le pipeline Azure Data Factory peut lever l’erreur suivante :

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Résolution :**

Sélectionnez l’option **Copie binaire** lors de la création de l’activité Copy. De cette façon, pour les copies en bloc ou la migration de vos données d’un lac de données vers un autre, Data Factory n’ouvre pas les fichiers pour lire le schéma. Au lieu de cela, Data Factory traite chaque fichier sous forme binaire et le copie dans l’autre emplacement.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>L’exécution de pipeline échoue quand vous atteignez la limite de capacité du runtime d’intégration pour le flux de données

**Problème**

Message d’erreur :

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Cause**

Vous avez atteint la limite de capacité du runtime d’intégration. Vous exécutez peut-être une grande quantité de flux de données simultanément à l’aide du même runtime d’intégration. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2).

**Résolution :**
 
- Exécutez vos pipelines à différents moments de déclenchement.
- Créez un nouveau runtime d’intégration et répartissez vos pipelines sur plusieurs runtimes d’intégration.

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>Erreur d’exécution du pipeline lors de l’appel d’API REST dans une activité web

**Problème**

Message d’erreur :

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**Cause**

Les pipelines peuvent utiliser l’activité web pour appeler les méthodes de l’API REST ADF si et seulement si le rôle contributeur est attribué au membre Azure Data Factory. Vous devez d’abord configurer l’ajout de l’identité gérée Azure Data Factory au rôle de sécurité contributeur. 

**Résolution :**

Avant d’utiliser l’API REST d’Azure Data Factory sous l’onglet Paramètres d’une activité web, la sécurité doit être configurée. Des pipelines Azure Data Factory peuvent utiliser l’activité web pour appeler les méthodes de l’API REST ADF si et seulement si le rôle *Contributeur* est attribué à l’identité managée Azure Data Factory. Commencez par ouvrir le portail Azure et cliquer sur le lien **Toutes les ressources** dans le menu de gauche. Sélectionnez **Azure Data Factory** pour ajouter l’identité managée ADF avec le rôle Contributeur en cliquant sur le bouton **Ajouter** dans la zone *Ajouter une attribution de rôle*.


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>Comment vérifier et créer des branches de réussite et d’échec au niveau de l’activité dans les pipelines

**Cause**

Une orchestration Azure Data Factory autorise une logique conditionnelle et permet aux utilisateurs d’emprunter différents chemins d’accès en fonction du résultat d’une activité précédente. Elle autorise quatre chemins d’accès conditionnels : **En cas de réussite** (réussite par défaut), **En cas d’échec**, **En cas d’achèvement** et **En cas d’omission**. 

Azure Data Factory évalue le résultat de toutes les activités au niveau feuille. Les résultats du pipeline sont réussis uniquement si toutes les feuilles réussissent. Si une activité de feuille a été ignorée, nous évaluons son activité parente à la place. 

**Résolution :**

* Implémentez les contrôles au niveau de l’activité en procédant de la manière décrite dans [Comment gérer les échecs et erreurs de pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Utilisez Azure Logic Apps pour surveiller les pipelines à intervalles réguliers en procédant de la manière décrite dans [Query By Factory](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Surveiller visuellement le pipeline](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Guide pratique pour surveiller les échecs de pipeline à intervalles réguliers

**Cause**

Vous devrez peut-être surveiller les pipelines Data Factory à intervalles réguliers, par exemple, de 5 minutes. Vous pouvez interroger et filtrer les exécutions du pipeline à partir d’une fabrique de données à l’aide du point de terminaison. 

**Résolution :**
* Vous pouvez configurer une application logique Azure pour interroger tous les pipelines ayant échoué toutes les 5 minutes, en procédant de la manière décrite dans [Query By Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Ensuite, vous pouvez signaler des incidents à votre système de tickets.
* [Surveiller visuellement le pipeline](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Augmenter le degré de parallélisme n’entraîne pas un débit plus élevé

**Cause** 

Le degré de parallélisme dans *ForEach* correspond en fait au degré maximal de parallélisme. Nous ne pouvons pas garantir le lancement simultané d’un nombre spécifique d’exécutions, mais ce paramètre garantit que la valeur définie ne sera jamais dépassée. Considérez ce paramètre comme une limite à utiliser lors du contrôle de l’accès simultané à vos sources et récepteurs.

Faits connus concernant *ForEach*
 * Foreach comporte une propriété appelée batch count(n), où la valeur par défaut est 20 et la valeur maximale est 50.
 * Le nombre de lots (batch count), n, sert à construire n files d’attente. 
 * Chaque file d’attente s’exécute séquentiellement, mais plusieurs files d’attente peuvent s’exécuter en parallèle.
 * Les files d’attente sont créées au préalable. Cela signifie qu’il n’y a aucun rééquilibrage des files d’attente pendant l’exécution.
 * À tout moment, un élément au maximum est traité par file d’attente. Cela signifie qu’au maximum n éléments sont traités à un moment donné.
 * Le temps de traitement total de foreach est égal au temps de traitement de la file d’attente la plus longue. Cela signifie que l’activité foreach dépend de la façon dont les files d’attente sont construites.
 
**Résolution :**

 * Vous ne devez pas utiliser l’activité *SetVariable* dans une commande *For Each* exécutée en parallèle.
 * En tenant compte de la façon dont les files d’attente sont construites, le client peut améliorer les performances de Foreach en définissant plusieurs commandes *Foreach* ayant chacune des éléments dont le temps de traitement est similaire. 
 * Cela permet de s’assurer que les longues exécutions sont traitées en parallèle plutôt que séquentiellement.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>L’état du pipeline est mis en file d’attente ou bloqué pendant une longue période
 
 **Cause**
 
 Cela peut se produire pour diverses raisons, telles que l’atteinte des limites de concurrence, les interruptions de service, les pannes réseau, etc.
 
 **Résolution :**
 
* Limite de concurrenciel : si votre pipeline a une stratégie de concurrencel, vérifiez qu’il n’y a pas d’anciennes exécutions de pipeline en cours. La concurrence de pipeline maximale autorisée dans Azure Data Factory est de 10 pipelines. 
* Limites de surveillance : accédez au canevas de création ADF, sélectionnez votre pipeline, puis déterminez si une propriété de concurrence lui est assignée. Si c’est le cas, accédez à la page de surveillance et assurez-vous qu’il n’y a aucune exécution en cours sur les 45 derniers jours. Si vous trouvez une exécution en cours, vous pouvez l’annuler. Cela aura pour effet de démarrer la nouvelle exécution de pipeline.
* Problèmes temporaires : il est possible que votre exécution ait été impactée par un problème réseau temporaire, des échecs des informations d’identification, des interruptions de service, etc.  Dans ce cas, Azure Data Factory possède un processus de récupération interne qui supervise toutes les exécutions et les démarre en cas de problème. Ce processus est exécuté toutes les heures. Si votre exécution reste bloquée pendant plus d’une heure, créez un cas de support.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Heures de démarrage plus longues pour les activités dans la copie ADF et Data Flow

**Cause**

Cela peut se produire si vous n’avez pas implémenté la fonctionnalité temps réel pour Data Flow ou SHIR optimisé.

**Résolution :**

* Si le démarrage de chaque activité de copie prend jusqu’à 2 minutes et que le problème se produit principalement à la jonction du réseau virtuel (plutôt que dans le runtime d’intégration Azure), il peut s’agir d’un problème de performances de copie. Pour passer en revue les étapes de dépannage, accédez à [Amélioration des performances de copie.](./copy-activity-performance-troubleshooting.md)
* Vous pouvez utiliser la fonctionnalité temps réel pour réduire le temps de démarrage du cluster pour les activités de flux de données. Consultez [Runtime d'intégration Data Flow](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime).

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Atteinte des problèmes de capacité dans le runtime d'intégration auto-hébergé (SHIR, Self Hosted Integration Runtime)
 
 **Cause**
 
Cela peut se produire si vous n’avez pas effectué de scale-up du SHIR en fonction de votre charge de travail.

**Résolution :**

* Si vous rencontrez un problème de capacité provenant du runtime d’intégration auto-hébergé, mettez à niveau la machine virtuelle pour augmenter le nœud afin d’équilibrer les activités. Si vous recevez un message d’erreur relatif à une erreur ou défaillance générale de l’IR auto-hébergé, à une mise à niveau de l’IR auto-hébergé ou à des problèmes de connectivité de l’IR auto-hébergé, ce qui peut générer une longue file d’attente, consultez [Résoudre les problèmes liés au runtime d’intégration auto-hébergé](./self-hosted-integration-runtime-troubleshoot-guide.md).

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Messages d’erreur dus à des files d’attente longues pour la copie ADF et Data Flow

**Cause**

Des messages d’erreur liés à de longues files d’attente peuvent apparaître pour différentes raisons. 

**Résolution :**
* Si vous recevez un message d’erreur de n’importe quelle source ou destination via des connecteurs, ce qui peut générer une longue file d’attente, accédez au [Guide de résolution des problèmes de connecteur](./connector-troubleshoot-guide.md).
* Si vous recevez un message d’erreur sur le flux de données de mappage, ce qui peut générer une longue file d’attente, accédez au [Guide de résolution des problèmes de flux de données](./data-flow-troubleshoot-guide.md).
* Si vous recevez un message d’erreur sur d’autres activités, telles que Databricks, les activités personnalisées ou HDI, ce qui peut générer une longue file d’attente, accédez au [Guide de résolution des problèmes d’activité](./data-factory-troubleshoot-guide.md).
* Si vous recevez un message d’erreur sur l’exécution des packages SSIS, ce qui peut générer une longue file d’attente, accédez au [Guide de résolution des problèmes d’exécution de package Azure-SSIS](./ssis-integration-runtime-ssis-activity-faq.md) et le [Guide de résolution des problèmes de gestion du runtime d’intégration.](./ssis-integration-runtime-management-troubleshoot.md)

### <a name="error-message---codebadrequest-messagenull"></a>Message d’erreur : "code":"BadRequest", "message":"null"

**Cause**

Il s’agit d’une erreur de l’utilisateur, car la charge utile JSON qui atteint management.azure.com est corrompue. Aucun journal ne sera stocké, car l’appel de l’utilisateur n’a pas atteint la couche de service ADF.

**Résolution :**

Effectuez le traçage réseau de votre appel d’API à partir du portail ADF à l’aide des **outils de développement** du navigateur Edge ou Chrome. Vous verrez la charge utile JSON incriminée, qui peut être due à des caractères spéciaux (par exemple $), des espaces et d’autres types d’entrées utilisateur. Une fois que vous avez corrigé l’expression de la chaîne, vous continuez avec les autres appels d’utilisation d’ADF dans le navigateur.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
