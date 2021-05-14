---
title: Utiliser dbForge Studio for MySQL pour migrer une base de données MySQL vers Azure Database pour MySQL
description: L’article explique comment migrer vers Azure Database pour MySQL à l’aide de dbForge Studio for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: f29d64da678ca65c91a90857cfa5a0e533cc72d6
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989159"
---
# <a name="migrate-data-to-azure-database-for-mysql-with-dbforge-studio-for-mysql"></a>Migrer vers Azure Database pour MySQL à l’aide de dbForge Studio for MySQL

Vous cherchez à déplacer vos bases de données MySQL vers Azure Database pour MySQL ? Pensez à utiliser les outils de migration de dbForge Studio for MySQL. Grâce à eux, le transfert de base de données peut être configuré, enregistré, modifié, automatisé et planifié.

Pour réaliser les exemples de cet article, vous devez télécharger et installer [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/).

## <a name="connect-to-azure-database-for-mysql"></a>Se connecter à la base de données Azure pour MySQL

1. Dans dbForge Studio for MySQL, sélectionnez **Nouvelle connexion** dans le menu **Base de données**.

1. Fournissez un nom d’hôte et des informations d’identification de connexion.

1. Sélectionnez **Tester la connexion** pour vérifier la configuration.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Capture d’écran montrant un test de connexion réussi à Azure Database pour MySQL." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png":::

## <a name="migrate-with-the-backup-and-restore-functionality"></a>Migrer avec la fonctionnalité Sauvegarde et restauration

Vous pouvez choisir parmi plusieurs options lorsque vous utilisez dbForge Studio for MySQL pour migrer des bases de données vers Azure. Si vous devez déplacer la base de données entière, il est préférable d’utiliser la fonctionnalité **Sauvegarde et restauration**.

Dans cet exemple, nous migrons la base de données *sakila* du serveur MySQL vers Azure Database pour MySQL. La logique sous-jacente à l’utilisation de la fonctionnalité **Sauvegarde et restauration** consiste à créer une sauvegarde de la base de données MySQL, puis à la restaurer dans Azure Database pour MySQL.

### <a name="back-up-the-database"></a>Sauvegarder la base de données

1. Dans dbForge Studio for MySQL, sélectionnez **Sauvegarder la base de données** dans le menu **Sauvegarde et restauration**. L’**Assistant Sauvegarde de base de données** s’affiche.

1. Sous l’onglet **Contenu de la sauvegarde** de l’**Assistant Sauvegarde de base de données**, sélectionnez les objets de base de données à sauvegarder.

1. Sous l’onglet **Options**, configurez le processus de sauvegarde en fonction de vos besoins.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Capture d’écran montrant le volet Options de l’Assistant Sauvegarde." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png":::

1. Sélectionnez **Suivant**, puis spécifiez le comportement du traitement des erreurs et les options de journalisation.

1. Sélectionnez **Sauvegarder**.

### <a name="restore-the-database"></a>Restaurer la base de données

