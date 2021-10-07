---
title: Empêcher la réplication d’objets sur les locataires du Répertoire actif Azure (préversion)
titleSuffix: Azure Storage
description: Empêcher la réplication d’objets entre locataires
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2fe98c0a15b1ec07ff1608e00aa030fd18360547
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599188"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>Empêcher la réplication d’objets sur les locataires du Répertoire actif Azure (préversion)

La réplication d’objets copie de façon asynchrone des objets Blob de blocs d’un conteneur dans un compte de stockage vers un conteneur d’un autre compte de stockage. Quand vous configurez une stratégie de réplication d’objet, vous spécifiez le compte et le conteneur source, ainsi que le compte et le conteneur de destination. Une fois la stratégie configurée, le Stockage Azure copie automatiquement les résultats des opérations de création, de mise à jour et de suppression sur un objet source vers l’objet de destination. Pour plus d’informations sur la réplication d’objets dans le Stockage Azure, consultez [Réplication d’objets pour les objets blob de blocs](object-replication-overview.md).

Par défaut, un utilisateur autorisé peut configurer une stratégie de réplication d’objets à l’emplacement du compte source dans un Répertoire actif Azure (Azure AD), et le compte de destination se trouve dans un autre locataire. Si vos stratégies de sécurité exigent que vous restreigniez la réplication d’objets aux comptes de stockage qui résident dans le même locataire uniquement, vous pouvez interdire la création de stratégies dans lesquelles les comptes source et de destination se trouvent dans des locataires différents (préversion). Par défaut, la réplication d’objets entre locataires est activée pour un compte de stockage, sauf si vous l’interdisez explicitement.

Cet article explique comment corriger la réplication d’objets entre locataires pour vos comptes de stockage. Il explique également comment créer des stratégies pour appliquer une interdiction sur la réplication d’objets entre locataires pour les comptes de stockage nouveaux et existants.

Pour plus d’informations sur la configuration des stratégies de réplication d’objets, y compris les stratégies entre locataires, consultez [Configurer la réplication d’objets pour les objets Blob de blocs](object-replication-configure.md).

## <a name="remediate-cross-tenant-object-replication"></a>Corriger la réplication d’objets entre locataires

Pour empêcher la réplication d’objets entre les locataires Azure AD, affectez la valeur **false** à la propriété **AllowCrossTenantReplication** pour le compte de stockage. Si un compte de stockage ne participe actuellement à aucune stratégie de réplication d’objet entre locataires, l’affectation de la valeur *false* à la propriété **AllowCrossTenantReplication** empêche la configuration ultérieure de stratégies de réplication d’objets entre locataires avec ce compte de stockage comme source ou destination. Toutefois, si un compte de stockage participe actuellement à une ou plusieurs stratégies de réplication d’objets entre locataires, l’affectation de la valeur *false* à la propriété **AllowCrossTenantReplication** n’est pas autorisée tant que vous n’avez pas supprimé les stratégies entre locataires existantes.

Les stratégies entre locataires sont autorisées par défaut pour un compte de stockage. Toutefois, la propriété **AllowCrossTenantReplication** n’est pas définie par défaut pour un compte de stockage nouveau ou existant et ne retourne pas de valeur tant que vous ne la définissez pas explicitement. Le compte de stockage peut participer aux stratégies de réplication d’objets entre locataires lorsque la valeur de la propriété est **null** ou **true**.

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>Corriger la réplication entre locataires pour un nouveau compte

Pour interdire la réplication entre locataires pour un nouveau compte de stockage, utilisez le portail Azure, PowerShell ou l’interface CLI Azure.

#### <a name="portal"></a>[Portail](#tab/portal)

Pour interdire la réplication d’objets entre locataires pour un nouveau compte de stockage, procédez comme suit :

1. Dans le portail Azure, accédez à la page **Comptes de stockage**, puis sélectionnez **Créer**.
1. Remplissez l’onglet **Fondamentaux** pour le nouveau compte de stockage.
1. Dans l’onglet **Avancé**, dans la section **Stockage d’objets Blob**, localisez le paramètre **Autoriser la réplication entre locataires**, puis désactivez la case à cocher.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="Capture d’écran montrant comment interdire la réplication d’objets entre locataires pour un nouveau compte de stockage":::

