---
title: Créer et gérer des informations d'identification pour les analyses
description: Découvrez les étapes permettant de créer et de gérer des informations d’identification dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 22923742e5389ac2bd6e5268e6dcd9055c23a703
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656063"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Informations d'identification pour l'authentification des sources dans Azure Purview

Cet article explique comment créer des informations d’identification dans Azure Purview. Le fait d’enregistrer les informations d’identification vous permet de réutiliser facilement les informations d’authentification enregistrées pour vos analyses de source de données.

## <a name="prerequisites"></a>Prérequis

- Un coffre de clés Azure. Pour savoir comment en créer un, consultez [Démarrage rapide : Créer un coffre de clés avec le portail Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introduction

Les informations d’identification permettent à Azure Purview de s’authentifier auprès des sources de données que vous avez inscrites. Vous pouvez créer un objet d’informations d’identification pour différents scénarios d’authentification, tels que l’authentification de base nécessitant un nom d’utilisateur et un mot de passe. Les informations d’identification capturent les informations nécessaires à l’authentification, selon le type de méthode d’authentification choisi. Les informations d'identification utilisent vos secrets Azure Key Vault existants pour récupérer des informations d'authentification sensibles pendant le processus de création des informations d'identification.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Utiliser l’identité managée Purview pour configurer des analyses

Si vous utilisez l'identité managée Purview pour configurer des analyses, vous n'aurez pas à créer explicitement d'informations d'identification et à lier votre coffre de clés à Purview pour les stocker. Pour obtenir des instructions détaillées sur l’ajout de l’identité managée Purview en vue d’accéder à l’analyse de vos sources de données, reportez-vous aux sections ci-dessous concernant l’authentification pour chaque source de données :

- [Stockage Blob Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Créer des connexions Azure Key Vault sur votre compte Azure Purview

Avant de pouvoir créer des informations d’identification, vous devez d’abord associer une ou plusieurs de vos instances Azure Key Vault existantes à votre compte Azure Purview.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre compte Azure Purview et ouvrez Azure Purview Studio. Accédez au **Centre de gestion** sur Azure Purview Studio, puis à  **Identifiants**.

2. Dans la page **Informations d’identification**, sélectionnez **Gérer les connexions Key Vault**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gérer les connexions Azure Key Vault":::

3. Sélectionnez **+Nouveau** dans la page Gérer les connexions Key Vault.

4. Renseignez les informations demandées, puis sélectionnez **Créer**.

5. Vérifiez que votre coffre de clés Key Vault a bien été associé à votre compte Azure Purview, en procédant de la façon suivante :

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Afficher les connexions Azure Key Vault à des fins de vérification":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Autoriser l'identité managée Purview à accéder à votre coffre de clés Azure Key Vault

Actuellement, Azure Key Vault prend en charge deux modèles d’autorisation :

- Option 1 : Stratégies d’accès 
- Option 2 : Contrôle d’accès en fonction du rôle 

Avant d’attribuer l’accès à l’identité managée Purview, commencez par identifier votre modèle d’autorisation Azure Key Vault à partir des **stratégies d’accès** aux ressources Key Vault dans le menu. Suivez les étapes ci-dessous en fonction du modèle d’autorisation concerné.  

:::image type="content" source="media/manage-credentials/akv-permission-model.png" alt-text="Modèle d’autorisation Azure Key Vault"::: 

### <a name="option-1---assign-access-using-using-key-vault-access-policy"></a>Option 1 : Attribuer l’accès à l’aide de la stratégie d’accès au coffre de clés  

Suivez ces étapes uniquement si le modèle d’autorisation dans votre ressource Azure Key Vault est défini sur **Stratégie d’accès au coffre** :

1. Accédez à votre coffre de clés Azure Key Vault.

2. Sélectionnez la page **Stratégies d’accès**.

3. Sélectionnez **Ajouter une stratégie d’accès**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Ajouter des MSI Purview à AKV":::

4. Dans la liste déroulante **Autorisations du secret**, sélectionnez les autorisations **Obtenir** et **Lister**.

5. Pour **Sélectionner le principal**, choisissez l’identité managée Purview. Vous pouvez rechercher le fichier MSI Purview en utilisant le nom de l’instance Purview **ou** l’ID d’application de l’identité managée. Les identités composées (nom d’identité managée + ID d’application) ne sont pas prises en charge.

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Ajouter une stratégie d’accès":::

6. Sélectionnez **Ajouter**.

7. Sélectionnez **Enregistrer** pour enregistrer la stratégie d’accès.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Enregistrer la stratégie d'accès":::

### <a name="option-2---assign-access-using-key-vault-azure-role-based-access-control"></a>Option 2 : Attribuer l’accès à l’aide du contrôle d’accès en fonction du rôle Azure de Key Vault 

Suivez ces étapes uniquement si le modèle d’autorisation dans votre ressource Azure Key Vault est défini sur **Contrôle d’accès en fonction du rôle Azure** :

1. Accédez à votre coffre de clés Azure Key Vault.

2. Dans le menu de navigation de gauche, sélectionnez **Contrôle d’accès (IAM)** .

3. Sélectionnez **Ajouter**.

4. Définissez le **rôle** sur **Utilisateur des secrets Key Vault** et entrez le nom de votre compte Azure Purview sous la zone d’entrée **Sélectionner**. Ensuite, sélectionnez Enregistrer pour fournir cette attribution de rôle à votre compte Purview.

   :::image type="content" source="media/manage-credentials/akv-add-rbac.png" alt-text="RBAC Azure Key Vault":::


## <a name="create-a-new-credential"></a>Créer de nouvelles informations d'identification

Ces types d’informations d’identification sont pris en charge dans Purview :

- Authentification de base : vous ajoutez le **mot de passe** comme secret dans le coffre de clés.
- Principal de service : vous ajoutez la **clé du principal de service** comme secret dans le coffre de clés.
- Authentification SQL : vous ajoutez le **mot de passe** comme secret dans le coffre de clés.
- Clé de compte : vous ajoutez la **clé de compte** comme secret dans le coffre de clés.
- ARN du rôle : pour une source de données Amazon S3, ajoutez votre **ARN du rôle** dans AWS. 

Pour plus d’informations, consultez [Ajouter un secret au coffre de clés](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) et [Créer un rôle AWS pour portée](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

Après avoir stocké vos secrets dans le coffre de clés :

1. Dans Azure Purview, accédez à la page Informations d’identification.

2. Créez vos nouvelles informations d’identification en sélectionnant **+ Nouveau**.

3. Fournissez les informations nécessaires. Sélectionnez la **Méthode d’authentification**, ainsi qu’une **Connexion Key Vault** à partir de laquelle sélectionner un secret.

4. Une fois tous les détails renseignés, sélectionnez **Créer**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nouvelles informations d'identification":::

5. Vérifiez que vos nouvelles informations d’identification figurent dans la liste et qu’elles sont prêtes à être utilisées.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Afficher les informations d'identification":::

## <a name="manage-your-key-vault-connections"></a>Gérer vos connexions Key Vault

1. Rechercher des connexions Key Vault par nom

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Rechercher le coffre de clés":::

2. Supprimer une ou plusieurs connexions Key Vault

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Supprimer un coffre de clés":::

## <a name="manage-your-credentials"></a>Gérer vos informations d'identification

1. Recherchez des informations d’identification par nom.
  
2. Sélectionnez et mettez à jour des informations d’identification existantes.

3. Supprimez des informations d’identification.

## <a name="next-steps"></a>Étapes suivantes

[Créer un ensemble de règles d’analyse](create-a-scan-rule-set.md)
