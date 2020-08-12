---
title: Gestion du cycle de vie de Stockage Azure
description: Découvrez comment créer des règles de stratégie du cycle de vie pour faire passer les données vieillissantes du niveau de stockage chaud à froid et aux niveaux d’archivage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 865263d22d6f92dec74ef2820e80481e1a308804
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494551"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gérer le cycle de vie du Stockage Blob Azure

Les jeux de données ont des cycles de vie différents. Tôt dans le cycle de vie, les utilisateurs accèdent souvent à certaines données. Mais à mesure que les données vieillissent, le nombre d’accès diminue considérablement. Certaines données restent inactives dans le cloud et sont rarement sollicitées une fois stockées. Certaines expirent plusieurs jours ou mois après leur création, tandis que d’autres jeux de données sont lus et modifiés de manière active tout au long de leur vie. La gestion du cycle de vie de Stockage Blob Azure offre une stratégie complète basée sur des règles pour les comptes Stockage Blob et GPv2. Utilisez la stratégie pour effectuer la transition de vos données vers les niveaux d’accès appropriés ou pour faire en sorte qu’elles expirent à la fin de leur cycle de vie.

La gestion du cycle de vie vous permet de :

- Faire passer les objets blob à un niveau de stockage plus froid (de Chaud à Froid, de Chaud à Archive ou de Froid à Archive) pour optimiser les performances et le coût.
- Supprimer les objets blob à la fin de leur cycle de vie
- Définir des règles à exécuter une fois par jour au niveau du compte de stockage.
- Appliquer des règles à des conteneurs ou à un sous-ensemble d’objets blob (en utilisant des préfixes de noms ou des [balises d’index d’objets blob](storage-manage-find-blobs.md) en tant que filtres)

Considérez un scénario où des données sont sollicitées fréquemment durant les premières étapes du cycle de vie, mais seulement occasionnellement après deux semaines. Au-delà du premier mois, le jeu de données est rarement sollicité. Dans ce scénario, le stockage chaud est préférable durant les premiers temps. Un stockage froid est plus approprié pour un accès occasionnel. L’option Stockage archive est la meilleure une fois que les données ont plus d’un mois. En ajustant les niveaux de stockage en fonction de l’ancienneté des données, vous pouvez concevoir les options de stockage les moins coûteuses par rapport à vos besoins. Pour effectuer cette transition, les règles de stratégie de gestion du cycle de vie permettent de déplacer les données vieillissantes vers des niveaux plus froids.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>Disponibilité et tarification

La fonctionnalité de gestion du cycle de vie est disponible dans toutes les régions Azure pour les comptes d’usage général v2 (GPv2), les comptes de stockage Blob et les comptes de stockage d’objets Blob de blocs Premium. Dans le portail Azure, vous pouvez mettre à niveau un compte de stockage universel (GPv1) existant en compte GPv2. Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).  

