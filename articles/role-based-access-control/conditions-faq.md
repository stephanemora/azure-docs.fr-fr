---
title: FAQ sur les conditions d’attribution de rôle Azure (préversion)
description: FAQ sur les conditions d’attribution de rôle Azure (préversion)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: 93eb1afb061182105766ce4d7c864601883ec0cc
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489667"
---
# <a name="faq-for-azure-role-assignment-conditions-preview"></a>FAQ sur les conditions d’attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="frequently-asked-questions"></a>Forum aux questions

**Pouvez-vous choisir le nom du conteneur de stockage ou le chemin d’accès de l’objet blob dans le générateur de conditions du Portail Azure ?**

Vous devez écrire le nom du conteneur de stockage, le chemin d’accès de l’objet blob, le nom de la balise ou les valeurs dans la condition. Il n’existe aucune possibilité de sélection pour les valeurs d’attribut.

**Est-il possible de regrouper des expressions ?**

Si vous ajoutez au moins trois expressions pour une action ciblée, vous devez définir le regroupement logique de ces expressions dans l’éditeur de code, Azure PowerShell ou Azure CLI. Le regroupement logique de `a AND b OR c` peut avoir la valeur `(a AND b) OR c` ou `a AND (b OR c )` .

**Est-il possible d’ajouter des conditions d’attribution de rôle dans l’étendue du groupe d’administration ?**
  
Le portail Azure ne vous permet pas de modifier ou d’afficher les conditions dans l’étendue du groupe d’administration. La colonne **Condition** n’est pas affichée pour l’étendue du groupe d’administration. Azure PowerShell et Azure CLI vous permettent d’ajouter des conditions au niveau de l’étendue du groupe d’administration.

**Les conditions sont-elles prises en charge via Azure AD Privileged Identity Management pour les ressources Azure en préversion ?**

Non.

**Les conditions sont-elles prises en charge pour les administrateurs classiques ?**

Non. 

**Est-il possible d’ajouter des conditions aux attributions de rôles personnalisées ?**

Oui, tant que le rôle personnalisé comprend des [actions qui prennent en charge les conditions](conditions-format.md#actions).
 
**Les conditions augmentent-elles la latence pour l’accès aux objets blob de stockage ?**

Non, d’après nos tests d’évaluation, les conditions ne sont pas censées ajouter de latence perceptible par l’utilisateur.

**Quelles sont les nouvelles propriétés qi ont été ajoutées au schéma d’attribution de rôle pour prendre en charge les conditions ?**

Les nouvelles propriétés de la condition sont les suivantes :

- `condition` : instruction de condition créée à l’aide d’une ou de plusieurs actions à partir de la définition du rôle et des attributs.
- `conditionVersion` : un numéro de version de la condition. La valeur par défaut est 2.0 et il s’agit de la seule version prise en charge publiquement.

Il existe également une nouvelle propriété de description pour les attributions de rôles :

- `description` : description de l’attribution de rôle qui peut être utilisée pour décrire la condition.

**Une condition est-elle appliquée à l’ensemble de l’attribution de rôle ou à certaines actions ?**

Les conditions s’appliquent uniquement aux actions ciblées.

**Quelles sont les limites d’une condition ?**

La longueur de la condition peut aller jusqu’à 8 Ko.

**Quelles sont les limites d’une description ?**

La longueur de la description peut aller jusqu’à 2 Ko.

**Est-il possible de créer une attribution de rôle avec et sans condition, mais en utilisant le même tuple de sécurité principal, de définition de rôle et d’étendue ?**

Non, si vous tentez de créer cette attribution de rôle, une erreur s’affiche.

**Les conditions dans les attributions de rôles offrent-elles un effet de refus explicite ?**

Non, les conditions dans les attributions de rôles n’ont pas d’effet de refus explicite. Les conditions dans les attributions de rôles filtrent l’accès accordé dans une attribution de rôle, ce qui peut entraîner un accès non autorisé. L’effet de refus explicite fait partie des affectation de refus.

## <a name="next-steps"></a>Étapes suivantes

- [Format et syntaxe des conditions d’attribution de rôle Azure (préversion)](conditions-format.md)
- [Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
