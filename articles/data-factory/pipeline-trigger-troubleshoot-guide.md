---
title: Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory
description: Utiliser différentes méthodes pour résoudre des problèmes de déclencheurs de pipeline dans Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0ceee3c65e8c4df5d843bb441fb6426a0f4eb696
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220250"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Une exécution du pipeline dans Azure Data Factory définit une instance d’une exécution du pipeline. Supposons par exemple que vous disposez d’un pipeline qui s’exécute à 8h00, 9h00 et 10h00. Dans ce cas, il y aura trois exécutions de pipeline distinctes. Chaque exécution de pipeline possède un ID d’exécution de pipeline unique. Un ID d’exécution est un identificateur global unique (GUID) qui définit cette exécution de pipeline spécifique.

Les exécutions de pipeline sont généralement instanciées en transmettant des arguments aux paramètres que vous définissez dans les pipelines. Vous pouvez exécuter un pipeline manuellement ou via un déclencheur. Pour plus de détails, consultez [Exécution et déclencheurs de pipeline dans Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="common-issues-causes-and-solutions"></a>Problèmes, causes et solutions courants

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Un pipeline de l’application Azure Functions lève une erreur liée à une connexion de point de terminaison privé
 
Data Factory et l’application de fonction Azure s’exécutent sur un point de terminaison privé. Vous essayez d’exécuter un pipeline qui interagit avec l’application de fonction. Vous avez essayé trois méthodes différentes, mais l’une retourne l’erreur « Requête incorrecte » et les deux autres méthodes retournent l’erreur « 103 Erreur Interdit ».

**Cause** : Data Factory ne prend actuellement pas en charge un connecteur de point de terminaison privé pour les applications de fonction. Azure Functions rejette les appels, car il est configuré pour autoriser uniquement les connexions à partir d’une liaison privée.

**Résolution** : Créez un point de terminaison **PrivateLinkService** et fournissez le DNS de votre application de fonction.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>L’exécution du pipeline est annulée mais le moniteur continue d’afficher l’état de progression

Quand vous annulez l’exécution d’un pipeline, la surveillance de celui-ci continue souvent d’afficher l’état de progression. Cela est dû à un problème de cache du navigateur. Vous ne disposez peut-être pas des filtres appropriés pour la surveillance.

**Résolution** : Actualisez le navigateur et appliquez les filtres appropriés pour la surveillance.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Une erreur « DelimitedTextMoreColumnsThanDefined » s’affiche lors de la copie d’un pipeline
 
Si un dossier que vous copiez contient des fichiers avec des schémas différents, comme un nombre variable de colonnes, des délimiteurs ou paramètres de guillemets différents, ou des problèmes quelconques liés aux données, le pipeline Azure Data Factory peut lever l’erreur suivante :

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Résolution** : Sélectionnez l’option **Copie binaire** lors de la création de l’activité Copy. De cette façon, pour les copies en bloc ou la migration de vos données d’un lac de données vers un autre, Data Factory n’ouvre pas les fichiers pour lire le schéma. Au lieu de cela, Data Factory traite chaque fichier sous forme binaire et le copie dans l’autre emplacement.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>L’exécution du pipeline échoue quand vous atteignez la limite de capacité du runtime d’intégration

Message d’erreur :

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Cause** : Vous avez atteint la limite de capacité du runtime d’intégration. Vous exécutez peut-être une grande quantité de flux de données simultanément à l’aide du même runtime d’intégration. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2).

**Résolution** :
 
- Exécutez vos pipelines à différents moments de déclenchement.
- Créez un nouveau runtime d’intégration et répartissez vos pipelines sur plusieurs runtimes d’intégration.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Il existe des erreurs et échecs au niveau des activités dans les pipelines

Une orchestration Azure Data Factory autorise une logique conditionnelle et permet aux utilisateurs d’emprunter différents chemins d’accès en fonction du résultat d’une activité précédente. Elle autorise quatre chemins d’accès conditionnels : **En cas de réussite** (réussite par défaut), **En cas d’échec**, **En cas d’achèvement** et **En cas d’omission**. 

Azure Data Factory évalue le résultat de toutes les activités au niveau feuille. Les résultats du pipeline sont réussis uniquement si toutes les feuilles réussissent. Si une activité de feuille a été ignorée, nous évaluons son activité parente à la place. 

**Résolution :**

1. Implémentez les contrôles au niveau de l’activité en procédant de la manière décrite dans [Comment gérer les échecs et erreurs de pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Utilisez Azure Logic Apps pour surveiller les pipelines à intervalles réguliers en procédant de la manière décrite dans [Query By Factory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>Surveiller les échecs de pipeline à intervalles réguliers

Vous devrez peut-être surveiller les pipelines Data Factory à intervalles réguliers, par exemple, de 5 minutes. Vous pouvez interroger et filtrer les exécutions du pipeline à partir d’une fabrique de données à l’aide du point de terminaison. 

Configurez une application logique Azure pour interroger tous les pipelines ayant échoué toutes les 5 minutes, en procédant de la manière décrite dans [Query By Factory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory). Ensuite, vous pouvez signaler des incidents à notre système de tickets.

Pour plus d’informations, consultez [Envoi de notifications à partir de Data Factory, partie 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
