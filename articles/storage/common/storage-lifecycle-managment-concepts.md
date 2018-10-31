---
title: Gérer le cycle de vie du stockage Azure
description: Découvrez comment créer des règles de stratégie du cycle de vie pour faire passer les données vieillissantes du niveau de stockage chaud à froid et aux niveaux d’archivage.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 05e7a7e3c2824a9b47ff723e91103611871d7ed2
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429556"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gérer le cycle de vie du stockage d'objets blob Azure (préversion)

Les jeux de données ont des cycles de vie différents. Certaines données sont souvent utilisées principalement en début de cycle et le besoin de pouvoir y accéder diminue considérablement avec le temps. Certaines données restent inactives dans le cloud et sont rarement utilisées une fois stockées. Certaines expirent plusieurs jours ou mois après leur création, tandis que d’autres jeux de données sont lus et modifiés de manière active tout au long de leur vie. La gestion de cycle de vie du stockage d’objets blob Azure (préversion) offre une stratégie riche basée sur des règles que vous pouvez utiliser pour faire passer vos données au niveau d’accès le plus adapté et faire expirer les données à la fin de leur cycle de vie.

La stratégie de gestion du cycle de vie vous aide à :

- Faire passer les objets blob à un niveau de stockage plus froid (de Chaud à Froid, de Chaud à Archive ou de Froid à Archive) pour optimiser les performances et le coût
- Supprimer les objets blob à la fin de leur cycle de vie
- Définir des règles à exécuter une fois par jour au niveau du compte de stockage (il prend à la fois en charge les comptes de stockage GPv2 et Blob)
- Appliquer des règles aux conteneurs ou à un sous-ensemble d’objets blob (en utilisant des préfixes comme filtres)

Imaginons un ensemble de données fréquemment utilisé au début de son cycle de vie, utilisé de manière occasionnelle au bout de deux semaines et rarement utilisé au-delà d’un mois. Dans ce scénario, le stockage chaud est préférable au cours des premiers jours, le stockage froid est plus adapté à un accès occasionnel et l’archivage est la meilleure option pour les données datant de plus d’un mois. En ajustant les niveaux de stockage en fonction de l’ancienneté des données, vous pouvez concevoir les options de stockage les moins coûteuses par rapport à vos besoins. Pour effectuer cette transition, les stratégies de gestion du cycle de vie permettent de déplacer les données vieillissantes vers des niveaux plus froids.

## <a name="storage-account-support"></a>Prise en charge du compte de stockage

La stratégie de gestion du cycle de vie est disponible avec le compte à usage général v2 (GPv2) et le compte de stockage d’objets blob. Vous pouvez convertir un compte à usage général (GPv1) existant en un compte GPv2 d’un simple clic dans le portail Azure. Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Tarifs 

