---
title: Guide de démarrage rapide des API de rôle et de stratégie de métadonnées des collections Azure Purview – Gérer le contrôle d’accès en fonction du rôle sur les collections Purview
description: Ce tutoriel traite de la gestion du contrôle d’accès en fonction du rôle sur ces collections pour accéder aux utilisateurs, groupes ou principaux de service au sein de votre entreprise via les API Azure Purview.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 52c38e40d96b6d6f650a8b57fe2d5826a9a81842
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219682"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-rbac-on-azure-purview-collections"></a>Tutoriel : Utiliser des API REST pour gérer le contrôle d’accès en fonction du rôle (RBAC) sur les collections Azure Purview 

En août 2021, le contrôle d’accès dans Purview est passé d’Azure IAM (plan de contrôle) aux [collections Azure Purview](how-to-create-and-manage-collections.md) (plan de données). Cette modification permet aux conservateurs et administrateurs de données d’entreprise de bénéficier d’un contrôle d’accès plus précis sur leurs sources de données analysées par Purview, et permet aux organisations d’auditer l’accès et l’utilisation de leurs données.

Ce tutoriel vous guide pas à pas dans l’utilisation des **[API de stratégie de métadonnées Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** pour ajouter des utilisateurs, des groupes ou des principaux de service dans une collection, et pour gérer ou supprimer leurs rôles au sein de cette collection. Les API REST sont une méthode alternative à l’utilisation du portail Azure ou de Purview Studio pour obtenir la même précision de contrôle d’accès en fonction du rôle.

Le [guide des autorisations Purview](catalog-permissions.md#roles) fournit plus d’informations sur les divers rôles intégrés dans Azure Purview et mappe le rôle au niveau des autorisations d’accès accordées à l’utilisateur.


## <a name="metadata-policy-api-reference-summary"></a>Résumé de la référence des API de stratégie de métadonnées
Ce tableau fournit une vue d’ensemble de la **[référence des API de stratégie de métadonnées Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** . Remplacez {pv-acc-name} par le nom de votre compte Purview avant d’exécuter ces API. Par exemple, si le nom de votre compte Purview est « FabrikamPurviewAccount », vos points de terminaison d’API deviennent « FabrikamPurviewAccount.purview.azure.com ».

|**Fonction API**|**Méthode REST**|**Point de terminaison d’API**|**Description**|
|:-|:-|:-|:-|
|Lire tous les rôles de métadonnées|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Lit tous les rôles de métadonnées à partir de votre compte Purview|
|Lire la stratégie de métadonnées par nom de collection|GET|https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Lit MetadataPolicy par un nom de collection donné (nom aléatoire à 6 caractères généré par Purview lors de la création de la stratégie)|
|Lire la stratégie de métadonnées par PolicyID|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Lit MetadataPolicy selon un ID de stratégie (PolicyID) donné. L’ID de stratégie est au format GUID.|
|Lire toutes les stratégies de métadonnées|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Lit toutes les stratégies de métadonnées à partir de votre compte Purview. Vous pouvez choisir une certaine stratégie à utiliser à partir de la liste de sortie JSON émise par cette API.|
|Mettre à jour/placer (PUT) la stratégie de métadonnées|PUT|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Met à jour MetadataPolicy selon un ID de stratégie (PolicyID) donné. L’ID de stratégie est au format GUID.|

## <a name="purview-catalog-collections-api-reference-summary"></a>Récapitulatif des informations de référence des API de collections de catalogues Purview
Ce tableau fournit une vue d’ensemble des **API de collections Purview**. Cliquez sur chacune des « OPÉRATIONS » d’API ci-dessous pour obtenir une documentation complète sur chaque API.

| **OPÉRATIONS** | **Description** |
|:-|:-|
| [Créer ou mettre à jour une collection](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Crée ou met à jour une entité de collection. |
| [Supprimer la collection](/rest/api/purview/accountdataplane/collections/delete-collection) |Supprime une entité de collection. |
| [Obtenir une collection](/rest/api/purview/accountdataplane/collections/get-collection) |Obtenir un regroupement|
| [Obtenir le chemin de collection](/rest/api/purview/accountdataplane/collections/get-collection-path) |Obtient le nom parent et les chaînes de nom d’affichage qui représentent le chemin de collection.|
| [Lister les noms des collections enfants](/rest/api/purview/accountdataplane/collections/list-child-collection-names) |Dresse la liste des noms des collections enfants de la collection.|
| [Lister les collections](/rest/api/purview/accountdataplane/collections/list-collections) |Dresse la liste des collections dans le compte.|


- Si vous utilisez une API, le principal de service (SP), l’utilisateur ou le groupe qui exécute l’API doit avoir le rôle [Administrateur de collections](how-to-create-and-manage-collections.md#check-permissions) affecté dans Purview pour exécuter correctement cette API.
- Pour toutes les API Purview nécessitant {collectionName}, vous avez besoin de « name » (et non de « friendlyName »). Remplacez {collectionName} par la chaîne de nom de collection alphanumérique à six caractères. Notez que ce nom est différent du nom d’affichage convivial que vous avez fourni lors de la création de la collection. Si vous n’avez pas ce {collectionName} à portée de main, utilisez l’API [Lister les collections](/rest/api/purview/accountdataplane/collections/list-collections) pour sélectionner le nom de collection à six caractères dans la sortie JSON.
- Exemple JSON : 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>Description du code JSON de stratégie
Détails sur certains identificateurs importants figurant dans la sortie JSON reçue des API de collection.

**Name** : nom de la stratégie. 

**Id** : identificateur unique de la stratégie.

**Version** : dernier numéro de version de la stratégie. \*\*(Le numéro de version est incrémenté chaque fois que l’API de mise à jour de stratégie des métadonnées est appelée. Veillez à extraire la copie la plus récente de la stratégie en appelant l’API d’obtention d’une stratégie par ID de stratégie. Cette actualisation doit être effectuée systématiquement avant d’appeler l’API de mise à jour de stratégie (PUT), afin que vous disposiez toujours de la dernière version du code JSON.)

**DecisionRules** : répertoriez les règles et l’effet de cette stratégie. Pour les stratégies de métadonnées, l’effet est toujours « Autoriser ».

## <a name="use-purview-rest-apis-to-add-or-remove-usergroupserviceprincipal-to-a-collection-or-role"></a>Utiliser les API REST Purview pour ajouter ou supprimer un utilisateur/groupe/principal de service dans une collection ou un rôle
L’utilisation détaillée des API est fournie avec des exemples de sorties JSON. Nous vous recommandons vivement de suivre les étapes ci-dessous de manière séquentielle pour mieux comprendre les API de stratégie de métadonnées Purview.

## <a name="get-all-metadata-roles"></a>Obtenir tous les rôles de métadonnées
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```
Répertorie tous les rôles d’autorisation d’accès aux métadonnées disponibles.

La sortie JSON décrit les rôles et leurs autorisations associées dans ce format.

### <a name="default-metadata-roles"></a>Rôles de métadonnées par défaut

|**ID de rôle**|**autorisations**|**Description du rôle**|
|:-|:-|:-|
|purviewmetadatarole\_builtin\_data-source-administrator|Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read|Accorde l’accès à d’autres utilisateurs pour lire, écrire la collection, inscrire des sources de données et déclencher des analyses.|
|purviewmetadatarole\_builtin\_collection-administrator|Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write|Accès complet de niveau administrateur à l’ensemble de la collection, y compris l’ajout et la suppression d’utilisateurs et de noms de principaux de service dans la collection, les droits de gestion, l’octroi et la révocation d’accès. Dans certains cas, l’administrateur de collections peut être différent du créateur de la collection.|
|purviewmetadatarole\_builtin\_purview-reader|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read|Octroie uniquement un accès en lecture au traitement des données et à toutes les métadonnées (classifications, étiquettes de sensibilité, insights, ressources de lecture dans une collection), à l’exception des liaisons d’analyse.|
|purviewmetadatarole\_builtin\_data-curator|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read|Octroie un accès complet au traitement des données et à toutes les métadonnées (classifications, étiquettes de sensibilité, insights, ressources de lecture dans une collection), à l’exception des liaisons d’analyse.|
|purviewmetadatarole\_builtin\_data-share-contributor|Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write|Octroie l’accès aux partages de données en tant que contributeur.|

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>Obtenir toutes les stratégies de métadonnées
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
Répertorie toutes les stratégies de métadonnées disponibles dans l’ensemble de la hiérarchie des collections, en commençant par la collection racine et toutes ses stratégies enfants dans le format d’arborescence. La hiérarchie commence par la collection racine en haut, suivie de sa collection enfant. Chaque collection enfant encapsule tous ses enfants de niveau suivant.
Exemple :

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-selected-metadata-policy"></a>Obtenir la stratégie de métadonnées sélectionnée 
Il existe deux API permettant d’extraire la structure JSON d’une stratégie de métadonnées d’une collection particulière : en fournissant {collectionName} ou {PolicyID}.
Les deux API (décrites dans les deux sections suivantes) contribuent au même objectif, et les sorties JSON des deux sont exactement les mêmes.

### <a name="get-metadatapolicy-of-the-collection-by-collectionname"></a>Obtenir l’objet metadataPolicy de la collection selon collectionName
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```
1. Le nom du compte Purview est {your_purview_account_name}. Remplacez-le par le nom de votre compte Purview.
1. Dans la sortie JSON de l’API précédente « Obtenir toutes les stratégies de métadonnées », recherchez la section : { "type": "CollectionReference", "referenceName": "7xkdg2"}
1. Remplacez "{collectionName}" dans l’URL de l’API par la valeur de "referenceName" : "{6-char-collection-name}". Par conséquent, si votre nom de collection à 6 caractères est « 7xkdg2 », l’URL de l’API ressemble à https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01
1. Exécutez maintenant cette API. 

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```

### <a name="get-metadatapolicy-of-the-collection-by-policyid"></a>Obtenir l’objet metadataPolicy de la collection selon policyID
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```
1. Le nom du compte Purview est {your_purview_account_name}. Remplacez-le par le nom de votre compte Purview.
1. Dans la sortie JSON de l’API précédente « Obtenir toutes les stratégies de métadonnées », recherchez la section : {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}
1. Remplacez « {policyId} » dans l’URL de l’API par la valeur de « id ». Par conséquent, si votre « {policy-guid} » est « c6639bb2-9c41-4be0-912b-775750e725de », l’URL de l’API ressemble à https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01
1. Exécutez maintenant cette API. Notez que la sortie de cet appel d’API est identique à celle de l’appel d’API précédent. Vous pouvez choisir l’une ou l’autre, comme mentionné précédemment dans ce tutoriel.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-policy-addremove-usergroup-from-collection"></a>Stratégie de mise à jour : ajouter/supprimer un utilisateur/groupe de la collection
```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

Cette étape met à jour le code JSON de stratégie obtenu à l’étape précédente et l’envoie au service Purview à l’aide d’une méthode REST PUT.
Que vous souhaitiez **ajouter** ou **supprimer** un utilisateur/groupe/principal de service, vous devez suivre le même processus d’API.

1. Fournissez les ID d’objets utilisateur/groupe/principal de service {guid} dans le tableau « attributeValueIncludedIn » du code JSON.
1. Recherchez dans la sortie JSON de l’API Obtenir-une-stratégie-par-ID pour le tableau « attributeValueIncludedIn » à l’étape précédente et **ajoutez** ou **supprimez** l’ID d’objet utilisateur/groupe/principal de service dans le tableau. En cas de doute sur la façon de récupérer l’ID d’objet d’utilisateur ou de groupe, lisez le tutoriel [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)
1. Notez qu’il y aura plusieurs sections dans le mappage JSON vers chacun des 4 rôles. Pour le rôle d’autorisation d’administrateur de collections, utilisez le repère de section « ID » appelé « purviewmetadatarole_builtin_collection-administrator ». De même, utilisez la section correspondante pour les autres rôles.
1. Pour mieux comprendre l’opération d’ajout/suppression, examinez attentivement la différence entre la sortie JSON de l’API précédente et celle ci-dessous. Vous pouvez noter que dans la sortie JSON ci-dessous, nous avons ajouté l’ID d’utilisateur : « 3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f » en tant qu’administrateur de collections.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-root-collection-admin"></a>Ajouter un administrateur de collections racine
Par défaut, l’utilisateur qui a créé le compte Purview est l’administrateur de collections racine (administrateur du niveau le plus élevé de la hiérarchie de collections). Toutefois, dans certains cas, une organisation a besoin de modifier l’administrateur de collections racine à l’aide de l’API. Par exemple, il est possible que l’administrateur de collections racine actuel n’existe plus dans l’organisation. Dans ce cas, le portail Azure peut ne pas être accessible à qui que ce soit dans l’organisation, en raison de quoi l’utilisation de l’API pour affecter un nouvel administrateur de collections et gérer les autorisations de collection devient inévitable et la seule façon de recouvrer l’accès au compte Purview.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Vous devez uniquement transmettre le nouvel ID d’objet de l’administrateur de collections racine. Comme mentionné précédemment, l’ID d’objet peut être celui d’un utilisateur, d’un groupe ou d’un principal de service.
```json
{"objectId": "{guid}"}
```
En cas de réussite, toutes les API REST retournent la réponse HTTP 200 OK.

> [!NOTE]
> L’utilisateur qui appelle cette API doit disposer de l’autorisation de propriétaire ou d’administrateur d’accès utilisateur (UAA) sur le compte Purview pour exécuter une action d’écriture sur ce compte.


##  <a name="purview-rest-api-combined-archive"></a>Archive combinée des API REST Purview
Pour vous aider à démarrer rapidement avec les API Purview, [téléchargez](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip) une archive de toutes les spécifications des API REST Purview réunies en un même emplacement : [azure-purview-rest-api-specs.zip](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip). Vous pouvez utiliser ces modèles d’API pour comprendre et créer votre propre collection d’API Purview de code, de script, d’automatisation, [AutoRest](https://github.com/Azure/autorest) ou Postman.

## <a name="powershell-utility-to-run-purview-apis"></a>Utilitaire PowerShell pour exécuter les API Purview
Vous pouvez choisir d’exécuter les API REST Purview à l’aide de l’utilitaire PowerShell [Purview-API-PowerShell](https://aka.ms/purview-api-ps). Il peut être installé facilement à partir de PowerShell Gallery. Il exécute les mêmes commandes, mais à partir de Windows PowerShell.

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 