La fonctionnalité de gestion du cycle de vie est gratuite. Les clients sont facturés au coût de fonctionnement normal pour les appels d’API [Définir le niveau d’objet blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier). L’opération de suppression est gratuite. Pour plus d’informations sur les prix, consultez [Tarification Objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Ajouter ou supprimer une stratégie

Vous pouvez ajouter, modifier ou supprimer une stratégie à l’aide de l’une des méthodes suivantes :

* [Azure portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Une stratégie peut être lue ou écrite dans son intégralité. Les mises à jour partielles ne sont pas prises en charge. 

> [!NOTE]
> Si vous activez les règles de pare-feu de votre compte de stockage, les requêtes de gestion du cycle de vie peuvent être bloquées. Vous pouvez débloquer ces requêtes en fournissant des exceptions pour les services Microsoft approuvés. Pour plus d’informations, consultez la section Exceptions dans [Configurer des pare-feu et des réseaux virtuels](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Cet article explique comment gérer une stratégie en utilisant le portail et des méthodes PowerShell.  

# <a name="portal"></a>[Portail](#tab/azure-portal)

Il existe deux façons d’ajouter une stratégie à l’aide du Portail Microsoft Azure. 

* [Mode Liste du Portail Microsoft Azure](#azure-portal-list-view)
* [Mode Code du Portail Microsoft Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Mode Liste du Portail Microsoft Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le Portail Azure, recherchez et sélectionnez votre compte de stockage. 

3. Sous **Service Blob**, sélectionnez **Gestion du cycle de vie** pour afficher ou modifier vos règles.

4. Sélectionnez l’onglet **Mode Liste**.

5. Sélectionnez **Ajouter une règle**, puis remplissez les champs de formulaire **Ensemble d’actions**. Dans l’exemple suivant, les objets BLOB sont déplacés vers le stockage froid s’ils n’ont pas été modifiés depuis 30 jours.

   ![Page Lifecycle management action set (Ensemble d’actions de gestion du cycle de vie) du Portail Microsoft Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Sélectionnez **Jeu de filtres** pour ajouter un filtre facultatif. Ensuite, sélectionnez **Parcourir** pour spécifier un conteneur et un dossier pour définir le filtre.

   ![Page Lifecycle management action set (Jeu de filtres de gestion du cycle de vie) du Portail Microsoft Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Sélectionnez **Vérifier + ajouter** pour passer en revue les paramètres de stratégie.

9. Sélectionnez **Ajouter** pour ajouter la stratégie.

#### <a name="azure-portal-code-view"></a>Mode Code du Portail Microsoft Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le Portail Azure, recherchez et sélectionnez votre compte de stockage.

3. Sous **Service Blob**, sélectionnez **Gestion du cycle de vie** pour afficher ou modifier votre stratégie.

4. Le code JSON suivant est un exemple de stratégie que vous pouvez coller dans l’onglet **Mode Code**.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Sélectionnez **Enregistrer**.

6. Pour plus d’informations sur cet exemple de JSON, voir les sections [Stratégie](#policy) et [Règles](#rules).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Le script PowerShell suivant permet d’ajouter une stratégie à votre compte de stockage. La variable `$rgname` doit être initialisée avec le nom de votre groupe de ressources. La variable `$accountName` doit être initialisée avec le nom de votre compte de stockage.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Modèle](#tab/template)

Vous pouvez définir une gestion du cycle de vie à l’aide de modèles Azure Resource Manager. Voici un exemple de modèle pour déployer un compte de stockage RA-GRS GPv2 avec une stratégie de gestion du cycle de vie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Policy

Une stratégie de gestion du cycle de vie est un ensemble de règles dans un document JSON :

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Une stratégie est un ensemble de règles :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| `rules`        | Un ensemble d’objets de règle | Une stratégie requiert au moins une règle. Vous pouvez définir jusqu’à 100 règles dans une stratégie.|

Chaque règle au sein de la stratégie a plusieurs paramètres :

| Nom du paramètre | Type de paramètre | Notes | Obligatoire |
|----------------|----------------|-------|----------|
| `name`         | String |Un nom de règle peut compter jusqu’à 256 caractères alphanumériques. Les noms de règle respectent la casse.  Ils doivent être uniques dans la stratégie. | True |
| `enabled`      | Boolean | Valeur booléenne facultative pour permettre la désactivation temporaire d’une règle. La valeur par défaut est true. | False | 
| `type`         | Une valeur enum | Le type valide actuel est `Lifecycle`. | True |
| `definition`   | Un objet qui définit la règle du cycle de vie | Chaque définition se compose d’un jeu de filtres et d’un jeu d’actions. | True |

## <a name="rules"></a>Règles

Chaque définition de règle se compose d’un jeu de filtres et d’un jeu d’actions. Le [jeu de filtres](#rule-filters) limite les actions de règle à un certain ensemble d’objets dans un conteneur ou des noms d’objets. L’[ensemble d’actions](#rule-actions) applique les actions de niveau ou de suppression à l’ensemble d’objets filtré.

### <a name="sample-rule"></a>Exemple de règle

L’exemple de règle suivant filtre le compte pour exécuter les actions sur des objets existant à l’intérieur de `container1` et commençant par `foo`.  

>[!NOTE]
>- La gestion du cycle de vie ne prend en charge que le type d’objet blob de blocs.<br>
>- La gestion du cycle de vie n’affecte pas les conteneurs système comme $logs et $web.

- Niveau objet blob sur accès froid 30 jours après la dernière modification
- Niveau objet blob sur accès archive 90 jours après la dernière modification
- Supprimer l’objet blob 2 555 jours (sept ans) après la dernière modification
- Supprimer les instantanés d’objet blob 90 jours après la création des instantanés

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtres de règle

Les filtres limitent les actions des règles à un sous-ensemble d’objets blob dans le compte de stockage. Si plusieurs filtres sont définis, une opération logique `AND` est exécutée sur tous les filtres.

Les filtres sont les suivants :

| Nom du filtre | Type de filtre | Notes | Est obligatoire |
|-------------|-------------|-------|-------------|
| blobTypes   | Un ensemble de valeurs enum prédéfinies. | La version actuelle prend en charge `blockBlob`. | Oui |
| prefixMatch | Un ensemble de chaînes pour les préfixes à mettre en correspondance. Chaque règle peut définir jusqu’à 10 préfixes. Une chaîne de préfixe doit commencer par un nom de conteneur. Par exemple, si vous souhaitez faire correspondre tous les objets blob sous `https://myaccount.blob.core.windows.net/container1/foo/...` pour une règle, prefixMatch est `container1/foo`. | Si vous ne définissez pas prefixMatch, la règle s’applique à tous les objets blob au sein du compte de stockage.  | Non |
| blobIndexMatch | Un ensemble de valeurs de dictionnaire constitué d’une clé de balise d’index d’objets blob et de conditions de valeur à mettre en correspondance. Chaque règle peut définir jusqu’à 10 conditions de balise d’index d’objets blob. Par exemple, si vous souhaitez mettre en correspondre tous les objets blob avec `Project = Contoso` sous `https://myaccount.blob.core.windows.net/` pour une règle, le blobIndexMatch est `{"name": "Project","op": "==","value": "Contoso"}`. | Si vous ne définissez pas blobIndexMatch, la règle s’applique à tous les objets blob au sein du compte de stockage. | Non |

> [!NOTE]
> L’index d’objets blob, actuellement en préversion publique, est disponible dans les régions **Canada Centre**, **Canada Est**, **France Centre** et **France Sud**. Pour en savoir plus sur cette fonctionnalité ainsi que sur les problèmes et limitations connus, consultez [Gérer et rechercher des données sur le Stockage Blob Azure avec un index d’objets blob (préversion)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Actions de règle

Des actions sont appliquées aux objets blob filtrés lorsque la condition d’exécution est remplie.

La gestion du cycle de vie prend en charge la hiérarchisation et la suppression des objets blob ainsi que la suppression des instantanés d’objets blob. Définissez au moins une action pour chaque règle sur les objets blob ou les instantanés d’objets blob.

| Action        | Objet blob de base                                   | Instantané      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Prend actuellement en charge les objets blob au niveau chaud         | Non pris en charge |
| tierToArchive | Prend actuellement en charge les objets blob au niveau chaud ou froid | Non pris en charge |
| supprimer        | Prise en charge                                   | Prise en charge     |

>[!NOTE]
>Si vous définissez plusieurs actions sur le même objet blob, la gestion du cycle de vie applique l’action la moins coûteuse à l’objet blob. Par exemple, l’action `delete` est moins coûteuse que l’action `tierToArchive`. L’action `tierToArchive` est moins coûteuse que l’action `tierToCool`.

Les conditions d’exécution sont basées sur l’âge. L’objet blob de base utilise l’heure de dernière modification pour suivre l’âge, tandis que les instantanés d’objets blob utilisent l’heure de création des instantanés.

| Condition d’exécution d’action             | Valeur de la condition                          | Description                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Nombre entier indiquant l’âge en jours | Condition pour les actions des objets blob de base     |
| daysAfterCreationGreaterThan     | Nombre entier indiquant l’âge en jours | Condition pour les actions des instantanés d’objet blob |

## <a name="examples"></a>Exemples

Les exemples suivants expliquent comment résoudre des scénarios courants avec les règles de stratégie du cycle de vie.

### <a name="move-aging-data-to-a-cooler-tier"></a>Déplacer les données vieillissantes vers un niveau plus froid

Cet exemple montre comment déplacer des objets blob de blocs ayant le préfixe `container1/foo` ou `container2/bar`. La stratégie déplace les objets blob qui n’ont pas été modifiés depuis plus de 30 jours vers le stockage froid et les objets blob non modifiés depuis 90 jours vers le niveau archive :

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-after-ingest"></a>Archiver les données après leur ingestion

D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées. La stratégie du cycle de vie suivante est configurée pour archiver les données peu après leur ingestion. Cet exemple déplace les objets blob de blocs du compte de stockage au sein du conteneur `archivecontainer` dans un niveau archive. Le déplacement est accompli en agissant sur les objets blob 0 jour après l’heure de dernière modification :

> [!NOTE] 
> Il est recommandé de charger vos objets BLOB directement au niveau Archive pour gagner en efficacité. Vous pouvez utiliser l’en-tête x-ms-access-tier pour [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) avec REST 2018-11-09 et les versions ultérieures ou nos dernières bibliothèques clientes de stockage d’objets BLOB. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Faire expirer les données selon l’âge

Certaines données sont supposées expirer un certain nombre de jours ou de mois après leur création. Vous pouvez configurer une stratégie de gestion du cycle de vie afin de faire expirer les données en les supprimant en fonction de leur ancienneté. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc datant de plus de 365 jours.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Supprimer des données avec des balises d’index d’objets blob
Certaines données ne doivent expirer que si elles sont marquées explicitement pour suppression. Vous pouvez configurer une stratégie de gestion du cycle de vie pour faire expirer les données qui sont marquées avec des attributs clé/valeur d’index d’objets blob. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc balisés avec `Project = Contoso`. Pour en savoir plus sur l’index d’objets blob, consultez [Gérer et rechercher des données sur le Stockage Blob Azure avec un index d’objets blob (préversion)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Supprimer les anciens instantanés

Pour les données qui sont modifiées et consultées régulièrement tout au long de leur durée de vie, les instantanés sont souvent utilisés pour suivre les versions antérieures des données. Vous pouvez créer une stratégie qui supprime les anciens instantanés selon leur ancienneté. L’âge de l’instantané est déterminé en regardant l’heure de création. Cette règle de stratégie supprime les instantanés d’objets blob de blocs au sein du conteneur `activedata` dont la création remonte à plus de 90 jours.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Questions fréquentes (FAQ)

**J’ai créé une stratégie. Pourquoi les actions ne s’exécutent-elles pas immédiatement ?**  
La plateforme exécute la stratégie de cycle de vie une fois par jour. Une fois que vous avez configuré une stratégie, jusqu’à 24 heures peuvent s’écouler avant que certaines actions s’exécutent pour la première fois.  

**Si je mets à jour une stratégie existante, combien de temps dois-je attendre avant que les actions soient effectuées ?**  
Cela peut prendre jusqu’à 24 heures avant que la stratégie mise à jour ne soit appliquée. Une fois la stratégie en vigueur, cela peut prendre jusqu’à 24 heures pour que les actions s’exécutent. Par conséquent, l’exécution des actions de la stratégie peut prendre jusqu’à 48 heures.   

**J’ai réactivé manuellement un blob archivé. Comment puis-je empêcher son renvoi temporaire au niveau Archives ?**  
Quand un objet blob est déplacé d’un niveau d’accès vers un autre, l’heure de sa dernière modification ne change pas. Si vous réactivez manuellement un blob archivé à un chaud, il est renvoyé au niveau archive par le moteur de gestion du cycle de vie. Désactivez la règle qui affecte temporairement cet objet BLOB pour empêcher son archivage. Réactivez la règle lorsque le BLOB peut être renvoyé en toute sécurité au niveau archive. Vous pouvez aussi copier le blob vers un autre emplacement s’il doit rester en permanence au niveau chaud ou froid.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment récupérer des données après une suppression accidentelle :

- [Suppression réversible pour les objets blob de Stockage Azure](../blobs/storage-blob-soft-delete.md)

Apprenez à gérer et à rechercher des données avec un index d’objets blob :

- [Gérer et rechercher des données dans le Stockage Blob Azure avec un index d’objets blob](storage-manage-find-blobs.md)
