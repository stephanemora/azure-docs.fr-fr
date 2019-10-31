---
title: Choisir la plateforme de développement frontal pour la création d’applications clientes avec Visual Studio et les services Azure
description: En savoir plus sur les langages natifs et les multiplateformes pris en charge pour créer des applications clientes.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795085"
---
# <a name="choose-mobile-development-framework"></a>Choisir un framework de développement mobile
Les développeurs peuvent utiliser des technologies côté client pour créer eux-mêmes des applications mobiles, en utilisant des frameworks et des modèles spécifiques pour une approche multiplateforme. Les développeurs peuvent créer des applications natives (natives à plateforme unique à l’aide de langages comme Objective-C et Java), des applications multiplateformes utilisant Xamarin, .NET et C#) et hybrides (à l’aide de Cordova) et leurs variantes, en fonction de leurs facteurs de décision.

## <a name="native-platforms"></a>Plateformes natives
La création d’une application native nécessite des langages de programmation spécifiques à la plateforme, des kits de développement logiciel (SDK), un environnement de développement et d’autres outils fournis par les fournisseurs de systèmes d’exploitation.

### <a name="ios"></a>iOS
Créées et développées par Apple, les applications basées sur iOS s’exécutent sur des appareils Apple, à savoir iPhone et iPad.

- **Langages de programmation** : Objective-C, Swift
- **IDE** : Xcode
- **Kit de développement logiciel (SDK)**  : iOS

### <a name="android"></a>Android
Conçues par Google et par le système d’exploitation le plus populaire du monde, les applications basées sur Android peuvent s’exécuter sur un éventail de smartphones et de tablettes.

- **Langage de programmation** : Java, Kotlin 
- **IDE** : Android Studio et outils de développement Android 
- **Kit de développement logiciel (SDK)**  : Android

### <a name="windows"></a>Windows
- **Langage de programmation** : C#
- **IDE** : Visual Studio, Visual Studio Code
- **Kit de développement logiciel (SDK)**  : Windows

#### <a name="pros"></a>Avantages
- Bonne expérience utilisateur
- Applications réactives haute performance dotées d’une capacité d’interface avec des bibliothèques natives
- Applications hautement sécurisées

#### <a name="cons"></a>Inconvénients
- L’application s’exécute uniquement sur une plateforme
- Plus de ressources pour les développeurs et création d’applications plus coûteuse
- Moins de réutilisation du code

## <a name="cross-platforms-and-hybrid-applications"></a>Applications multiplateformes et hybrides
Les applications multiplateformes vous permettent d’écrire des applications mobiles natives une fois, de partager du code et de les exécuter sur iOS, Android et Windows.

### <a name="xamarin"></a>Xamarin
Détenu par Microsoft, [Xamarin](https://visualstudio.microsoft.com/xamarin/) vous permet de créer des applications mobiles multiplateformes robustes dans C#, avec une bibliothèque de classes et un runtime qui fonctionne sur plusieurs plateformes, notamment iOS, Android et Windows, tout en compilant toujours des applications natives (non interprétées) très performantes. Xamarin combine toutes les capacités des plateformes natives et ajoute un certain nombre de ses fonctionnalités puissantes.

- **Langage de programmation** : C#
- **IDE** : Visual Studio sur Windows ou Mac

### <a name="react-native"></a>React Native
Publié par Facebook en 2015, [React Native](https://facebook.github.io/react-native/) est un framework [open source](https://github.com/facebook/react-native) JavaScript permettant d’écrire des applications mobiles réelles de rendu en mode natif pour iOS et Android. Il est basé sur React, la bibliothèque JavaScript de Facebook pour la création d’interfaces utilisateur, mais au lieu de cibler le navigateur, il cible les plateformes mobiles. React Native utilise des composants natifs au lieu de composants web comme blocs de construction.
 
- **Langage de programmation** : JavaScript
- **IDE** : Visual Studio Code

### <a name="unity"></a>Unity
 Unity est un moteur optimisé pour créer des jeux, qui permet aux développeurs de concevoir des applications 2D/3D C# de haute qualité avec différentes plateformes, notamment Windows, iOS, Android et Xbox.

### <a name="cordova"></a>Cordova
Cordova vous permet de créer des applications hybrides à l’aide de Visual Studio Tools pour Apache Codova ou Visual Studio Code avec des extensions pour Cordova. L’approche hybride vous permet de partager des composants avec des sites web et de réutiliser des applications basées sur un serveur web avec des approches d’applications web hébergées basées sur Cordova.

#### <a name="pros"></a>Avantages
- Facilité d’utilisation accrue du code grâce à la création d’un code base pour plusieurs plateformes
- Répondre aux besoins d’une audience plus vaste sur de nombreuses plateformes
- Réduction spectaculaire de la durée de développement
- Facile à lancer et à mettre à jour

#### <a name="cons"></a>Inconvénients
- Moins bonnes performances
- Manque de flexibilité
- Chaque plateforme possède un ensemble unique de composants et de fonctionnalités pour rendre l’application native plus créative
- Durée de conception de l’interface utilisateur accrue
- Limitation en termes d’outils
