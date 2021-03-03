---
title: Concepts de l’intégration de la téléphonie RTCP pour Azure Communication Services
description: Découvrez comment intégrer des fonctionnalités d’appel RTCP dans votre application Azure Communication Services.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7c2b0168c914c836aca7b551a9c22d4718c7b6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100419391"
---
# <a name="telephony-concepts"></a>Concepts de la téléphonie

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Les bibliothèques de client Azure Communication Services Calling peuvent être utilisées pour ajouter la téléphonie et le RTCP à vos applications. Cette page récapitule les concepts et les fonctionnalités clés de la téléphonie. Pour plus d’informations sur les langages et les fonctionnalités spécifiques de la bibliothèque de client, consultez [bibliothèque Calling](../../quickstarts/voice-video-calling/calling-client-samples.md).

## <a name="overview-of-telephony"></a>Vue d’ensemble de la téléphonie
Chaque fois que vos utilisateurs interagissent avec un numéro de téléphone traditionnel, les appels sont facilités par les appels vocaux RTCP (réseau téléphonique commuté public). Pour émettre et recevoir des appels RTPC, vous devez ajouter des fonctionnalités de téléphonie à votre ressource Azure Communication Services. Dans ce cas, la signalisation et le contenu multimédia utilisent une combinaison de technologies basées sur IP et RTPC pour connecter vos utilisateurs. Communication Services fournit deux moyens discrets d’atteindre le réseau RTCP : Azure Cloud Calling et interface SIP.

### <a name="azure-cloud-calling"></a>Azure Cloud Calling

Un moyen simple d’ajouter une connectivité RTCP à votre application ou service ; dans ce cas, Microsoft est votre fournisseur de télécommunication. Vous pouvez acheter des numéros directement auprès de Microsoft. Azure Cloud Calling est une solution de téléphonie entièrement cloud pour les services de communication. C’est l’option la plus simple qui connecte ACS au RTCP pour permettre les appels aux réseaux filaires et aux téléphones portables dans le monde entier. Avec cette option, Microsoft agit comme opérateur RTCP, comme le montre le diagramme suivant :

![Diagramme d’Azure Cloud Calling.](../media/telephony-concept/azure-calling-diagram.png)

Si vous répondez « oui » à ce qui suit, Azure Cloud Calling est la bonne solution pour vous :
- Azure Cloud Calling est disponible dans votre région.
- Vous n’avez pas besoin de conserver votre opérateur RTCP actuel.
- Vous voulez utiliser l’accès géré par Microsoft au RTCP.

Avec cette option :
- Vous recevez des numéros directement de Microsoft et vous pouvez appeler des téléphones dans le monde entier.
- Vous n’avez pas besoin de déploiement ou de maintenance d’un déploiement local, car Azure Cloud Calling fonctionne à partir d’Azure Communication Services.
- Remarque : Si nécessaire, vous pouvez choisir de connecter un SBC pris en charge via l’interface SIP pour l’interopérabilité avec des PABX de tiers, des appareils analogiques et d’autres équipements de téléphonie de tiers pris en charge par le SBC.

Cette option nécessite une connexion sans interruptions à Azure Communication Services.

### <a name="sip-interface"></a>Interface SIP

Avec cette option, vous pouvez connecter la téléphonie locale héritée et l’opérateur de votre choix à Azure Communication Services. Elle fournit des fonctionnalités d’appel RTCP à vos applications ACS, même si Azure Cloud Calling n’est pas disponible dans votre pays/région. 

![Diagramme de l’interface SIP.](../media/telephony-concept/sip-interface-diagram.png)

Si vous répondez « oui » à une des questions suivantes, l’interface SIP est la bonne solution pour vous :

- Vous voulez utiliser ACS avec des fonctionnalités d’appel RTCP.
- Vous devez conserver votre opérateur RTCP actuel.
- Vous voulez combiner le routage, avec certains appels passant par Azure Cloud Calling et d’autres passant par votre opérateur.
- Vous devez interagir avec des PABX et/ou des équipements de tiers, comme des haut-parleurs de plafond, des appareils analogiques, etc.

Avec cette option :

- Vous connectez vos propres SBC pris en charge à Azure Communication Services sans avoir besoin de logiciels locaux supplémentaires.
- Vous pouvez utiliser absolument n’importe quel opérateur de téléphonie avec ACS.
- Vous pouvez choisir de configurer et de gérer cette option, ou bien elle peut être configurée et gérée par votre opérateur ou votre partenaire (demandez si votre opérateur ou votre partenaire fournit cette option).
- Vous pouvez configurer l’interopérabilité entre votre équipement de téléphonie, par exemple un PABX et des appareils analogiques de tiers, et ACS.

Cette option nécessite les conditions suivantes :

- Une connexion sans interruptions à Azure.
- Le déploiement et la maintenance d’un SBC pris en charge.
- Un contrat avec un opérateur tiers. (Sauf s’ils sont déployés en tant qu’option pour fournir une connexion à un PABX de tiers, à des appareils analogiques ou à d’autres équipements de téléphonie pour des utilisateurs qui sont sur Communication Services.)

## <a name="next-steps"></a>Étapes suivantes

### <a name="conceptual-documentation"></a>Documentation conceptuelle

- [Types de numéros de téléphone dans Azure Communication Services](./plan-solution.md)
- [Planifier l’interface SIP](./sip-interface-infrastructure.md)
- [Tarification](../pricing.md)

### <a name="quickstarts"></a>Démarrages rapides

- [Obtenir un numéro de téléphone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Appel à un téléphone](../../quickstarts/voice-video-calling/pstn-call.md)
