---
title: Gérer l’accès aux espaces de travail, aux données et aux pipelines
description: Découvrez comment gérer le contrôle d’accès aux espaces de travail, aux données et aux pipelines dans Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: c4304aeadf2950c1a91ee50ba9ecd895b2561b41
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461353"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gérer l’accès aux espaces de travail, aux données et aux pipelines

Découvrez comment gérer le contrôle d’accès aux espaces de travail, aux données et aux pipelines dans Azure Synapse Analytics.

> [!NOTE]
> Dans la version en disponibilité générale, Azure RBAC sera plus développé via l’introduction de rôles Azure spécifiques à Synapse

## <a name="access-control-for-workspace"></a>Contrôle d’accès pour l’espace de travail

Pour un déploiement de production dans un espace de travail Azure Synapse, nous vous suggérons d’organiser votre environnement de manière à faciliter le provisionnement des utilisateurs et des administrateurs.

> [!NOTE]
> L’approche adoptée ici consiste à créer plusieurs groupes de sécurité, puis à configurer l’espace de travail pour les utiliser de manière cohérente. Une fois les groupes configurés, l’administrateur doit uniquement gérer l’appartenance aux groupes de sécurité.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Étape 1 : Configurer des groupes de sécurité avec des noms suivant ce modèle

1. Créer un groupe de sécurité nommé `Synapse_WORKSPACENAME_Users`
2. Créer un groupe de sécurité nommé `Synapse_WORKSPACENAME_Admins`
3. Ajout de `Synapse_WORKSPACENAME_Admins` à `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Découvrez comment créer un groupe de sécurité réseau en consultant [cet article](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Découvrez comment ajouter un groupe de sécurité à partir d’un autre groupe de sécurité en consultant [cet article](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME : vous devez remplacer cette partie par le nom réel de votre espace de travail.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Étape 2 : Préparer le compte ADLS Gen2 par défaut

Quand vous provisionniez votre espace de travail, vous deviez choisir un compte [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) et un conteneur pour le système de fichiers que devait utiliser l’espace de travail.

1. Ouvrez le [portail Azure](https://portal.azure.com)
2. Accédez au compte Azure Data Lake Storage Gen2.
3. Accédez au conteneur (système de fichiers) que vous avez choisi pour l’espace de travail Azure Synapse
4. Sélectionnez **Contrôle d’accès (IAM)** .
5. Affectez les rôles suivants :
   1. Rôle **Lecteur** à : `Synapse_WORKSPACENAME_Users`
   2. Rôle **Propriétaire des données Blob du stockage** à : `Synapse_WORKSPACENAME_Admins`
   3. Rôle **Contributeur aux données Blob du stockage** à : `Synapse_WORKSPACENAME_Users`
   4. Rôle **Propriétaire des données Blob du stockage** à : `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME : vous devez remplacer cette partie par le nom réel de votre espace de travail.

### <a name="step-3-configure-the-workspace-admin-list"></a>Étape 3 : Configurer la liste des administrateurs de l’espace de travail

