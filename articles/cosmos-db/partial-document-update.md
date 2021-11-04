---
title: Mise à jour partielle de document dans Azure Cosmos DB
description: Découvrez la Mise à jour partielle de document dans Azure Cosmos DB.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7f5925ffa70977d13a2eefb25dc48898ac513f6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434082"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Mise à jour partielle de document dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La fonctionnalité Mise à jour partielle de document Azure Cosmos DB (également appelée API Patch) offre un moyen pratique de modifier un document dans un conteneur. Actuellement, pour mettre à jour un document dont le client a besoin pour le lire, il faut exécuter des vérifications de contrôle d’accès concurrentiel optimiste (si nécessaire), mettre à jour le document localement, puis l’envoyer en tant qu’appel d’API de remplacement de document entier. 

La fonctionnalité de mise à jour partielle de document améliore cette expérience de manière significative. Le client peut juste envoyer les propriétés/champs modifiés dans un document sans effectuer une opération de remplacement de document entier. Les principaux avantages de cette fonctionnalité sont les suivants :

- **Amélioration de la productivité des développeurs** : fournit une API pratique pour faciliter l’utilisation et pouvoir mettre à jour le document de manière conditionnelle. 
- **Améliorations des performances** : évite des cycles de processeur supplémentaires côté client et réduit la latence et la bande passante réseau de bout en bout.
- **Écritures multirégions** : prend en charge la résolution automatique et transparente des conflits avec des mises à jour partielles sur des chemins discrets dans le même document.
 
## <a name="supported-operations"></a>Opérations prises en charge

Le tableau ci-dessous récapitule les opérations prises en charge par cette fonctionnalité.

> [!NOTE]
> Le *chemin cible* fait référence à un emplacement dans le document JSON.

| **Type d’opération** | **Description** |
| ------------ | -------- |
| **Add**      | `Add` effectue l’une des actions suivantes, en fonction du chemin cible : <br/><ul><li>Si le chemin cible spécifie un élément qui n’existe pas, celui-ci est ajouté.</li><li>Si le chemin cible spécifie un élément qui existe déjà, sa valeur est remplacée.</li><li>Si le chemin cible est un index de tableau valide, un nouvel élément est inséré dans le tableau à l’index spécifié. Il déplace les éléments existants vers la droite.</li><li>Si l’index spécifié est égal à la longueur du tableau, il ajoute un élément au tableau. Au lieu de spécifier un index, vous pouvez également utiliser le caractère `-`. Cela entraîne également l’ajout de l’élément au tableau.</li></ul> <br/> **Remarque** : Si vous spécifiez un index plus grand que la longueur du tableau, une erreur se produit.|
| **Set**      | L’opération `Set` est similaire à `Add`, sauf dans le cas du type de données de tableau - si le chemin cible est un index de tableau valide, l’élément existant à cet index est mis à jour.| 
| **Replace**      | L’opération `Replace` est similaire à `Set`, sauf qu’elle suit une sémantique de remplacement seul _stricte_. Si le chemin cible spécifie un élément ou un tableau qui n’existe pas, une erreur se produit.  | 
| **Remove**     | `Remove` effectue l’une des actions suivantes, en fonction du chemin cible : <br/><ul><li>Si le chemin cible spécifie un élément qui n’existe pas, une erreur se produit. </li><li> Si le chemin cible spécifie un élément qui existe déjà, celui-ci est supprimé. </li><li> Si le chemin cible est un index de tableau, celui-ci est supprimé et tous les éléments situés au-dessus de l’index spécifié sont décalés d’une position vers la gauche.</li></ul> <br/> **Remarque** : Si vous spécifiez un index supérieur ou égal à la longueur du tableau, une erreur se produit.  |
| **Incrément**     | Cet opérateur incrémente un champ de la valeur spécifiée. Il peut accepter des valeurs à la fois positives et négatives. Si le champ n’existe pas, il crée le champ et lui affecte la valeur spécifiée. |

## <a name="supported-modes"></a>Modes pris en charge

La fonctionnalité de mise à jour partielle de document prend en charge les modes de fonctionnement suivants. Consultez le document [Bien démarrer](partial-document-update-getting-started.md) pour obtenir des exemples de code.

* **Patch mono-document** : Vous pouvez appliquer un patch à un seul document en fonction de son ID et de la clé de partition. Il est possible d’exécuter plusieurs opérations de patch sur un seul document. La [limite maximale est de 10 opérations](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-).