1. Terminez le processus de création du compte.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour désactiver la réplication d’objets entre locataires pour un nouveau compte de stockage, appelez la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) et incluez le paramètre `AllowCrossTenantReplication` avec la valeur *$false*.

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour désactiver la réplication d’objets entre locataires pour un nouveau compte de stockage, appelez la commande [Créer un compte de stockage az](/cli/azure/storage/account#az_storage_account_create) et incluez le paramètre `allow-cross-tenant-replication` avec la valeur *false*.

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>Corriger la réplication entre locataires pour un compte existant

Pour interdire la réplication entre locataires pour un compte de stockage existant, utilisez le portail Azure, PowerShell ou l’interface CLI Azure.

#### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour désactiver la réplication d’objets entre locataires pour un compte de stockage existant qui ne participe actuellement à aucune stratégie entre locataires, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Gestion des données**, sélectionnez **Réplication d’objets**.
1. Sélectionnez **Paramètres avancés**.
1. Décochez **Autoriser la réplication entre locataires**. Par défaut, cette case est cochée, car la réplication d’objets entre locataires est autorisée pour un compte de stockage, sauf si vous l’interdisez explicitement.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="Capture d’écran montrant comment interdire la réplication d’objets entre locataires pour un compte de stockage existant":::

1. Sélectionnez **OK** pour enregistrer vos modifications.

Si le compte de stockage participe actuellement à une ou plusieurs stratégies de réplication entre locataires, vous ne pourrez pas interdire la réplication d’objets entre locataires tant que vous n’aurez pas supprimé ces stratégies. Dans ce scénario, le paramètre n’est pas disponible dans le portail Azure, comme illustré dans l’image suivante.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="Capture d’écran":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour désactiver la réplication d’objets entre locataires pour un compte de stockage existant qui ne participe actuellement à aucune stratégie entre locataires, installez d’abord le [module de Stockage AZ PowerShell ](https://www.powershellgallery.com/packages/Az.Storage)\, version 3.7.0 ou ultérieure. Ensuite, configurez la propriété **AllowCrossTenantReplication** pour le compte de stockage.

L’exemple suivant montre comment interdire la réplication d’objets entre locataires pour un compte de stockage existant avec PowerShell. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

Si le compte de stockage participe actuellement à une ou plusieurs stratégies de réplication entre locataires, vous ne pourrez pas interdire la réplication d’objets entre locataires tant que vous n’aurez pas supprimé ces stratégies. PowerShell fournit une erreur indiquant que l’opération a échoué en raison de stratégies de réplication entre locataires existantes.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour désactiver la réplication d’objets entre locataires pour un compte de stockage existant qui ne participe actuellement à aucune stratégie entre locataires, commencez par installer l’interface CLI Azure version 2.24.0 ou ultérieure. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli). Ensuite, configurez la propriété **allowCrossTenantReplication** pour un compte de stockage nouveau ou existant.

L’exemple suivant montre comment interdire la réplication d’objets entre locataires pour un compte de stockage existant avec l’interface CLI Azure. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

Si le compte de stockage participe actuellement à une ou plusieurs stratégies de réplication entre locataires, vous ne pourrez pas interdire la réplication d’objets entre locataires tant que vous n’aurez pas supprimé ces stratégies. Azure CLI fournit une erreur indiquant que l’opération a échoué en raison de stratégies de réplication entre locataires existantes.

---

Une fois que vous avez interdit la réplication entre locataires, toute tentative de configuration d’une stratégie entre locataires avec le compte de stockage comme source ou destination échoue. Le Stockage Azure retourne une erreur indiquant que la réplication d’objets entre locataires n’est pas autorisée pour le compte de stockage.

Lorsque la réplication d’objets entre locataires n’est pas autorisée pour un compte de stockage, toutes les nouvelles stratégies de réplication d’objet que vous créez avec ce compte doivent inclure l’ID complet du Gestionnaire de ressource Azure pour le compte source et le compte de destination. Le Stockage Azure nécessite l’ID de ressource complet pour vérifier si les comptes source et de destination se trouvent dans le même locataire. Pour plus d’informations, consultez [Spécifier des ID de ressources complets pour les comptes source et de destination](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts).

La propriété **AllowCrossTenantReplication** est prise en charge pour les comptes de stockage qui utilisent uniquement le modèle de déploiement du Gestionnaire de ressource Azure. Pour plus d’informations sur les comptes de stockage qui utilisent le modèle de déploiement Azure Resource Manager, consultez [Types de compte de stockage](../common/storage-account-overview.md#types-of-storage-accounts).

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>Autorisations permettant d’autoriser ou d’interdire la réplication entre locataires

Pour définir la propriété **AllowCrossTenantReplication** pour un compte de stockage, un utilisateur doit disposer des autorisations nécessaires pour créer et gérer des comptes de stockage. Les rôles de contrôle d’accès en fonction du rôle Azure (Azure RBAC) qui fournissent ces autorisations comprennent l’action **Microsoft.Storage/storageAccounts/write** ou l’action ***Microsoft.Storage/storageAccounts/\*** . Parmi les rôles intégrés comportant cette action figurent :

- Le rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager
- Le rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) d’Azure Resource Manager
- Le rôle [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ces rôles ne donnent pas accès aux données d’un compte de stockage par le biais d’Azure Active Directory (Azure AD). Toutefois, ils incluent l’action **Microsoft.Storage/storageAccounts/listkeys/action**, qui accorde l’accès aux clés d’accès du compte. Avec cette autorisation, un utilisateur peut utiliser les clés d’accès du compte pour accéder à toutes les données d’un compte de stockage.

Les attributions de rôles doivent être définies au niveau du compte de stockage ou à un niveau supérieur pour permettre à un utilisateur d’autoriser ou d’interdire la réplication d’objets entre locataires pour le compte de stockage. Pour plus d’informations sur l’étendue des rôles, consultez [Comprendre l’étendue pour Azure RBAC](../../role-based-access-control/scope-overview.md).

Veillez à limiter l’attribution de ces rôles aux seules personnes qui ont besoin de créer un compte de stockage ou de mettre à jour ses propriétés. Appliquez le principe des privilèges minimum pour que les utilisateurs disposent des autorisations nécessaires les plus faibles possibles pour accomplir leurs tâches. Pour plus d’informations sur la gestion de l’accès avec Azure RBAC, consultez [Meilleures pratiques pour Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Les rôles d’administrateur d’abonnement classique Administrateur de service et Co-administrateur incluent l’équivalent du rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager. Le rôle **Propriétaire** comprend toutes les actions. Par conséquent, un utilisateur disposant de l’un de ces rôles d’administration peut également créer et gérer des comptes de stockage. Pour plus d’informations, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="use-azure-policy-to-audit-for-compliance"></a>Utiliser Azure Policy pour auditer la conformité

Si vous avez un grand nombre de comptes de stockage, il se peut que vous deviez effectuer un audit pour vous assurer que ces comptes sont configurés de manière à empêcher la réplication d’objets entre locataires. Pour auditer la conformité d’un ensemble de comptes de stockage, utilisez Azure Policy. Azure Policy est un service que vous pouvez utiliser pour créer, attribuer et gérer des stratégies qui appliquent des règles à des ressources Azure. Lorsque vous utilisez Azure Policy, les ressources restent conformes à vos normes d’entreprise et contrats de niveau de service. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Créer une stratégie avec un effet d’audit

Azure Policy prend en charge les effets qui déterminent ce qui se produit quand une règle de stratégie est évaluée par rapport à une ressource. L’effet d’audit crée un avertissement quand une ressource n’est pas conforme, mais n’arrête pas la demande. Pour plus d’informations, consultez [Comprendre les effets d’Azure Policy](../../governance/policy/concepts/effects.md).

Pour créer une stratégie avec un effet d’audit pour le paramètre de la réplication d’objets entre locataires pour un compte de stockage avec le portail Azure, procédez comme suit :

1. Dans le Portail Azure, accédez au service Azure Policy.
1. Dans la section **Création**, sélectionnez **Définitions**.
1. Sélectionnez **Ajouter une définition de stratégie** pour créer une nouvelle définition de stratégie.
1. Pour le champ **emplacement de la définition**, sélectionnez le bouton **Autres** pour spécifier l’emplacement de la ressource de stratégie d’audit.
1. Spécifiez un nom pour la stratégie. Vous pouvez éventuellement spécifier une description et une catégorie.
1. Sous **Règle de stratégie**, ajoutez la définition de stratégie suivante à la section **policyrule**.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Enregistrez la stratégie.

### <a name="assign-the-policy"></a>Affecter la stratégie

Ensuite, attribuez la stratégie à une ressource. L’étendue de la stratégie correspond à cette ressource et à toutes les ressources qu’elle contient. Pour plus d’informations sur l’attribution de stratégie, consultez [Structure d’affectation d’Azure Policy](../../governance/policy/concepts/assignment-structure.md).

Pour attribuer la stratégie avec le portail Azure, procédez comme suit :

1. Dans le portail Azure, accédez au service Azure Policy.
1. Dans la section **Création**, sélectionnez **Attributions**.
1. Sélectionnez **Attribuer une stratégie** pour créer une attribution de stratégie.
1. Pour le champ **Étendue**, sélectionnez l’étendue de l’attribution de stratégie.
1. Pour le champ **Définition de stratégie**, sélectionnez le bouton **Autres**, puis la stratégie que vous avez définie dans la section précédente dans la liste.
1. Entrez un nom pour l’attribution de stratégie. La description est facultative.
1. Laissez l’option **Application de stratégie** définie sur *Activée*. Ce paramètre n’a aucun effet sur la stratégie d’audit.
1. Sélectionnez **Vérifier + créer** pour créer l’attribution.

### <a name="view-compliance-report"></a>Afficher le rapport de conformité

Une fois que vous avez attribué la stratégie, vous pouvez afficher le rapport de conformité. Le rapport de conformité d’une stratégie d’audit fournit des informations sur les comptes de stockage qui autorisent toujours les stratégies de réplication d’objets entre locataires. Pour plus d’informations, consultez [Obtenir les données de conformité de la stratégie](../../governance/policy/how-to/get-compliance-data.md).

La disponibilité du rapport de conformité peut prendre plusieurs minutes après la création de l’attribution de stratégie.

Pour afficher le rapport de conformité dans le Portail Azure, procédez comme suit :

1. Dans le portail Azure, accédez au service Azure Policy.
1. Sélectionnez **Conformité**.
1. Filtrez les résultats pour le nom de l’attribution de stratégie que vous avez créée à l’étape précédente. Le rapport indique le nombre de ressources qui ne sont pas conformes à la stratégie.
1. Vous pouvez explorer le rapport au niveau du détail pour obtenir des détails supplémentaires, notamment une liste des comptes de stockage qui ne sont pas conformes.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="Capture d’écran montrant le rapport de conformité de la stratégie d’audit pour la réplication d’objets Blob entre locataires":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>Utiliser la Stratégie Azure pour appliquer les stratégies de réplication de locataire identique

Azure Policy prend en charge la gouvernance cloud en s’assurant que les ressources Azure respectent les exigences et les normes. Pour vous assurer que les comptes de stockage de votre organisation interdisent la réplication d’objets entre locataires, vous pouvez créer une stratégie qui empêche la création d’un nouveau compte de stockage autorisant les stratégies de réplication d’objets entre locataires. La stratégie d’application utilise l’effet de refus pour empêcher une requête de créer ou de modifier un compte de stockage pour autoriser la réplication d’objets entre locataires. Cette stratégie empêchera également toutes les modifications de configuration apportées à un compte existant si le paramètre de réplication d’objets entre locataires pour ce compte n’est pas conforme à la stratégie. Pour plus d’informations sur l’effet de refus, consultez [Comprendre les effets de la Stratégie Azure](../../governance/policy/concepts/effects.md).

Pour créer une stratégie avec un effet de refus pour la réplication d’objets entre locataires, suivez les mêmes étapes que celles décrites dans [Utiliser la Stratégie Azure pour auditer la conformité](#use-azure-policy-to-audit-for-compliance), mais fournissez le code JSON suivant dans la section **policyRule** de la définition de stratégie :

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Une fois que vous avez créé la stratégie avec l’effet de refus et l’avez attribuée à une étendue, un utilisateur ne peut plus créer de compte de stockage qui autorise la réplication d’objets entre locataires. Un utilisateur ne peut pas non plus apporter des changements de configuration à un compte de stockage existant qui autorise actuellement la réplication d’objets entre locataires. Toute tentative en ce sens entraîne une erreur. La propriété **AllowCrossTenantReplication** du compte de stockage doit avoir la valeur **false** pour poursuivre la création du compte ou les mises à jour de la configuration, conformément à la stratégie.

L’image suivante montre l’erreur qui se produit si vous tentez de créer un compte de stockage qui autorise la réplication d’objets entre locataires (valeur par défaut pour un nouveau compte) lorsqu’une stratégie avec effet de refus exige que la réplication d’objets entre locataires soit interdite.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="Capture d’écran montrant l’erreur qui se produit lors de la création d’un compte de stockage en violation de la stratégie":::

## <a name="see-also"></a>Voir aussi

- [Réplication d'objets blob de blocs](object-replication-overview.md)
- [Configurer la réplication d’objets pour des objets blob de blocs](object-replication-configure.md)
