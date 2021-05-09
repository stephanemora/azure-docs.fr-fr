---
title: Connecter tous les journaux de diagnostics et d’audit Azure SQL Database à Azure Sentinel
description: Découvrez comment utiliser Azure Policy pour appliquer la connexion des journaux de diagnostic et des journaux d’audit de sécurité Azure SQL Database à Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: ba4cefaca7225f25076efa5cdcb81de46aa5cd60
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891317"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Connecter les journaux de diagnostics et d’audit Azure SQL Database

Azure SQL est un moteur de base de données PaaS (Platform-as-a-Service) complètement managé qui prend en charge la plupart des fonctions de gestion de base de données telles que la mise à niveau, la mise à jour corrective, les sauvegardes et la surveillance, sans nécessiter l’intervention de l’utilisateur. 

Le connecteur de base de données Azure SQL vous permet de diffuser les journaux d’audit et de diagnostics de vos bases de données dans Sentinel, de façon à pouvoir surveiller en continu l’activité dans toutes vos instances.

- La connexion des journaux de diagnostics vous permet d’envoyer des journaux de diagnostics de base de données de différents types à votre espace de travail Sentinel.

- La connexion des journaux d’audit vous permet de diffuser des journaux d’audit de sécurité à partir de toutes vos bases de données Azure SQL au niveau du serveur.

En savoir plus sur la [télémétrie de diagnostic Azure SQL Database](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) et sur l’[audit des serveurs Azure SQL](../azure-sql/database/auditing-overview.md).

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour connecter des journaux d’audit, vous devez disposer d’autorisations en lecture et en écriture sur les paramètres d’audit d’Azure SQL Server.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à des ressources de serveur et de base de données Azure SQL, vous devez avoir le rôle Propriétaire pour l’étendue de l’attribution de stratégie.

## <a name="connect-to-azure-sql-database"></a>Connexion à la base de données Azure SQL

Ce connecteur utilise Azure Policy pour appliquer une configuration unique de streaming de journaux Azure SQL à une collection d’instances, définie en tant qu’étendue. Le connecteur Azure SQL Database envoie deux types de journaux à Azure Sentinel : les journaux de diagnostic (à partir des bases de données SQL) et les journaux d’audit (au niveau du serveur SQL). Vous pouvez voir les types de journaux ingérés à partir des bases de données et des serveurs Azure SQL sur le côté gauche de la page du connecteur, sous **Types de données**.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Bases de données Azure SQL** dans la galerie des connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, notez les deux catégories de journaux que vous pouvez connecter.

### <a name="connect-diagnostics-logs"></a>Connecter des journaux de diagnostics

1. Développez **Diffuser en continu les journaux de diagnostic de vos bases de données Azure SQL à grande échelle**.

1. Sélectionnez le bouton **Lancer l’Assistant Affectation Azure Policy**.

    L’Assistant d’attribution de stratégie s’ouvre, prêt à créer une stratégie nommée **Déployer : Configurer les paramètres de diagnostic pour les bases de données SQL pour l’envoi à un espace de travail Log Analytics**.

    1. Sous l’onglet **Informations de base**, cliquez sur le bouton avec les trois points sous **Étendue** pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Dans l’onglet **Paramètres**, laissez les deux premiers paramètres tels quels. Choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**. Les champs de liste déroulante restants représentent les types de journaux de diagnostic disponibles. Conservez la valeur « True » pour tous les types de journaux que vous souhaitez ingérer.

    1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

### <a name="connect-audit-logs"></a>Connecter des journaux d’audit

1. De retour dans la page du connecteur, développez **Diffuser en continu les journaux d’audit de vos bases de données Azure SQL au niveau du serveur à grande échelle**.

1. Sélectionnez le bouton **Lancer l’Assistant Affectation Azure Policy**.

    L’Assistant d’attribution de stratégie s’ouvre, prêt à créer une stratégie nommée **Déployer : Configurer les paramètres d’audit pour les bases de données SQL pour l’envoi à un espace de travail Log Analytics**.

    1. Sous l’onglet **Informations de base**, cliquez sur le bouton avec les trois points sous **Étendue** pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Sous l’onglet **Paramètres**, choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**. Laissez le paramètre **Effet** tel quel.

    1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment utiliser Azure Policy pour connecter des journaux de diagnostics et d’audit de la base de données Azure SQL à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
