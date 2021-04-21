---
title: Comment analyser des espaces de travail Azure Synapse
description: Découvrez comment analyser un espace de travail Synapse dans votre catalogue de données Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031277"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Inscrire et analyser des espaces de travail Azure Synapse

Cet article explique comment inscrire un espace de travail Azure Synapse dans Purview et configurer une analyse sur celui-ci.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Les analyses d’espaces de travail Azure Synapse prennent en charge la capture de métadonnées et de schémas pour les bases de données SQL dédiées et serverless qu’ils contiennent. Il classe également les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.
- Configuration de l’authentification comme décrit dans les sections ci-dessous

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Configuration de l’authentification pour l’énumération des ressources de bases de données SQL dédiées dans un espace de travail Synapse

1. Dans le portail Azure, accédez au **groupe de ressources** ou à l’**abonnement** dans lequel se trouve l’espace de travail Synapse.  
1. Sélectionnez  **Access Control (IAM)**  dans le menu de navigation de gauche 
1. Vous devez être le propriétaire ou l’administrateur de l’accès utilisateur pour ajouter un rôle à l’abonnement ou au groupe de ressources. Cliquez sur le bouton *+Ajouter*. 
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview (il s’agit de son fichier MSI) dans la zone d’entrée Sélectionner. Cliquez sur *Enregistrer* pour terminer l’attribution de rôle.
1. Suivez les étapes 2 à 4 ci-dessus pour ajouter également le rôle **Lecteur des données BLOB du stockage** pour la fonctionnalité MSI d’Azure Purview sur le groupe de ressources ou l’abonnement dans lequel se trouve l’espace de travail Synapse.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Configuration de l’authentification pour l’énumération des ressources de bases de données SQL serverless dans un espace de travail Synapse

