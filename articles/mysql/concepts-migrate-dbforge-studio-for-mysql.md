---
title: Se connecter à Azure Database pour MySQL à l’aide de dbForge Studio pour MySQL
description: L’article explique comment se connecter à Azure Database pour MySQL Server via dbForge Studio pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107043"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Se connecter à Azure Database pour MySQL à l’aide de dbForge Studio pour MySQL

Pour vous connecter à Azure DB pour MySQL à l’aide de dbForge Studio pour MySQL :

1. Dans le menu Base de données, sélectionnez Nouvelle connexion.

2. Fournissez un nom d’hôte et des informations d’identification de connexion.

3. Sélectionnez le bouton Tester la connexion pour vérifier la configuration.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Connexion Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrer une base de données à l’aide de la fonctionnalité Sauvegarde et restauration

Le Studio permet de migrer des bases de données vers Azure de nombreuses façons dont le choix dépend uniquement de vos besoins. Si vous devez déplacer la base de données entière, il est préférable d’utiliser la fonctionnalité Sauvegarde et restauration. Dans cet exemple, nous allons migrer la base de données *sakila* qui réside sur MySQL Server vers Azure Database pour MySQL. La logique sous-tendant le processus de migration à l’aide de la fonctionnalité Sauvegarde et restauration de dbForge Studio pour MySQL consiste à créer une sauvegarde de la base de données MySQL, puis à la restaurer dans Azure Database pour MySQL.

### <a name="back-up-the-database"></a>Sauvegarder la base de données

1. Dans le menu Base de données, pointez sur Sauvegarde et restauration, puis sélectionnez Sauvegarder la base de données. L’Assistant Sauvegarde de base de données s’affiche.

2. Sous l’onglet Contenu de la sauvegarde de l’Assistant Sauvegarde de base de données, sélectionnez les objets de base de données à sauvegarder.

3. Sous l’onglet Options, configurez le processus de sauvegarde en fonction de vos besoins.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Options de l’Assistant Sauvegarde":::

4. Ensuite, spécifiez le comportement de traitement des erreurs et les options de journalisation.

5. Sélectionnez Sauvegarder.

### <a name="restore-the-database"></a>Restaurer la base de données

1. Connectez-vous à Azure Database pour MySQL comme décrit ci-dessus.

2. Cliquez avec le bouton droit sur le corps de l’explorateur de base de données, pointez sur Sauvegarde et restauration, puis sélectionnez Restaurer la base de données.

3. Dans l’Assistant Restauration de base de données qui s’ouvre, sélectionnez un fichier avec une sauvegarde de base de données.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Étape de restauration":::

4. Sélectionnez Restaurer.

5. Vérifiez le résultat.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrer une base de données à l’aide de la fonctionnalité Copier des bases de données

La fonctionnalité Copier les bases de données est similaire à la fonctionnalité Sauvegarde et restauration, à ceci près que vous n’avez pas besoin de deux étapes pour migrer une base de données. Qui plus est, la fonctionnalité permet de transférer au moins deux bases de données en une seule fois. La fonctionnalité Copier les bases de données est disponible uniquement dans l’édition Enterprise de dbForge Studio pour MySQL.
Dans cet exemple, nous migrons la base de données *world_x* du serveur MySQL vers Azure Database pour MySQL.
Pour migrer une base de données à l’aide de la fonctionnalité Copier les bases de données :

1. Dans le menu Base de données, sélectionnez Copier les bases de données. 

2. Sous l’onglet Copier les bases de données qui s’affiche, spécifiez les connexions source et cible, puis sélectionnez la ou les bases de données à migrer. Accédez à la connexion Azure MySQL, puis sélectionnez la base de données *world_x*. Sélectionnez la flèche verte pour lancer le processus.

3. Vérifiez le résultat.

Suite à nos efforts de migration de base de données, la base de données *world_x* s’est correctement affichée dans Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Résultat de la copie de bases de données":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrer une base de données à l’aide d’outils de comparaison de schémas et de données

dbForge Studio pour MySQL intègre un quelques outils qui permettent de migrer des bases de données MySQL, des schémas MySQL et/ou des données vers Azure. Le choix de la fonctionnalité dépend de vos besoins et des exigences de votre projet. Si vous devez déplacer de manière sélective une base de données, c’est-à-dire migrer certaines tables MySQL vers Azure, il est préférable d’utiliser la fonctionnalité Comparaison de schémas et de données.
Dans cet exemple, nous migrons la base de données *world* qui réside sur MySQL Server vers Azure Database pour MySQL. La logique sous-tendant le processus de migration à l’aide de la fonctionnalité Comparaison de schémas et de données de dbForge Studio pour MySQL consiste à créer une base de données vide dans Azure Database pour MySQL, à la synchroniser avec la base de données MySQL requise d’abord avec l’outil de comparaison de schémas, puis avec l’outil de comparaison de données. Ainsi, les schémas et données MySQL sont correctement déplacés vers Azure.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Se connecter à Azure Database pour MySQL et créer une base de données vide

Connectez-vous Azure MySQL Database et créez une base de données vide.

### <a name="step-2-schema-synchronization"></a>Étape 2. Synchronisation de schémas

1. Dans le menu Comparaison, sélectionnez Nouvelle comparaison de schémas.
L’Assistant Nouvelle comparaison de schémas s’affiche.

2. Sélectionnez la source et la cible, puis spécifiez les options de comparaison de schémas. Sélectionnez Comparer.

3. Dans la grille Résultats de la comparaison qui s’affiche, sélectionnez des objets pour la synchronisation. Sélectionnez le bouton flèche verte pour ouvrir l’Assistant Synchronisation de schémas.

4. Suivez les étapes de l’Assistant Configuration de la synchronisation. Sélectionnez Synchroniser pour déployer les modifications.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Assistant Synchronisation de schémas":::

### <a name="data-comparison"></a>Comparaison de données

1. Dans le menu Comparaison, sélectionnez Nouvelle comparaison de données. L’Assistant Nouvelle comparaison de données s’affiche.

2. Sélectionnez la source et la cible, puis spécifiez les options de comparaison de données et modifiez les mappages si nécessaire. Sélectionnez Comparer.

3. Dans la grille Résultats de la comparaison qui s’affiche, sélectionnez des objets pour la synchronisation. Sélectionnez le bouton flèche verte pour ouvrir l’Assistant Synchronisation de données.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Résultat de la comparaison des données":::

4. Suivez les étapes de l’Assistant Configuration de la synchronisation. Sélectionnez Synchroniser pour déployer les modifications.

5. Vérifiez le résultat.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Résultat de la synchronisation des données":::

## <a name="summary"></a>Résumé

Aujourd’hui, de plus en plus d’entreprises migrent leurs bases de données vers Azure Database pour MySQL, car ce service de base de données est facile à configurer, à gérer et à mettre à l’échelle. Cette migration n’a pas besoin d’être pénible. dbForge Studio pour MySQL offre des outils de migration parfaits qui peuvent faciliter considérablement le processus. Le Studio permet de facilement configurer, enregistrer, modifier, automatiser et planifier le transfert de base de données.

## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de MySQL](overview.md)