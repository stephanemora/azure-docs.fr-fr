---
title: Résoudre les problèmes liés à vos connexions dans Azure Purview
description: Cet article explique les étapes à suivre pour résoudre les problèmes liés à vos connexions dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/04/2021
ms.openlocfilehash: 812c7871a6fd9501164530f0e9feee92f275426b
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111526485"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Résoudre les problèmes liés à vos connexions dans Azure Purview

Cet article explique comment résoudre les erreurs de connexion lors de la configuration d’analyses sur des sources de données dans Azure Purview.

## <a name="permission-the-credential-on-the-data-source"></a>Autoriser les informations d’identification sur la source de données

Si vous utilisez une identité managée ou un principal de service comme méthode d’authentification pour les analyses, vous devez autoriser ces identités à avoir accès à votre source de données.

Il existe des instructions spécifiques pour chaque type de source :

- [Plusieurs sources Azure](register-scan-azure-multiple-sources.md#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)
- [Stockage Blob Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Explorateur de données Azure](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>Vérification du contrôle d’accès en fonction du rôle Azure pour énumérer les ressources Azure dans Azure Purview Studio

### <a name="registering-single-azure-data-source"></a>Inscription d’une seule source de données Azure
Pour inscrire une source de données dans Azure Purview, comme un stockage Blob Azure ou une base de données Azure SQL, vous devez disposer au minimum du rôle **Lecteur** sur la ressource ou avoir hérité d’une étendue plus grande comme un groupe de ressources ou un abonnement. Notez que certains rôles RBAC Azure, par exemple Administrateur de la sécurité, ne disposent pas d’un accès en lecture pour afficher les ressources Azure dans le plan de contrôle.  

Pour ce faire, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), accédez à la ressource que vous essayez d’inscrire dans Azure Purview. Si vous pouvez afficher la ressource, c’est certainement parce que vous avez déjà au moins un rôle Lecteur sur la ressource. 
2. Sélectionnez **Contrôle d’accès (IAM)**  > **Attributions de rôles**.
3. Recherchez par nom ou adresse e-mail l’utilisateur qui tente d’inscrire des sources de données dans Azure Purview.
4. Vérifiez si des attributions de rôles comme Lecteur figurent dans la liste ou ajoutez une nouvelle attribution de rôle si nécessaire.

### <a name="scanning-multiple-azure-data-sources"></a>Analyse de plusieurs sources de données Azure
1. Dans le [portail Azure](https://portal.azure.com), accédez à l’abonnement ou au groupe de ressources.  
2. Sélectionnez  **Contrôle d’accès (IAM)**   dans le menu de gauche. 
3. Sélectionnez **+Ajouter**. 
4. Dans la zone **Sélectionner l’entrée**, sélectionnez le rôle **Lecteur** et entrez le nom de votre compte Azure Purview (qui représente son nom MSI). 
5. Sélectionnez **Enregistrer** pour terminer l’attribution de rôle.
6. Répétez les étapes ci-dessus pour ajouter l’identité de l’utilisateur qui tente de créer une analyse pour plusieurs sources de données dans Azure Purview.

## <a name="scanning-data-sources-using-private-link"></a>Analyse de sources de données avec Private Link 
Si le point de terminaison public est limité sur vos sources de données, vous devez configurer un runtime d’intégration auto-hébergé et créer des informations d’identification pour analyser des sources de données Azure avec Private Link. 

> [!IMPORTANT]
> L’analyse de plusieurs sources de données contenant des bases de données Azure SQL avec _Refuser l’accès au réseau public_ échoue. Pour analyser ces sources de données avec un point de terminaison privé, utilisez plutôt l’option d’inscription de source de données unique.

Pour plus d’informations sur la configuration d’un runtime d’intégration auto-hébergé, consultez [Points de terminaison privés d’ingestion et sources d’analyse dans des réseaux privés et virtuels, et derrière des points de terminaison privés](catalog-private-link.md#ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints).

Pour plus d’informations sur la création d’informations d’identification dans Azure Purview, consultez [Informations d’identification pour l’authentification des sources dans Azure Purview](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Stockage de vos informations d’identification dans votre coffre de clés et utilisation du nom et de la version appropriés pour le secret

Vous devez également stocker vos informations d’identification dans votre instance Azure Key Vault et utiliser le nom et la version appropriés pour le secret.

Pour ce faire, procédez comme suit :

1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres** > **Secrets**.
1. Sélectionnez le secret que vous utilisez pour vous authentifier auprès de votre source de données à des fins d’analyse.
1. Sélectionnez la version que vous prévoyez d’utiliser et vérifiez que le mot de passe ou la clé de compte est correct en cliquant sur **Afficher la valeur du secret**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Vérifier les autorisations de l’identité managée Purview sur votre coffre Azure Key Vault

Vérifiez que les autorisations appropriées ont été configurées pour que l’identité managée Purview accède à votre coffre Azure Key Vault.

Pour cela, procédez comme suit :

1. Accédez à votre coffre de clés et à la section **Stratégies d’accès**.

1. Vérifiez que votre identité managée Purview s’affiche sous la section *Stratégies d’accès actuelles* avec au minimum des autorisations **Get** et **List** sur les secrets.

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Image représentant la liste déroulante des options pour les autorisations Get et List":::

Si vous ne voyez pas votre identité managée Purview dans la liste, suivez les étapes décrites dans [Créer et gérer des informations d’identification pour les analyses](manage-credentials.md) afin de pouvoir l’ajouter. 

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
