---
title: Bibliothèques de client et API REST pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: En savoir plus sur les bibliothèques de client et les API REST Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f39aa76e4bd2ce3d298e555f56b09d0218ef0862
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929947"
---
# <a name="client-libraries-and-rest-apis"></a>Bibliothèques de client et API REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Les fonctionnalités Azure Communication Services sont organisées de façon conceptuelle en six domaines. Certains domaines possèdent des bibliothèques de client entièrement open source. La bibliothèque de client Appel utilise des interfaces réseau propriétaires et est actuellement en source fermée ; la bibliothèque Conversation comporte une dépendance de source fermée. Les liens vers tous les kits de développement logiciel (SDK) et les exemples sont conservés dans le [référentiel GitHub Azure Communication Services](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Bibliothèques clientes

| Assembly               | Protocoles             |Open source et source fermée| Espaces de noms                          | Fonctionnalités                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Ouvrir            | Azure.ResourceManager.Communication | Approvisionner et gérer les ressources Azure Communication Services             |
| Courant                 | REST | Ouvrir               | Azure.Communication.Common          | Fournit des types de base pour d’autres bibliothèques de client |
| Administration         | REST |                | Azure.Communication.Administration  | Gérer les utilisateurs, les jetons d’accès et les numéros de téléphone, allouer des serveurs STUN et TURN conformes aux normes |
| Conversation                   | REST avec signalisation propriétaire | Ouvert avec package de signalisation propriétaire    | Azure.Communication.Chat            | Ajouter des conversations basées sur du texte en temps réel dans vos applications  |
| SMS                    | REST | Ouvrir              | Azure.Communication.SMS             | Envoyer et recevoir des messages SMS |
| Appel                | Transport propriétaire | Fermés |Azure.Communication.Calling         | Tirez parti de la voix, de la vidéo, du partage d’écran et d’autres fonctionnalités de communication de données en temps réel          |

### <a name="client-library-language-support"></a>Prise en charge du langage de la bibliothèque de client

Vous trouverez ci-dessous un calendrier et un guide de disponibilité pour chaque package de bibliothèques de client. La [feuille de route Azure](https://azure.microsoft.com/updates/) fournit des informations supplémentaires sur les fonctionnalités à venir.

| Domaine           | JavaScript | .NET | Python | Java | Swift ou Obj-C | Java (Android) | Autres                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Pas encore pris en charge*  | GO et Azure CLI *Pas encore pris en charge* |
| Courant         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Administration | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | Interface de ligne de commande                            |
| Conversation           | ✔️         | ✔️    | ✔️      | ✔️   | *Pas encore pris en charge*  | *Pas encore pris en charge*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Appel        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Prise en charge du référentiel public de la bibliothèque de client

Communication Services publie des bibliothèques générées dans différents référentiels publics.

| Langage       | Optimisé pour...                       | Packaging |
| -------------- | ------------------------------------ | --------- |
| .NET           | Système multiplateforme                       | NuGet     |
| Python         | Serveurs Windows et Linux              | Pypi      |
| Java (J2EE)    | JVM sur serveurs Windows ou Linux      | Maven     |
| Java (Android) | Applications clientes Android          | Maven     |
| JavaScript     | Applications clientes de navigateur et Node | Npm       |

## <a name="rest-apis"></a>API REST

Les API Communication Services ainsi que d’autres API REST Azure sont documentées dans [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/). Dans cette documentation, vous trouverez des conseils sur la structure de vos messages HTTP et l’utilisation de Postman. Cette documentation est également disponible au format Swagger sur [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Informations supplémentaires sur la prise en charge

### <a name="ios-and-android-support-details"></a>Informations sur la prise en charge iOS et Android

- Les bibliothèques de client iOS Communication Services ciblent iOS 13 et plus, et Xcode 11 et plus.
- Les bibliothèques de client Java Android ciblent l’API Android niveau 21 et plus, et Android Studio 4.0 et plus

### <a name="net-support-details"></a>Informations sur la prise en charge .NET

À l’exception de la bibliothèque Appel, les packages Communication Services ciblent .NET Standard 2.0 qui prend en charge les plateformes suivantes.

Prise en charge via .NET Framework 4.6.1
- Windows 10, 8.1, 8 et 7
- Windows Server 2012 R2, 2012 et 2008 R2 SP1

Prise en charge via .NET Core 2.0 :
- Windows 10 (1607 et plus), 7 SP1 et plus, 8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 et plus
- Versions/distributions multiples de Linux
- UWP 10.0.16299 (RS3) Septembre 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="api-stability-expectations"></a>Attentes en matière de stabilité des API 

> [!IMPORTANT]
> Cette section fournit des conseils sur les API REST et les bibliothèques de client ayant l’indication **stable**. Les API ayant l’indication version préliminaire, préversion ou bêta sont susceptibles d’être modifiées ou dépréciées **sans préavis**. Azure Communication Services est actuellement en **préversion publique** et les API portent cette même indication.

À l’avenir, il est possible que des versions des bibliothèques de client Communication Services soient supprimées, et que des modifications avec rupture soient apportées à nos API REST et bibliothèques de client publiées. Azure Communication Services respecte *généralement* deux stratégies de prise en charge pour la suppression de versions de service :

- En cas de modification de l’interface Communication Services nécessitant un changement de code, vous serez informé au moins trois ans à l’avance. Toutes les API REST et API de bibliothèque de client documentées reçoivent généralement un avertissement trois ans avant la suppression d’une interface.
- Vous serez informé au moins un an avant d’avoir à mettre à jour les assemblys de bibliothèques de client vers la dernière version mineure. Ces mises à jour obligatoires ne doivent nécessiter aucune modification de code, car elles ont lieu dans la même version principale. Cela concerne particulièrement les bibliothèques Appel et Conversation, dont les composants en temps réel nécessitent souvent des mises à jour de sécurité et de performances. Nous vous encourageons vivement à maintenir à jour vos bibliothèques de client Communication Services.

### <a name="api-and-client-library-decommissioning-examples"></a>Exemples de désaffectation d’API et de bibliothèques de client

**Vous avez intégré la version v24 de l’API REST SMS à votre application. Azure Communication publie la version v25.**

Vous serez averti 3 ans avant la mise hors service de ces API et la mise à niveau forcée vers la version v25. Cette mise à jour peut nécessiter une modification du code.

**Vous avez intégré la version v2.02 de la bibliothèque de client Appel à votre application. Azure Communication publie la version v2.05.**

Vous serez probablement invité à mettre à jour vers la version v2.05 de la bibliothèque de client Appel dans les 12 mois suivant la publication de cette version. Il ne devrait s’agir que d’un simple remplacement de l’artefact sans modification du code, car la version v2.05 se trouve dans la version principale v2 et ne comporte aucune modification avec rupture.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les présentations de bibliothèques de client suivantes :

- [Vue d’ensemble de la bibliothèque de client Appel](../concepts/voice-video-calling/calling-sdk-features.md)
- [Vue d’ensemble de la bibliothèque de client Conversation](../concepts/chat/sdk-features.md)
- [Vue d’ensemble de la bibliothèque de client SMS](../concepts/telephony-sms/sdk-features.md)

Pour prendre en main Azure Communication Services :

- [Créez des ressources Azure Communication](../quickstarts/create-communication-resource.md)
- Générez des [jetons d’accès utilisateur](../quickstarts/access-tokens.md)