> [!NOTE]
> Pour exécuter ces commandes, vous devez être un **administrateur Synapse** sur l’espace de travail. Pour en savoir plus sur les autorisations Synapse, cliquez [ici](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Accédez à votre espace de travail Synapse.
1. Accédez à la section **Données** et à l’une de vos bases de données SQL serverless.
1. Cliquez sur l’icône de sélection et démarrer un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **sysadmin** sur les bases de données SQL serverless en exécutant la commande ci-dessous dans votre script SQL :
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Configuration de l’authentification pour l’analyse des ressources dans un espace de travail Synapse

Il existe trois façons de configurer l’authentification pour une source Azure Synapse :

- Identité managée
- Principal de service
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Utilisation d’une identité managée pour les bases de données SQL dédiées

1. Accédez à votre **espace de travail Synapse**.
1. Accédez à la section **Données** et à l’une de vos bases de données SQL serverless.
1. Cliquez sur l’icône de sélection et démarrer un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **db_owner** sur la base de données SQL dédiée en exécutant la commande ci-dessous dans votre script SQL :

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Utilisation d’une identité managée pour les bases de données SQL serverless

1. Accédez à votre **espace de travail Synapse**.
1. Accédez à la section **Données** et à l’une de vos bases de données SQL serverless.
1. Cliquez sur l’icône de sélection et démarrer un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **sysadmin** sur les bases de données SQL serverless en exécutant la commande ci-dessous dans votre script SQL :
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Utilisation d’un principal de service pour les bases de données SQL dédiées

> [!NOTE]
> Vous devez d’abord configurer les nouvelles **informations d’identification** de type Principal de service en suivant les instructions fournies [ici](manage-credentials.md).

1. Accédez à votre **espace de travail Synapse**.
1. Accédez à la section **Données** et à l’une de vos bases de données SQL serverless.
1. Cliquez sur l’icône de sélection et démarrer un nouveau script SQL.
1. Ajoutez l’**ID du principal de service** comme **db_owner** sur la base de données SQL dédiée en exécutant la commande ci-dessous dans votre script SQL :

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Utilisation d’un principal de service pour les bases de données SQL serverless

1. Accédez à votre **espace de travail Synapse**.
1. Accédez à la section **Données** et à l’une de vos bases de données SQL serverless.
1. Cliquez sur l’icône de sélection et démarrer un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **sysadmin** sur les bases de données SQL serverless en exécutant la commande ci-dessous dans votre script SQL :
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Vous devez configurer l’authentification sur chaque base de données SQL dédiée dans votre espace de travail Synapse que vous souhaitez inscrire et analyser. Les autorisations mentionnées ci-dessus pour la base de données SQL serverless s’appliquent à toutes les bases de données de votre espace de travail, c’est-à-dire que vous ne devrez l’exécuter qu’une seule fois.
    
## <a name="register-an-azure-synapse-source"></a>Inscrire une source Azure Synapse

Pour inscrire une nouvelle source Azure Synapse dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Sources** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sur **Inscrire des sources**, sélectionnez **Azure Synapse Analytics (multiple)** .
1. Sélectionnez **Continue** (Continuer)

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Configurer une source Azure Synapse":::

Sur l’écran **Inscrire des sources (Azure Synapse Analytics)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
1. Si vous le souhaitez, choisissez un **abonnement** pour filtrer les valeurs.
1. **Sélectionnez un nom d’espace de travail Synapse** dans la liste déroulante. Les points de terminaison SQL sont automatiquement renseignés en fonction de votre espace de travail sélectionné. 
1. Sélectionnez une **collection** ou créez-en une (facultatif)
1. **Terminez** l’inscription de la source de données

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Renseigner les détails de la source Azure Synapse":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Accédez à la section **Sources**.

1. Sélectionnez la source de données que vous avez inscrite.

1. Cliquez sur Afficher les détails, puis sélectionnez **+ Nouvelle analyse** ou utilisez l’icône d’action rapide d’analyse sur la vignette source

1. Renseignez le *nom* et sélectionnez tous les types de ressources que vous souhaitez analyser dans cette source. **Base de données SQL** est le seul type que nous prenons en charge actuellement dans un espace de travail Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Analyse d’une source Azure Synapse":::

1. Sélectionnez les **informations d’identification** pour vous connecter aux ressources dans votre source de données. 
  
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom.
1.  Cliquez sur **Continuer** pour continuer. 

1.  Sélectionnez un **ensemble de règles d’analyse** de type Azure Synapse SQL. Vous pouvez également créer des ensembles de règles d’analyse inline.

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution **hebdomadaire/mensuelle** ou **unique**

1. Vérifiez votre analyse et sélectionnez Enregistrer pour terminer la configuration   

## <a name="viewing-your-scans-and-scan-runs"></a>Affichage des analyses et des exécutions d’analyse

1. Pour afficher les détails de la source, cliquez sur **afficher les détails** dans la vignette sous la section Sources. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Détails de la source Azure Synapse"::: 

1. Pour afficher les détails de l’exécution de l’analyse, accédez à la page **Détails de l’analyse**.
    1. La *barre d’état* présente un bref résumé de l’état d’exécution des ressources enfants. Elle s’affiche sur l’analyse au niveau de l’espace de travail.
    1. Le vert indique que l’opération a réussi, tandis que le rouge indique qu’elle a échoué. Le gris indique que l’analyse est en cours
    1. Vous pouvez cliquer sur chaque analyse pour afficher plus de détails

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Détails de l’analyse Azure Synapse" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Affichez un résumé des exécutions d’analyse récentes qui ont échoué en bas de la page Détails de la source. Vous pouvez également cliquer pour afficher des détails plus précis sur ces exécutions.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gérer vos analyses : modifier, supprimer ou annuler
Pour gérer ou supprimer une analyse, procédez comme suit :

- Accédez au centre d’administration. Sélectionnez Sources de données sous la section Sources et analyse, puis sélectionnez la source de données souhaitée.

- Sélectionnez l’analyse que vous souhaitez gérer. Vous pouvez modifier l’analyse en sélectionnant Modifier.

- Vous pouvez supprimer votre analyse en sélectionnant Supprimer.
- Si une analyse est en cours d’exécution, il est également possible de l’annuler.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)   