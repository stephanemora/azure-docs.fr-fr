---
title: Migrer vos alertes classiques dans Azure Monitor à l’aide de l’outil de migration volontaire
description: Découvrez comment utiliser l’outil de migration volontaire pour migrer vos règles d’alerte classiques.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632262"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Utilisez l’outil de migration volontaire pour migrer vos règles d’alerte classiques

En tant que [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront retirés dans juillet 2019. L’outil de migration pour déclencher volontairement de migration est disponible dans le portail Azure et est en cours pour les clients qui utilisent des règles d’alerte classiques. Cet article vous guidera sur l’utilisation de l’outil de migration pour migrer volontairement vos règles d’alerte classiques avant la migration automatique démarre en juillet 2019.

## <a name="benefits-of-new-alerts"></a>Avantages de nouvelles alertes

Alertes classiques sont remplacés par la nouvelle génération unifiée d’alertes dans Azure Monitor. La nouvelle plateforme alertes présente les avantages suivants :

- Alerte sur un éventail de métriques multidimensionnelles pour [de nombreux services Azure supplémentaires](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Prise en charge des alertes de métrique de nouvelle [règles d’alerte multi-ressources](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) qui réduisent considérablement la surcharge de la gestion de nombreuses règles.
- Mécanisme de notification unifiée
  - [Groupes d’actions](action-groups.md) est un mécanisme de notification modulaire qui fonctionne avec tous les nouveaux types d’alertes (mesures, journaux et journal d’activité)
  - Vous serez également en mesure de tirer parti des nouveaux mécanismes de notification tels que SMS, voix et connecteur ITSM
- Le [alerte expérience unifiée](alerts-overview.md) apporte toutes les alertes sur des signaux différents (activité métrique, journal et le journal) dans un seul endroit

## <a name="before-you-migrate"></a>Avant de migrer

Dans le cadre de la migration, les règles d’alerte classiques sont converties en équivalents nouvelles règles d’alerte et les groupes d’actions sont créées.

- Le format de charge utile de notification, ainsi que les API pour créer et gérer de nouvelles règles d’alerte est différent de celui de règles d’alerte classiques car ils prennent en charge davantage de fonctionnalités. En savoir plus [comment préparer la migration](alerts-prepare-migration.md).

- Certaines règles d’alerte classiques ne peuvent pas être migrés à l’aide de l’outil. [Découvrez les règles qui ne sont pas migrables et apprenez à les migrer](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Processus de migration n’affectera pas l’évaluation de vos règles d’alerte classiques. Elles continueront à s’exécuter et envoyer des alertes jusqu'à ce qu’ils sont migrés et de nouvelles règles d’alerte commencent à évaluer.


## <a name="how-to-use-the-migration-tool"></a>Comment utiliser l’outil de migration

La procédure suivante décrit comment déclencher la migration de vos règles d’alerte classiques dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Moniteur**.

2. Cliquez sur **alertes** puis cliquez sur **gérer les règles d’alerte** ou **afficher les alertes classiques**.

3. Cliquez sur **migrer vers les nouvelles règles** pour accéder à la page d’accueil de migration. Cette page affiche une liste de tous vos abonnements et l’état de migration pour eux.

    ![lancement de la migration](media/alerts-migration/migration-landing.png "migrer les règles")

4. Tous les abonnements qui peuvent être migrés à l’aide de l’outil seront marquées comme **prêt pour la migration**.

    > [!NOTE]
    > L’outil de migration déploie en plusieurs phases à tous les abonnements qui utilisent des règles d’alerte classiques. Dans les premières phases de déploiement, vous pouvez voir certains abonnements pas prêt pour la migration.

5. Sélectionnez un ou plusieurs abonnements, cliquez sur **afficher un aperçu de la migration**

6. Dans cette page, vous pouvez voir les détails des règles d’alerte classiques qui seront migrées pour un abonnement à la fois. Vous pouvez également **télécharger les détails de migration pour cet abonnement** au format .csv.

    ![version préliminaire de la migration](media/alerts-migration/migration-preview.png "afficher un aperçu de la migration")

7. Fournir un ou plusieurs **adresses de messagerie** recevoir les notifications d’état de la migration. Nous enverrons un e-mail lors de la migration terminée ou une action est requise de votre part.

8. Cliquez sur **démarrer la Migration**. Lire les informations affichées dans la boîte de dialogue de confirmation et vérifiez si vous êtes prêt à commencer le processus de migration.

    >[!IMPORTANT]
    > Une fois que vous lancez le processus de migration pour un abonnement, vous ne serez pas en mesure de créer/modifier les règles d’alerte classiques pour l’abonnement. Toutefois, vos règles d’alerte classiques continuera en cours d’exécution et en générant des alertes jusqu'à ce qu’ils soient migrés. Il s’agit pour garantir la fidélité entre les règles d’alerte classiques et les nouvelles règles créées pendant la migration. Une fois la migration est terminée pour votre abonnement, vous ne pouvez pas utiliser les règles d’alerte classiques plus.

    ![migration de confirmation](media/alerts-migration/migration-confirm.png "confirmer démarrer la migration")

9. Comme nous terminer la migration ou que vous avez besoin d’une action de votre part, vous recevrez un courrier électronique sur les adresses e-mail fournies à l’étape 8. Vous pouvez vérifier périodiquement l’état à partir de la page d’accueil de migration dans le portail.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Pourquoi mon abonnement apparaît comme non prêt pour la migration ?**

L’outil de migration est introduites dans les phases à tous les clients. Dans les premières phases, la plupart ou tous vos abonnements peuvent être marqués comme **n’est pas prêt pour la migration**. Toutefois, en moyenne-avril, tous les abonnements doivent être prêts à migrer.

Quand un abonnement est prêt pour la migration, les propriétaires d’abonnements recevra un e-mail de notification de la disponibilité de l’outil. Surveillez votre pour cette notification.

### <a name="who-can-trigger-the-migration"></a>**Qui peut déclencher la migration ?**

Les utilisateurs qui ont le rôle Contributeur de surveillance au niveau de l’abonnement sera en mesure de déclencher la migration. En savoir plus sur [Role Based Access Control pour le processus de migration](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**La durée pendant laquelle la migration va prendre ?**

Pour la plupart des abonnements, migration se termine généralement moins d’une heure. Vous pouvez effectuer le suivi de la progression de la migration à partir de la page d’accueil de migration.  Pendant ce temps, veuillez être assuré que vos alertes sont en cours d’exécution dans le système d’alertes classiques ou une nouvelle.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Que puis-je faire si je rencontre un problème pendant la migration ?**

Veuillez suivre le [guide pour connaître les étapes de mise à jour pour les problèmes que vous pouvez rencontrer lors de la migration de dépannage](alerts-understand-migration.md#common-issues-and-remediations). Si une action est nécessaire de vous pour terminer la migration, vous serez averti sur les adresses e-mail fournies pendant la migration.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer la migration](alerts-prepare-migration.md)
- [Comprendre le fonctionne de l’outil de migration](alerts-understand-migration.md)
