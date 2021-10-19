---
title: Créer des stratégies de sauvegarde pour les disques à l’aide de l’API REST de protection des données
description: Dans cet article, vous apprendrez à créer et gérer des stratégies de sauvegarde pour les disques à l’aide de l’API REST.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621038"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>Créer des stratégies de sauvegarde de la protection des données Azure pour les disques à l’aide de l’API REST

Une stratégie de sauvegarde régit la rétention et la planification de vos sauvegardes. La sauvegarde de disque Azure propose plusieurs sauvegardes par jour.

Vous pouvez réutiliser la stratégie de sauvegarde pour configurer la sauvegarde de plusieurs disques Azure dans un coffre ou [créer une stratégie de sauvegarde pour un coffre Azure Recovery Services à l’aide de l’API REST](/rest/api/dataprotection/backup-policies/create-or-update).

Pour créer une stratégie de sauvegarde des disques, procédez comme suit :

## <a name="create-a-policy"></a>Créer une stratégie

>[!IMPORTANT]
>Actuellement, ni la mise à jour ni la modification d’une stratégie existante n’est prise en charge. Sinon, vous pouvez créer une nouvelle stratégie avec les détails requis et l’attribuer à l’instance de sauvegarde appropriée.

Pour créer une stratégie de sauvegarde Azure, utilisez l’opération *PUT* suivante :

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` et `{vaultName}` sont fournis dans l’URI. Vous trouverez des informations supplémentaires dans le corps de la requête.

## <a name="create-the-request-body"></a>Créer le corps de la demande

Par exemple, pour créer une stratégie pour une sauvegarde de disques, le corps de la demande a besoin des composants suivants :

|Nom  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy : [BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propriétés BaseBackupPolicyResource        |

Pour obtenir la liste complète des définitions de corps de la requête, reportez-vous au [document sur l’API REST backup policy](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Exemple de corps de demande

La stratégie indique ce qui suit :

- Déclencheur programmé toutes les 4 heures (PT4H). Les sauvegardes sont effectuées à intervalles de 4 heures environ, de façon à être réparties équitablement sur la journée.
- Vous pouvez choisir l’intervalle de déclenchement toutes les 4, 6, 8 ou 12 heures. Pour planifier une sauvegarde une fois par jour, utilisez **P1D**. Les sauvegardes sont déclenchées une fois par jour à l’heure prévue.
- Le magasin de données est le _magasin opérationnel_, car les sauvegardes sont locales et aucune donnée n’est stockée dans le coffre Sauvegarde. Dans le magasin opérationnel, chaque instance de sauvegarde est stockée pendant sept jours (P7D).

```json
{
"properties": {
    "datasourceTypes": [
        "Microsoft.Compute/disks"
      ],
      "name": "DiskPolicy",
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "backupParameters": {
            "backupType": "Incremental",
            "objectType": "AzureBackupParams"
          },
          "dataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          },
          "name": "BackupHourly",
          "objectType": "AzureBackupRule",
          "trigger": {
            "objectType": "ScheduleBasedTriggerContext",
            "schedule": {
              "repeatingTimeIntervals": [
                "R/2020-04-05T13:00:00+00:00/PT4H"
              ]
            },
            "taggingCriteria": [
              {
                "isDefault": true,
                "tagInfo": {
                  "id": "Default_",
                  "tagName": "Default"
                },
                "taggingPriority": 99
              }
            ]
          }
        },
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P7D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              }
            }
          ],
          "name": "Default",
          "objectType": "AzureRetentionRule"
        }
      ]
    }
}
```

>[!IMPORTANT]
>Les formats d’heure ne prennent en charge que DateHeure. Ils ne prennent pas en charge qu’Heure. L’heure de la journée indique l’heure de début de la sauvegarde, et pas l’heure de fin.

Le temps nécessaire pour effectuer l’opération de sauvegarde dépend de différents facteurs, notamment de la taille du disque et du taux d’attrition entre les sauvegardes consécutives. Toutefois, la Sauvegarde de disque Azure est une sauvegarde sans agent qui utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) sans avoir d’impact sur le niveau de performance des applications de production.

Pour plus d’informations sur la création de stratégies, reportez-vous au document sur la [stratégie de sauvegarde de disque Azure](backup-managed-disks.md#create-backup-policy).

## <a name="responses"></a>Réponses

La création/mise à jour de la stratégie de sauvegarde est une opération synchrone et retourne OK une fois l’opération réussie.

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Ok       |

### <a name="example-responses"></a>Exemples de réponses

Une fois l’opération terminée, elle retourne 200 (OK) avec le contenu de la stratégie dans le corps de la réponse.

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
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
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

[Activer la protection des disques Azure](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Pour plus d’informations sur les API REST de Sauvegarde Azure, voir les articles suivants :

- [API REST de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
