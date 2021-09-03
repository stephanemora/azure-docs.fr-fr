---
title: Créer des stratégies de sauvegarde pour les blobs à l’aide de l’API REST de protection des données
description: Dans cet article, vous apprendrez à créer et gérer des stratégies de sauvegarde pour les blobs à l’aide de l’API REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471559"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>Créer des stratégies de sauvegarde de la protection des données Azure pour les blobs à l’aide de l’API REST

> [!IMPORTANT]
> Lisez [cette section](blob-backup-configure-manage.md#before-you-start) avant de commencer à créer la stratégie et à configurer des sauvegardes pour les objets blob Azure.

Une stratégie de sauvegarde régit généralement la rétention et la planification de vos sauvegardes. Étant donné que la sauvegarde opérationnelle d’objets blob est continue par nature, vous n’avez pas besoin d’une planification pour effectuer des sauvegardes. La stratégie est essentiellement nécessaire pour spécifier la période de rétention. Vous pouvez réutiliser la stratégie de sauvegarde afin de configurer la sauvegarde de plusieurs comptes de stockage dans un coffre.

>[!NOTE]
>Restaurer sur de longues durées peut conduire à des opérations de restauration plus longues. Par ailleurs, le temps nécessaire à la restauration d’un jeu de données dépend du nombre d’opérations d’écriture et de suppression effectuées au cours de la période de restauration. Par exemple, la restauration à un point situé 30 jours auparavant d’un compte avec 1 million d’objets, 3 000 objets ajoutés par jour et 1 000 objets supprimés par jour nécessite environ deux heures. Une période de rétention et une restauration à plus de 90 jours dans le passé ne sont pas recommandées pour un compte avec ce taux de changement.

Les étapes de création d’une stratégie de sauvegarde pour un coffre Azure Recovery Services sont décrites dans le [document sur l’API REST](/rest/api/dataprotection/backup-policies/create-or-update) de la stratégie. Nous allons utiliser ce document comme référence pour créer une stratégie pour les blobs dans un compte de stockage.

## <a name="create-a-policy"></a>Créer une stratégie

> [!IMPORTANT]
> Actuellement, nous ne prenons pas en charge la mise à jour ni la modification d’une stratégie existante. Une alternative consiste à créer une nouvelle stratégie avec les détails requis et à l’attribuer à l’instance de sauvegarde appropriée.

Pour créer une stratégie de sauvegarde Azure, utilisez l’opération *PUT* suivante

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` et `{vaultName}` sont fournis dans l’URI. Vous trouverez des informations supplémentaires dans le corps de la requête.

## <a name="create-the-request-body"></a>Créer le corps de la demande

Par exemple,pour créer une stratégie pour une sauvegarde blob, voici les composants du corps de la demande.

|Nom  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy : [BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propriétés BaseBackupPolicyResource        |

Pour obtenir la liste complète des définitions de corps de la requête, reportez-vous au [document sur l’API REST backup policy](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Exemple de corps de demande

Le corps de la demande suivant définit une stratégie de sauvegarde pour les sauvegardes blobs.

La stratégie indique ce qui suit :

- La période de rétention est de 30 jours.
- Le magasin de données est « magasin opérationnel », car les sauvegardes sont locales et aucune donnée n’est stockée dans le coffre de sauvegarde.

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P30D",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "OperationalStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> Les formats de l’heure ne prennent en charge que DateHeure. Ils ne prennent pas en charge le format Time uniquement.

## <a name="responses"></a>Réponses

La création/mise à jour de la stratégie de sauvegarde est une opération synchrone et retourne OK une fois l’opération réussie.

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Ok       |

### <a name="example-responses"></a>Exemples de réponses

Une fois l’opération terminée, elle retourne 200 (OK) avec le contenu de la stratégie dans le corps de la réponse.

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
            "sourceDataStore": {
              "dataStoreType": "OperationalStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        ],
        "isDefault": true,
        "name": "Default",
        "objectType": "AzureRetentionRule"
      }
    ],
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Activez la protection des blobs dans un compte de stockage.

Pour plus d’informations sur les API REST de la Sauvegarde Azure, voir les documents suivants :

- [API REST de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
