---
title: Découvrir les API de rôles et de stratégie de métadonnées des collections Azure Purview
description: Ce tutoriel explique comment gérer le contrôle d’accès en fonction du rôle sur les collections Azure Purview pour les utilisateurs, les groupes ou les principaux de service.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 79848d8f54ea1d019f2a9e6cf93a61cc8b7c49f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245926"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-on-azure-purview-collections"></a>Tutoriel : Utiliser des API REST pour gérer le contrôle d’accès en fonction du rôle sur les collections Azure Purview 

En août 2021, le contrôle d’accès dans Azure Purview est passé d’Azure Identity & Access Management (IAM) (plan de contrôle) aux [collections Azure Purview](how-to-create-and-manage-collections.md) (plan de données). Cette modification procure aux conservateurs et administrateurs de données d’entreprise un contrôle d’accès plus précis sur leurs sources de données analysées par Azure Purview. Elle permet également aux organisations d’auditer l’accès et l’utilisation de leurs données.

Ce tutoriel vous guide pas à pas dans l’utilisation des API de stratégie de métadonnées Azure Purview pour vous aider à ajouter des utilisateurs, des groupes ou des principaux de service dans une collection, et pour gérer ou supprimer leurs rôles au sein de cette collection. Les API REST sont une méthode alternative à l’utilisation du portail Azure ou d’Azure Purview Studio pour obtenir la même précision de contrôle d’accès en fonction du rôle.

