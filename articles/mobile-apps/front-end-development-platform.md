---
title: Choisir la plateforme de développement frontal pour la création d’applications clientes avec Visual Studio et les services Azure
description: En savoir plus sur les langages natifs et les multiplateformes pris en charge pour créer des applications clientes.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240861"
---
# <a name="choose-mobile-development-frameworks"></a>Choisir des infrastructures de développement mobile
Les développeurs peuvent utiliser des technologies côté client pour créer eux-mêmes des applications mobiles en utilisant des infrastructures et des modèles spécifiques pour une approche multiplateforme. En fonction de leurs facteurs de décision, les développeurs peuvent créer :
- des applications natives pour plateforme unique à l’aide de langages tels qu’Objective-C et Java ;
- des applications multiplateformes à l’aide de Xamarin, .NET et C# ;
- des applications hybrides à l’aide de Cordova et de ses variantes.

## <a name="native-platforms"></a>Plateformes natives
La création d’une application native nécessite des langages de programmation spécifiques à la plateforme, des kits de développement logiciel (SDK), des environnements de développement et d’autres outils fournis par les fournisseurs de systèmes d’exploitation.

### <a name="ios"></a>iOS
Créé et développé par Apple, iOS est utilisé pour créer des applications sur des appareils Apple, à savoir iPhone et iPad.

- **Langages de programmation** : Objective-C, Swift
- **IDE** : Xcode
- **Kit de développement logiciel (SDK)**  : iOS

### <a name="android"></a>Android
Conçu par Google et par le système d’exploitation le plus populaire du monde, Android permet de créer des applications pouvant s’exécuter sur un éventail de smartphones et de tablettes.

- **Langage de programmation** : Java, Kotlin 
- **IDE** : Android Studio et outils de développement Android 
- **Kit SDK** : Kit de développement logiciel Android

### <a name="windows"></a>Windows
- **Langage de programmation** : C#
- **IDE** : Visual Studio, Visual Studio Code
- **Kit SDK** : Kit de développement logiciel (SDK) Windows

#### <a name="pros"></a>Avantages
- Bonne expérience utilisateur
- Applications réactives haute performance et la capacité d’interface avec des bibliothèques natives
- Applications hautement sécurisées

#### <a name="cons"></a>Inconvénients
- L’application s’exécute sur une seule plateforme
- Plus de ressources de développement requises et création d’applications plus coûteuse
- Moins de réutilisation du code

## <a name="cross-platforms-and-hybrid-applications"></a>Applications multiplateformes et hybrides
Les applications multiplateformes vous permettent d’écrire des applications mobiles natives une fois, de partager du code et de les exécuter sur iOS, Android et Windows.

### <a name="xamarin"></a>Xamarin
Détenu par Microsoft, [Xamarin](https://visualstudio.microsoft.com/xamarin/) est utilisé pour créer des applications mobiles multiplateformes robustes en C#. Xamarin dispose d’une bibliothèque de classes et d’un runtime qui fonctionne sur plusieurs plateformes, telles qu’iOS, Android et Windows. Il compile également les applications natives (non interprétées) qui offrent des performances élevées. Xamarin combine toutes les capacités des plateformes natives et ajoute un certain nombre de ses fonctionnalités puissantes.

- **Langage de programmation** : C#
- **IDE** : Visual Studio sur Windows ou Mac

### <a name="react-native"></a>React Native
Publié par Facebook en 2015, [React Native](https://facebook.github.io/react-native/) est une infrastructure [open source](https://github.com/facebook/react-native) JavaScript permettant d’écrire des applications mobiles réelles de rendu en mode natif pour iOS et Android. Il est basé sur REACT, la bibliothèque JavaScript de Facebook pour la création d’interfaces utilisateur. Au lieu de cibler le navigateur, il cible les plateformes mobiles. React Native utilise des composants natifs au lieu de composants web comme blocs de construction.
 
- **Langage de programmation** : JavaScript
- **IDE** : Visual Studio Code

### <a name="unity"></a>Unity
 Unity est un moteur optimisé pour la création de jeux. Vous pouvez l’utiliser pour concevoir des applications 2D ou 3D de haute qualité en C# pour des plateformes telles que Windows, iOS, Android et Xbox.

### <a name="cordova"></a>Cordova
Cordova vous permet de créer des applications hybrides à l’aide de Visual Studio Tools pour Apache Cordova ou Visual Studio Code avec des extensions pour Cordova. L’approche hybride vous permet de partager des composants avec des sites web et de réutiliser des applications basées sur un serveur web avec des approches d’applications web hébergées basées sur Cordova.

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
