---
title: Contrôle d’accès en fonction du rôle (RBAC) dans Azure Cosmos DB
description: Découvrez comment Azure Cosmos DB assure la protection des bases de données grâce à l’intégration d’Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: cbb97dd260e5aee53595afc24e577ce08334e2b2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027016"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Contrôle d’accès en fonction du rôle (RBAC) dans Azure Cosmos DB

Azure Cosmos DB fournit un contrôle d’accès en fonction du rôle (RBAC) intégré pour les scénarios de gestion courants dans Azure Cosmos DB. Une personne disposant d’un profil dans Azure Active Directory peut assigner ces rôles RBAC à des utilisateurs, des groupes, des principaux de services ou des identités gérées pour accorder ou refuser l’accès aux ressources et aux opérations sur les ressources de Azure Cosmos DB. L’attribution des rôles est limitée au seul accès au plan de contrôle, ce qui inclut l’accès aux comptes, bases de données, conteneurs et offres (débit) d’Azure Cosmos.

## <a name="built-in-roles"></a>Rôles intégrés

Voici les rôles intégrés pris en charge par Azure Cosmos DB :

|**Rôle intégré**  |**Description**  |
|---------|---------|
|[Contributeur de compte DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Gérer des comptes Azure Cosmos DB.|
|[Lecteur de compte Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Lire les données de comptes Azure Cosmos DB.|
|[Opérateur de sauvegarde Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Peut envoyer une requête de restauration d’une base de données Azure Cosmos ou d’un conteneur.|
|[Opérateur Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Peut provisionner des comptes, des bases de données et des conteneurs Azure Cosmos, mais pas d’accéder aux clés nécessaires pour accéder aux données.|

> [!IMPORTANT]
> La prise en charge du RBAC dans Azure Cosmos DB s’applique uniquement aux opérations de plan de contrôle. Les opérations du plan de données sont sécurisées à l’aide de clés principales ou de jetons de ressources. Pour en savoir plus, veuillez consulter [Sécuriser l’accès aux données dans Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestion des identités et des accès

Le volet **Contrôle d’accès (IAM)** du Portail Microsoft Azure permet de configurer le contrôle d’accès en fonction du rôle sur les ressources Azure Cosmos. Les rôles sont appliqués aux utilisateurs, aux groupes, aux principaux de service et aux identités gérées dans Active Directory. Vous pouvez utiliser des rôles intégrés ou des rôles personnalisés pour les individus et les groupes. La capture d’écran suivante montre l’intégration Active Directory (RBAC) à l’aide du contrôle d’accès (IAM) dans le Portail Microsoft Azure :

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Contrôle d’accès (IAM) dans le portail Azure – Démonstration de la sécurité de la base de données":::

## <a name="custom-roles"></a>Rôles personnalisés

En plus des rôles intégrés, les utilisateurs peuvent également créer des [rôles personnalisés](../role-based-access-control/custom-roles.md) dans Azure et appliquer ces rôles aux principaux de service pour tous les abonnements dans leur locataire Active Directory. Les rôles personnalisés permettent aux utilisateurs de créer des définitions de rôles RBAC avec un ensemble personnalisé d’opérations de fournisseur de ressources. Pour savoir quelles opérations sont disponibles pour créer des rôles personnalisés pour Azure Cosmos DB veuillez consulter, [Opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Prévention des modifications du Kit de développement logiciel (SDK) Cosmos

> [!WARNING]
> Cette fonctionnalité risque d’avoir un impact dangereux sur votre application. Veuillez lire cette section attentivement avant de l’activer.

Il est possible de verrouiller le fournisseur de ressources Azure Cosmos DB pour empêcher toute modification des ressources par un client connecté au moyen de clés de compte (c’est-à-dire des applications connectées par le kit de développement logiciel Cosmos), y compris les modifications apportées à partir du Portail Azure. Ce comportement pourra intéresser les utilisateurs qui souhaitent des niveaux de contrôle et de gouvernance plus élevés pour les environnements de production et qui activent des fonctionnalités telles que les verrous de ressources, ainsi que les journaux de diagnostic pour les opérations de plan de contrôle. Les clients qui se connectent au moyen du kit de développement logiciel (SDK) Cosmos DB ne pourront pas modifier les propriétés des comptes, des bases de données, des conteneurs ni du débit Cosmos. Les opérations impliquant la lecture et l’écriture de données dans les conteneurs Cosmos proprement dits ne sont pas affectées.

Lorsque cette fonctionnalité est définie, seuls les utilisateurs possédant le rôle RBAC et les informations d’identification Azure Active Directory appropriés, notamment les identités Managed Service Identity, peuvent apporter des modifications aux ressources.

### <a name="check-list-before-enabling"></a>Liste de contrôle avant activation

Ce paramètre empêche toute modification de ressources Cosmos provenant d’un client qui se connecte à l’aide de clés de compte, notamment les kits SDK Cosmos DB, les outils qui utilisent des clés de compte pour établir une connexion ou encore le Portail Azure. Pour éviter des problèmes ou des erreurs dans les applications après coup, vérifiez avant d’activer cette fonctionnalité si des applications ou des utilisateurs du Portail Azure effectuent l’une des actions suivantes :

- Modification du compte Cosmos, notamment la modification de propriétés, l’ajout ou la suppression de régions.

- Création ou suppression de ressources enfants, comme des bases de données et des conteneurs, y compris les ressources d’autres API telles que Cassandra, MongoDB, Gremlin et les ressources de table.

- Mise à jour du débit sur des ressources de niveau base de données ou conteneur.

- Modification des propriétés du conteneur, notamment la stratégie d’index, la durée de vie (TTL) et les clés uniques.

- Modification des procédures stockées, des déclencheurs ou des fonctions définies par l’utilisateur.

Si vos applications (ou les utilisateurs qui passent par le Portail Azure) effectuent l’une de ces actions, il sera nécessaire de les migrer pour qu’elles s’exécutent via par le biais de [Modèles ARM](manage-sql-with-resource-manager.md), [PowerShell](manage-with-powershell.md), [Azure CLI](manage-with-cli.md), [REST](/rest/api/cosmos-db-resource-provider/) ou la [bibliothèque de gestion Azure](https://github.com/Azure-Samples/cosmos-management-net). Notez que la gestion Azure est disponible dans [plusieurs langues](https://docs.microsoft.com/azure/?product=featured#languages-and-tools).

### <a name="set-via-arm-template"></a>Définition au moyen d’un modèle ARM

Pour définir cette propriété à l’aide d’un modèle ARM, mettez à jour votre modèle ou exportez-en un nouveau pour votre déploiement actuel, puis ajoutez `"disableKeyBasedMetadataWriteAccess": true` aux propriétés des ressources databaseAccounts. Vous trouverez ci-dessous un exemple de modèle Azure Resource Manager de base avec ce paramètre de propriété.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Veillez à inclure les autres propriétés de votre compte et de vos ressources enfants lors du redéploiement avec cette propriété. Ne déployez pas ce modèle tel quel, car toutes les propriétés de votre compte seraient réinitialisées.

### <a name="set-via-azure-cli"></a>Définition au moyen d’Azure CLI

Pour activer l’utilisation d’Azure CLI, exécutez la commande ci-dessous :

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Définition au moyen de PowerShell

Pour activer l’utilisation d’Azure PowerShell, exécutez la commande ci-dessous :

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md)
- [Rôles intégrés pour les ressources Azure](../role-based-access-control/custom-roles.md)
- [Opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
