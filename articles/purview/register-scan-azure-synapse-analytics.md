---
title: Comment analyser des pools SQL dédiés
description: Ce guide pratique explique en détail comment analyser des pools SQL dédiés dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 26d4327c5763a1296cd492730004b80947269afa
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218334"
---
# <a name="register-and-scan-dedicated-sql-pools-formerly-sql-dw"></a>Inscrire et analyser des pools SQL dédiés (anciennement SQL DW)

> [!NOTE]
> Si vous cherchez à inscrire et à analyser une base de données SQL dédiée au sein d’un espace de travail Synapse, vous devez suivre les instructions fournies [ici](register-scan-synapse-workspace.md).

Cet article explique comment inscrire et analyser une instance de pool SQL dédié (anciennement SQL DW) dans Purview.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Azure Synapse Analytics (anciennement SQL DW) prend en charge les analyses complètes et incrémentielles pour capturer les métadonnées et le schéma. Ces analyses permettent également de classer les données automatiquement selon des règles de classification système et personnalisées.

### <a name="known-limitations"></a>Limitations connues

> * Azure Purview prend en charge 300 colonnes au maximum sous l’onglet Schéma. Au-delà, il affiche « Additional-Columns-Truncated » (Colonnes-Supplémentaires-Tronquées). 

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.
- Accès réseau entre le compte Purview et Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Il existe trois façons de configurer l’authentification pour Azure Synapse Analytics :

- Identité managée
- Authentification SQL
- Principal de service

    > [!Note]
    > Seule la connexion principale au niveau du serveur (créée par le processus de configuration) ou les membres du rôle de base de données `loginmanager` dans la base de données master peuvent créer des connexions. Cela nécessite environ 15 minutes une fois l’autorisation accordée. Le compte Purview doit disposer des autorisations appropriées pour pouvoir analyser la ou les ressources.

### <a name="managed-identity-recommended"></a>Identité managée (recommandé) 
   
Votre compte Purview possède sa propre identité managée, qui est fondamentalement votre nom Purview lorsque vous l’avez créé. Vous devez créer un utilisateur Azure AD dans Azure Synapse Analytics (anciennement SQL DW) avec le nom d’identité managée exact de Purview en suivant les conditions préalables et le didacticiel sur [Créer des utilisateurs Azure AD avec des applications Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Exemple de syntaxe SQL pour créer l’utilisateur et accorder l’autorisation :

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

L’authentification doit avoir l’autorisation d’obtenir des métadonnées pour la base de données, les schémas et les tables. Elle doit également être en mesure d’interroger les tables à échantillonner pour la classification. Il est recommandé d’attribuer l’autorisation `db_datareader` à l’identité.

### <a name="service-principal"></a>Principal de service

Si vous souhaitez appliquer l’authentification par principal de service pour les analyses, vous pouvez utiliser un principal de service existant ou en créer un. 

> [!Note]
> Si vous devez créer un principal de service, procédez comme suit :
> 1. Accédez au [portail Azure](https://portal.azure.com).
> 1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
> 1. Sélectionnez **Inscriptions d’applications**.
> 1. Sélectionnez **+ Nouvelle inscription d’application**.
> 1. Entrez un nom pour l’**application** (nom du principal de service).
> 1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
> 1. Pour l’URI de redirection, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ni qu’elle fonctionne.
> 1. Sélectionnez ensuite **Inscription**.

Il est nécessaire de récupérer l’ID d’application et le secret du principal de service :

1. Accédez à votre principal de service sur le [Portail Azure](https://portal.azure.com).
1. Copiez les valeurs **ID d’application (client)** dans **Vue d’ensemble** et **Secret client** dans **Certificats et secrets**.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** de votre choix et la **valeur** comme **secret client** de votre principal de service.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse. 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Octroi de l’accès du principal de service à votre instance Azure Synapse Analytics (anciennement SQL DW)

En outre, vous devez créer un utilisateur Azure AD dans Azure Synapse Analytics en suivant les conditions préalables et le didacticiel sur [Créer des utilisateurs Azure AD avec des applications Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Exemple de syntaxe SQL pour créer l’utilisateur et accorder l’autorisation :

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview a besoin de l’**ID d’application (client)** et du **secret client** pour effectuer l’analyse.

### <a name="sql-authentication"></a>Authentification SQL

Vous pouvez suivre les instructions dans [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) pour créer une connexion pour Azure Synapse Analytics (anciennement SQL DW) si vous n’en avez pas déjà une.

Lorsque la méthode d’authentification sélectionnée est **Authentification SQL**, vous devez vous procurer votre mot de passe et le stocker dans le coffre de clés :

1. Obtenir le mot de passe pour votre connexion SQL
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **Nom** et la **Valeur** comme *mot de passe* pour votre connexion SQL.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

## <a name="register-a-sql-dedicated-pool-formerly-sql-dw"></a>Inscrire un pool SQL dédié (anciennement SQL DW)

Pour inscrire un nouveau pool SQL dédié dans Purview, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans **Inscrire des sources**, sélectionnez **Pool SQL dédié (anciennement SQL DW)** .
1. Sélectionnez **Continue** (Continuer)

Sur l’écran **Inscrire des sources (Azure Synapse Analytics)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer les espaces de travail Azure Synapse.
3. Sélectionnez un espace de travail Azure Synapse.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source de pool SQL dédié que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