1. Accédez à l’[**interface utilisateur web d’Azure Synapse**](https://web.azuresynapse.net).
2. Accédez à **Gérer**  > **Sécurité** > **Contrôle d’accès**.
3. Sélectionnez **Ajouter un administrateur**, puis sélectionnez `Synapse_WORKSPACENAME_Admins`.

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Étape 4 : Configurer l’accès administrateur SQL pour l’espace de travail

1. Accédez au [Portail Azure](https://portal.azure.com).
2. Accédez à votre espace de travail.
3. Accédez à **Paramètres** > **Administrateur Active Directory**.
4. Sélectionnez **Définir l’administrateur**.
5. Sélectionnez `Synapse_WORKSPACENAME_Admins`
6. Choisissez **Select** (Sélectionner)
7. Sélectionnez **Enregistrer**.

> [!NOTE]
> WORKSPACENAME : vous devez remplacer cette partie par le nom réel de votre espace de travail.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Étape 5 : Ajouter et supprimer des utilisateurs et des administrateurs dans des groupes de sécurité

1. Ajoutez des utilisateurs qui ont besoin d’un accès administratif à `Synapse_WORKSPACENAME_Admins`.
2. Ajoutez tous les autres utilisateurs à `Synapse_WORKSPACENAME_Users`.

> [!NOTE]
> Découvrez comment ajouter un utilisateur comme membre à un groupe de sécurité en consultant [cet article](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal).
> 
> WORKSPACENAME : vous devez remplacer cette partie par le nom réel de votre espace de travail.

## <a name="access-control-to-data"></a>Contrôle d’accès aux données

Le contrôle d’accès aux données sous-jacentes se divise en trois parties :

- L’accès du plan de données au compte de stockage (déjà configuré à l’étape 2)
- L’accès du plan de données aux bases de données SQL (pour les pools SQL dédiés et le pool SQL serverless)
- Création d’informations d’identification pour les bases de données de pool SQL serverless dans le compte de stockage

## <a name="access-control-to-sql-databases"></a>Contrôle d’accès aux bases de données SQL

> [!TIP]
> Les étapes ci-dessous doivent être exécutées pour **chaque** base de données SQL afin d’accorder l’accès utilisateur à toutes les bases de données SQL, sauf dans la section [Autorisation au niveau du serveur](#server-level-permission) où vous pouvez attribuer un rôle sysadmin à l’utilisateur.

### <a name="serverless-sql-pool"></a>Pool SQL serverless

Cette section illustre, à l’aide de différents exemples, comment accorder à un utilisateur une autorisation d’accès à une base de données particulière ou des autorisations de serveur complètes.

#### <a name="database-level-permission"></a>Autorisation au niveau base de données

Pour accorder à un utilisateur l’accès à une base de données de pool SQL serverless **unique**, suivez les étapes de cet exemple :

1. Créer des informations de connexion (LOGIN)

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Créer un utilisateur (USER)

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Ajouter USER aux membres du rôle spécifié

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Remplacez l’alias par l’alias de l’utilisateur auquel vous voulez accorder l’accès et le domaine par le domaine d’entreprise que vous utilisez.

#### <a name="server-level-permission"></a>Autorisation au niveau du serveur

Pour accorder à un utilisateur l’accès complet à **toutes** les bases de données de pool SQL serverless, suivez cet exemple :

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Pool SQL dédié

Pour accorder à un utilisateur l’accès à une base de données SQL **unique**, effectuez les étapes suivantes :

1. Créez l’utilisateur dans la base de données en exécutant la commande suivante qui cible la base de données souhaitée dans le sélecteur de contexte (liste déroulante permettant de sélectionner des bases de données) :

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Accordez à l’utilisateur un rôle pour accéder à la base de données :

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* et *db_datawriter* peuvent fonctionner avec les autorisations en lecture/écriture si vous ne souhaitez pas accorder l’autorisation *db_owner*.
> Pour qu’un utilisateur Spark puisse lire et écrire directement à partir de Spark dans/depuis un pool SQL dédié, l’autorisation *db_owner* est nécessaire.

Après avoir créé les utilisateurs, vérifiez que vous pouvez interroger le compte de stockage en utilisant le pool SQL serverless.

## <a name="access-control-to-workspace-pipeline-runs"></a>Contrôle d’accès aux exécutions de pipelines de l’espace de travail

### <a name="workspace-managed-identity"></a>Identité managée par l’espace de travail

> [!IMPORTANT]
> Pour exécuter des pipelines comprenant des jeux de données ou des activités qui référencent un pool SQL dédié, l’identité de l’espace de travail doit être autorisée à accéder directement au pool SQL.

Exécutez les commandes suivantes sur chaque pool SQL dédié pour autoriser l’identité managée de l’espace de travail à exécuter des pipelines sur la base de données du pool SQL :

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Cette autorisation peut être supprimée en exécutant le script suivant sur le même pool SQL :

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble de l’identité managée par l’espace de travail Synapse, consultez [Identité managée de l’espace de travail Azure Synapse](../security/synapse-workspace-managed-identity.md). Pour plus d’informations sur les principaux de base de données, consultez [Principaux](https://msdn.microsoft.com/library/ms181127.aspx). Pour plus d’informations sur les rôles de base de données, consultez l’article [Rôles de base de données](https://msdn.microsoft.com/library/ms189121.aspx).
