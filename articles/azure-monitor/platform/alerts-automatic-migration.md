---
title: Comprendre le fonctionnement du processus de migration automatique pour vos alertes classiques Azure Monitor
description: Découvrir le fonctionnement du processus de migration automatique.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668245"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Comprendre le processus de migration automatique pour vos règles d’alerte classiques

Comme [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront mises hors service en septembre 2019 (c’était initialement prévu pour juillet 2019). Dans le cadre du processus de mise hors service, [un outil de migration](alerts-using-migration-tool.md) est disponible dans le portail Azure pour que les clients puissent déclencher la migration eux-mêmes. Si vous n’avez pas utilisé l’outil de migration avant le 31 août 2019, Azure Monitor démarrera le processus de migration automatique de vos alertes classiques à partir du 1er septembre 2019.
Cet article présente le processus de migration automatique et vous aide à résoudre les problèmes que vous pourriez rencontrer.

  > [!NOTE]
  > Cet article s’applique uniquement au cloud public Azure. Le processus de mise hors service des alertes classiques Azure Monitor dans le cloud Azure Government t dans Azure China 21Vianet sera annoncé à une date ultérieure.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Que se passe-t-il pendant le processus de migration automatique ?

- À partir du **1er septembre 2019**, les clients ne pourront plus créer de nouvelles règles d’alerte classiques, sauf sur [certaines métriques](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Pour ces exceptions, le client peut continuer à créer de nouvelles règles d’alerte classiques et à utiliser ses alertes classiques jusqu’en juin 2020.
- À partir du **1er septembre 2019**, la migration des alertes classiques sera déclenchée par lots pour tous les clients disposant d’alertes classiques.
- Comme avec l’outil de migration volontaire, certaines règles d’alerte classiques ne pouvant pas être migrées seront conservées. Ces règles d’alerte classiques seront prises en charge jusqu’en juin 2020. Cependant, les règles d’alerte classiques non valides seront supprimées, car elles ne sont pas fonctionnelles.
Les règles d’alerte classiques surveillant les ressources cibles supprimées ou les [ métriques qui ne sont plus prises en charge](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) sont considérées comme non valides.
- Une fois que la migration de votre abonnement démarre, la migration devraitt être effectuée dans l’heure, à moins qu'un problème ne survienne. Les clients peuvent surveiller l’état de la migration dans [le panneau de migration d’Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Les propriétaires des abonnements recevront un e-mail une fois la migration terminée avec succès.
- En cas de problème lors de la migration, les propriétaires d’abonnement en seront également informés par e-mail. Les clients peuvent utiliser le panneau migration pour voir le problème en détail.
- Si le client doit effectuer une action, par exemple désactiver temporairement le verrou de ressource ou modifier une attribution de stratégie, ce dernier devra résoudre les problèmes avant le 31 octobre 2019. Si les problèmes ne sont pas résolus à cette date, nous ne pouvons pas garantir que la migration de vos alertes classiques sera effectuée avec succès.

    > [!NOTE]
    > Si vous ne souhaitez pas attendre le démarrage du processus de migration automatique, vous pouvez toujours déclencher la migration volontairement à l’aide de l’outil de migration.

## <a name="important-things-to-note"></a>Quelques points importants à prendre en considération :

Le processus de migration convertit les règles d’alerte classiques en de nouvelles règles équivalentes et crée des groupes d’actions. Lors de la préparation, tenez compte des points suivants :

- Les formats de charge utile de notification pour les nouvelles règles d’alerte sont différents de ceux des règles d’alerte classiques, car ils prennent en charge davantage de fonctionnalités. Si vous avez des applications logiques, des runbooks ou des webhooks qui sont déclenchés par une règle d’alerte classique, ils risquent de cesser de fonctionner comme prévu une fois la migration terminée en raison de différences dans les charges utiles de notification. [Découvrez comment préparer la migration](alerts-prepare-migration.md).

- Certaines règles d’alerte classiques ne peuvent pas être migrées à l’aide de l’outil. [Découvrez les règles qui ne peuvent pas être migrées et la marche à suivre](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Le processus de migration n’affecte pas l’évaluation de vos règles d’alerte classiques. Elles continuent à s’exécuter et envoyer des alertes jusqu'à ce qu’elles soient migrées et que les nouvelles règles d’alerte prennent effet.

## <a name="what-if-the-automatic-migration-fails"></a>Que se passe-t-il si la migration automatique échoue ?

Lorsque le processus de migration automatique échoue, les propriétaires d’abonnement reçoivent un e-mail les informant du problème. Les clients peuvent utiliser le panneau de migration dans Azure Monitor pour voir le problème plus en détail.

Consultez le [guide de dépannage](alerts-understand-migration.md#common-problems-and-remedies) pour vous aider à résoudre les problèmes que vous pourriez rencontrer pendant la migration.

  > [!NOTE]
  > Si le client doit effectuer une action, par exemple désactiver temporairement le verrou de ressource ou modifier une attribution de stratégie, ce dernier devra résoudre les problèmes avant le 31 octobre 2019. Si les problèmes ne sont pas résolus à cette date, nous ne pouvons pas garantir que la migration de vos alertes classiques sera effectuée avec succès.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer la migration](alerts-prepare-migration.md)
- [Comprendre le fonctionnement de l’outil de migration](alerts-understand-migration.md)
