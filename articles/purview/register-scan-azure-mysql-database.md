---
title: Inscrire et analyser une base de données Azure MySQL Database
description: Ce tutoriel explique comment analyser une base de données Azure MySQL.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: a5edf1b85d97798e5ed159433961c4c0d76cfcca
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114208205"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>Inscrire et analyser une base de données Azure MySQL Database

Cet article explique comment inscrire et analyser une base de données Azure MySQL.


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge
- **Analyses complètes et incrémentielles** pour capturer les métadonnées et la classification dans des bases de données Azure MySQL.

- **Traçabilité** entre les ressources de données pour les activités de copie et de flux de données ADF

### <a name="known-limitations"></a>Limitations connues
Purview prend uniquement en charge l’authentification SQL pour les bases de données Azure MySQL.


## <a name="prerequisites"></a>Prérequis

1. Créez un compte Purview si vous n’en avez pas déjà un.

2. Accès réseau entre votre compte Purview et la base de données Azure MySQL.

### <a name="set-up-authentication-for-a-scan"></a>Configurer l’authentification pour une analyse

Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

Suivez les instructions de [CREATE DATABASES AND USERS](../mysql/howto-create-users.md) pour créer une connexion pour votre base de données Azure MySQL.

1. Accédez à votre coffre de clés dans le portail Azure.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** et la **valeur** comme *mot de passe* de votre base de données Azure SQL Database.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez de nouvelles informations d’identification](manage-credentials.md#create-a-new-credential) d’authentification SQL à l’aide du **nom d’utilisateur** et du **mot de passe** pour configurer votre analyse.

## <a name="register-an-azure-mysql-database-data-source"></a>Inscrire une source de données Azure MySQL

Pour inscrire une nouvelle base de données Azure MySQL Database dans votre catalogue de données, effectuez les actions suivantes :

1. Accédez à votre compte Purview.

1. Sélectionnez **Sources** dans la barre de navigation à gauche.

1. Sélectionnez **Inscription**.

1. Sous **Inscrire des sources**, sélectionnez **Azure MySQL Database**. Sélectionnez **Continuer**.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Dans l’écran **Inscrire des sources (Azure MySQL Database)** , procédez comme suit :

1. Entrez un **nom** pour votre source de données. Il s’agit du nom d’affichage de cette source de données dans votre catalogue.
1. Sélectionnez **À partir de l’abonnement Azure**, puis sélectionnez l’abonnement approprié dans la zone de liste déroulante **Abonnement Azure** et le serveur approprié dans la zone de liste déroulante **Nom du serveur**.
1. Sélectionnez **Inscrire** pour inscrire la source de données. 

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source Azure Database pour MySQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez l’identifiant pour vous connecter à votre source de données et vérifiez votre connexion pour vous assurer que votre identifiant est correctement configuré.

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.-->

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="examiner votre analyse" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * La suppression de votre analyse ne supprime pas vos ressources créées à partir des analyses Azure MySQL Database précédentes.
> * La ressource n’est plus mise à jour avec les modifications de schéma si votre table source a été modifiée et que vous relancez l’analyse de la table source après avoir modifié la description sous l’onglet Schéma de Purview.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
