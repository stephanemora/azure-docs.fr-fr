---
title: Comprendre la migration pour les alertes Azure Monitor
description: Comprendre le fonctionnement de la migration des alertes et résoudre les problèmes.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: fdac8015cf87ffa0a25a8558668329a8cd82327f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737186"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Comprendre les options de migration vers les alertes plus récentes

Les alertes classiques sont [hors service](./monitoring-classic-retirement.md) pour les utilisateurs du cloud public. Elles restent néanmoins disponibles pour un usage limité jusqu’au **31 mai 2021**. Les alertes classiques pour Azure Government Cloud et Azure China 21Vianet vont être mises hors service le **29 février 2024**.

Cet article explique comment fonctionne l’outil de migration manuelle et de migration volontaire, qui sera utilisé pour migrer les règles d’alerte restantes. Il décrit également des solutions à certains problèmes courants.

> [!IMPORTANT]
> Les alertes de journal d’activité (y compris les alertes d’intégrité de service) et les alertes de journal ne sont pas affectées par la migration. La migration s’applique uniquement aux règles d’alerte classiques décrites [ici](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Si vos règles d’alerte classiques ne sont pas valides, par exemple si elles portent sur des [métriques déconseillées](#classic-alert-rules-on-deprecated-metrics) ou des ressources qui ont été supprimées, elles ne seront pas migrées et ne seront pas disponibles une fois le service mis hors service.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Migration manuelle des alertes classiques vers des alertes plus récentes

Les clients qui souhaitent migrer manuellement leurs alertes restantes peuvent déjà le faire à l’aide des sections suivantes. Cette migration comprend également des métriques mises hors service qui ne peuvent pas être migrées directement.

### <a name="guest-metrics-on-virtual-machines"></a>Métriques d’invité sur les machines virtuelles

Avant de pouvoir créer de nouvelles alertes de métriques sur des métriques invitées, celles-ci doivent être envoyées au magasin de journaux Azure Monitor. Pour créer des alertes, suivez ces instructions :

- [Activation de la collecte de métriques invitées pour log Analytics](../agents/agent-data-sources.md)
- [Création d’alertes de journal dans Azure Monitor](./alerts-log.md)

Il existe d’autres options pour collecter des métriques invitées et des alertes sur celles-ci. [En savoir plus](../agents/agents-overview.md).

### <a name="storage-and-classic-storage-account-metrics"></a>Métriques de compte de stockage et de stockage classique

Toutes les alertes classiques sur les comptes de stockage peuvent être migrées, à l’exception des alertes sur ces métriques :

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Les règles d’alerte classiques sur les métriques de pourcentage doivent être migrées selon [le mappage entre les anciennes et nouvelles métriques de stockage](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Les seuils devront être modifiés en conséquence, car la nouvelle métrique disponible est absolue.

Les règles d’alerte classiques sur AnonymousThrottlingError, SASThrottlingError et ThrottlingError doivent être fractionnées en deux nouvelles alertes, car il n’existe aucune métrique combinée offrant les mêmes fonctionnalités. Les seuils devront être adaptés de manière appropriée.

### <a name="cosmos-db-metrics"></a>Métriques Cosmos DB

Toutes les alertes classiques sur les mesures de Cosmos DB peuvent être migrées, à l’exception des alertes sur ces mesures :

- Nombre moyen de requêtes par seconde
- Niveau de cohérence
- Http 2xx
- Http 3xx
- Nombre maximal de RUPM consommées par minute
- Nombre maximal de RU par seconde
- Autres frais des requêtes Mongo
- Autres taux des requêtes Mongo
- Latence de lecture observée
- Latence d’écriture observée
- Disponibilité des services
- Capacité de stockage

Le nombre moyen de demandes par seconde, le niveau de cohérence, le nombre maximal de RUPM consommées par minute, le nombre maximal de RU par seconde, la latence de lecture observée, la latence d’écriture observée et la capacité de stockage ne sont pas disponibles actuellement dans le [nouveau système](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Les alertes sur les métriques de demande, telles que Http 2xx, Http 3xx et Disponibilité du service ne sont pas migrées, car la manière dont les demandes sont dénombrées diffère entre les métriques classiques et les nouvelles métriques. Les alertes sur ces métriques doivent être recréées manuellement avec des seuils ajustés.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Règles d’alerte classiques sur des métriques déconseillées

Les règles suivantes sont des règles d’alerte classiques sur des métriques qui étaient précédemment prises en charge, mais qui ont fini par être déconseillées. Un petit pourcentage de clients peut avoir des règles d’alerte classiques non valides sur de telles métriques. Dans la mesure où ces règles d’alerte ne sont pas valides, elles ne seront pas migrées.

| Type de ressource| Métriques déconseillées |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Comment les nouvelles règles d’alerte et les groupes d’actions équivalents sont créés

L’outil de migration convertit vos règles d’alerte classiques en nouvelles règles d’alerte et groupes d’actions équivalents. Pour la plupart des règles d’alerte classiques, les nouvelles règles d’alerte équivalentes portent sur la même métrique avec les mêmes propriétés, comme `windowSize` et `aggregationType`. Toutefois, il existe des règles d’alerte classiques portant sur des métriques qui ont une métrique équivalente différente dans le nouveau système. Les principes suivants s’appliquent à la migration des alertes classiques, sauf mention contraire dans la section ci-dessous :

- **Fréquence** : Définit la fréquence de vérification de la condition pour une règle d’alerte classique ou nouvelle. La `frequency` dans les règles d’alerte classiques n’était pas configurable par l’utilisateur et était toujours de 5 minutes pour tous les types de ressources. La fréquence des règles équivalentes est également définie sur 5 minutes.
- **Type d’agrégation** : Définit la façon dont les métriques sont agrégées pendant la fenêtre d’intérêt. Le `aggregationType` est également le même entre les alertes classiques et les nouvelles alertes pour la plupart des métriques. Dans certains cas, étant donné que la métrique est différente entre les alertes classiques et les nouvelles alertes, le `aggregationType` équivalent ou le `primary Aggregation Type` défini pour la métrique est utilisé.
- **Unités** : La propriété de la métrique sur laquelle l’alerte est créée. Certaines métriques équivalentes ont des unités différentes. Le seuil est ajusté de manière appropriée en fonction des besoins. Par exemple, si la métrique d’origine utilise les secondes comme unité mais que la nouvelle métrique équivalente utilise les millisecondes, le seuil d’origine est multiplié par 1 000 pour garantir le même comportement.
- **Taille de la fenêtre** : Définit la fenêtre pendant laquelle les données de métrique sont agrégées pour être comparées au seuil. Pour les valeurs de `windowSize` standard telles que 5 minutes, 15 minutes, 30 minutes, 1 heure, 3 heures, 6 heures, 12 heures et 1 jour, aucune modification n’a été apportée pour la nouvelle règle d’alerte équivalente. Pour les autres valeurs, la `windowSize` la plus proche est utilisée. Pour la plupart des clients, cette modification n’a aucune incidence. Pour un petit pourcentage de clients, il pourrait être nécessaire d’ajuster le seuil pour obtenir un comportement rigoureusement identique.

Dans les sections suivantes, nous détaillons les métriques qui ont une métrique différente et équivalente dans le nouveau système. Les métriques qui restent identiques pour les règles d’alerte classiques et nouvelles ne sont pas répertoriées. Vous trouverez une liste des métriques prises en charge dans le nouveau système [ici](../essentials/metrics-supported.md).

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts et Microsoft.ClassicStorage/storageAccounts

Pour les services de compte de stockage tels que blob, table, fichier et file d’attente, les métriques suivantes sont mappées aux métriques équivalentes, comme indiqué ci-dessous :

| Métrique dans les alertes classiques | Métrique équivalente dans les nouvelles alertes | Commentaires|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » et « Authentication » = « Anonymous »| |
| AnonymousClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError » et « Authentication » = « Anonymous » | |
| AnonymousClientTimeOutError| Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » et « Authentication » = « Anonymous » | |
| AnonymousNetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » et « Authentication » = « Anonymous » | |
| AnonymousServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » et « Authentication » = « Anonymous » | |
| AnonymousServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError » et « Authentication » = « Anonymous » | |
| AnonymousSuccess | Métrique de transactions avec les dimensions « ResponseType » = « Success » et « Authentication » = « Anonymous » | |
| AuthorizationError | Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacité | BlobCapacity | Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| ClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError »  | |
| ClientTimeoutError | Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » | |
| ContainerCount | ContainerCount | Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| NetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » | |
| ObjectCount | BlobCount| Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| SASAuthorizationError | Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » et « Authentication » = « SAS » | |
| SASClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError » et « Authentication » = « SAS » | |
| SASClientTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » et « Authentication » = « SAS » | |
| SASNetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » et « Authentication » = « SAS » | |
| SASServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » et « Authentication » = « SAS » | |
| SASServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError » et « Authentication » = « SAS » | |
| SASSuccess | Métrique de transactions avec les dimensions « ResponseType » = « Success » et « Authentication » = « SAS » | |
| ServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » | |
| ServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError »  | |
| Succès | Métrique de transactions avec les dimensions « ResponseType » = « Success » | |
| TotalBillableRequests| Transactions | |
| TotalEgress | Sortie | |
| TotalIngress | Entrée | |
| TotalRequests | Transactions | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Pour Cosmos DB, les mesures équivalentes sont indiquées ci-dessous :

| Métrique dans les alertes classiques | Métrique équivalente dans les nouvelles alertes | Commentaires|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Taille des données | DataUsage| |
| Nombre de documents | DocumentCount||
| Taille d'index | IndexUsage||
| Service indisponible | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Requêtes limitées | TotalRequests avec la dimension « StatusCode » = « 429 »| Le type d’agrégation « average » est corrigé en « Count »|
| Erreurs internes du serveur | TotalRequests avec la dimension « StatusCode » = « 500 »}| Le type d’agrégation « average » est corrigé en « Count »|
| Http 401 | TotalRequests avec la dimension « StatusCode » = « 401 »| Le type d’agrégation « average » est corrigé en « Count »|
| HTTP 400 | TotalRequests avec la dimension « StatusCode » = « 400 »| Le type d’agrégation « average » est corrigé en « Count »|
| Total de requêtes | TotalRequests| Le type d’agrégation « Max » est corrigé en « Count »|
| Nombre de frais des requêtes Mongo| MongoRequestCharge avec la dimension « CommandName » = « count »||
| Nombre de taux des requêtes Mongo | MongoRequestCount avec la dimension « CommandName » = « count »||
| Frais de requête de suppression Mongo | MongoRequestCharge avec la dimension « CommandName » = « delete »||
| Taux de requête de suppression Mongo | MongoRequestsCount avec la dimension « CommandName » = « delete »||
| Frais de requête d’insertion Mongo | MongoRequestCharge avec la dimension « CommandName » = « insert »||
| Taux de requête d’insertion Mongo | MongoRequestsCount avec la dimension « CommandName » = « insert »||
| Frais de requêtes d’interrogation Mongo | MongoRequestCharge avec la dimension « CommandName » = « find »||
| Taux de requêtes d’interrogation Mongo | MongoRequestsCount avec la dimension « CommandName » = « find »||
| Frais de requête de mise à jour Mongo | MongoRequestCharge avec la dimension « CommandName » = « update »||
| Requêtes d’insertion ayant échoué Mongo | MongoRequestCount avec les dimensions « CommandName » = « Insert » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|
| Requêtes interrogées ayant échoué Mongo | MongoRequestCount avec les dimensions « CommandName » = « Query » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|
| Nombre d’échecs de requêtes Mongo | MongoRequestCount avec les dimensions « CommandName » = « count » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|
| Requêtes ayant échoué mises à jour Mongo | MongoRequestCount avec les dimensions « CommandName » = « update » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|
| Autres demandes ayant échoué Mongo | MongoRequestCount avec les dimensions « CommandName » = « other » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|
| Requêtes ayant échoué supprimées Mongo | MongoRequestCount avec les dimensions « CommandName » = « delete » et « Status » = « Failed »| Le type d’agrégation « average » est corrigé en « Count »|

### <a name="how-equivalent-action-groups-are-created"></a>Comment les groupes d’actions équivalents sont créés

Les règles d’alerte classiques avaient les actions d’e-mail, de webhook, d’application logique et de runbook liées à la règle d’alerte elle-même. Les nouvelles règles d’alerte utilisent des groupes d’actions qui peuvent être réutilisés dans plusieurs règles d’alerte. L’outil de migration crée un groupe d’actions unique pour les actions similaires, quel que soit le nombre de règles d’alerte utilisant l’action. Les groupes d’actions créés par l’outil de migration utilisent le format d’attribution de noms « Migrated_AG* ».

> [!NOTE]
> Les alertes classiques ont envoyé des e-mails localisés en fonction des paramètres régionaux de l’administrateur classique lorsqu’ils sont utilisés pour notifier les rôles d’administrateur classiques. Les nouveaux e-mails d’alerte sont envoyés par le biais de groupes d’actions et sont uniquement en anglais.

## <a name="rollout-phases"></a>Phases de déploiement

L’outil de migration est livré en plusieurs phases aux clients qui utilisent des règles d’alerte classiques. Les propriétaires d’abonnements recevront un e-mail lorsque leur abonnement sera prêt à être migré à l’aide de l’outil.

> [!NOTE]
> Étant donné que l’outil est déployé en plusieurs phases, vous pourriez constater que certains de vos abonnements ne sont pas encore prêts à être migrés lors des premières phases.

La plupart des abonnements sont actuellement marqués comme prêts pour la migration. Seuls les abonnements qui ont des alertes classiques sur les types de ressources suivants ne sont pas encore prêts pour la migration.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Qui peut déclencher la migration ?

Tout utilisateur ayant le rôle intégré de contributeur de surveillance au niveau de l’abonnement peut déclencher la migration. Les utilisateurs qui ont un rôle personnalisé avec les autorisations suivantes peuvent également déclencher la migration :

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> En plus d’avoir les autorisations ci-dessus, votre abonnement doit être inscrit avec le fournisseur de ressources Microsoft.AlertsManagement. Cela est nécessaire pour réussir la migration des alertes d’anomalies d’échec sur Application Insights. 

## <a name="common-problems-and-remedies"></a>Problèmes courants et solutions

Après avoir [déclenché la migration](alerts-using-migration-tool.md), vous recevez un e-mail aux adresses que vous avez fournies pour vous avertir que la migration est terminée ou si une action est nécessaire de votre part. Cette section décrit certains problèmes courants et comment les résoudre.

### <a name="validation-failed"></a>Échec de validation

En raison des modifications apportées récemment aux règles d’alerte classiques dans votre abonnement, l’abonnement ne peut pas être migré. Ce problème est temporaire. Vous pourrez redémarrer la migration une fois que l’état de migration sera de nouveau **prêt pour la migration**, dans quelques jours.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Un verrou d’étendue nous empêche de migrer vos règles

Dans le cadre de la migration, de nouvelles alertes de métrique et de nouveaux groupes d’actions seront créés, et les règles d’alerte classiques seront alors supprimées. Toutefois, un verrou d’étendue peut nous empêcher de créer ou de supprimer des ressources. En fonction du verrou d’étendue, une partie ou l’intégralité des règles n’ont pas pu être migrées. Vous pouvez résoudre ce problème en supprimant le verrou d’étendue pour l’abonnement, le groupe de ressources ou la ressource, lequel est listé dans l’[outil de migration](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), puis en redéclenchant la migration. Le verrou d’étendue ne peut pas être désactivé et doit être supprimé pendant le processus de migration. [En savoir plus sur la gestion des verrous d’étendue](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Une stratégie avec effet de refus nous empêche de migrer vos règles

Dans le cadre de la migration, de nouvelles alertes de métrique et de nouveaux groupes d’actions seront créés, et les règles d’alerte classiques seront alors supprimées. Toutefois, une affectation [Azure Policy](../../governance/policy/index.yml) peut nous empêcher de créer des ressources. En fonction de l’attribution de stratégie, certaines ou l’intégralité des règles n’ont pas pu être migrées. Les affectations de stratégie qui bloquent le processus sont listées dans l’[outil de migration](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Pour résoudre ce problème, effectuez l’une ou l’autre des étapes suivantes :

- Excluez les abonnements, les groupes de ressources ou les ressources individuelles pendant le processus de migration à partir de l’attribution de stratégie. [En savoir plus sur la gestion des étendues de l’exclusion de stratégie](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Définissez le « mode d’application » sur **Désactivé** sur l’affectation de stratégie. [En savoir plus sur la propriété enforcementMode de l’affectation de stratégie](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Définissez une exemption Azure Policy (préversion) sur les abonnements, des groupes de ressources ou des ressources individuelles pour l’attribution de stratégie. [En savoir plus sur la structure d’exemption Azure Policy](../../governance/policy/concepts/exemption-structure.md).
- Supprimez l’effet ou modifiez-le en le remplaçant par un effet désactivé, d’audit, d’ajout ou de modification (qui, par exemple, permet de résoudre les problèmes liés aux balises manquantes). [En savoir plus sur la gestion des effets de stratégie](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Préparer la migration](alerts-prepare-migration.md)