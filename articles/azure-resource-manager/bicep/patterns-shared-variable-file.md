---
title: Modèle de fichier de variables partagées
description: Décrit le modèle de fichier de variables partagées.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 812cb0b861418d3bd3d13959cf074442d9a81538
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535331"
---
# <a name="shared-variable-file-pattern"></a>Modèle de fichier de variables partagées

Réduisez la répétition des valeurs partagées dans vos fichiers Bicep. Au lieu de cela, chargez ces valeurs à partir d’un fichier JSON partagé dans votre fichier Bicep. Lorsque vous utilisez des tableaux, concaténez les valeurs partagées avec des valeurs spécifiques au déploiement dans votre code Bicep.

## <a name="context-and-problem"></a>Contexte et problème

Lorsque vous écrivez votre code Bicep, vous pouvez avoir des variables communes que vous réutilisez dans un ensemble de fichiers Bicep. Vous pouvez dupliquer les valeurs chaque fois que vous déclarez la ressource, par exemple en copiant et en collant les valeurs entre vos fichiers Bicep. Toutefois, cette approche est sujette aux erreurs et, lorsque vous devez apporter des modifications, vous devez mettre à jour chaque définition de ressource pour qu’elle soit synchronisée avec les autres.

En outre, lorsque vous utilisez des variables définies en tant que tableaux, vous pouvez avoir un ensemble de valeurs communes dans plusieurs fichiers Bicep et également avoir besoin d’ajouter des valeurs spécifiques pour la ressource que vous déployez. Lorsque vous combinez les variables partagées avec les variables spécifiques aux ressources, il est plus difficile pour une personne de percevoir la différence entre les deux catégories de variables.

## <a name="solution"></a>Solution

Créez un fichier JSON qui contient les variables que vous devez partager. Utilisez les fonctions Bicep `json()` et `loadTextContent()` pour charger le fichier et accéder aux variables. Pour les variables de tableau, utilisez la fonction `concat()`pour combiner les valeurs partagées avec toutes les valeurs personnalisées pour la ressource spécifique.

## <a name="example-1-naming-prefixes"></a>Exemple 1 : préfixes d’attribution de noms

Supposons que vous ayez plusieurs fichiers Bicep qui définissent des ressources. Vous devez utiliser un préfixe d’attribution de noms cohérent pour toutes vos ressources.

Définissez un fichier JSON qui comprend les préfixes d’attribution de noms communs qui s’appliquent à l’ensemble de votre entreprise :

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

Dans votre fichier Bicep, déclarez une variable qui importe les préfixes d’attribution de noms partagés :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

Lorsque vous définissez vos noms de ressources, utilisez l’interpolation de chaîne pour concaténer les préfixes de noms partagés avec des suffixes de noms uniques :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>Exemple 2 : Règles de groupe de sécurité réseau

Supposons que vous ayez plusieurs fichiers Bicep qui définissent leurs propres groupes de sécurité réseau (NSG). Vous avez un ensemble commun de règles de sécurité qui doivent être appliquées à chaque NSG, puis des règles spécifiques à l’application qui doivent être ajoutées.

Définissez un fichier JSON qui comprend les règles spécifiques à l’application qui s’appliquent à l’ensemble de votre entreprise :

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

Dans votre fichier Bicep, déclarez une variable qui importe les règles spécifiques à l’application :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

Créez un tableau de variables qui représente les règles personnalisées pour ce groupe de sécurité réseau spécifique :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

Définissez la ressource NSG. Utilisez la fonction `concat()` pour combiner les deux tableaux et définir la `securityRules` propriété :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>Considérations

- Lorsque vous utilisez cette approche, le fichier JSON est inclus dans le modèle ARM généré par Bicep. Les modèles ARM JSON générés par Bicep ont une limite de fichiers de 4 Mo. Il est donc important d’éviter d’utiliser des fichiers de variables partagées volumineux.
- Vérifiez que vos tableaux de variables partagées ne sont pas en conflit avec les valeurs de tableau spécifiées dans chaque fichier Bicep. Par exemple, lorsque vous utilisez le modèle de jeu de configuration pour définir des groupes de sécurité réseau, assurez-vous que vous n’avez pas plusieurs règles qui définissent les mêmes priorités et direction.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le modèle de jeu de configuration.](patterns-configuration-set.md)