* **Patch multi-document** : Vous pouvez appliquer un patch à plusieurs documents dans la même clé de partition dans le [cadre d’une transaction](transactional-batch.md). Cette transaction est validée seulement si toutes les opérations réussissent dans l’ordre dans lequel elles sont décrites. Si une opération échoue, la totalité de la transaction est restaurée.

* **Mise à jour conditionnelle** : Pour les modes susmentionnés, il est également possible d’ajouter un prédicat de filtre de type SQL (par exemple, *de c. où c.taskNum = 3*) de sorte que l’opération échoue si la précondition spécifiée dans le prédicat n’est pas remplie. 

* Vous pouvez également utiliser les API en bloc des kits SDK pris en charge pour exécuter une ou plusieurs opérations de patch sur plusieurs documents.


## <a name="similarities-and-differences"></a>Similitudes et différences

### <a name="add-vs-set"></a>Add vs. Set

L’opération `Set` est similaire à l’opération `Add` pour tous les types de données, sauf `Array`. Une opération `Add` à un index (valide) entraîne l’ajout d’un élément à l’index spécifié et tous les éléments existants dans le tableau sont décalés vers la droite. Cela diffère de l’opération `Set` qui met à jour l’élément existant à l’index spécifié. 

### <a name="add-vs-replace"></a>Add ou Replace

L’opération `Add` ajoute une propriété si celle-ci n’existe pas déjà (y compris le type de données `Array`). L’opération `Replace` échoue si la propriété n’existe pas (s’applique également au type de données `Array`).

### <a name="set-vs-replace"></a>Set vs. Replace

L’opération `Set` ajoute une propriété si celle-ci n’existe pas déjà (sauf s’il y avait un `Array`). L’opération `Replace` échoue si la propriété n’existe pas (s’applique également au type de données `Array`).

> [!NOTE]
> `Replace` est un bon candidat lorsque l’utilisateur s’attend à ce qu’une partie des propriétés soit toujours présente, et vous permet de le déclarer/l’appliquer.

## <a name="partial-document-update-specification"></a>Spécification de la mise à jour partielle de document

Le composant orienté client de la fonctionnalité de mise à jour partielle de document est implémenté en tant qu’API REST de premier niveau. Voici un exemple de la façon dont une opération `Conditional Add` est modélisée par Azure Cosmos DB :

```bash
PATCH /dbs/{db}/colls/{coll}/documents/{doc}
HTTP/1.1
Content-Type:application/json-patch+json

{
   "condition":"from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))", 
   "operations":[ 
      { 
         "op":"add", 
         "path":"amount", 
         "value":80000 
      }
   ]
} 
```

> [!NOTE]
> Dans ce cas, la valeur du chemin `amount` est définie sur `80000` (l’opération) *seulement* si la valeur de `TotalDue` est 0 ou n’est pas présente (la condition).

## <a name="document-level-vs-path-level-conflict-resolution"></a>Résolution des conflits au niveau du document vs. au niveau du chemin  

Si votre compte Azure Cosmos DB est configuré avec plusieurs régions d’écriture, des [conflits et des stratégies de résolution des conflits](conflict-resolution-policies.md) sont applicables au niveau du document, `LWW` (Last Write Wins) étant la stratégie de résolution des conflits par défaut. Pour les mises à jour partielles de document, les opérations de patch sur plusieurs régions détectent et résolvent les conflits à un niveau de chemin plus précis.

Un exemple permet de mieux comprendre.

Supposons que vous ayez le document suivant dans Azure Cosmos DB :

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold"
} 
```

Les opérations de patch ci-dessous sont émises simultanément par différents clients de différentes régions :

- `Set` attribue `/level` sur platinum  
- `Remove` 67890 de `/phone`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="Image montrant une résolution de conflits dans des opérations simultanées de mise à jour partielle multirégions." border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

Étant donné que les demandes de patch ont été faites sur des chemins non conflictuels dans le document, les conflits de celles-ci sont résolus de manière automatique et transparente (contrairement à la stratégie LWW au niveau du document).    

Le client verra le document suivant après la résolution des conflits :

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345"
   ], 
   "level":"platinum",
} 
```

> [!NOTE]
> Si la même propriété d’un document est corrigée simultanément dans plusieurs régions, les [stratégies de résolution de conflits](conflict-resolution-policies.md) standard s’appliquent.

## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment [démarrer](partial-document-update-getting-started.md) avec la Mise à jour partielle de document en utilisant .NET, Java et Node
- [Questions fréquentes](partial-document-update-faq.yml) sur la Mise à jour partielle de document
