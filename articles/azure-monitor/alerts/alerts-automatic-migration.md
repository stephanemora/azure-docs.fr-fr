---
title: Comprendre le fonctionnement du processus de migration automatique pour vos alertes classiques Azure Monitor
description: Découvrir le fonctionnement du processus de migration automatique.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734857"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Comprendre le processus de migration automatique pour vos règles d’alerte classiques

Comme [annoncé précédemment](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor sont mises hors service pour les utilisateurs du cloud public, bien qu’il reste possible de les utiliser de façon limitée jusqu’au **31 mai 2021**. Les alertes classiques pour Azure Government Cloud et Azure China 21Vianet seront mises hors service le **29 février 2024**.

Un [outil de migration](alerts-using-migration-tool.md) est disponible dans le portail Azure, qui permet aux clients de déclencher la migration eux-mêmes. Cet article explique le processus de migration automatique dans le cloud public, qui commencera après le 31 mai 2021. Il décrit également des problèmes et solutions que vous pouvez rencontrer.

## <a name="important-things-to-note"></a>Quelques points importants à prendre en considération :

Le processus de migration convertit les règles d’alerte classiques en de nouvelles règles équivalentes et crée des groupes d’actions. Lors de la préparation, tenez compte des points suivants :

- Les formats de charge utile de notification pour les nouvelles règles d’alerte diffèrent des charges utiles des règles d’alerte classiques, car ils prennent en charge davantage de fonctionnalités. Si vous avez une règle d’alerte classique avec des applications logiques, des runbooks ou des webhooks qui pourraient cesser de fonctionner comme prévu après la migration en raison de différences sur le plan de la charge utile. [Découvrez comment préparer la migration](alerts-prepare-migration.md).

- Certaines règles d’alerte classiques ne peuvent pas être migrées à l’aide de l’outil. [Découvrez les règles qui ne peuvent pas être migrées et la marche à suivre](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Que se passe-t-il pendant le processus de migration automatique dans le cloud public ?

- À partir du 31 mai 2021, vous ne pourrez pas créer de nouvelles règles d’alerte classiques, et la migration des alertes classiques sera déclenchée par lots.
- Les règles d’alerte classiques surveillant les ressources cibles supprimées ou les [ métriques qui ne sont plus prises en charge](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) sont considérées comme non valides.
- Les règles d’alerte classiques qui ne sont pas valides seront supprimées quelque temps après le 31 mai 2021.
- Une fois que la migration de votre abonnement démarre, la migration devrait prendre une heure. Les clients peuvent surveiller l’état de la migration à l’aide de [l’outil de migration dans Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Les propriétaires d’abonnement recevront un e-mail les notifiant de la réussite ou de l’échec de la migration.

    > [!NOTE]
    > Si vous ne souhaitez pas attendre le démarrage du processus de migration automatique, vous pouvez toujours déclencher la migration volontairement à l’aide de l’outil de migration.

## <a name="what-if-the-automatic-migration-fails"></a>Que se passe-t-il si la migration automatique échoue ?

Lorsque le processus de migration automatique échoue, les propriétaires d’abonnement reçoivent un e-mail les informant du problème. Vous pouvez utiliser l’outil de migration dans Azure Monitor pour examiner tous les détails du problème. Consultez le [guide de dépannage](alerts-understand-migration.md#common-problems-and-remedies) pour vous aider à résoudre les problèmes que vous pourriez rencontrer pendant la migration.

  > [!NOTE]
  > Si les clients doivent effectuer une action, par exemple désactiver temporairement le verrou d’une ressource ou modifier une attribution de stratégie, ils devront résoudre ces problèmes. Si les problèmes ne sont pas résolus à cette date, nous ne pouvons pas garantir que la migration de vos alertes classiques sera effectuée avec succès.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer la migration](alerts-prepare-migration.md)
- [Comprendre le fonctionnement de l’outil de migration](alerts-understand-migration.md)