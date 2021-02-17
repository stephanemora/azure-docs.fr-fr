---
title: Configurer les autorisations de restauration d’un compte Azure Cosmos DB
description: Découvrez comment isoler et restreindre les autorisations de restauration pour le compte de sauvegarde continue à un rôle ou à un principal spécifique. Cet article montre comment attribuer un rôle intégré à l’aide de Portail Azure ou de l’interface CLI ou comment définir un rôle personnalisé.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d30f5325162b9ea447d54aadc092dbd9aa29132
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538691"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Gérer les autorisations de restauration d’un compte Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB vous permet d’isoler et de restreindre les autorisations de restauration pour le compte de sauvegarde continue (préversion) à un rôle ou à un principal spécifique. Le propriétaire du compte peut déclencher une restauration et attribuer un rôle à d’autres principaux pour effectuer l’opération de restauration. Ces autorisations peuvent être appliquées au niveau de l’étendue de l’abonnement ou plus précisément au niveau de l’étendue du compte source, comme illustré dans l’image suivante :

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Liste des rôles requis pour effectuer l’opération de restauration." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

L’étendue est un ensemble de ressources ayant accès. Pour en savoir plus sur les étendues, consultez la documentation d’[Azure RBAC](../role-based-access-control/scope-overview.md). Dans Azure Cosmos DB, les étendues applicables sont l’abonnement source et le compte de base de données pour la plupart des cas d’usage. Le principal effectuant les actions de restauration doit disposer d’autorisations en écriture sur le groupe de ressources de destination.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Attribuer des rôles pour la restauration à l’aide du portail Azure

Pour effectuer une restauration, un utilisateur ou un principal doit avoir l’autorisation de restaurer (qui est l’autorisation « restore/action ») et l’autorisation d’approvisionner un nouveau compte (qui est l’autorisation « write »).  Pour accorder ces autorisations, le propriétaire peut attribuer les rôles intégrés « CosmosRestoreOperator » et « Opérateur Cosmos DB » à un principal.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre abonnement, rendez-vous dans l’onglet **Contrôle d’accès (IAM)** et sélectionnez **Ajouter** > **Ajouter une attribution de rôle**.

1. Dans le volet **Ajouter une attribution de rôle**, pour le champ **Rôle**, sélectionnez le rôle **CosmosRestoreOperator**. Choisissez **Utilisateur, groupe ou principal de service** pour le champ **Attribuer l’accès à** et recherchez le nom ou l’adresse e-mail de l’utilisateur comme indiqué dans l’image suivante :

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Attribuer les rôles CosmosRestoreOperator et Opérateur Cosmos DB." border="true":::

1. Sélectionnez **Enregistrer** pour accorder l’autorisation « restore/action ».

1. Répétez l’étape 3 avec le rôle **Opérateur Cosmos DB** pour accorder l’autorisation en écriture. Lorsque vous attribuez ce rôle à partir du portail Azure, il accorde l’autorisation de restauration à l’intégralité de l’abonnement.

## <a name="permission-scopes"></a>Étendues d’autorisation

|Étendue  |Exemple  |
|---------|---------|
|Abonnement | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Ressource de compte restaurable CosmosDB | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

La ressource de compte restaurable peut être extraite de la sortie de la commande `az cosmosdb restorable-database-account list --name <accountname>` dans l’interface CLI ou de la cmdlet `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` dans PowerShell. L’attribut « name » dans la sortie représente l’« instanceID » du compte restaurable. Pour plus d’informations, consultez l’article relatif à [PowerShell](continuous-backup-restore-powershell.md) ou à [CLI](continuous-backup-restore-command-line.md).

## <a name="permissions"></a>Autorisations

Les autorisations suivantes sont requises pour effectuer les différentes activités relatives à la restauration des comptes en mode de sauvegarde continue :

