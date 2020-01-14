---
title: Mettre à jour les propriétés de configuration du coffre Recovery Services à l’aide de l’API REST
description: Dans cet article, découvrez comment mettre à jour la configuration du coffre à l’aide de l’API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 3739f95e3962def9ab669970c48b2d1c546fdc0e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390534"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Mettre à jour les configurations du coffre Azure Recovery Services à l’aide de l’API REST

Cet article explique comment mettre à jour les configurations liées à la sauvegarde dans le coffre Azure Recovery Services à l’aide de l’API REST.

## <a name="soft-delete-state"></a>État de suppression réversible

La suppression des sauvegardes d’un élément protégé est une opération importante qui doit être supervisée. Pour vous protéger contre les suppressions accidentelles, le coffre Azure Recovery Services dispose d’une fonctionnalité de suppression réversible. Cette fonctionnalité permet aux clients de restaurer des sauvegardes supprimées, si nécessaire, au cours d’une période après la suppression.

Toutefois, il existe des scénarios dans lesquels cette fonctionnalité n’est pas nécessaire. Un coffre Azure Recovery Services ne peut pas être supprimé s’il contient des éléments de sauvegarde, même dans l’état de suppression réversible. Cela peut poser un problème si le coffre doit être supprimé immédiatement. Par exemple, les opérations de déploiement nettoient souvent les ressources créées dans le même workflow. Un déploiement peut créer un coffre, configurer des sauvegardes pour un élément, effectuer une restauration de test, puis supprimer les éléments de sauvegarde et le coffre. En cas d’échec de la suppression du coffre, l’intégralité du déploiement peut échouer. La désactivation de la suppression réversible est le seul moyen de garantir la suppression immédiate.

Ainsi, le client doit choisir avec précaution s’il souhaite activer ou désactiver la suppression réversible pour un coffre particulier en fonction du scénario. Pour plus d’informations, consultez l’[article sur la suppression réversible](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Extraire l’état de suppression réversible à l’aide de l’API REST

Par défaut, l’état de suppression réversible est activé pour tout coffre Recovery Services créé. Pour extraire/mettre à jour l’état de suppression réversible pour un coffre, utilisez le [document de l’API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) relatif à la configuration du coffre de sauvegarde.

Pour extraire l’état actuel de suppression réversible pour un coffre, utilisez l’opération *GET*.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L’URI GET contient les paramètres `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`. Dans cet exemple, `{vaultName}` est « testVault » et `{vaultresourceGroupName}` est « testVaultRG ». Comme tous les paramètres nécessaires sont fournis dans l’URI, un corps de demande séparé est inutile.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Réponses

La réponse correcte pour l’opération « GET » est indiquée ci-dessous :

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemple de réponse

Une fois la demande « GET » envoyée, une réponse 200 (réussite) est retournée.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Mettre à jour l’état de suppression réversible à l’aide de l’API REST

Pour mettre à jour l’état de suppression réversible du coffre Recovery Services à l’aide de l’API REST, utilisez l’opération *PATCH* suivante :

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L’URI PATCH contient les paramètres `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`. Dans cet exemple, `{vaultName}` est « testVault » et `{vaultresourceGroupName}` est « testVaultRG ». Si nous remplaçons l’URI par les valeurs ci-dessus, l’URI ressemble à ce qui suit.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Créer le corps de la demande

Les définitions courantes suivantes permettent de créer un corps de demande.

Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body).

|Name  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag facultatif       |
|location     |  true       |String         |   Emplacement de la ressource      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriétés du coffre       |
|tags     |         | Object        |     Balises de ressource    |

#### <a name="example-request-body"></a>Exemple de corps de demande

L’exemple suivant permet de définir l’état de suppression réversible sur « Disabled » (désactivé).

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Réponses

La réponse correcte pour l’opération « PATCH » est indiquée ci-dessous :

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemple de réponse

Une fois la demande « PATCH » envoyée, une réponse 200 (réussite) est retournée.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

[Créez une stratégie de sauvegarde pour sauvegarder une machine virtuelle Azure dans ce coffre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Pour plus d’informations sur l’envoi de travaux à l’aide des API REST Azure, consultez les documents suivants :

- [API REST du fournisseur Azure Recovery Services](/rest/api/recoveryservices/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