1.  Dans dbForge Studio for MySQL, connectez-vous à Azure Database pour MySQL. [Reportez-vous aux instructions](#connect-to-azure-database-for-mysql).

1. Sélectionnez **Restaurer une base de données** dans le menu **Sauvegarde et restauration**. L’**Assistant Restauration de base de données** s’affiche.

1. Dans l’**Assistant Restauration de base de données**, sélectionnez un fichier avec une sauvegarde de base de données.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Capture d’écran montrant l’étape Restaurer de l’Assistant Restauration de base de données." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png":::

1. Sélectionnez **Restaurer**.

1. Vérifiez le résultat.

## <a name="migrate-with-the-copy-databases-functionality"></a>Migrer à l’aide de la fonctionnalité Copier des bases de données

La fonctionnalité **Copier des bases de données** dans dbForge Studio for MySQL est similaire à **Sauvegarde et restauration**, à ceci près qu’elle ne nécessite pas deux étapes pour migrer une base de données. Elle vous permet également de transférer plusieurs bases de données à la fois.

>[!NOTE]
> La fonctionnalité **Copier des bases de données** est disponible uniquement dans l’édition Enterprise de dbForge Studio for MySQL.

Dans cet exemple, nous migrons la base de données *world_x* du serveur MySQL vers Azure Database pour MySQL.

Pour migrer une base de données à l’aide de la fonctionnalité Copier les bases de données :

1. Dans dbForge Studio for MySQL, sélectionnez **Copier des bases de données** dans le menu **Base de données**. 

1. Dans l’onglet **Copier des bases de données**, spécifiez la connexion source et la connexion cible. Sélectionnez également les bases de données à migrer. 

   Accédez à la connexion Azure MySQL, puis sélectionnez la base de données *world_x*. Sélectionnez la flèche verte pour démarrer le processus.

1. Vérifiez le résultat.

Vous verrez que la base de données *world_x* s’affiche dans Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Capture d’écran montrant les résultats de la fonctionnalité Copier des bases de données." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png":::

## <a name="migrate-a-database-with-schema-and-data-comparison"></a>Migrer une base de données avec comparaison des schémas et des données

Vous pouvez choisir parmi plusieurs options lorsque vous utilisez dbForge Studio for MySQL pour migrer des bases de données, des schémas ou des données vers Azure. Si vous devez déplacer certaines tables d’une base de données MySQL vers Azure, il est préférable d’utiliser les fonctionnalités **Comparaison de schémas** et **Comparaison de données**.

Dans cet exemple, nous migrons la base de données *world* du serveur MySQL vers Azure Database pour MySQL. 

La logique sous-jacente à l’utilisation de la fonctionnalité **Sauvegarde et restauration** consiste à créer une sauvegarde de la base de données MySQL, puis à la restaurer dans Azure Database pour MySQL.

La logique sous-jacente de cette approche consiste à créer une base de données vide dans Azure Database pour MySQL et à la synchroniser avec la base de données MySQL source. Nous utilisons tout d’abord l’outil **Comparaison de schémas**, puis la fonctionnalité **Comparaison de données**. Ces étapes garantissent que les schémas et les données MySQL sont correctement déplacés vers Azure.

Pour réaliser cet exercice, vous devez d’abord [vous connecter à Azure Database pour MySQL](#connect-to-azure-database-for-mysql) et créer une base de données vide.

### <a name="schema-synchronization"></a>Synchronisation de schémas

1. Dans le menu **Comparaison**, sélectionnez **Nouvelle comparaison de schémas**. L’**Assistant Nouvelle comparaison de schémas** s’affiche.

1. Choisissez votre source et votre cible, puis spécifiez les options de comparaison des schémas. Sélectionnez **Comparer**.

1. Dans la grille Résultats de la comparaison qui s’affiche, sélectionnez des objets pour la synchronisation. Sélectionnez le bouton flèche verte pour ouvrir l’**Assistant Synchronisation de schémas**.

1. Suivez les étapes de l’Assistant pour configurer la synchronisation. Sélectionnez **Synchroniser** pour déployer les modifications.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Capture d’écran montrant l’Assistant Synchronisation de schémas." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png":::

### <a name="data-comparison"></a>Comparaison de données

1. Dans le menu **Comparaison**, sélectionnez **Nouvelle comparaison de données**. L’**Assistant Nouvelle comparaison de données** s’affiche.

1. Choisissez votre source et votre cible, puis spécifiez les options de comparaison des données. Modifiez les mappages le cas échéant, puis sélectionnez **Comparer**.

1. Dans la grille Résultats de la comparaison qui s’affiche, sélectionnez des objets pour la synchronisation. Sélectionnez le bouton flèche verte pour ouvrir l’**Assistant Synchronisation de données**.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Capture d’écran montrant les résultats de la comparaison des données." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png":::

1. Suivez les étapes de l’Assistant Configuration de la synchronisation. Sélectionnez **Synchroniser** pour déployer les modifications.

1. Vérifiez le résultat.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Capture d’écran montrant les résultats de l’Assistant Synchronisation de données." lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png":::

## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de MySQL](overview.md)
