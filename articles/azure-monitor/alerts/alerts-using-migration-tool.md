---
title: Migrer des règles d’alerte Azure Monitor
description: Découvrez comment utiliser l’outil de migration volontaire pour migrer vos règles d’alerte classiques.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fa487bec49ab9faa0f7c3dce752a30e4440fb873
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037674"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Utiliser l’outil de migration volontaire pour migrer vos règles d’alerte classiques

Comme [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor sont mises hors service pour les utilisateurs du cloud public, avec une utilisation limitée jusqu’au **31 mai 2021**. Les alertes classiques pour Azure Government Cloud et Azure China 21Vianet vont être mises hors service le **29 février 2024**.

Un outil de migration est disponible dans le Portail Azure pour les clients qui utilisaient des règles d’alerte classiques et qui souhaitent déclencher la migration eux-mêmes. Cet article explique comment utiliser l’outil de migration.

## <a name="before-you-migrate"></a>Avant de migrer

Le processus de migration convertit les règles d’alerte classiques en de nouvelles règles équivalentes et crée des groupes d’actions. Lors de la préparation, tenez compte des points suivants :

- Le format de charge utile de notification et les API pour créer et gérer les nouvelles règles d’alerte sont différents des règles d’alerte classiques, car ils prennent en charge davantage de fonctionnalités. [Découvrez comment préparer la migration](alerts-prepare-migration.md).

- Certaines règles d’alerte classiques ne peuvent pas être migrées à l’aide de l’outil. [Découvrez les règles qui ne peuvent pas être migrées et que faire avec](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Le processus de migration n’affecte pas l’évaluation de vos règles d’alerte classiques. Elles continuent à s’exécuter et envoyer des alertes jusqu'à ce qu’elles soient migrées et que les nouvelles règles d’alerte prennent effet.

## <a name="how-to-use-the-migration-tool"></a>Guide pratique pour utiliser l’outil de migration

Pour déclencher la migration de vos règles d’alerte classiques dans le portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Surveiller**.

1. Sélectionnez **Alertes**, puis **Gérer les règles d’alerte** ou **Afficher les alertes classiques**.

1. Sélectionnez **Migrer vers les nouvelles règles** pour accéder à la page d’accueil de migration. Cette page affiche une liste de tous vos abonnements et leur état de migration :

    ![Capture d’écran montrant la page Migrer les règles d’alerte.](media/alerts-using-migration-tool/migration-landing.png "Migrer les règles")

    Tous les abonnements qui peuvent être migrés à l’aide de l’outil sont marqués comme **Prêts pour la migration**.

    > [!NOTE]
    > L’outil de migration est déployé en plusieurs phases à tous les abonnements qui utilisent des règles d’alerte classiques. Dans les premières phases du déploiement, vous pouvez voir certains abonnements marqués comme n’étant pas prêts pour la migration.

1. Sélectionnez un ou plusieurs abonnements, puis **Aperçu de la migration**.

    La page résultante affiche les détails des règles d’alerte classiques qui seront migrées pour un abonnement à la fois. Vous pouvez également sélectionner **Télécharger les détails de la migration pour cet abonnement** pour obtenir les détails au format CSV.

    ![Capture d’écran montrant la page Migrer les règles d’alerte avec un lien Télécharger les détails de la migration pour cet abonnement, où vous pouvez spécifier une adresse e-mail pour la notification de migration.](media/alerts-using-migration-tool/migration-preview.png "Prévisualiser la migration")

1. Spécifiez une ou plusieurs adresses e-mail pour être informé de l’état de la migration. Vous recevrez un e-mail lorsque de la migration sera terminée ou si une action est nécessaire de votre part.

1. Sélectionnez **Démarrer la migration**. Lisez les informations affichées dans la boîte de dialogue de confirmation et confirmez que vous êtes prêt à commencer le processus de migration.

    > [!IMPORTANT]
    > Une fois que vous initiez la migration pour un abonnement, vous ne pourrez pas modifier ou créer des règles d’alerte classiques pour cet abonnement. Cette restriction garantit qu’aucune modification de vos règles d’alerte classiques n’est perdue pendant la migration vers les nouvelles règles. Bien que vous ne pourrez pas modifier vos règles d’alerte classiques, elles continueront à exécuter et à fournir des alertes jusqu'à leur migration. Une fois la migration terminée pour votre abonnement, vous ne pourrez plus utiliser les règles d’alerte classiques.

    ![Capture d’écran montrant une invite de confirmation pour votre migration, comprenant des informations importantes avec des liens pour en savoir plus avant de continuer.](media/alerts-using-migration-tool/migration-confirm.png "Confirmer le démarrage de la migration")

1. Lorsque la migration est terminée, ou si une action est nécessaire de votre part, vous recevrez un e-mail aux adresses que vous avez fournies précédemment. Vous pouvez également régulièrement vérifier l’état sur la page d’accueil de migration dans le portail.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Pourquoi mon abonnement est-il marqué comme « Not Ready for migration » (Pas prêt pour la migration) ?

L’outil de migration est livré aux clients en plusieurs phases. Dans les premières phases, la plupart ou la totalité de vos abonnements pourraient être marqués comme **non prêts pour la migration**. 

Quand un abonnement est prêt pour la migration, le propriétaire de l’abonnement reçoit un e-mail indiquant que l’outil est disponible. Surveillez l’arrivée de ce message.

### <a name="who-can-trigger-the-migration"></a>Qui peut déclencher la migration ?

Les utilisateurs qui ont le rôle Contributeur de surveillance au niveau de l’abonnement peuvent déclencher la migration. [Apprenez-en davantage sur le contrôle d’accès en fonction du rôle Azure pour le processus de migration](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Combien de temps prendra la migration ?

La migration est effectuée pour la plupart des abonnements en moins d’une heure. Vous pouvez suivre la progression de la migration sur la page d’accueil de migration. Pendant la migration, vous avez la garantie que vos alertes continuent à s’exécuter dans le système d’alertes classique ou le nouveau.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Que puis-je faire si je rencontre un problème pendant la migration ?

Consultez le [guide de dépannage](alerts-understand-migration.md#common-problems-and-remedies) pour vous aider à résoudre les problèmes que vous pourriez rencontrer pendant la migration. Si une action est nécessaire de votre part pour terminer la migration, vous en serez averti sur les adresses e-mail que vous avez fournies lorsque vous avez configuré l’outil.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer la migration](alerts-prepare-migration.md)
- [Comprendre le fonctionnement de l’outil de migration](alerts-understand-migration.md)