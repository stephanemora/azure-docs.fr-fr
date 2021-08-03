---
title: Guide pratique pour analyser Azure Cosmos DB (API SQL)
description: Ce guide pratique explique en détail comment analyser Azure Cosmos DB (API SQL).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 23825bd3ab41891f775f26e2ee2b052e3a041401
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560098"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Inscription et analyse d’Azure Cosmos DB (API SQL)

Cet article explique comment inscrire un compte Azure Cosmos DB (API SQL) dans Azure Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Azure Cosmos DB (API SQL) prend en charge les analyses complètes et incrémentielles pour capturer les métadonnées et le schéma. Ces analyses permettent également de classer les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Il n’existe qu’une seule façon de configurer l’authentification pour Azure Cosmos DB (API SQL) :

- Clé de compte
 
### <a name="account-key"></a>Clé de compte

Si la méthode d’authentification sélectionnée est **Clé de compte**, vous devez récupérer votre clé d’accès et la stocker dans le coffre de clés :

1. Accédez à votre compte Cosmos DB sur le Portail Azure. 
1. Sélectionnez **Paramètres** > **Clés**. 
1. Copiez une clé PRIMAIRE ou SECONDAIRE à partir des *clés en lecture-écriture* ou des *clés en lecture seule*, puis enregistrez-la quelque part pour les étapes suivantes.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer**, puis entrez le **nom** et la **valeur** comme *clé* de votre compte Azure Cosmos DB.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Inscription d’un compte Azure Cosmos DB (API SQL)

Pour inscrire un nouveau compte Azure Cosmos DB (API SQL) dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Sources** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Cosmos DB (API SQL)** .
1. Sélectionnez **Continue** (Continuer)

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Sur l’écran **Inscrire des sources (Azure Cosmos DB (API SQL))** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer les bases de données Azure Cosmos DB.
3. Sélectionnez un nom de compte Cosmos DB approprié.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="options pour inscrire des sources" border="true":::


## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source de données Azure Cosmos DB que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données. 

   :::image type="content" source="media/register-scan-azure-cosmos-database/set-up-scan-cosmos.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des bases de données spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-cosmos-database/cosmos-database-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-cosmos-database/select-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-cosmos-database/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