|Autorisation  |Impact  |Étendue minimale  |Étendue maximale  |
|---------|---------|---------|---------|
|Microsoft.Resources/deployments/validate/action, Microsoft.Resources/deployments/write | Ces autorisations sont requises pour le déploiement du modèle ARM afin de créer le compte restauré. Consultez l’exemple d’autorisation [RestorableAction]() ci-dessous pour savoir comment définir ce rôle. | Non applicable | Non applicable  |
|Microsoft.DocumentDB/databaseAccounts/write | Cette autorisation est requise pour restaurer un compte dans un groupe de ressources. | Groupe de ressources sous lequel le compte restauré est créé. | Abonnement sous lequel le compte restauré est créé. |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action |Cette autorisation est requise sur l’étendue du compte de base de données restaurable source pour permettre l’exécution d’actions de restauration sur celle-ci.  | Ressource « RestorableDatabaseAccount » appartenant au compte source en cours de restauration. Cette valeur est également fournie par la propriété « ID » de la ressource de compte de base de données restaurable. Exemple de compte restaurable : `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`. | Abonnement contenant le compte de base de données restaurable. Le groupe de ressources ne peut pas être choisi comme étendue.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read |Cette autorisation est requise sur l’étendue du compte de base de données restaurable source pour répertorier les comptes de base de données qui peuvent être restaurés.  | Ressource « RestorableDatabaseAccount » appartenant au compte source en cours de restauration. Cette valeur est également fournie par la propriété « ID » de la ressource de compte de base de données restaurable. Exemple de compte restaurable : `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`.| Abonnement contenant le compte de base de données restaurable. Le groupe de ressources ne peut pas être choisi comme étendue.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read | Cette autorisation est requise sur l’étendue du compte restaurable source pour permettre la lecture des ressources restaurables telles que la liste des bases de données et des conteneurs pour un compte restaurable.  | Ressource « RestorableDatabaseAccount » appartenant au compte source en cours de restauration. Cette valeur est également fournie par la propriété « ID » de la ressource de compte de base de données restaurable. Exemple de compte restaurable : `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`.| Abonnement contenant le compte de base de données restaurable. Le groupe de ressources ne peut pas être choisi comme étendue. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scénarios d’attribution de rôle Azure CLI pour restaurer dans différentes étendues

Les rôles avec autorisation peuvent être attribués à différentes étendues afin d’obtenir un contrôle granulaire sur les personnes autorisées à effectuer l’opération de restauration au sein d’un abonnement ou d’un compte donné.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Attribuer la capacité de restauration à partir d’un compte restaurable dans un abonnement

Attribuez le rôle intégré `CosmosRestoreOperator` au niveau de l’abonnement.

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Attribuer la capacité de restauration à partir d’un compte spécifique

* Attribuez une action d’écriture utilisateur sur le groupe de ressources spécifique. Cette action est nécessaire pour créer un nouveau compte dans le groupe de ressources.

* Attribuez le rôle intégré « CosmosRestoreOperator » au compte de base de données restaurable spécifique qui doit être restauré. Dans la commande suivante, l’étendue pour « RestorableDatabaseAccount » est récupérée à partir de la propriété « ID » dans la sortie de `az cosmosdb restorable-database-account` (si vous utilisez l’interface CLI) ou `Get-AzCosmosDBRestorableDatabaseAccount` (si vous utilisez PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Attribuer la capacité de restauration à partir d’un compte source dans un groupe de ressources
Cette opération n’est pas prise en charge actuellement.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Création d’un rôle personnalisé pour l’action de restauration à l’aide de l’interface CLI

Le propriétaire de l’abonnement peut fournir l’autorisation de restauration à n’importe quelle autre identité Azure AD. L’autorisation de restauration est basée sur l’action suivante : « Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action », et doit être incluse dans son autorisation de restauration. Un rôle intégré appelé « CosmosRestoreOperator » est inclus dans ce rôle. Vous pouvez soit attribuer l’autorisation à l’aide de ce rôle intégré, soit créer un rôle personnalisé.

RestorableAction ci-dessous représente un rôle personnalisé. Vous devez créer explicitement ce rôle. Le modèle JSON suivant crée un rôle personnalisé « RestorableAction » avec autorisation de restauration :

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Utilisez ensuite la commande de déploiement de modèle suivante pour créer un rôle avec autorisation de restauration à l’aide du modèle ARM :

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant le [portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).