La fonctionnalité de gestion du cycle de vie est gratuite dans la version préliminaire. Les clients sont facturés le coût de fonctionnement normal pour les appels d’API [Répertorier les objets blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) et [Définir le niveau d’objet blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier). Pour en savoir plus sur la tarification, consultez [Tarification Objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>S’inscrire pour la préversion 
Pour vous inscrire à la version préliminaire publique, vous devez envoyer une demande afin d’enregistrer cette fonctionnalité dans votre abonnement. Une fois votre demande approuvée (au bout de quelques jours), cette fonctionnalité est activée pour tout compte GPv2 ou Stockage Blob existant ou nouveau dans la région USA Ouest 2, USA Centre-Ouest, USA Est 2 et Europe Ouest. Lors de la version préliminaire, seul l’objet blob de blocs est pris en charge. Comme pour la plupart des versions préliminaires, cette fonctionnalité ne doit pas être utilisée pour les charges de production avant d’avoir atteint la disponibilité générale.

Pour soumettre une requête, exécutez les commandes PowerShell ou CLI suivantes.

### <a name="powershell"></a>PowerShell

Pour soumettre une requête :

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Vous pouvez vérifier l’état de l'approbation de l’inscription à l’aide de la commande suivante :
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Si la fonctionnalité est approuvée et correctement inscrite, vous devriez recevoir l’état « Inscrit ».

### <a name="azure-cli"></a>Azure CLI

Pour soumettre une requête : 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Vous pouvez vérifier l’état de l'approbation de l’inscription à l’aide de la commande suivante :
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Si la fonctionnalité est approuvée et correctement inscrite, vous devriez recevoir l’état « Inscrit ». 


## <a name="add-or-remove-policies"></a>Ajouter ou supprimer des stratégies 

Vous pouvez ajouter, modifier ou supprimer une stratégie à l’aide du Portail Azure, de [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), des [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/managementpolicies_createorupdate) ou des outils clients dans les langages suivants : [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Pour accéder à votre compte de stockage, sélectionnez Toutes les ressources, puis sélectionnez votre compte de stockage.

3. Dans le panneau Paramètres, cliquez sur **Gestion du cycle de vie** regroupé sous le service blob pour afficher et/ou modifier des stratégies.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Si vous activez les règles de pare-feu de votre compte de stockage, les requêtes de gestion du cycle de vie peuvent être bloquées. Vous pouvez débloquer le système en indiquant des exceptions. Pour plus d’informations, consultez la section Exceptions dans [Configurer des pare-feu et des réseaux virtuels](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Stratégies

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


Dans une stratégie, deux paramètres sont requis :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| version        | Une chaîne exprimée sous la forme `x.x` | Le numéro de la version préliminaire est 0,5. |
| règles          | Un ensemble d’objets de règle | Au moins une règle est requise dans chaque stratégie. Lors de la version préliminaire, vous pouvez spécifier jusqu’à 4 règles stratégie. |

Les paramètres nécessaires au sein d’une règle sont :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| NOM           | Chaîne | Un nom de règle peut contenir n’importe quelle combinaison de caractères alphanumériques. Les noms de règle respectent la casse. Ils doivent être uniques dans la stratégie. |
| Type           | Une valeur enum | La valeur valide pour la version préliminaire est `Lifecycle` |
| Définition     | Un objet qui définit la règle du cycle de vie | Chaque définition se compose d’un jeu de filtres et d’un jeu d’actions. |

## <a name="rules"></a>Règles

Chaque définition de règle se compose d’un jeu de filtres et d’un jeu d’actions. L’exemple de règle suivant modifie le niveau des objets blob de bloc de base avec le préfixe `container1/foo`. Dans la stratégie, ces règles sont définies en tant que :

- Niveau objet blob sur stockage froid 30 jours après la dernière modification
- Niveau objet blob sur stockage archive 90 jours après la dernière modification
- Supprimer l’objet blob 2 555 jours (7 ans) après la dernière modification
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

Les filtres limitent les actions des règles à un sous-ensemble d’objets blob dans le compte de stockage. Si plusieurs filtres sont définis, un `AND` logique est effectué sur tous les filtres.

Lors de la version d’évaluation, les filtres valides sont les suivants :

| Nom du filtre | Type de filtre | Notes | Est obligatoire |
|-------------|-------------|-------|-------------|
| blobTypes   | Un ensemble de valeurs enum prédéfinies. | Pour la version préliminaire, seul `blockBlob` est pris en charge. | Oui |
| prefixMatch | Un ensemble de chaînes servant à faire correspondre les préfixes. Une chaîne de préfixe doit commencer par un nom de conteneur. Par exemple, si une règle doit s’appliquer à tous les blobs figurant sous « https://myaccount.blob.core.windows.net/mycontainer/mydir/... », le préfixe est « mycontainer/mydir ». | Si elle n’a pas été définie, cette règle s’applique à tous les objets blob dans le compte. | Non  |

### <a name="rule-actions"></a>Actions de règle

Des actions sont appliquées aux objets blob filtrés lorsque la condition d’exécution est remplie.

Dans la version préliminaire, la gestion du cycle de vie prend en charge la hiérarchisation et la suppression des objets blob ainsi que la suppression des instantanés d’objet blob. Chaque règle doit avoir au moins une action définie sur les objets blob ou les instantanés d’objet blob.

| Action        | Objet blob de base                                   | Instantané      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Prend actuellement en charge les objets blob au niveau Chaud         | Non pris en charge |
| tierToArchive | Prend actuellement en charge les objets blob au niveau Chaud ou Froid | Non pris en charge |
| delete        | Pris en charge                                   | Pris en charge     |

>[!NOTE] 
Si plusieurs actions sont définies sur le même objet blob, la gestion du cycle de vie applique l’action la moins coûteuse à l’objet blob (par exemple, l’action `delete` est moins chère que l’action `tierToArchive` et l’action `tierToArchive` est moins chère que l’action `tierToCool`).

Dans la version préliminaire, les conditions d’exécution des actions sont basées sur l’âge. L’objet blob de base utilise l’heure de dernière modification pour suivre l’âge tandis que les instantanés d’objet blob utilisent l’heure de création des instantanés.

| Condition d’exécution de l’action | Valeur de la condition | Description |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Nombre entier indiquant l’âge en jours | Condition valide pour les actions des objets blob de base |
| daysAfterCreationGreaterThan     | Nombre entier indiquant l’âge en jours | Condition valide pour les actions des instantanés d’objet blob | 

## <a name="examples"></a>Exemples
Les exemples suivants expliquent comment résoudre des scénarios courants avec les règles de stratégie du cycle de vie.

### <a name="move-aging-data-to-a-cooler-tier"></a>Déplacer les données vieillissantes vers un niveau plus froid

L’exemple suivant montre comment déplacer des objets blob de blocs ayant le préfixe `container1/foo` ou `container2/bar`. La stratégie déplace les objets blob qui n’ont pas été modifiés depuis plus de 30 jours vers le stockage froid et les objets blob non modifiés depuis 90 jours vers le niveau archive :

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées. Il vaut mieux dans ce cas archiver ces données immédiatement dès leur réception. La stratégie du cycle de vie suivante est configurée pour archiver des données à la réception. Cet exemple déplace immédiatement les objets blob de blocs du compte de stockage au sein du conteneur `archivecontainer` dans un niveau archive. Le déplacement immédiat est accompli en agissant sur les objets blob 0 jours après l’heure de dernière modification :

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Certaines données sont conçues pour expirer plusieurs jours ou mois après leur création afin de réduire les coûts ou de se conformer aux réglementations gouvernementales. Une stratégie de gestion du cycle de vie peut être configurée de telle sorte à faire expirer les données d’épuisement selon l’ancienneté des données. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc (sans aucun préfixe spécifié) antérieurs à 365 jours.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
      "definition": 
        {
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
## <a name="faq"></a>Forum Aux Questions
### <a name="i-created-a-new-policy-why-are-the-actions-specified-not-executed-immediately"></a>J’ai créé une stratégie. Pourquoi les actions spécifiées ne sont-elles pas exécutées immédiatement ? 

La stratégie de cycle de vie est exécutée une fois par jour par la plateforme. Une fois qu’une nouvelle stratégie est définie, l’initiation et l’exécution des actions telles que la hiérarchisation ou la suppression peuvent prendre 24 heures.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment récupérer des données après une suppression accidentelle :

- [Suppression réversible pour objets blob de stockage Azure ](../blobs/storage-blob-soft-delete.md)
