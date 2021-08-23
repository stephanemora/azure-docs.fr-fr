---
title: Inscrire et analyser une base de données Azure pour PostgreSQL
description: Ce tutoriel explique comment analyser une base de données Azure pour PostgreSQL
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: 4f4e9b51a44ee193a946f76499c95d6fe77f2e5e
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665396"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>Inscrire et analyser une base de données Azure pour PostgreSQL

Cet article explique comment inscrire et analyser une base de données Azure pour PostgreSQL.


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge
- **Analyses complètes et incrémentielles** pour capturer les métadonnées et la classification dans des bases de données Azure pour PostgreSQL.

- **Traçabilité** entre les ressources de données pour les activités de copie et de flux de données ADF

### <a name="known-limitations"></a>Limitations connues

Purview prend uniquement en charge l’authentification SQL pour les bases de données Azure pour PostgreSQL.


## <a name="prerequisites"></a>Prérequis

1. Créez un compte Purview si vous n’en avez pas déjà un.

2. Accès réseau entre votre compte Purview et la base de données Azure pour PostgreSQL.

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>Authentification SQL pour une base de données Azure pour PostgreSQL

La connexion d’une base de données Azure Database pour PostgreSQL nécessite un nom de serveur complet et des informations d’identification de connexion. Vous pouvez suivre les instructions fournies dans [CONNECTER ET INTERROGER](/azure/postgresql/connect-python) pour créer une connexion pour une base de données Azure pour PostgreSQL si vous n’en avez pas. Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

1. Accédez à votre coffre de clés dans le portail Azure.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** et la **valeur** comme *mot de passe* de votre base de données Azure PostgreSQL
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez de nouvelles informations d’identification](manage-credentials.md#create-a-new-credential) d’authentification SQL à l’aide du **nom d’utilisateur** et du **mot de passe** pour configurer votre analyse

## <a name="register-an-azure-database-for-postgresql-data-source"></a>Inscrire une source de données de base de données Azure pour PostgreSQL

Pour inscrire une nouvelle base de données Azure pour PostgreSQL dans votre catalogue de données, effectuez les actions suivantes :

1. Accédez à votre compte Purview.

1. Sélectionnez **Sources** dans la barre de navigation à gauche.

1. Sélectionnez **Inscription**.

1. Sous **Inscrire des sources**, sélectionnez **Azure Database pour PostgreSQL**. Sélectionnez **Continuer**.

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Dans l’écran **Inscrire des sources de base de données Azure pour PostgreSQL**, procédez comme suit :

1. Entrez un **nom** pour votre source de données. Il s’agit du nom d’affichage de cette source de données dans votre catalogue.
1. Sélectionnez **À partir de l’abonnement Azure**, puis sélectionnez l’abonnement approprié dans la zone de liste déroulante **Abonnement Azure** et le serveur approprié dans la zone de liste déroulante **Nom du serveur**.
1. Sélectionnez **Inscrire** pour inscrire la source de données. 
 

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source Azure Database pour PostgreSQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="Déclencher une analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * La suppression de votre analyse ne supprime pas les ressources de votre catalogue créées à partir des analyses précédentes.
> * La ressource n’est plus mise à jour avec les modifications de schéma si votre table source a été modifiée et que vous relancez l’analyse de la table source après avoir modifié la description sous l’onglet Schéma de Purview.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
