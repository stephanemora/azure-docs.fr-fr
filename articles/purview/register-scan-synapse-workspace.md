---
title: Comment analyser des espaces de travail Azure Synapse Analytics
description: Découvrez comment analyser un espace de travail Azure Synapse dans votre catalogue de données Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: a74e88d72d1e7109b6e0acfa81485476eed9e00b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562430"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>Inscrire et analyser des espaces de travail Azure Synapse Analytics

Cet article explique comment inscrire un espace de travail Azure Synapse dans Azurez Purview et configurer une analyse sur celui-ci.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Les analyses d’espaces de travail Azure Synapse Analytics prennent en charge la capture de métadonnées et de schémas pour les bases de données SQL dédiées et serverless qu’ils contiennent. Ces analyses permettent également de classer les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Configuration requise

- Avant d’inscrire vos sources de données, créez un compte Azure Purview. Pour plus d’informations, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vérifiez que vous êtes bien administrateur de la source de données Azure Purview.
- Configurez l’authentification comme décrit dans les sections suivantes.

## <a name="register-and-scan-an-azure-synapse-workspace"></a>Inscrire et analyser un espace de travail Azure Synapse

> [!IMPORTANT]
> Pour analyser correctement votre espace de travail, procédez comme suit et appliquez les autorisations exactement telles qu’elles sont décrites dans les sections suivantes.

### <a name="step-1-register-your-source"></a>**Étape 1** : inscription de votre source

> [!NOTE]
> Seuls les utilisateurs disposant d’au moins un rôle *Lecteur* sur l’espace de travail Azure Synapse et qui sont également *administrateurs de source de données* dans Azure Purview peuvent mener à bien cette étape.

Pour inscrire une nouvelle source Azure Synapse dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Azure Purview.
1. Dans le volet gauche, sélectionnez **Sources**.
1. Sélectionnez **Inscription**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Synapse Analytics (multiple)** .
1. Sélectionnez **Continuer**.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Capture d’écran d’une sélection de sources dans Azure Purview, incluant Azure Synapse Analytics.":::

1. Sur la page **Inscrire les sources (Azure Synapse Analytics)** , procédez comme suit :

    a. Entrez un **Nom**. Il sera utilisé pour répertorier la source de données dans le catalogue.  
    b. Si vous le souhaitez, choisissez un **abonnement** pour filtrer les valeurs.  
    c. Dans la liste déroulante **Nom de l’espace de travail**, sélectionnez l’espace de travail que vous utilisez.  
    d. Dans les listes déroulantes des points de terminaison, les points de terminaison SQL sont renseignés automatiquement en fonction de la sélection de votre espace de travail.  
    e. Dans la liste déroulante **Sélectionner une collection**, choisissez la collection que vous utilisez ou, si vous le souhaitez, créez-en une.  
    f. Ensuite, sélectionnez **Inscrire** pour inscrire la source de données.
    
    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Capture d’écran de la page « Inscrire les sources (Azure Synapse Analytics) » qui permet de saisir des informations sur la source Azure Synapse.":::


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**Étape 2** : application des autorisations pour énumérer les contenus de l’espace de travail Azure Synapse

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>Configurer l’authentification pour l’énumération des ressources de bases de données SQL dédiées

1. Dans le portail Azure, accédez à la ressource de l’espace de travail Azure Synapse.  
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** . 

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle à la ressource.
   
1. Sélectionnez le bouton **Ajouter**.   
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview, qui représente son identité de service managé (ou MSI, pour « Managed Service Identity »).
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.

> [!NOTE]
> Si vous prévoyez d’inscrire et d’analyser plusieurs espaces de travail Azure Synapse dans votre compte Azure Purview, vous pouvez aussi attribuer le rôle à partir d’un niveau supérieur, tel qu’un groupe de ressources ou un abonnement. 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>Configurer l’authentification pour l’énumération des ressources de bases de données SQL serverless

Pour que Purview puisse énumérer vos ressources de base de données SQL serverless, vous devrez définir l’authentification pour trois emplacements : l’espace de travail Synapse, le stockage associé et les bases de données serverless. Pour ce faire, procédez comme suit :

1. Dans le portail Azure, accédez à la ressource de l’espace de travail Azure Synapse.  
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** . 

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle à la ressource.
   
1. Sélectionnez le bouton **Ajouter**.   
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview, qui représente son identité de service managé (ou MSI, pour « Managed Service Identity »).
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.
1. Dans le portail Azure, accédez au **groupe de ressources** ou à l’**abonnement** dans lequel se trouve l’espace de travail Azure Synapse.
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** . 

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle dans le champ **Groupe de ressources** ou **Abonnement**. 

1. Sélectionnez le bouton **Ajouter**. 
1. Définissez le rôle **Lecteur des données blob du stockage**, puis entrez le nom de votre compte Azure Purview (qui représente sa MSI) dans la zone **Sélectionner**. 
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.
1. Accédez à votre espace de travail Azure Synapse et ouvrez Synapse Studio.
1. Sélectionnez l’onglet **Données** sur la gauche.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté d’une de vos bases de données, puis démarrez un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) aux bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**Étape 3** : application des autorisations pour analyser les contenus de l’espace de travail

Vous pouvez configurer l’authentification pour une source Azure Synapse de deux manières :

- Utiliser une identité managée
- Utiliser un principal de service

> [!IMPORTANT]
> Ces étapes pour les bases de données serverless ne s’appliquent **pas** aux bases de données répliquées. Actuellement, dans Synapse, les bases de données serverless qui sont répliquées à partir des bases de données Spark sont en lecture seule. Pour en savoir plus, consultez [cette page](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database).

