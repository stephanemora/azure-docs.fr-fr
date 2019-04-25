---
title: Comprendre comment volontaire outil de migration d’alertes dans Azure Monitor fonctionne
description: Comprendre le fonctionne de l’outil de migration d’alerte et résoudre les problèmes si vous rencontrez des problèmes.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347598"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendre le fonctionne de l’outil de migration

En tant que [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront retirés dans juillet 2019. L’outil de migration pour déclencher volontairement de migration est disponible dans le portail Azure et est en cours pour les clients qui utilisent des règles d’alerte classiques.

Cet article vous guidera dans le fonctionnement de l’outil de migration volontaire. Elle décrit également la mise à jour pour certains problèmes courants.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Les règles d’alerte classiques peuvent être migrés ?

Alors que presque toutes les règles d’alerte classiques peuvent être migrées à l’aide de l’outil, il existe quelques exceptions. Les règles d’alerte suivants ne seront pas migrés à l’aide de l’outil (ou lors de la migration automatique dans 2019 de juillet)

- Règles d’alerte classiques des métriques d’invité de machine virtuelle (Windows et Linux). [Consultez les conseils sur la façon de recréer ces règles d’alerte dans les nouvelles alertes de métrique](#guest-metrics-on-virtual-machines)
- Règles d’alerte classiques des métriques de stockage classique. [Consultez les conseils sur la surveillance de vos comptes de stockage classique](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Règles d’alerte classiques sur certaines métriques de compte de stockage. [Détails ci-dessous](#storage-account-metrics)

Si votre abonnement a des règles classiques de ce type, autres règles seront migrés, mais ces règles doivent être migrées manuellement. Nous ne pouvons pas fournir une migration automatique, toutes ces existants alertes de métriques classiques continuera pour 2020 de juin pour fournir votre temps à transférer sur les nouvelles alertes arriver à des. Toutefois, aucune nouvelle alerte classique ne peut être créés après juin 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Mesures invitées sur des machines virtuelles

Pour être en mesure de créer de nouvelles alertes de métrique des mesures invitées, les mesures invitées doivent être envoyés dans le magasin de mesures personnalisées d’Azure Monitor. Suivez les instructions ci-dessous pour activer le récepteur Azure Monitor dans les paramètres de diagnostic.

- [L’activation des mesures invitées pour les machines virtuelles Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [L’activation des mesures invitées pour les machines virtuelles Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Une fois les étapes ci-dessus sont effectuées, les nouvelles alertes de métriques peuvent être créés sur les mesures invitées. Une fois que vous avez recréé nouvelles alertes de métriques, alertes classiques peuvent être supprimés.

### <a name="storage-account-metrics"></a>Métriques du compte de stockage

Toutes les alertes classiques sur les comptes de stockage peuvent être migrés à l’exception de ces alertes sur les mesures suivantes :

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Règles d’alerte classiques sur les mesures de pourcentage seront doivent être migrées en fonction de [le mappage entre les métriques de stockage nouvelles et anciennes](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Seuils seront doivent être modifiés comme il convient que la nouvelle mesure disponible est absolu.

Règles d’alerte classiques erreur AnonymousThrottlingError et erreur SASThrottlingError doivent être fractionnée en deux nouvelles alertes qu’il n’est aucune mesure combinée qui fournit les mêmes fonctionnalités. Seuils seront doivent être adaptées en conséquence.

## <a name="roll-out-phases"></a>Phases de déploiement

L’outil de migration est livrée en plusieurs phases aux clients qui utilisent des règles d’alerte classiques. **Propriétaires d’abonnements** recevra un e-mail lorsque l’abonnement est prêt à être migrés à l’aide de l’outil.

> [!NOTE]
> Comme l’outil est déployé en plusieurs phases, dans les premières phases, vous pouvez voir que la plupart de vos abonnements n’est pas encore prête à migrer.

Actuellement un **sous-ensemble** d’abonnements, ce qui **uniquement** ont des règles d’alerte classiques sur la ressource suivante types sont marqués comme prêts pour la migration. Prise en charge d’autres types de ressources sera ajoutée dans les phases à venir.

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

Tout utilisateur ayant le rôle intégré de **contributeur de surveillance** à l’abonnement au niveau sera en mesure de déclencher la migration. Les utilisateurs avec un rôle personnalisé avec les autorisations suivantes peuvent également déclencher la migration :

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Problèmes courants et des corrections

Une fois que vous [déclencher la migration](alerts-using-migration-tool.md), nous allons utiliser les adresses e-mail fournies pour vous informer de la fin de la migration, ou si une action est requise de votre part. La section suivante décrit certains problèmes courants et comment les corriger

### <a name="validation-failed"></a>Échec de la validation

En raison de modifications apportées récemment à des règles d’alerte classiques dans votre abonnement, l’abonnement ne peut pas être migré. Il s’agit d’un problème temporaire. Vous pouvez redémarrer la migration une fois que l’état de migration déplace le curseur **prêt pour la migration** dans quelques jours.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Verrou d’étendue de la stratégie nous empêche de migration de vos règles

Dans le cadre de la migration, les nouvelles alertes de métriques et des groupes d’actions seront créés et des règles d’alerte classiques sont supprimés (une fois que les nouvelles règles sont créées). Toutefois, il existe une stratégie ou une étendue de verrou empêche la création de ressources. Selon le verrou d’étendue ou de stratégie, certaines ou toutes les règles n’a pas pu être migrés. Vous pouvez résoudre ce problème en supprimant la stratégie étendue verrou temporairement et déclencher à nouveau la migration.

## <a name="next-steps"></a>Étapes suivantes

- [Comment utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Préparer la migration](alerts-prepare-migration.md)
