---
title: Connecter les journaux de diagnostics et d’audit Azure SQL Database à Azure Sentinel
description: Découvrez comment connecter les journaux de diagnostics et les journaux d’audit de sécurité Azure SQL Database à Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103882"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Connecter les journaux de diagnostics et d’audit Azure SQL Database

Azure SQL est un moteur de base de données PaaS (Platform-as-a-Service) complètement managé qui prend en charge la plupart des fonctions de gestion de base de données telles que la mise à niveau, la mise à jour corrective, les sauvegardes et la surveillance, sans intervention de l’utilisateur. 

Le connecteur Azure SQL Database vous permet de diffuser les journaux d’audit et de diagnostics de vos bases de données dans Sentinel, ce qui vous permet de surveiller en continu l’activité dans toutes vos instances.

- La connexion des journaux de diagnostics vous permet d’envoyer des journaux de diagnostics de base de données de différents types de données à votre espace de travail Sentinel.

- La connexion des journaux d’audit vous permet de diffuser des journaux d’audit de sécurité à partir de toutes vos bases de données Azure SQL au niveau du serveur.

En savoir plus sur la [supervision des bases de données Azure SQL](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour connecter des journaux d’audit, vous devez disposer d’autorisations en lecture et en écriture sur les paramètres d’audit d’Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Connexion à la base de données Azure SQL
    
1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Azure SQL Database** dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, notez les deux catégories de journaux que vous pouvez connecter.

### <a name="connect-diagnostics-logs"></a>Connecter des journaux de diagnostics

1. Sous **Journaux de diagnostics**, développez **Activer manuellement les journaux de diagnostics sur chacune de vos bases de données Azure SQL**.

1. Sélectionnez le lien **Ouvrir Azure SQL >** pour ouvrir le panneau des ressources **Azure SQL**.

1. **(Facultatif)** Pour faciliter la recherche de votre ressource de base de données, sélectionnez **Ajouter un filtre** dans la barre des filtres en haut.
    1. Dans la liste déroulante **Filtre**, sélectionnez **Type de ressource**.
    1. Dans la liste déroulante **Valeur**, désélectionnez **Sélectionner tout**, puis sélectionnez **Base de données SQL**.
    1. Cliquez sur **Appliquer**.
    
1. Sélectionnez la ressource de base de données dont vous souhaitez envoyer les journaux de diagnostics à Azure Sentinel.

    > [!NOTE]
    > Pour chaque ressource de base de données dont vous souhaitez collecter les journaux, vous devez répéter ce processus, en commençant par cette étape.

1. Dans la page des ressources de la base de données que vous avez sélectionnée, sous **Surveillance** dans le menu de navigation, sélectionnez **Paramètres de diagnostic**.

    1. Sélectionnez le lien **+ Ajouter un paramètre de diagnostic** en bas du tableau.

    1. Dans l’écran **Paramètre de diagnostic**, entrez un nom dans le champ **Nom du paramètre de diagnostic**.
    
    1. Dans la colonne **Détails de la destination**, cochez la case **Envoyer à l’espace de travail Log Analytics**. Deux nouveaux champs s’affichent sous celle-ci. Choisissez les valeurs **Abonnement** et **Espace de travail Log Analytics** appropriées (où se trouve Azure Sentinel).

    1. Dans la colonne **Détails de la catégorie**, cochez les cases des types de journaux et de métriques que vous souhaitez ingérer. Nous vous recommandons de sélectionner tous les types disponibles sous **journal** et **métrique**.

    1. En haut de l’écran, sélectionnez **Enregistrer**.

- Vous pouvez également utiliser le **Script PowerShell** fourni pour connecter vos journaux de diagnostics.
    1. Sous **Journaux de diagnostics**, développez **Activer avec un script PowerShell**.

    1. Copiez le bloc de code et collez-le dans PowerShell.

### <a name="connect-audit-logs"></a>Connecter des journaux d’audit

1. Sous **Journaux d’audit (préversion)** , développez **Activer les journaux d’audit sur toutes les bases de données Azure SQL (au niveau du serveur)** .

1. Sélectionnez le lien **Ouvrir Azure SQL >** pour ouvrir le panneau des ressources **Serveurs SQL**.

1. Sélectionnez le serveur SQL Server dont vous souhaitez envoyer les journaux d’audit à Azure Sentinel.

    > [!NOTE]
    > Pour chaque ressource de serveur dont vous souhaitez collecter les journaux, vous devez répéter ce processus, en commençant par cette étape.

1. À partir de la page des ressources du serveur que vous avez sélectionné, sous **Sécurité** dans le menu de navigation, sélectionnez **Audit**.

    1. Basculez **Activer l’audit Azure SQL** sur **ON** (Activé).

    1. Sous **Destination du journal d’audit**, sélectionnez **Log Analytics (préversion)** .
    
    1. Dans la liste des espaces de travail qui s’affiche, choisissez votre espace de travail (où se trouve Azure Sentinel).

    1. En haut de l’écran, sélectionnez **Enregistrer**.

- Vous pouvez également utiliser le **Script PowerShell** fourni pour connecter vos journaux de diagnostics.
    1. Sous **Journaux d’audit**, développez **Activer avec un script PowerShell**.

    1. Copiez le bloc de code et collez-le dans PowerShell.


> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie de connecteurs de données et des icônes de connexion en regard des noms de types de données) s’affichent comme *Connectés* (en vert) uniquement si les données ont été ingérées au cours des deux dernières semaines. Après deux semaines passées sans ingestion de données, le connecteur déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des journaux de diagnostics et d’audit de la base de données Azure SQL à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).