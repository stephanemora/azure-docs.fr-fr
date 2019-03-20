---
title: Gérer le cycle de vie du stockage Azure
description: Découvrez comment créer des règles de stratégie du cycle de vie pour faire passer les données vieillissantes du niveau de stockage chaud à froid et aux niveaux d’archivage.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 93c19bc39f64df21dfa9db2490ab2103aba8191d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086103"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gestion du cycle de vie de Stockage Blob Azure (préversion)

Les jeux de données ont des cycles de vie différents. Tôt dans le cycle de vie, les utilisateurs accèdent souvent à certaines données. Mais à mesure que les données vieillissent, le nombre d’accès diminue considérablement. Certaines données restent inactives dans le cloud et sont rarement sollicitées une fois stockées. Certaines expirent plusieurs jours ou mois après leur création, tandis que d’autres jeux de données sont lus et modifiés de manière active tout au long de leur vie. La gestion du cycle de vie de Stockage Blob Azure (préversion) offre une stratégie complète basée sur des règles pour les comptes Stockage Blob et GPv2. Utilisez la stratégie pour effectuer la transition de vos données vers les niveaux d’accès appropriés ou pour faire en sorte qu’elles expirent à la fin de leur cycle de vie.

La gestion du cycle de vie vous permet de :

- Faire passer les objets blob à un niveau de stockage plus froid (de Chaud à Froid, de Chaud à Archive ou de Froid à Archive) pour optimiser les performances et le coût.
- Supprimer les objets blob à la fin de leur cycle de vie
- Définir des règles à exécuter une fois par jour au niveau du compte de stockage.
- Appliquer des règles aux conteneurs ou à un sous-ensemble d’objets blob (en utilisant des préfixes comme filtres)

Considérez le scénario où un jeu de données est sollicité fréquemment durant les premières étapes du cycle de vie, mais seulement occasionnellement après deux semaines. Au-delà du premier mois, le jeu de données est rarement sollicité. Dans ce scénario, le stockage chaud est préférable durant les premiers temps. Le stockage froid est plus adapté à un accès occasionnel, tandis que l’archivage est la meilleure option pour les données datant de plus d’un mois. En ajustant les niveaux de stockage en fonction de l’ancienneté des données, vous pouvez concevoir les options de stockage les moins coûteuses par rapport à vos besoins. Pour effectuer cette transition, les règles de stratégie de gestion du cycle de vie permettent de déplacer les données vieillissantes vers des niveaux plus froids.

## <a name="storage-account-support"></a>Prise en charge du compte de stockage

La stratégie de gestion du cycle de vie est disponible avec les comptes v2 universels (GPv2) et les comptes Stockage Blob. Dans le portail Azure, vous pouvez transformer un compte universel (GPv1) existant en compte GPv2 d’un simple clic. Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Tarifs 

