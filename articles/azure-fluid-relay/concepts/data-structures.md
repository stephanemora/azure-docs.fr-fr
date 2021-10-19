---
title: Structures de données distribuées
description: Les structures de données distribuées sont les blocs élémentaires des applications Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/data-structures/overview/
ms.openlocfilehash: c79fea26de7cda6d3097c2a8c9403420451fe665
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661863"
---
# <a name="distributed-data-structures"></a>Structures de données distribuées

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

L’Infrastructure Fluid fournit aux développeurs des structures de données distribuées (DDS) qui vérifient automatiquement que chaque client connecté a accès au même état. Les API fournies par les DDS sont conçues pour être familières aux programmeurs qui ont déjà utilisé des structures de données courantes.

> [!NOTE]
> Cet article suppose que vous êtes familiarisé avec la [présentation des structures de données distribuées](https://fluidframework.com/docs/build/dds/) sur fluidframework.com.

Une structure de données distribuées se comporte comme une structure de données locale. Votre code peut y ajouter des données, supprimer des données, la mettre à jour, etc. Il ne s’agit toutefois pas d’un objet local. Une DDS peut également être modifiée par d’autres clients qui exposent le même conteneur parent de la DDS. Les utilisateurs pouvant modifier simultanément la même DDS, vous devez identifier la DDS à utiliser pour la modélisation de vos données.

> [!NOTE]
> **Signification de « simultanément »**
>
> Plusieurs clients sont considérés comme apportant une modification *simultanément* si chacun d’eux effectue une modification avant d’avoir reçu les modifications des autres depuis le serveur.

Le choix de la structure de données appropriée pour votre scénario peut améliorer les performances et la structure de code de votre application.

Les DDS varient les unes des autres selon les trois caractéristiques suivantes :

- **Structure de données de base** : par exemple, paire clé-valeur, séquence ou file d’attente.
- **Autonomie du client** : une DDS *optimiste* permet à n’importe quel client de modifier unilatéralement une valeur et la nouvelle valeur est relayée à tous les autres clients. Une DDS de *consensus* autorise uniquement une modification si elle est acceptée par les autres clients dans le cadre d’un processus de consensus.
- **Stratégie de fusion** : stratégie qui détermine le mode de résolution des modifications conflictuelles des clients.

Ci-dessous, nous avons énuméré les structures de données et décrit quand elles peuvent s’avérer les plus utiles.

## <a name="key-value-data"></a>Données de clé-valeur

Ces DDS sont utilisées pour stocker les données de clé-valeur. Elles sont optimistes et utilisent une stratégie de type last-writer-wins (dernière version valide). Bien que la valeur d’une paire puisse être un objet complexe, la valeur d’une paire donnée ne peut pas être modifiée directement. La valeur entière doit être remplacée par une nouvelle valeur contenant les modifications souhaitées.

- **SharedMap** : structure de données clé-valeur de base.

### <a name="key-value-scenarios"></a>Scénarios de clé-valeur

Les structures de données clé-valeur sont le choix le plus courant pour de nombreux scénarios.

- Données de préférence de l’utilisateur.
- État actuel d’une enquête.
- Configuration d’une vue.

### <a name="common-issues-and-best-practices-for-key-value-ddses"></a>Problèmes courants et meilleures pratiques pour les DDS de clé-valeur

- Le stockage d’un compteur dans une SharedMap aura un comportement inattendu. Utilisez le SharedCounter à la place.
- Le stockage de tableaux, de listes ou de journaux dans une entrée clé-valeur peut entraîner un comportement inattendu, car les utilisateurs ne peuvent pas modifier collectivement des parties d’une entrée. Essayez de stocker les données de tableau ou de liste dans une SharedSequence ou une SharedInk.
- Le stockage d’une grande quantité de données dans une entrée clé-valeur peut entraîner des problèmes de performances ou de fusion. Chaque mise à jour met à jour la valeur entière au lieu de fusionner deux mises à jour. Essayez de fractionner les données sur plusieurs clés.

## <a name="sequences"></a>Séquences

Ces DDS sont utilisées pour le stockage des données séquentielles. Elles sont optimistes. Les structures de données de séquence sont utiles lorsque vous devez ajouter ou supprimer des données à un index spécifié dans une liste ou un tableau. Contrairement aux structures de données clé-valeur, les séquences ont un ordre séquentiel et peuvent gérer des insertions simultanées de plusieurs utilisateurs.

- **SharedNumberSequence** : séquence de nombres.
- **SharedObjectSequence** : séquence d’objets simples.

### <a name="sequence-scenarios"></a>Scénarios de séquence

- Listes
- Chronologies

### <a name="common-issues-and-best-practices-for-sequence-ddses"></a>Problèmes courants et meilleures pratiques pour les DDS de séquence

- Stockez uniquement les données immuables en tant qu’élément d’une séquence. La seule façon de modifier la valeur d’un élément consiste à le supprimer d’abord de la séquence, puis à insérer une nouvelle valeur à la position de l’ancienne valeur. Toutefois, étant donné que les autres clients peuvent effectuer des insertions et des suppressions, il n’existe aucun moyen fiable d’obtenir la nouvelle valeur à la position souhaitée.

## <a name="strings"></a>Chaînes

La DDS SharedString est utilisée pour les données de texte non structurées qui peuvent être modifiées collectivement. Elle est optimiste.

- `SharedString` : structure de données pour gérer le texte collaboratif.

### <a name="string-scenarios"></a>Scénarios de chaîne

- Éditeurs de texte

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les DDS et leur utilisation, consultez les sections suivantes de fluidframework.com :

- [Présentation des structures de données distribuées](https://fluidframework.com/docs/build/dds/).
- [Types de structures de données distribuées](https://fluidframework.com/docs/data-structures/overview/).
