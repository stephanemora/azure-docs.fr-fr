---
title: Écrire un audit sur un compte de stockage situé derrière un réseau virtuel et un pare-feu
description: Configurer l’audit pour écrire des événements de base de données sur un compte de stockage situé derrière un réseau virtuel et un pare-feu
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 556b9c2861461ffdfc63f5607cca2157226e15bb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658305"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Écrire un audit sur un compte de stockage situé derrière un réseau virtuel et un pare-feu

L’audit pour [Azure SQL Database](sql-database-technical-overview.md) et [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) prend en charge l’écriture d’événements de base de données sur un [compte Stockage Azure](../storage/common/storage-account-overview.md) situé derrière un réseau virtuel et un pare-feu. 

Cet article explique deux façons de configurer Azure SQL Server et le compte de stockage Azure pour cette option. La première utilise le Portail Azure, la seconde utilise REST.

### <a name="background"></a>Arrière-plan

[Réseau virtuel Microsoft Azure (VNet)](../virtual-network/virtual-networks-overview.md) est le bloc de construction fondamental pour votre réseau privé dans Azure. Le réseau virtuel permet à de nombreux types de ressources Azure, telles que les machines virtuelles (VM) Azure, de communiquer de manière sécurisée entre elles, avec Internet et avec les réseaux locaux. Le réseau virtuel est similaire à un réseau traditionnel dans votre propre centre de données, mais avec les avantages supplémentaires de l’infrastructure Azure, tels que la mise à l’échelle, la disponibilité et l’isolation.

Pour en savoir plus sur les concepts de réseau virtuel, les meilleures pratiques et bien plus encore, consultez [Présentation de Réseau virtuel Microsoft Azure](../virtual-network/virtual-networks-overview.md).

Pour en savoir plus sur la création d’un réseau virtuel, consultez [Démarrage rapide : Créer un réseau virtuel avec le portail Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Prérequis

Pour que l’audit écrive dans un compte de stockage situé derrière un réseau virtuel ou un pare-feu, les conditions préalables suivantes sont requises :

> [!div class="checklist"]
> * Un compte de stockage universel v2. Si vous disposez d’un compte de stockage universel v1 ou d’un compte de stockage blob, [mettez-les à niveau vers un compte de stockage universel v2](../storage/common/storage-account-upgrade.md). Pour plus d’informations, consultez [Types de comptes de stockage](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Le compte de stockage doit se trouver sur le même abonnement et au même emplacement que le serveur Azure SQL Database. 
> * Le compte Stockage Azure requiert `Allow trusted Microsoft services to access this storage account`. Définissez ce paramètre sur le compte de stockage **Pare-feu et réseaux virtuels**.
> * Vous devez disposer de l’autorisation `Microsoft.Authorization/roleAssignments/write` sur le compte de stockage sélectionné. Pour plus d’informations, voir [Rôles intégrés Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurer dans le portail Azure

Connectez-vous au [Portail Azure](https://portal.azure.com) avec votre abonnement. Accédez au groupe de ressources et au serveur de base de données Azure SQL.

1. Cliquez sur **Audit** sous le titre Sécurité. Sélectionnez **Activé**.

2. Sélectionnez **Stockage**. Sélectionnez le compte de stockage dans lequel les journaux seront enregistrés. Le compte de stockage doit respecter les conditions requises indiquées dans [Conditions préalables](#prerequisites).

3. Ouvrez **Détails du stockage**. 

  > [!NOTE]
  > Si le compte de stockage sélectionné se trouve derrière un réseau virtuel, le message suivant s’affiche :
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Si vous ne voyez pas ce message, cela signifie que le compte de stockage ne se trouve pas derrière un réseau virtuel.

4. Sélectionnez le nombre de jours pour la période de rétention. Cliquez ensuite sur **OK**. Une fois la période de conservation écoulée, les journaux sont supprimés.

5. Sélectionnez **Enregistrer** sur vos paramètres d’audit.

Vous avez correctement configuré l’audit pour écrire dans un compte de stockage situé derrière un réseau virtuel ou un pare-feu. 

## <a name="configure-with-rest-commands"></a>Configurer avec des commandes REST

En guise d’alternative à l’utilisation du Portail Azure, vous pouvez utiliser des commandes REST pour configurer l’audit afin d’écrire des événements de base de données sur un compte de stockage situé derrière un réseau virtuel et un pare-feu. 

Les exemples de scripts de cette section vous obligent à mettre à jour le script avant de les exécuter. Remplacez les valeurs suivantes dans le script :

|Exemple de valeur|Exemple de description|
|:-----|:-----|
|`<subscriptionId>`| ID d’abonnement Azure|
|`<resource group>`| Resource group|
|`<sql database server>`| Nom du serveur Azure SQL Database.|
|`<administrator login>`| Compte Administrateur de la base de données SQL |
|`<complex password>`| Mot de passe complexe pour le compte Administrateur|

Pour configurer l’audit SQL afin d’écrire dans un compte de stockage situé derrière un réseau virtuel ou un pare-feu :

1. Inscrivez votre serveur Azure SQL Database auprès d’Azure Active Directory (AAD). Utilisez PowerShell ou l’API REST.

   **PowerShell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**API REST**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate) :

   Exemple de requête

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Corps de la demande

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à votre compte de stockage. Recherchez **Contrôle d’accès (IAM)** , puis cliquez sur **+ Ajouter une attribution de rôle**. Attribuez le rôle RBAC de **Contributeur aux données blob du stockage** au serveur SQL Azure qui héberge la base de données Azure SQL que vous avez enregistrée auprès d’Azure Active Directory (Azure AD), comme à l’étape précédente.

   > [!NOTE]
   > Seuls les membres dotés du privilège Propriétaire peuvent effectuer cette étape. Pour découvrir les divers rôles intégrés pour les ressources Azure, reportez-vous à [Rôles intégrés Azure](../role-based-access-control/built-in-roles.md).

3. Configurez la [stratégie d’audit des blobs du serveur SQL Azure](/rest/api/sql/server%20auditing%20settings/createorupdate), sans spécifier de *storageAccountAccessKey* :

   Exemple de requête

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Corps de la demande

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

- [Créer ou mettre à jour une stratégie d’audit de base de données (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Créer ou mettre à jour une stratégie de serveur (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel, puis une règle de réseau virtuel pour Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Règles de réseau virtuel : opérations avec les API REST](/rest/api/sql/virtualnetworkrules)
- [Utiliser des points de terminaison de service de réseau virtuel et des règles pour les serveurs de base de données](sql-database-vnet-service-endpoint-rule-overview.md)
