---
title: Comprendre le fonctionne de l’outil de migration volontaire pour les alertes Azure Monitor
description: Comprendre le fonctionne de l’outil de migration d’alertes et résoudre les problèmes.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015589"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendre le fonctionne de l’outil de migration

En tant que [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront retirés en septembre 2019 (a été initialement juillet 2019). Un outil de migration est disponible dans le portail Azure pour les clients qui utilisent des règles d’alerte classiques qui souhaitent déclencher migration eux-mêmes.

Cet article explique le fonctionne de l’outil de migration volontaire. Elle décrit également les solutions pour certains problèmes courants.

> [!NOTE]
> En raison de retards dans le déploiement de l’outil de migration, la date de suppression pour la migration des alertes classiques a été [étendue pour le 31 août 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) à partir de la date annoncée à l’origine du 30 juin 2019.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Les règles d’alerte classiques peuvent être migrés ?

Bien que l’outil peut migrer presque toutes classiques règles d’alerte, il existe quelques exceptions. Les règles d’alerte suivants ne seront pas migrés à l’aide de l’outil (ou lors de la migration automatique en septembre 2019) :

- Règles d’alerte classiques des métriques d’invité de machine virtuelle (Windows et Linux). Consultez le [des conseils permettant de recréer ces règles d’alerte dans les nouvelles alertes de métrique](#guest-metrics-on-virtual-machines) plus loin dans cet article.
- Règles d’alerte classiques des métriques de stockage classique. Consultez le [des conseils pour la surveillance de vos comptes de stockage classic](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Règles d’alerte classiques sur certaines métriques de compte de stockage. Consultez [détails](#storage-account-metrics) plus loin dans cet article.

Si votre abonnement a des règles classiques de ce type, vous devez les migrer manuellement. Étant donné que nous ne pouvons pas fournir une migration automatique, les alertes de métrique existants, classiques de ces types continue de fonctionner jusqu'à juin 2020. Cette extension vous donne le temps à transférer sur les nouvelles alertes. Toutefois, aucune nouvelle alerte classique ne peut être créés après août 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Mesures invitées sur des machines virtuelles

Avant de pouvoir créer des nouvelles alertes de métrique des mesures invitées, les mesures invitées doivent être envoyés à la banque de mesures personnalisées d’Azure Monitor. Suivez ces instructions pour activer le récepteur Azure Monitor dans les paramètres de diagnostic :

- [L’activation des mesures invitées pour les machines virtuelles Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [L’activation des mesures invitées pour les machines virtuelles Linux](collect-custom-metrics-linux-telegraf.md)

Une fois ces étapes sont effectuées, vous pouvez créer de nouvelles alertes de métrique des mesures invitées. Et une fois que vous avez créé les nouvelles alertes de métrique, vous pouvez supprimer des alertes classiques.

### <a name="storage-account-metrics"></a>Métriques du compte de stockage

Toutes les alertes classiques sur les comptes de stockage peuvent être migrés à l’exception des alertes sur ces mesures :

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Alerte classique règles sur les mesures de pourcentage doivent être migrés selon [le mappage entre les métriques de stockage nouvelles et anciennes](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Seuils seront doivent être modifiés en conséquence, car la nouvelle mesure disponible est absolu.

Règles d’alerte classiques sur l’erreur AnonymousThrottlingError et erreur SASThrottlingError doivent être fractionnées en deux nouvelles alertes, car il n’existe aucune mesure combinée qui fournit les mêmes fonctionnalités. Seuils seront doivent s’adapter de manière appropriée.

## <a name="rollout-phases"></a>Phases de déploiement

L’outil de migration est livrée en plusieurs phases aux clients qui utilisent des règles d’alerte classiques. Propriétaires d’abonnements recevront un message électronique lorsque l’abonnement est prêt à être migré à l’aide de l’outil.

> [!NOTE]
> Étant donné que l’outil est déployé en plusieurs phases, vous pouvez voir que la plupart de vos abonnements n’est pas encore prête à être migrée pendant les premières phases.

Actuellement, un sous-ensemble des abonnements est marqué comme prêt pour la migration. Le sous-ensemble inclut les abonnements qui ont des règles d’alerte classiques uniquement sur les types suivants de la ressource. Prise en charge d’autres types de ressources sera ajoutée dans les phases à venir.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.DataFactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Qui peut déclencher la migration ?

Tout utilisateur ayant le rôle intégré contributeur de surveillance au niveau de l’abonnement peut déclencher la migration. Les utilisateurs qui ont un rôle personnalisé avec les autorisations suivantes peuvent également déclencher la migration :

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Problèmes courants et solutions

Après avoir [déclencher la migration](alerts-using-migration-tool.md), vous recevez un e-mail aux adresses que vous avez fourni pour vous avertir que la migration est terminée ou si une action est nécessaire de vous. Cette section décrit certains problèmes courants et comment les traiter.

### <a name="validation-failed"></a>Échec de la validation

En raison de modifications apportées récemment à des règles d’alerte classiques dans votre abonnement, l’abonnement ne peut pas être migré. Ce problème est temporaire. Vous pouvez redémarrer la migration une fois que l’état de migration déplace le curseur **prêt pour la migration** dans quelques jours.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Verrou de stratégie ou étendue nous empêche de migration de vos règles

Dans le cadre de la migration, les nouvelles alertes de métriques et des groupes d’actions seront créés et des règles d’alerte classiques ne seront supprimés. Toutefois, il existe une stratégie ou une étendue de verrou empêche la création de ressources. Selon le verrou d’étendue ou de stratégie, certaines ou toutes les règles n’a pas pu être migrés. Vous pouvez résoudre ce problème en supprimant le verrou d’étendue ou de la stratégie temporairement et en déclenchant à nouveau la migration.

## <a name="next-steps"></a>Étapes suivantes

- [Comment utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Préparer la migration](alerts-prepare-migration.md)
