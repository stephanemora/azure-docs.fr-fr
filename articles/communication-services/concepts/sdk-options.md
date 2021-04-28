---
title: Kits de développement logiciel (SDK) et API REST pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: En savoir plus sur les Kits de développement logiciel (SDK) et les API REST Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 21910718d837750db7ae87afa36a2e7576a729cf
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108016560"
---
# <a name="sdks-and-rest-apis"></a>Kits SDK et API REST

Les fonctionnalités Azure Communication Services sont organisées de façon conceptuelle en six domaines. La plupart des domaines possèdent des Kits de développement logiciel (SDK) entièrement open source, programmés sur des API REST publiées que vous pouvez utiliser directement sur Internet. Le Kit de développement logiciel (SDK) Appel utilise des interfaces réseau privées et est actuellement fermé. Des exemples et des informations plus techniques sur les Kits de développement logiciel (SDK) sont publiés dans le [référentiel GitHub pour Azure Communication Services](https://github.com/Azure/communication).

## <a name="rest-apis"></a>API REST
Les API Communication Services ainsi que d’autres API REST Azure sont documentées dans [docs.microsoft.com](/rest/api/azure/). Dans cette documentation, vous trouverez des conseils sur la structure de vos messages HTTP et l’utilisation de Postman. Cette documentation est également disponible au format Swagger sur [GitHub](https://github.com/Azure/azure-rest-api-specs).


## <a name="sdks"></a>Kits SDK

| Assembly | Espaces de noms| Protocoles | Fonctionnalités |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Approvisionner et gérer les ressources Azure Communication Services|
| Courant | Azure.Communication.Common| REST | Fournit des types de base pour d’autres Kits de développement logiciel (SDK) |
| Identité | Azure.Communication.Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Gérez les utilisateurs et les jetons d’accès|
| Numéros de téléphone _(bêta)_| Azure.Communication.PhoneNumbers| [REST](/rest/api/communication/phonenumbers)| Acquérir et gérer des numéros de téléphone |
| Conversation | Azure.Communication.Chat| [REST](https://docs.microsoft.com/rest/api/communication/) avec signalisation protégée | Ajouter des conversations basées sur du texte en temps réel dans vos applications |
| SMS| Azure.Communication.SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| Envoyer et recevoir des messages SMS|
| Appel| Azure.Communication.Calling | Transport propriétaire | Utiliser la voix, la vidéo, le partage d’écran et d’autres capacités de communication de données en temps réel |

Les Kits de développement logiciel (SDK) Azure Resource Manager, Identité et SMS sont axés sur l’intégration de services et, dans de nombreux cas, des problèmes de sécurité se posent si vous intégrez ces fonctions dans des applications destinées aux utilisateurs finaux. Les Kits de développement logiciel (SDK) Courant et Conversation sont adaptées aux applications clientes et aux applications de service. Le Kit de développement logiciel (SDK) Appel est conçu pour les applications clientes. Un Kit de développement logiciel (SDK) axé sur les scénarios de service est en cours de développement.


### <a name="languages-and-publishing-locations"></a>Langages et emplacements de publication

Vous trouverez ci-dessous des emplacements de publication pour chaque package de Kit de développement logiciel (SDK).

| Domaine           | JavaScript | .NET | Python | Java SE | iOS | Android | Autres                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPI](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Courant         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/A      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identité | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPI](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Numéros de téléphone | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPI](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Conversation           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPI](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPI](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Appel        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Documentation de référence     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>Limitations de l’API REST
Certaines API REST et les méthodes de kit de développement logiciel (SDK) correspondantes sont soumises à des limites dont vous devez tenir compte. Le dépassement de ces limites déclenchera une réponse d’erreur `429 - Too Many Requests`. Ces limites peuvent être augmentées à l’aide [d’une demande adressée à Support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

| API                                                                                                                          | Limitation            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Toutes les API Rechercher un plan de numéros de téléphone](/rest/api/communication/phonenumbers)         | 4 demandes/jour      |
| [Acheter un plan de numéros de téléphone](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 achat par mois  |
| [Envoyer un SMS](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 demandes/minute |


## <a name="sdk-platform-support-details"></a>Détails de la prise en charge de la plateforme des Kits de développement logiciel (SDK)

### <a name="ios-and-android"></a>iOS et Android 

- Les Kits de développement logiciel (SDK) Communication Services iOS ciblent iOS 13 et versions ultérieures et Xcode 11 et versions ultérieures.
- Les Kits de développement logiciel (SDK) Java Android ciblent le niveau d’API Android 21 et versions ultérieures et Android Studio 4.0 et versions ultérieures.

### <a name="net"></a>.NET 

À l’exception d’Appel, les packages Communication Services ciblent .NET Standard 2.0 qui prend en charge les plateformes suivantes.

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
> Cette section fournit une aide sur les API REST et les Kits de développement logiciel (SDK) ayant l’indication **stable**. Les API ayant l’indication version préliminaire, préversion ou bêta sont susceptibles d’être modifiées ou dépréciées **sans préavis**.

À l’avenir, il est possible que des versions des Kits de développement logiciel (SDK) Communication Services soient mises hors service et que des changements cassants soient apportés à nos API REST et aux Kits de développement logiciel (SDK) publiés. Azure Communication Services respecte *généralement* deux stratégies de prise en charge pour la suppression de versions de service :

- En cas de modification de l’interface Communication Services nécessitant un changement de code, vous serez informé au moins trois ans à l’avance. Toutes les API REST et API de Kit de développement logiciel (SDK) documentées reçoivent généralement un avertissement trois ans avant la suppression d’une interface.
- Vous serez informé au moins un an avant d’avoir à mettre à jour les assemblys de Kits de développement logiciel (SDK) vers la dernière version mineure. Ces mises à jour obligatoires ne doivent nécessiter aucune modification de code, car elles ont lieu dans la même version principale. Cela concerne particulièrement les bibliothèques Appel et Conversation, dont les composants en temps réel nécessitent souvent des mises à jour de sécurité et de performances. Nous vous encourageons vivement à maintenir à jour vos Kits de développement logiciel (SDK) Communication Services.

### <a name="api-and-sdk-decommissioning-examples"></a>Exemples de suppression d’API et de Kit de développement logiciel (SDK)

**Vous avez intégré la version v24 de l’API REST SMS à votre application. Azure Communication publie la version v25.**

Vous serez averti 3 ans avant la mise hors service de ces API et la mise à niveau forcée vers la version 25. Cette mise à jour peut nécessiter une modification du code.

**Vous avez intégré la version 2.02 du Kit de développement logiciel (SDK) Appel à votre application. Azure Communication publie la version 2.05.**

Vous serez probablement invité à effectuer une mise à jour vers la version 2.05 du Kit de développement logiciel (SDK) Appel dans les 12 mois suivant la publication de cette version. Il ne devrait s’agir que d’un simple remplacement de l’artefact sans modification du code, car la version v2.05 se trouve dans la version principale v2 et ne comporte aucune modification avec rupture.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les présentations de Kit de développement logiciel (SDK) suivantes :

- [Vue d’ensemble du Kit de développement logiciel (SDK) Appel](../concepts/voice-video-calling/calling-sdk-features.md)
- [Vue d’ensemble du Kit de développement logiciel (SDK) Conversation](../concepts/chat/sdk-features.md)
- [Vue d’ensemble du Kit de développement logiciel (SDK) SMS](../concepts/telephony-sms/sdk-features.md)

Pour prendre en main Azure Communication Services :

- [Créez des ressources Azure Communication](../quickstarts/create-communication-resource.md)
- Générez des [jetons d’accès utilisateur](../quickstarts/access-tokens.md)