Pour plus d’informations sur les rôles intégrés dans Azure Purview, consultez le [Guide des autorisations Azure Purview](catalog-permissions.md#roles). Le guide mappe les rôles au niveau des autorisations d’accès accordées aux utilisateurs.

## <a name="metadata-policy-api-reference-summary"></a>Récapitulatif des informations de référence sur les API de stratégie de métadonnées
Le tableau suivant fournit une vue d’ensemble des [informations de référence sur les API de stratégie de métadonnées Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy). 

Remplacez {pv-acc-name} par le nom de votre compte Azure Purview avant d’exécuter ces API. Par exemple, si le nom de votre compte Azure Purview est *FabrikamPurviewAccount*, vos points de terminaison d’API deviennent *FabrikamPurviewAccount.purview.azure.com*.

| Fonction&nbsp;API | Méthode&nbsp;REST | Point de terminaison&nbsp;d’API | Description | 
| :- | :- | :- | :- | 
| Lire tous les rôles de métadonnées| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Lit tous les rôles de métadonnées à partir de votre compte Azure Purview.| 
| Lire la stratégie de métadonnées par nom de collection| GET| https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Lit la stratégie de métadonnées en utilisant un nom de collection spécifié (le nom aléatoire de 6 caractères généré par Azure Purview quand il crée la stratégie).| 
| Lire la stratégie de métadonnées par PolicyID| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Lit la stratégie de métadonnées en utilisant un ID de stratégie spécifié. L’ID de stratégie est au format GUID.| 
| Lire toutes les stratégies de métadonnées| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Lit toutes les stratégies de métadonnées à partir de votre compte Azure Purview. Vous pouvez choisir une certaine stratégie à utiliser à partir de la liste de sortie JSON qui est générée par cette API.| 
| Mettre à jour/placer (PUT) la stratégie de métadonnées| PUT| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Met à jour la stratégie de métadonnées en utilisant un ID de stratégie spécifié. L’ID de stratégie est au format GUID.|
| | | 

## <a name="azure-purview-catalog-collections-api-reference-summary"></a>Récapitulatif des informations de référence des API de collections de catalogues Azure Purview
Le tableau suivant donne une vue d’ensemble des API de collections Azure Purview. Pour obtenir une documentation complète sur chaque API, sélectionnez l’opération d’API dans la colonne de gauche.

| Opération | Description | 
| :- | :- | 
| [Créer ou mettre à jour une collection](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Crée ou met à jour une entité de collection. | 
| [Supprimer une collection](/rest/api/purview/accountdataplane/collections/delete-collection) | Supprime une entité de collection. | 
| [Obtenir une collection](/rest/api/purview/accountdataplane/collections/get-collection) | Obtient une collection.| 
| [Obtenir le chemin de collection](/rest/api/purview/accountdataplane/collections/get-collection-path) | Obtient le nom parent et les chaînes de nom d’affichage qui représentent le chemin de collection.| 
| [Lister les noms des collections enfants](/rest/api/purview/accountdataplane/collections/list-child-collection-names) | Dresse la liste des noms des collections enfants de la collection.| 
| [Lister les collections](/rest/api/purview/accountdataplane/collections/list-collections) | Dresse la liste des collections dans le compte.| 


- Si vous utilisez l’API, le principal de service, l’utilisateur ou le groupe qui exécute l’API doit avoir un rôle [Administrateur de collections](how-to-create-and-manage-collections.md#check-permissions) affecté dans Azure Purview pour exécuter correctement cette API.

- Pour toutes les API Azure Purview nécessitant {collectionName}, vous devez utiliser *« name »* (et non *« friendlyName »* ). Remplacez {collectionName} par la chaîne de nom de collection alphanumérique à six caractères. 
   > [!NOTE]
   > Ce nom est différent du nom d’affichage convivial que vous avez fourni quand vous avez créé la collection. Si vous n’avez pas {collectionName} à portée de main, utilisez l’[API Lister les collections](/rest/api/purview/accountdataplane/collections/list-collections) pour sélectionner le nom de collection à six caractères dans la sortie JSON.

Voici un exemple de fichier JSON : 

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

Voici certains identificateurs importants figurant dans la sortie JSON qui est reçue des API de collection :

**Name** : nom de la stratégie. 

**Id** : identificateur unique de la stratégie.

**Version** : dernier numéro de version de la stratégie. 
   > [!IMPORTANT]
   > Le numéro de version est incrémenté chaque fois que l’API de mise à jour de stratégie des métadonnées est appelée. Veillez à extraire la copie la plus récente de la stratégie en appelant l’API d’obtention d’une stratégie par ID de stratégie. Effectuez cette actualisation systématiquement avant d’appeler l’API de mise à jour de stratégie (PUT), afin que vous disposiez toujours de la dernière version du fichier JSON.

**DecisionRules** : répertoriez les règles et l’effet de cette stratégie. Pour les stratégies de métadonnées, l’effet est toujours *« Autoriser »* .

## <a name="add-or-remove-users-from-a-collection-or-role"></a>Ajouter des utilisateurs à une collection ou à un rôle, ou en supprimer de ceux-ci

Utilisez les API REST Azure Purview pour ajouter un utilisateur, un groupe ou un principal de service à une collection ou à un rôle, ou pour en supprimer de ceux-ci. L’utilisation détaillée des API est fournie avec des exemples de sorties JSON. Nous vous recommandons vivement de suivre les instructions des sections suivantes dans l’ordre pour une meilleure compréhension des API de stratégie de métadonnées Azure Purview.

## <a name="get-all-metadata-roles"></a>Obtenir tous les rôles de métadonnées

Pour lister tous les rôles d’autorisation d’accès aux métadonnées disponibles, exécutez la commande suivante :

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```

La sortie JSON décrit les rôles et leurs autorisations associées dans ce format.

Les rôles de métadonnées par défaut sont présentés dans le tableau suivant :

| ID de rôle | Autorisations | Description de rôle | 
| :- | :- | :- | 
| purviewmetadatarole\_builtin\_data-source-administrator| Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read| Accorde l’accès à d’autres utilisateurs pour lire, écrire la collection, inscrire des sources de données et déclencher des analyses.| 
| purviewmetadatarole\_builtin\_collection-administrator| Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write| Accès complet de niveau administrateur à l’ensemble de la collection, y compris l’ajout et la suppression d’utilisateurs et de noms de principaux de service (SPN) dans la collection, les droits de gestion et l’octroi ou la révocation d’accès. Dans certains cas, l’administrateur de collections peut être différent du créateur de la collection.| 
| purviewmetadatarole\_builtin\_purview-reader| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read| Octroie uniquement un accès en lecture au traitement des données et à toutes les métadonnées, notamment les classifications, les étiquettes de sensibilité, les insights et les ressources de lecture dans une collection, à l’exception des liaisons d’analyse.| 
| purviewmetadatarole\_builtin\_data-curator| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read| Octroie un accès complet au traitement des données et à toutes les métadonnées, notamment les classifications, les étiquettes de sensibilité, les insights et les ressources de lecture dans une collection, à l’exception des liaisons d’analyse.| 
| purviewmetadatarole\_builtin\_data-share-contributor| Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write| Octroie l’accès aux partages de données en tant que contributeur. | 

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
La commande précédente liste toutes les stratégies de métadonnées disponibles sur l’ensemble de la hiérarchie de collections sous forme d’arborescence, depuis la collection racine située en haut jusqu’à toutes ses stratégies enfants. Chaque collection enfant contient tous ses enfants de niveau suivant.

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

## <a name="get-the-selected-metadata-policy"></a>Obtenir la stratégie de métadonnées sélectionnée 

Vous pouvez utiliser deux API pour extraire la structure JSON d’une stratégie de métadonnées d’une collection particulière en fournissant {collectionName} ou {PolicyID}.

Comme décrit dans les deux sections suivantes, les deux API contribuent au même objectif et les sorties JSON des deux sont exactement les mêmes.

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-collection-name"></a>Obtenir la stratégie de métadonnées de la collection en utilisant le nom de la collection

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```

1. Le nom du compte Azure Purview est {your_purview_account_name}. Remplacez-le par le nom de votre compte Azure Purview.

1. Dans la sortie JSON de l’API précédente « Obtenir toutes les stratégies de métadonnées », recherchez la section suivante : 

    { "type": "CollectionReference", "referenceName": "7xkdg2"}

1. Remplacez "{collectionName}" dans l’URL de l’API par la valeur de "referenceName" : "{6-char-collection-name}". Par exemple, si votre nom de collection à six caractères est « 7xkdg2 », l’URL de l’API présente le format suivant : 

   https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01

1. Exécutez l’API suivante : 

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

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-policy-id"></a>Obtenir la stratégie de métadonnées de la collection en utilisant l’ID de la stratégie

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

1. Le nom du compte Azure Purview est {your_purview_account_name}. Remplacez-le par le nom de votre compte Azure Purview.

1. Dans la sortie JSON de l’API précédente « Obtenir toutes les stratégies de métadonnées », recherchez la section suivante : 

    {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}

1. Remplacez « {policyId} » dans l’URL de l’API par la valeur de « id ». Par exemple, si « {policy-guid} » est « c6639bb2-9c41-4be0-912b-775750e725de », l’URL de l’API présente le format suivant :
 
   https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01

1. À présent, exécutez l’API suivante : 

   > [!NOTE]
   > La sortie de cet appel d’API est identique à celle de l’appel d’API précédent. Comme indiqué précédemment, vous pouvez choisir l’une ou l’autre des API.

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


## <a name="update-the-collection-policy"></a>Mettre à jour la stratégie de collection

```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

Dans cette section, vous allez mettre à jour la sortie JSON de stratégie que vous avez obtenu à l’étape précédente en ajoutant ou en supprimant un utilisateur, un groupe ou un principal de service dans la collection. Vous pourrez ensuite l’envoyer au service Azure Purview en utilisant une méthode REST PUT.

Que vous ajoutiez ou supprimiez un utilisateur, un groupe ou un principal de service, vous suivez le même processus d’API.

1. Fournissez l’ID d’objet d’utilisateur, de groupe ou de principal de service {guid} dans le tableau « attributeValueIncludedIn » du code JSON.

1. Dans la sortie JSON de l’API d’obtention d’une stratégie par ID, recherchez le tableau « attributeValueIncludedIn » obtenu à l’étape précédente et ajoutez ou supprimez l’ID d’objet d’utilisateur, de groupe ou de principal de service dans le tableau. Si vous n’êtes pas sûr de savoir comment extraire l’ID d’objet de l’utilisateur, du groupe ou du principal du service, consultez [Get-AzureADUser](/powershell/module/azuread/get-azureaduser).

1. Il existe plusieurs sections dans le mappage JSON pour chacun des quatre rôles. Pour le rôle d’autorisations d’administrateur de collections, utilisez le repère de section ID appelé « purviewmetadatarole_builtin_collection-administrator ». De même, utilisez la section correspondante pour les autres rôles.

1. Pour mieux comprendre l’opération d’ajout/suppression, examinez attentivement la différence entre la sortie JSON de l’API précédente et la sortie suivante. Dans la sortie JSON suivante, nous avons ajouté l’ID d’utilisateur « 3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f » en tant qu’administrateur de collections.

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
## <a name="add-the-root-collection-administrator-role"></a>Ajouter le rôle d’administrateur de collection racine
Par défaut, l’utilisateur qui a créé le compte Azure Purview est l’administrateur de collection racine (c’est-à-dire l’administrateur du niveau le plus élevé de la hiérarchie de collections). Toutefois, dans certains cas, une organisation a besoin de changer l’administrateur de collection racine en utilisant l’API. Par exemple, il est possible que l’administrateur de collection racine actuel n’existe plus dans l’organisation. Dans ce cas, le portail Azure peut être inaccessible à toute personne au sein de l’organisation. Pour cette raison, l’utilisation de l’API pour affecter un nouvel administrateur de collection racine et pour gérer les autorisations des collections s’avère la seule façon de récupérer l’accès au compte Azure Purview.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Pour exécuter la commande précédente, vous devez uniquement transmettre l’ID d’objet du nouvel administrateur de collection racine. Comme mentionné précédemment, l’ID d’objet peut être celui d’un utilisateur, d’un groupe ou d’un principal de service.

```json
{"objectId": "{guid}"}
```

> [!NOTE]
> Les utilisateurs qui appellent cette API doivent disposer d’autorisations de propriétaire ou de compte d’utilisateur et d’authentification sur le compte Azure Purview pour exécuter une action d’écriture sur le compte.

## <a name="additional-resources"></a>Ressources supplémentaires

Vous pouvez choisir d’exécuter les API REST Azure Purview en recourant à l’[utilitaire PowerShell](https://aka.ms/purview-api-ps). Il peut être installé facilement à partir de PowerShell Gallery. Avec cet utilitaire, vous pouvez exécuter toutes les mêmes commandes, mais à partir de Windows PowerShell.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