> [!NOTE]
> Vous devez configurer l’authentification sur chaque base de données SQL dédiée dans votre espace de travail Azure Synapse que vous souhaitez inscrire et analyser. Les autorisations mentionnées dans les sections suivantes pour une base de données SQL serverless s’appliquent à toutes les bases de données dans votre espace de travail. Autrement dit, vous ne devez configurer l’authentification qu’une seule fois.

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>Utiliser une identité managée pour les bases de données SQL dédiées

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL dédiées.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.

   > [!NOTE]
   > Pour exécuter les commandes de la procédure suivante, vous devez être *administrateur Azure Synapse* sur l’espace de travail. Pour plus d’informations sur les autorisations Azure Synapse Analytics, consultez la page [Guide pratique pour configurer le contrôle d’accès pour votre espace de travail Synapse](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **db_datareader** sur la base de données SQL dédiée. Pour ce faire, exécutez la commande suivante dans votre script SQL :

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```
#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>Utiliser une identité managée pour les bases de données SQL serverless

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données** et procédez comme suit pour **chaque base de données que vous souhaitez analyser**.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté de votre base de données, puis démarrez un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **db_datareader** sur les bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>Utiliser un principal de service pour les bases de données SQL dédiées

> [!NOTE]
> Vous devez d’abord configurer de nouvelles *informations d’identification* de type *Principal de service* en suivant les instructions de la page [Informations d'identification pour l'authentification des sources dans Azure Purview](manage-credentials.md).

1. Accédez à votre **espace de travail Azure Synapse**.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL dédiées.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.
1. Ajoutez l’**ID du principal de service** comme **db_datareader** sur la base de données SQL dédiée. Pour ce faire, exécutez la commande suivante dans votre script SQL :

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```
> [!NOTE]
> Répétez l’étape précédente pour toutes les bases de données SQL dédiées dans votre espace de travail Synapse. 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>Utiliser un principal de service pour les bases de données SQL serverless

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL serverless.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.
1. Ajoutez l’**ID de principal de service** sur les bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. Ajoutez l’**ID de principal de service** en tant que **db_datareader** sur chaque base de données SQL serverless que vous voulez analyser. Pour ce faire, exécutez la commande suivante dans votre script SQL :
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**Étape 4** : configuration de l’accès à l’espace de travail Synapse dans le pare-feu

1. Dans le portail Azure, accédez à l’espace de travail Azure Synapse. 

1. Dans le volet gauche, sélectionnez **Pare-feux**.

1. Cliquez sur **ON** (Activer) pour **Autoriser les services et ressources Azure à accéder à cet espace de travail**.

1. Sélectionnez **Enregistrer**.

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**Étape 5** : configuration d’une analyse sur l’espace de travail

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source de données que vous avez inscrite.

1. Sélectionnez **Afficher les détails**, puis l'onglet **Nouvelle analyse**. Vous pouvez également sélectionner l’icône d’action rapide **Analyse** sur la mosaïque source.

1. Dans le volet d’informations **Analyse**, dans la zone **Nom**, entrez un nom pour l’analyse.
1. Dans la liste déroulante **Type**, sélectionnez les types de ressources que vous souhaitez analyser au sein de cette source. **Base de données SQL** est le seul type que nous prenons en charge actuellement dans un espace de travail Azure Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Capture d’écran du volet d’informations de l’analyse de source Azure Synapse.":::

1. Dans la liste déroulante **Informations d’identification**, sélectionnez vos informations d’identification pour vous connecter aux ressources dans votre source de données. 
  
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom.

1.  Sélectionnez **Continuer** pour poursuivre. 

1.  Sélectionnez **Ensemble de règles d’analyse**, puis le type **Azure Synapse SQL**. Vous pouvez également créer des ensembles de règles d’analyse inline.

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution **hebdomadaire, mensuelle** ou **unique**.

1. Vérifiez votre analyse, puis sélectionnez **Enregistrer** pour terminer la configuration.   

#### <a name="view-your-scans-and-scan-runs"></a>Afficher vos analyses et exécutions d’analyse

1. Pour afficher les détails de la source, sélectionnez **Afficher les détails** dans la mosaïque située sous la section Sources. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Capture d’écran de la page Détails de la source Azure Synapse Analytics."::: 

1. Pour afficher les détails de l’exécution de l’analyse, accédez à la page **Détails de l’analyse**.

    * La **barre d’état** présente un bref résumé de l’état d’exécution des ressources enfants. L’état de l’analyse de l’espace de travail est affiché.  
    * Le vert indique une exécution de l’analyse réussie, le rouge indique l’échec de l’analyse et le gris indique que l’exécution de l’analyse est toujours en cours.  
    * Vous pouvez afficher des informations plus détaillées sur les exécutions d’analyse en cliquant dessus.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Capture d’écran de la page de détails de l’analyse Azure Synapse Analytics." lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * En bas de la page de **détails de la source**, vous pouvez consulter un résumé des exécutions d’analyse récentes ayant échoué. Ici aussi, vous pouvez cliquer sur les exécutions d’analyse pour accéder à des informations plus détaillées sur ces dernières.

#### <a name="manage-your-scans"></a>Gestion de vos analyses

Pour modifier, supprimer ou annuler une analyse, procédez comme suit :

1. Accédez au centre de gestion. Dans la section **Sources et analyse**, sélectionnez **Sources de données**, puis la source de données que vous souhaitez gérer.

1. Sélectionnez l’analyse que vous souhaitez gérer, puis sélectionnez **Modifier**.

   - Pour supprimer votre analyse, sélectionnez **Supprimer**.
   - Si une analyse est en cours d’exécution, vous pouvez l’annuler.

## <a name="next-steps"></a>Étapes suivantes

- [Naviguer dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)   
