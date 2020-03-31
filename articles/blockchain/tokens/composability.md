---
title: Composabilité Azure Blockchain Tokens
description: La composabilité Azure Blockchain Tokens offre la flexibilité de créer des jetons pour des scénarios avancés.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325118"
---
# <a name="azure-blockchain-tokens-composability"></a>Composabilité Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

La composabilité des jetons offre la flexibilité de créer des jetons pour des scénarios avancés. Vous pouvez avoir un scénario complexe qui ne peut pas être implémenté au moyen des [quatre modèles de jetons prédéfinis](templates.md#base-token-types). La composabilité des jetons vous permet de concevoir vos propres modèles de jetons en ajoutant ou en supprimant des comportements définis pour créer votre propre modèle de jeton. Lors de la création d’un modèle de jeton, Azure Blockchain Tokens vérifie toutes les règles de grammaire du jeton. Les modèles composés sont enregistrés dans le service Azure Blockchain Tokens pour une émission sur les réseaux blockchain connectés.

Vous pouvez utiliser les [comportements de jeton](templates.md#token-behaviors) dans les sections suivantes pour concevoir votre modèle de jeton.

## <a name="burnable-b"></a>Consommable (b)

Possibilité de supprimer les jetons du provisionnement.

Par exemple, lorsque vous échangez des points de carte de crédit en ligne contre une carte cadeau, les points de carte de crédit sont consommés.

## <a name="delegable-g"></a>Délégable (g)

Possibilité de déléguer les actions effectuées sur le jeton dont vous êtes propriétaire.

Le délégué peut accomplir des actions en tant que propriétaire du jeton. Par exemple, vous pouvez utiliser un jeton délégable pour participer à un vote. Un jeton délégable permet au propriétaire du jeton de vote de désigner une personne qui vote en son nom.

## <a name="logable-l"></a>Journalisable (l)

Possibilité de journaliser.

Par exemple, vous pouvez émettre un jeton journalisable pour une distribution de film, dans chaque salle de cinéma projetant un film spécifique. Pour que le film soit visionné, la projection doit consigner une transaction à chaque séance, car les paiements de redevance se font à la séance, lors de la sortie du film. Les acteurs du film peuvent utiliser les jetons de film pour valider les paiements par film projeté à chaque séance, dans chaque cinéma de cette distribution.

## <a name="mint-able-m"></a>Forgeable (m)

Possibilité de forger des jetons supplémentaires pour la classe des jetons. Le rôle de forgeur comprend le comportement de pouvoir fabriquer.

Par exemple, une entreprise de vente au détail qui souhaite implémenter un programme de fidélité peut utiliser des jetons forgeables pour son programme de fidélisation. Elle peut forger des points de fidélité supplémentaires pour ses clients au fur et à mesure que sa clientèle se développe.  

## <a name="non-subdividable-or-whole-d"></a>Non subdivisible ou entier (~ d)

Restriction pour empêcher un jeton d’être divisé en plus petites parties.

Par exemple, un tableau de peinture ne peut pas être divisé en plusieurs parties plus petites. 

## <a name="non-transferable-t"></a>Non transmissible (~ t)

Restriction pour empêcher un changement de propriété à partir du premier propriétaire du jeton.

Par exemple, un diplôme universitaire est un jeton non transmissible. Une fois qu’un diplôme est décerné à un diplômé, il ne peut pas être transmis par le diplômé à une autre personne.

## <a name="roles-r"></a>Rôles (r)

Possibilité de définir des rôles dans la classe des modèles de jeton pour des comportements spécifiques.

Vous pouvez fournir une liste de noms de rôles à prendre en charge par un jeton au moment de la création des jetons. Quand des rôles sont précisés, l’utilisateur peut attribuer des rôles à ces comportements. Actuellement, seul le rôle de forgeur est pris en charge.

## <a name="singleton-s"></a>Singleton (s)

Restriction autorisant le provisionnement d’un jeton.

Par exemple, un objet de musée est un jeton singleton. Les artéfacts de musée sont uniques. Un jeton représentant un artéfact ne possède qu’un seul élément dans le provisionnement.

## <a name="subdividable-d"></a>Subdivisible (d)

Possibilité de diviser un jeton en plusieurs parties plus petites.

Par exemple, un dollar peut être subdivisé en cents.

## <a name="transferable-t"></a>Transmissible (t)

Possibilité de transmettre la propriété du jeton.

Par exemple, un titre de propriété est un jeton transférable, qui peut être transmis d’une personne à une autre lorsque la propriété est vendue.

## <a name="next-steps"></a>Étapes suivantes

Découvrez la [gestion des comptes Azure Blockchain Tokens](account-management.md).
