---
title: Gestion des fonctionnalités Azure App Configuration
description: Vue d’ensemble de la façon dont Azure App Configuration peut être utilisé pour activer et désactiver des fonctionnalités des applications à la demande.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898737"
---
# <a name="feature-management-overview"></a>Vue d’ensemble de la gestion des fonctionnalités

Généralement, fournir une nouvelle fonctionnalité d’application nécessite un redéploiement complet de l’application elle-même. Pour tester une fonctionnalité, vous devez probablement déployer votre application plusieurs fois pour contrôler à quel moment elle devient visible ou qui peut la voir.

La gestion des fonctionnalités est une pratique de développement de logiciels moderne qui dissocie la publication des fonctionnalités et le déploiement du code. De plus, elle permet de changer rapidement la disponibilité des fonctionnalités à la demande. Elle utilise une technique nommée *indicateurs de fonctionnalités* (également appelée *bascules de fonctionnalité* ou *commutateurs de fonctionnalité*, etc.) pour gérer dynamiquement le cycle de vie d’une fonctionnalité.

La gestion des fonctionnalités permet aux développeurs de solutionner les problèmes suivants :

* **Gestion des branches de code** : Les indicateurs de fonctionnalités sont souvent utilisés pour wrapper les nouvelles fonctionnalités d’application qui sont en cours de développement. Ces fonctionnalités sont « masquées » par défaut. Bien qu’elles ne soient pas terminées, vous pouvez les fournir sans problème. Elles restent dormantes en production. Grâce à cette approche, appelée *déploiement masqué (dark)* , vous pouvez publier l’ensemble de votre code à la fin de chaque cycle de développement. Vous n’avez plus besoin d’assurer la maintenance de branches de code sur plusieurs cycles en raison d’une fonctionnalité dont la finalisation nécessite plus qu’un cycle.
* **Test en production** : Vous pouvez utiliser des indicateurs de fonctionnalités pour accorder un accès en avant-première aux nouvelles fonctionnalités en production. Par exemple, vous pouvez limiter cet accès aux seuls membres de votre équipe ou seulement à des bêta-testeurs internes. Ces utilisateurs obtiendront l’expérience de production de fidélité optimale au lieu d’une expérience simulée ou partielle dans un environnement de test.
* **Distribution de version d’évaluation** : Vous pouvez également utiliser des indicateurs de fonctionnalités pour déployer de façon incrémentielle de nouvelles fonctionnalités auprès des utilisateurs finaux. Vous pouvez d’abord cibler un petit pourcentage de votre population d’utilisateurs, puis augmenter progressivement ce pourcentage au fil du temps, une fois que vous avez acquis plus de confiance dans l’implémentation.
* **Interrupteur instantané** : Les indicateurs de fonctionnalités offrent un dispositif de sécurité inhérent pour la publication de nouvelles fonctionnalités. Avec ceux-ci, vous pouvez facilement activer et désactiver les fonctionnalités des applications. Si nécessaire, vous pouvez rapidement désactiver une fonctionnalité sans regénérer et redéployer votre application.
* **Activation sélective** : Alors que la plupart des indicateurs de fonctionnalités existent jusqu’à la publication réussie de leurs fonctionnalités associées, certains peuvent durer longtemps. Vous pouvez les utiliser pour segmenter vos utilisateurs et fournir un ensemble spécifique de fonctionnalités à chaque groupe. Par exemple, vous pouvez avoir une fonctionnalité qui fonctionne seulement sur un navigateur web donné. Vous pouvez définir un indicateur de fonctionnalité de sorte que seuls les utilisateurs de ce navigateur peuvent voir et utiliser la fonctionnalité. Avec cette approche, vous pouvez facilement étendre par la suite la liste des navigateurs pris en charge sans avoir à changer le code.

## <a name="basic-concepts"></a>Concepts de base

Voici plusieurs nouveaux termes liés à la gestion des fonctionnalités :

* **Indicateur de fonctionnalités** : Un indicateur de fonctionnalité est une variable dont l’état binaire est *activé* ou *désactivé*. L’indicateur de fonctionnalité a également un bloc de code associé. L’état de l’indicateur de fonctionnalité détermine si le bloc de code s’exécute ou non.
* **Gestionnaire de fonctionnalités** : Un gestionnaire de fonctionnalités est un package d’application qui gère le cycle de vie de tous les indicateurs de fonctionnalités dans une application. En général, il fournit des fonctionnalités supplémentaires comme la mise en cache d’indicateurs et la mise à jour de leur état.
* **Filtre** : Un filtre est une règle destinée à évaluer l’état d’un indicateur de fonctionnalité. Un groupe d’utilisateurs, un type d’appareil ou de navigateur, un emplacement géographique et une fenêtre de temps sont des exemples de ce qu’un filtre peut représenter.

Une implémentation efficace de la gestion des fonctionnalités est constituée d’au moins deux composants qui fonctionnent de concert :

* Une application qui utilise les indicateurs de fonctionnalités.
* Un référentiel distinct qui stocke les indicateurs de fonctionnalités et leur état actuel.

La façon dont ces composants interagissent est illustrée dans les exemples suivants.

## <a name="feature-flag-usage-in-code"></a>Utilisation d’indicateurs de fonctionnalités dans du code

Le modèle de base pour l’implémentation d’indicateurs de fonctionnalités dans une application est simple. Vous pouvez voir un indicateur de fonctionnalité comme une variable d’état booléenne utilisée avec une instruction conditionnelle `if` dans votre code :

```csharp
if (featureFlag) {
    // Run the following code
}
```

Dans le cas présent, si `featureFlag` est défini sur `True`, le bloc de code délimité est exécuté ; sinon, il est ignoré. Vous pouvez définir la valeur de `featureFlag` de façon statique, comme dans l’exemple de code suivant :

```csharp
bool featureFlag = true;
```

Vous pouvez également évaluer l’état de l’indicateur selon certaines règles :

```csharp
bool featureFlag = isBetaUser();
```

Un modèle d’indicateur de fonctionnalité un peu plus complexe inclut également une instruction `else` :

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Vous pouvez cependant réécrire ce comportement dans le modèle de base. La rubrique [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md) montre les avantages liés à la standardisation d’un modèle de code simple. Par exemple :

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Référentiel d’indicateurs de fonctionnalités

Pour utiliser efficacement les indicateurs de fonctionnalités, vous devez externaliser tous ceux qui sont utilisés dans une application. Cette approche vous permet de changer l’état des indicateurs de fonctionnalités sans modifier et redéployer l’application elle-même.

Azure App Configuration est conçu pour être un référentiel centralisé pour les indicateurs de fonctionnalités. Vous pouvez l’utiliser pour définir différents types d’indicateurs de fonctionnalités et manipuler leur état de façon rapide et sûre. Vous pouvez ensuite utiliser les bibliothèques App Configuration pour différents frameworks de langage de programmation pour accéder facilement à ces indicateurs de fonctionnalités à partir de votre application.

La rubrique [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md) montre comment le fournisseur App Configuration .NET Core et les bibliothèques de gestion des fonctionnalités sont utilisés ensemble pour implémenter des indicateurs de fonctionnalités pour votre application web ASP.NET.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des indicateurs de fonctionnalités dans une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
