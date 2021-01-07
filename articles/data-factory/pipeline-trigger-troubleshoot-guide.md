---
title: Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory
description: Utiliser différentes méthodes pour résoudre des problèmes de déclencheurs de pipeline dans Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589149"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Résoudre les problèmes liés à l’orchestration et aux déclencheurs de pipeline dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Une exécution du pipeline dans Azure Data Factory définit une instance d’une exécution du pipeline. Par exemple, vous disposez d’un pipeline qui s’exécute à 8h00, 9h00 et 10h00. Dans ce cas, il y aura trois exécutions distinctes du pipeline. Chaque exécution de pipeline possède un ID d’exécution de pipeline unique. Une ID d’exécution est un GUID (identificateur global unique) qui définit cette exécution de pipeline spécifique.

Les exécutions de pipeline sont généralement instanciées en transmettant des arguments aux paramètres que vous définissez dans les pipelines. Vous pouvez exécuter un pipeline manuellement ou via un déclencheur. Pour plus de détails, consultez [Exécution et déclencheurs de pipeline dans Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="common-issues-causes-and-solutions"></a>Problèmes, causes et solutions courants

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Le pipeline avec Fonction Azure lève une erreur liée à une connexion de point de terminaison privé
 
#### <a name="issue"></a>Problème
Dans certains cas, Data Factory et l’Application Azure Function s’exécutent sur un point de terminaison privé. Vous essayez d’obtenir un pipeline qui interagit avec l’Application Azure Function pour fonctionner. Vous avez essayé trois méthodes différentes, mais l’une retourne une erreur `Bad Request`, et les deux autres méthodes retournent `103 Error Forbidden`.

#### <a name="cause"></a>Cause 
Azure Data Factory ne prend actuellement pas en charge un connecteur de point de terminaison privé pour l’Application Azure Function. C’est la raison pour laquelle l’Application Azure Function rejette les appels, car il est configuré pour autoriser uniquement les connexions à partir d’une liaison privée.

#### <a name="resolution"></a>Résolution
Vous pouvez créer un point de terminaison privé de type **PrivateLinkService** et fournir le DNS de votre application de fonction pour que la connexion fonctionne.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>L’exécution du pipeline est interrompue mais le moniteur continue d’afficher l’état de progression

#### <a name="issue"></a>Problème
Souvent, lorsque vous interrompez l’exécution d’un pipeline, la surveillance de celui-ci continue d’affiche l’état de progression. Cela se produit en raison du problème de cache dans le navigateur et parce que vous n’avez pas de filtre approprié pour la surveillance.

#### <a name="resolution"></a>Résolution
Actualisez le navigateur et appliquez les filtres appropriés pour la surveillance.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Échec de pipeline de copie : le nombre de colonnes trouvé est supérieur au nombre attendu (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problème  
Si un dossier que vous copiez contient des fichiers avec des schémas différents, comme un nombre variable de colonnes, des délimiteurs ou paramètres de guillemets différents, ou des problèmes quelconques liés aux données, le pipeline Azure Data Factory cesse de s’exécuter en levant l’erreur suivante :

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Résolution
Sélectionnez l’option « Copie binaire » lors de la création de l’activité Copier des données. Ainsi, pour la copie en bloc ou la migration de vos données d’un Data Lake vers un autre, avec l’option **binaire**, Azure Data Factory n’ouvre pas les fichiers pour lire le schéma, mais traite simplement chacun d’eux comme fichier binaire et les copie vers l’autre emplacement.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>L’exécution du pipeline échoue quand la limite de capacité du runtime d’intégration est atteinte

#### <a name="issue"></a>Problème
Message d’erreur :

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

L’erreur indique la limitation par runtime d’intégration, qui est actuellement de 50. Pour plus d’informations, consultez [Limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2).

Si vous exécutez une grande quantité de flux de données simultanément à l’aide du même runtime d’intégration, cela peut entraîner ce type d’erreur.

#### <a name="resolution"></a>Résolution 
- Séparez ces pipelines en fonction des différents temps de déclenchement pour les exécuter.
- Créez un nouveau runtime d’intégration et fractionnez ces pipelines en plusieurs runtimes d’intégration.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Comment surveiller les échecs de pipeline à intervalles réguliers

#### <a name="issue"></a>Problème
Il est souvent nécessaire de surveiller les pipelines Azure Data Factory à intervalles réguliers, par exemple, de 5 minutes. Vous pouvez interroger et filtrer les exécutions du pipeline à partir d’une fabrique de données à l’aide du point de terminaison. 

#### <a name="recommendation"></a>Recommandation
1. Configurez une application logique Azure pour interroger tous les pipelines ayant échoué toutes les 5 minutes.
2. Ensuite, vous pouvez signaler des incidents à notre système de billetterie comme décrit dans [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Référence
- [Envoi à l’extérieur de notifications à partir d’Azure Data Factory](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Comment gérer les erreurs et échecs au niveau des activités dans les pipelines

#### <a name="issue"></a>Problème
Une orchestration Azure Data Factory autorise une logique conditionnelle et permet à l’utilisateur d’emprunter différents chemins d’accès en fonction des résultats d’une activité précédente. Elle autorise quatre chemins d’accès conditionnels : « En cas de réussite (réussite par défaut) », « En cas d’échec », « En cas d’achèvement » et « En cas d’omission ». L’utilisation de chemins d’accès différents est autorisée.

Azure Data Factory définit le succès et l’échec de l’exécution du pipeline comme suit :

- Évaluez le résultat de toutes les activités au niveau feuille. Si une activité de feuille a été ignorée, nous évaluons son activité parente à la place.
- Le résultat du pipeline est réussi si et seulement si toutes les feuilles réussissent.

#### <a name="recommendation"></a>Recommandation
- Implémentez les contrôles de niveau d’activité en procédant de la manière décrite dans [Comment gérer les échecs et erreurs de pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Utilisez une application logique Azure pour surveiller les pipelines à intervalles réguliers en procédant de la manière décrite dans [Interroger par DataFactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)