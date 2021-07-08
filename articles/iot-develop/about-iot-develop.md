---
title: Présentation du développement d’applications et d’appareils Azure IoT
description: Découvrez comment utiliser Azure IoT pour développer des appareils embarqués et créer des applications cloud compatibles avec les appareils.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: 7b23569b07893ebbecf86336a40e52cd8627851a
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111590069"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Qu’est-ce que le développement d’applications et d’appareils Azure IoT ?

Azure IoT est un ensemble de services gérés et de plateforme qui connectent, surveillent et contrôlent vos appareils IoT. Azure IoT offre aux développeurs un ensemble complet d’options. Vos options incluent des plateformes d’appareils, des services cloud, des Kits de développement logiciel (SDK) et des outils pour créer des applications cloud compatibles avec les appareils.

Cet article présente plusieurs points essentiels à prendre en compte pour les développeurs qui débutent avec Azure IoT. Ces concepts vous permettront, en tant que développeur d’appareils IoT, de vous familiariser avec les options Azure IoT et de savoir par où commencer. Plus précisément, l’article présente les concepts suivants :
- [Fonctionnement des rôles de développement d’appareils](#device-development-roles)
- [Choix de votre matériel](#choosing-your-hardware)
- [Choix d’un Kit de développement logiciel (SDK)](#choosing-an-sdk)
- [Sélection des options de connexion](#selecting-connection-options)

## <a name="device-development-roles"></a>Rôles de développement d’appareils
Cet article traite de deux rôles communs que l’on peut observer chez les développeurs d’appareils. Tel qu’utilisé ici, un rôle est une collection de tâches de développement qui sont associées. Il est utile de comprendre quel type de rôle de développement vous utilisez actuellement. Votre rôle a un impact sur de nombreux choix de développement que vous faites.

* **Développement d’applications pour appareils :** S’aligne sur les pratiques de développement modernes, cible de nombreux langages d’ordre supérieur et s’exécute sur un système d’exploitation à usage général tel que Windows ou Linux.

* **Développement d’appareils intégrés :** Décrit le développement ciblant les appareils à ressources restreintes. Un appareil à ressources restreintes est souvent utilisé pour réduire les coûts unitaires, la consommation d’énergie ou la taille de l’appareil. Ces appareils ont un contrôle direct sur la plateforme matérielle sur laquelle ils s’exécutent.

### <a name="device-application-development"></a>Développement d’applications pour appareils
Les développeurs d’applications pour appareils adaptent les appareils existants pour qu’ils puissent se connecter au cloud et s’intégrer à leurs solutions IoT. Ces appareils peuvent prendre en charge des langages d’ordre supérieur, tels que C# ou Python, et prennent souvent en charge un système d’exploitation à usage général robuste, tel que Windows ou Linux. Les appareils cibles courants incluent les PC, les conteneurs, les ordinateurs Raspberry Pi et les appareils mobiles. 

Au lieu de développer des appareils limités à grande échelle, ces développeurs se concentrent sur l’activation d’un scénario IoT spécifique requis par leur solution cloud. Certains de ces développeurs travaillent également sur des appareils limités pour leur solution cloud. Les développeurs qui travaillent avec des appareils limités sont invités à consulter [Développement d’appareils intégrés](#embedded-device-development) ci-dessous.

> [!TIP]
> Consultez les [Kits de développement logiciel (SDK) pour appareils non limités](about-iot-sdks.md#unconstrained-device-sdks) pour commencer.

### <a name="embedded-device-development"></a>Développement d’appareils intégrés
Le développement intégré cible des appareils limités qui ont une mémoire et un traitement restreints. Les appareils limités restreignent ce qui peut être réalisé par rapport à une plateforme de développement traditionnelle.

Les appareils intégrés utilisent généralement un système d’exploitation en temps réel (RTOS) ou aucun système d’exploitation. Les appareils intégrés ont un contrôle total sur leur matériel en raison de l’absence de système d’exploitation à usage général. De ce fait, les appareils intégrés constituent un bon choix pour les systèmes en temps réel.

Les Kits de développement logiciel (SDK) intégrés actuels ciblent le langage **C**. Les Kits de développement logiciel (SDK) intégrés ne fournissent pas de système d’exploitation ou ne prennent pas en charge Azure RTOS. Ils sont conçus avec des cibles intégrées à l’esprit. Les considérations relatives à la conception comprennent la nécessité d’une empreinte mémoire minimale et d’une structure sans allocation de mémoire.

Si votre appareil est en mesure d’exécuter un système d’exploitation à usage général, nous vous recommandons de suivre la voie du [développement d’applications pour appareils](#device-application-development). Cela offre un ensemble plus riche d’options de développement.

> [!TIP]
> Consultez les [Kits de développement logiciel (SDK) pour appareils limités](about-iot-sdks.md#constrained-device-sdks) pour commencer.

## <a name="choosing-your-hardware"></a>Choix de votre matériel
Les appareils Azure IoT sont les éléments de base d’une solution IoT et sont chargés d’observer et d’interagir avec leur environnement. Il existe de nombreux types d’appareils IoT, et il est utile de comprendre les types d’appareils qui existent et la manière dont ils peuvent avoir un impact sur votre processus de développement.

Pour plus d’informations sur la différence entre les types d’appareils abordés dans cet article, consultez [À propos des types d’appareils IoT](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Choix d’un Kit de développement logiciel (SDK)
En tant que développeur d’appareils Azure IoT, vous disposez d’un ensemble diversifié de Kits de développement logiciel (SDK) pour appareils et de Kits de développement logiciel (SDK) pour services Azure pour vous aider à créer des applications cloud compatibles avec les appareils. Les Kits de développement logiciel (SDK) rationalisent votre effort de développement et simplifient une grande partie de la complexité de la connexion et de la gestion des appareils. 

Comme indiqué dans la section [Rôles de développement d’appareils](#device-development-roles), il existe trois types de Kits de développement logiciel (SDK) IoT pour le développement d’appareils :
- Kits de développement logiciel (SDK) pour appareils intégrés, pour les appareils limités
- Kits de développement logiciel (SDK) pour appareils, pour utiliser des langages d’ordre supérieur afin de connecter des appareils existants à des applications IoT
- Kits de développement logiciel (SDK) pour services, pour créer des solutions Azure IoT qui connectent des appareils aux services

Pour en savoir plus sur le choix d’un appareil Azure IoT ou d’un Kit de développement logiciel (SDK) pour services, consultez [Vue d’ensemble des kits Azure IoT device SDK](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Sélection des options de connexion
Une étape importante du processus de développement consiste à choisir l’ensemble d’options que vous allez utiliser pour connecter et gérer vos appareils. Il existe deux aspects essentiels à prendre en compte :
- Le choix d’une plateforme d’application IoT pour héberger vos appareils. Pour Azure IoT, cela signifie que vous devez choisir IoT Hub ou IoT Central.
- Le choix d’outils de développement pour vous aider à connecter, gérer et surveiller des appareils.

Pour en savoir plus sur la sélection d’une plateforme d’application et d’outils, consultez [Vue d’ensemble : options de connexion pour les développeurs d’appareils Azure IoT](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Étapes suivantes
Sélectionnez la série de démarrages rapides de développement intégré ou de développement d’applications la plus pertinente pour votre rôle de développement. Ces articles présentent les principes fondamentaux de la création d’une application Azure IoT pour héberger des appareils, l’utilisation d’un Kit de développement logiciel (SDK), la connexion d’un appareil et l’envoi de données de télémétrie.  
- [Développement d’appareil Azure IoT](index.yml)
