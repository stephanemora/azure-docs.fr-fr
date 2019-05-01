---
title: Migrer vos alertes classiques dans Azure Monitor à l’aide de l’outil de migration volontaire
description: Découvrez comment utiliser l’outil de migration volontaire pour migrer vos règles d’alerte classiques.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 55e6b515328df635a423e0ecf736b03e17a90b8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698419"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Utilisez l’outil de migration volontaire pour migrer vos règles d’alerte classiques

En tant que [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront retirés dans juillet 2019. Un outil de migration est disponible dans le portail Azure pour les clients qui utilisent des règles d’alerte classiques qui souhaitent déclencher migration eux-mêmes. Cet article explique comment utiliser l’outil de migration pour migrer volontairement vos règles d’alerte classiques avant la migration automatique démarre en juillet 2019.

## <a name="benefits-of-new-alerts"></a>Avantages de nouvelles alertes

Alertes classiques sont remplacés par les nouvelles et unifiée des alertes dans Azure Monitor. La nouvelle plateforme alertes présente les avantages suivants :

- Vous pouvez avertir sur diverses métriques multidimensionnelles pour [de nombreux services Azure supplémentaires](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Prise en charge des alertes de la nouvelle mesure [règles d’alerte multi-ressources](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) qui réduisent considérablement la surcharge de la gestion de nombreuses règles.
- Le mécanisme de notification unifiée, qui prend en charge :
  - [Groupes d’actions](action-groups.md), un mécanisme de notification modulaire qui fonctionne avec tous les nouveaux types d’alertes (mesures, journaux et journal d’activité).
  - Nouveaux mécanismes de notification tels que SMS, voix et le connecteur ITSM.
- Le [alerte expérience unifiée](alerts-overview.md) apporte toutes les alertes sur des signaux différents (mesures, journaux et journal d’activité) dans un seul endroit.

## <a name="before-you-migrate"></a>Avant de migrer

Le processus de migration convertit les règles d’alerte classiques à nouveau, ce qui équivaut règles d’alerte et crée des groupes d’actions. Dans la préparation, tenez compte des points suivants :

- Le format de charge utile de notification et les API pour créer et gérer de nouvelles règles d’alerte sont différents de ceux de règles d’alerte classiques, car ils prennent en charge davantage de fonctionnalités. [Apprenez à préparer la migration](alerts-prepare-migration.md).

- Certaines règles d’alerte classiques ne peuvent pas être migrés à l’aide de l’outil. [Découvrez les règles qui ne peuvent pas être migrés et que faire avec eux](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Le processus de migration n’affecte pas l’évaluation de vos règles d’alerte classiques. Ils continuent à s’exécuter et envoyer des alertes jusqu'à ce qu’ils sont migrés et les règles d’alerte prennent effet.

## <a name="how-to-use-the-migration-tool"></a>Comment utiliser l’outil de migration

Pour déclencher la migration de vos règles d’alerte classiques dans le portail Azure, procédez comme suit :

1. Dans [Azure portal](https://portal.azure.com), sélectionnez **moniteur**.

1. Sélectionnez **alertes**, puis sélectionnez **gérer les règles d’alerte** ou **afficher les alertes classiques**.

1. Sélectionnez **migrer vers les nouvelles règles** pour accéder à la page d’accueil de migration. Cette page affiche une liste de tous vos abonnements et leur état de migration :

    ![lancement de la migration](media/alerts-migration/migration-landing.png "migrer les règles")

    Tous les abonnements qui peuvent être migrés à l’aide de l’outil sont marqués comme **prêt pour la migration**.

    > [!NOTE]
    > L’outil de migration déploie en plusieurs phases à tous les abonnements qui utilisent des règles d’alerte classiques. Dans les premières phases du déploiement, vous pouvez voir certains abonnements marqués comme non prêt pour la migration.

1. Sélectionnez un ou plusieurs abonnements, puis **afficher un aperçu de la migration**.

    La page résultante affiche les détails des règles d’alerte classiques qui seront migrées pour un abonnement à la fois. Vous pouvez également sélectionner **télécharger les détails de migration pour cet abonnement** pour obtenir les détails dans un format CSV.

    ![version préliminaire de la migration](media/alerts-migration/migration-preview.png "afficher un aperçu de la migration")

1. Spécifiez une ou plusieurs adresses de messagerie pour être informé de l’état de la migration. Vous recevez le courrier électronique lors de la migration est terminée ou si aucune action n’est nécessaire de vous.

1. Sélectionnez **démarrer la Migration**. Lire les informations affichées dans la boîte de dialogue de confirmation et confirmez que vous êtes prêt à commencer le processus de migration.

    > [!IMPORTANT]
    > Une fois que vous initiez une migration pour un abonnement, vous ne pourrez pas modifier ou créer des règles d’alerte classiques pour cet abonnement. Cette restriction garantit qu’aucune modification de vos règles d’alerte classiques n’est perdues pendant la migration vers les nouvelles règles. Bien que vous ne pourrez pas modifier vos règles d’alerte classiques, ils continuent toujours à exécuter et pour fournir des alertes jusqu'à ce qu’ils ont été migrés. Une fois la migration terminée pour votre abonnement, vous ne pouvez pas utiliser les règles d’alerte classiques plus.

    ![migration de confirmation](media/alerts-migration/migration-confirm.png "confirmer démarrer la migration")

1. Lors de la migration est terminée, ou si l’action est nécessaire de vous, vous recevrez un e-mail aux adresses que vous avez fourni précédemment. Vous pouvez également régulièrement vérifier l’état sur la page d’accueil de migration dans le portail.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Pourquoi mon abonnement apparaît comme non prêt pour la migration ?

L’outil de migration est livrée aux clients en plusieurs phases. Dans les premières phases, la plupart ou la totalité de vos abonnements peuvent être marquées comme **n’est pas prêt pour la migration**. Toutefois, en moyenne-avril, tous les abonnements doivent être prêts à migrer.

Quand un abonnement est prêt pour la migration, le propriétaire d’abonnement reçoit un message e-mail indiquant que l’outil est disponible. Surveillez votre pour ce message.

### <a name="who-can-trigger-the-migration"></a>Qui peut déclencher la migration ?

Les utilisateurs qui ont le rôle Contributeur de surveillance au niveau de l’abonnement sont en mesure de déclencher la migration. [En savoir plus sur le contrôle d’accès en fonction du rôle pour le processus de migration](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Temps que prendra la migration ?

La migration est effectuée pour la plupart des abonnements dans moins d’une heure. Vous pouvez effectuer le suivi de la progression de la migration sur la page d’accueil de migration. Pendant la migration, la garantie que vos alertes sont en cours d’exécution dans le système d’alertes classiques ou dans une nouvelle.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Que puis-je faire si je rencontre un problème pendant la migration ?

Consultez le [guide de dépannage](alerts-understand-migration.md#common-problems-and-remedies) pour vous aider à des problèmes que vous pouvez rencontrer pendant la migration. Si une action est nécessaire de vous pour terminer la migration, vous serez averti sur les adresses de messagerie que vous avez fourni lorsque vous configurez l’outil.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer la migration](alerts-prepare-migration.md)
- [Comprendre le fonctionne de l’outil de migration](alerts-understand-migration.md)
