---
title: Gestion des fonctionnalités Azure App Configuration | Microsoft Docs
description: Vue d’ensemble de la façon dont Azure App Configuration peut être utilisé pour activer et désactiver des fonctionnalités d’application à la demande
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413763"
---
# <a name="feature-management"></a>Gestion des fonctionnalités

Généralement, fournir une nouvelle fonctionnalité d’application nécessite un redéploiement complet de l’application elle-même. Pour tester une fonctionnalité, vous devrez probablement déployer votre application plusieurs fois pour contrôler à quel moment elle devient visible ou qui peut la voir. La gestion des fonctionnalités est une pratique de développement de logiciels moderne qui dissocie la publication des fonctionnalités et le déploiement du code. De plus, elle permet de changer rapidement la disponibilité des fonctionnalités à la demande. Elle utilise une technique nommée *indicateur de fonctionnalité* (également appelée *bascule de fonctionnalité* ou *commutateur de fonctionnalité*, entre autres) pour gérer dynamiquement le cycle de vie d’une fonctionnalité. Elle permet aux développeurs de traiter les problèmes suivants :

* **Gestion des branches de code** : Les indicateurs de fonctionnalités sont souvent utilisés pour wrapper les nouvelles fonctionnalités d’application qui sont en cours de développement. Ces fonctionnalités sont « masquées » par défaut. Bien qu’elles ne soient pas terminées, vous pouvez les fournir sans problème. Elles restent dormantes en production. À l’aide de cette approche, appelée *déploiement masqué (dark)*, vous pouvez fournir l’ensemble de votre code à la fin de chaque cycle de développement. Vous n’avez plus besoin d’assurer la maintenance de branches de code entre plusieurs cycles en raison d’une fonctionnalité dont l’exécution prend plus d’une période.
* **Test en production** : Vous pouvez utiliser des indicateurs de fonctionnalités pour accorder un accès en avant-première aux nouvelles fonctionnalités en production. Par exemple, vous pouvez autoriser cet accès uniquement aux membres de votre équipe ou à des bêta testeurs internes. Ces utilisateurs obtiendront l’expérience de production de fidélité optimale au lieu d’une expérience simulée ou partielle dans un environnement de test.
* **Distribution de version d’évaluation** : Vous pouvez également utiliser des indicateurs de fonctionnalités pour déployer de façon incrémentielle de nouvelles fonctionnalités pour les utilisateurs finaux. Vous pouvez d’abord cibler un petit pourcentage de votre population d’utilisateurs, puis augmenter progressivement ce pourcentage au fil du temps, une fois que vous avez acquis plus de confiance dans l’implémentation.
* **Interrupteur instantané** : Les indicateurs de fonctionnalités offrent un dispositif de sécurité inhérent pour la publication de nouvelles fonctionnalités. Ils vous permettent non seulement d’activer, mais également d’arrêter facilement des fonctionnalités d’application. Si nécessaire, vous pouvez désactiver rapidement une fonctionnalité sans avoir à recréer et à redéployer votre application.
* **Activation sélective** : Alors que la plupart des indicateurs de fonctionnalités existent uniquement jusqu’à la publication réussie de leurs fonctionnalités associées, certaines peuvent durer longtemps. Vous pouvez les utiliser comme moyen de segmenter vos utilisateurs et de fournir un ensemble spécifique de fonctionnalités à chaque groupe. Par exemple, vous pouvez avoir une fonctionnalité qui fonctionne sur un navigateur web donné. Vous pouvez définir un indicateur de fonctionnalité de sorte que seuls les utilisateurs de ce navigateur puissent voir et utiliser la fonctionnalité. L’avantage de cette approche est que vous pouvez facilement étendre par la suite la liste des navigateurs pris en charge sans avoir à changer le code.

## <a name="basic-concepts"></a>Concepts de base

Voici plusieurs nouveaux termes liés à la gestion des fonctionnalités.

* **Indicateur de fonctionnalités** : Un indicateur de fonctionnalité est une variable dont l’état binaire est *on* ou *off* et à laquelle un bloc de code est associé. Son état détermine si le bloc de code s’exécute ou non.
* **Gestionnaire de fonctionnalités** : Un gestionnaire de fonctionnalités est un package d’application qui gère le cycle de vie de tous les indicateurs de fonctionnalités dans une application. En général, il fournit des fonctionnalités supplémentaires comme la mise en cache d’indicateurs et la mise à jour de leur état.
* **Filtre** : Un filtre est une règle destinée à évaluer l’état d’un indicateur de fonctionnalité. Un groupe d’utilisateurs, un type d’appareil ou de navigateur, un emplacement géographique et une fenêtre de temps sont des exemples de ce qu’un filtre peut représenter.

Une implémentation efficace de la gestion des fonctionnalités se compose d’au moins deux composants qui fonctionnent conjointement : une application qui utilise des indicateurs de fonctionnalités et un référentiel distinct qui stocke les indicateurs de fonctionnalités ainsi que leur état actuel. Leurs interactions sont illustrées ci-dessous.

## <a name="feature-flag-usage-in-code"></a>Utilisation d’indicateurs de fonctionnalités dans du code

Le modèle de base pour l’implémentation d’indicateurs de fonctionnalités dans une application est simple. Vous pouvez le conceptualiser comme une variable d’état booléenne utilisée avec une instruction conditionnelle `if` dans votre code.

```csharp
if (featureFlag) {
    // Run the following code
}
```

Dans le cas présent, si `featureFlag` est défini sur `True`, le bloc de code délimité par des accolades est exécuté ; sinon, il est ignoré. La valeur de `featureFlag` peut être définie de manière statique :

```csharp
bool featureFlag = true;
```

Elle peut également être évaluée en fonction de certaines règles :

```csharp
bool featureFlag = isBetaUser();
```

Un modèle d’indicateur de fonctionnalité un peu plus complexe inclut également une instruction `else`.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Il peut toutefois être réécrit dans le modèle de base. La rubrique [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md) montre l’avantage de standardiser un modèle de code simple.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Référentiel d’indicateurs de fonctionnalités

Pour utiliser efficacement tous les indicateurs de fonctionnalités utilisés dans une application, vous devez les externaliser. Cela vous permettra de changer l’état des indicateurs de fonctionnalités sans modifier et redéployer l’application elle-même. Azure App Configuration est conçu pour être un référentiel centralisé pour les indicateurs de fonctionnalités. Vous pouvez l’utiliser pour définir différents types d’indicateurs de fonctionnalités et manipuler leur état de façon rapide et sûre. Vous pouvez ensuite accéder facilement à ces indicateurs de fonctionnalités à partir de votre application en utilisant les bibliothèques App Configuration pour différents frameworks de langage de programmation. La rubrique [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md) montre comment le fournisseur App Configuration .NET Core et les bibliothèques de gestion des fonctionnalités sont utilisés ensemble comme solution complète pour implémenter des indicateurs de fonctionnalités pour votre application web ASP.NET.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des indicateurs de fonctionnalités dans une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