La fonctionnalité de gestion du cycle de vie est gratuite dans la préversion. Les clients sont facturés le coût de fonctionnement normal pour les appels d’API [Répertorier les objets blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) et [Définir le niveau d’objet blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier). Pour plus d’informations sur les prix, consultez [Tarification Objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>S’inscrire pour la préversion 
Pour vous inscrire à la préversion publique, vous devez envoyer une demande afin d’enregistrer cette fonctionnalité dans votre abonnement. Les demandes sont généralement approuvées dans les 72 heures. Une fois votre demande acceptée, tous les comptes Stockage Blob ou GPv2 nouveaux et existants dans les régions suivantes incluent la fonctionnalité : USA Ouest 2, USA Centre-Ouest, USA Est 2 et Europe Ouest. La préversion prend uniquement en charge les objets blob de blocs. Comme avec la plupart des préversions, vous ne devez pas utiliser cette fonctionnalité pour les charges de production avant qu’elle ne soit en disponibilité générale.

Pour soumettre une requête, exécutez les commandes PowerShell ou CLI suivantes.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour soumettre une requête :

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Vous pouvez vérifier l’état de l'approbation de l’inscription à l’aide de la commande suivante :
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Avec l’approbation et l’inscription appropriée, vous recevez l’état *Inscrit* quand vous envoyez les requêtes précédentes.

### <a name="azure-cli"></a>Azure CLI

Pour soumettre une requête : 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Vous pouvez vérifier l’état de l'approbation de l’inscription à l’aide de la commande suivante :
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Avec l’approbation et l’inscription appropriée, vous recevez l’état *Inscrit* quand vous envoyez les requêtes précédentes.


## <a name="add-or-remove-a-policy"></a>Ajouter ou supprimer une stratégie 

Vous pouvez ajouter, modifier ou supprimer une stratégie à l’aide du portail Azure, de [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), d’[Azure CLI](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), des [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate) ou des outils clients dans les langages suivants : [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Toutes les ressources**, puis sélectionnez votre compte de stockage.

3. Sélectionnez **Gestion du cycle de vie (préversion)** regroupé sous Service Blob pour afficher ou modifier votre stratégie.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }'

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
> Si vous activez les règles de pare-feu de votre compte de stockage, les requêtes de gestion du cycle de vie peuvent être bloquées. Vous pouvez débloquer ces requêtes en fournissant des exceptions. Pour plus d’informations, consultez la section Exceptions dans [Configurer des pare-feu et des réseaux virtuels](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Stratégie

Une stratégie de gestion du cycle de vie est un ensemble de règles dans un document JSON :

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


Une stratégie nécessite deux paramètres :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| version        | Une chaîne exprimée sous la forme `x.x` | Le numéro de la préversion est 0.5. |
| règles          | Un ensemble d’objets de règle | Au moins une règle est requise dans chaque stratégie. Lors de la version préliminaire, vous pouvez spécifier jusqu’à 4 règles stratégie. |

Chaque règle au sein de la stratégie nécessite trois paramètres :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| Nom           | Chaîne | Un nom de règle peut inclure n’importe quelle combinaison de caractères alphanumériques. Les noms de règle respectent la casse. Ils doivent être uniques dans la stratégie. |
| Type           | Une valeur enum | La valeur valide pour la préversion est `Lifecycle`. |
| Définition     | Un objet qui définit la règle du cycle de vie | Chaque définition se compose d’un jeu de filtres et d’un jeu d’actions. |

## <a name="rules"></a>Règles

Chaque définition de règle se compose d’un jeu de filtres et d’un jeu d’actions. Le [jeu de filtres](#rule-filters) limite les actions de règle à un certain ensemble d’objets dans un conteneur ou des noms d’objets. L’[ensemble d’actions](#rule-actions) applique les actions de niveau ou de suppression à l’ensemble d’objets filtré.

### <a name="sample-rule"></a>Exemple de règle
L’exemple de règle suivante filtre le compte pour exécuter les actions uniquement sur `container1/foo`. Exécutez les actions suivantes pour tous les objets qui existent à l’intérieur de `container1` **ET** commencent par `foo` : 

- Niveau objet blob sur accès froid 30 jours après la dernière modification
- Niveau objet blob sur accès archive 90 jours après la dernière modification
- Supprimer l’objet blob 2 555 jours (sept ans) après la dernière modification
- Supprimer les instantanés d’objet blob 90 jours après la création des instantanés

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "ruleFoo",
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

Lors de la version d’évaluation, les filtres valides sont les suivants :

| Nom du filtre | Type de filtre | Notes | Est obligatoire |
|-------------|-------------|-------|-------------|
| blobTypes   | Un ensemble de valeurs enum prédéfinies. | La préversion prend uniquement en charge `blockBlob`. | Oui |
| prefixMatch | Un ensemble de chaînes servant à faire correspondre les préfixes. Une chaîne de préfixe doit commencer par un nom de conteneur. Par exemple, si vous souhaitez faire correspondre tous les objets BLOB sous «<https://myaccount.blob.core.windows.net/container1/foo/>... » pour une règle, le prefixMatch est `container1/foo`. | Si vous ne définissez pas prefixMatch, les règles s’appliquent à tous les objets blob dans le compte. | Non  |

### <a name="rule-actions"></a>Actions de règle

Des actions sont appliquées aux objets blob filtrés lorsque la condition d’exécution est remplie.

Dans la préversion, la gestion du cycle de vie prend en charge la hiérarchisation et la suppression des objets blob ainsi que la suppression des instantanés d’objets blob. Définissez au moins une action pour chaque règle sur les objets blob ou les instantanés d’objets blob.

| Action        | Objet blob de base                                   | Instantané      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Prend actuellement en charge les objets blob au niveau chaud         | Non pris en charge |
| tierToArchive | Prend actuellement en charge les objets blob au niveau chaud ou froid | Non pris en charge |
| delete        | Pris en charge                                   | Pris en charge     |

> [!NOTE]
> Si vous définissez plusieurs actions sur le même objet blob, la gestion du cycle de vie applique l’action la moins coûteuse à l’objet blob. Par exemple, l’action `delete` est moins coûteuse que l’action `tierToArchive`. L’action `tierToArchive` est moins coûteuse que l’action `tierToCool`.

Dans la version préliminaire, les conditions d’exécution des actions sont basées sur l’âge. L’objet blob de base utilise l’heure de dernière modification pour suivre l’âge, tandis que les instantanés d’objets blob utilisent l’heure de création des instantanés.

| Condition d’exécution de l’action | Valeur de la condition | Description |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Nombre entier indiquant l’âge en jours | Condition valide pour les actions des objets blob de base |
| daysAfterCreationGreaterThan     | Nombre entier indiquant l’âge en jours | Condition valide pour les actions des instantanés d’objet blob | 

## <a name="examples"></a>Exemples
Les exemples suivants expliquent comment résoudre des scénarios courants avec les règles de stratégie du cycle de vie.

### <a name="move-aging-data-to-a-cooler-tier"></a>Déplacer les données vieillissantes vers un niveau plus froid

Cet exemple montre comment déplacer des objets blob de blocs ayant le préfixe `container1/foo` ou `container2/bar`. La stratégie déplace les objets blob qui n’ont pas été modifiés depuis plus de 30 jours vers le stockage froid et les objets blob non modifiés depuis 90 jours vers le niveau archive :

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "agingRule",
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

### <a name="archive-data-at-ingest"></a>Archiver les données à la réception 

D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées. Archivez ces données immédiatement une fois qu’elles ont été ingérées. La stratégie du cycle de vie suivante est configurée pour archiver des données à la réception. Cet exemple déplace immédiatement les objets blob de blocs du compte de stockage au sein du conteneur `archivecontainer` dans un niveau archive. Le déplacement immédiat est accompli en agissant sur les objets blob 0 jours après l’heure de dernière modification :

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "archiveRule",
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

Certaines données sont conçues pour expirer plusieurs jours ou mois après leur création afin de réduire les coûts ou de se conformer à la réglementation du pays. Vous pouvez configurer une stratégie de gestion du cycle de vie afin de faire expirer les données en les supprimant en fonction de leur ancienneté. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc (sans aucun préfixe spécifié) antérieurs à 365 jours.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "expirationRule",
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

### <a name="delete-old-snapshots"></a>Supprimer les anciens instantanés

Pour les données qui sont modifiées et consultées régulièrement tout au long de leur durée de vie, les instantanés sont souvent utilisés pour suivre les versions antérieures des données. Vous pouvez créer une stratégie qui supprime les anciens instantanés selon leur ancienneté. L’âge de l’instantané est déterminé en regardant l’heure de création. Cette règle de stratégie supprime les instantanés d’objets blob de blocs au sein du conteneur `activedata` dont la création remonte à plus de 90 jours.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "snapshotRule",
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
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>FAQ - J’ai créé une stratégie, pourquoi les actions ne sont pas exécutées immédiatement ? 

La plateforme exécute la stratégie de cycle de vie une fois par jour. Une fois que vous avez défini une nouvelle stratégie, le démarrage et l’exécution de certaines actions (telles que la hiérarchisation et la suppression) peuvent prendre jusqu’à 24 heures.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment récupérer des données après une suppression accidentelle :

- [Suppression réversible pour objets BLOB Azure Storage](../blobs/storage-blob-soft-delete.md)
