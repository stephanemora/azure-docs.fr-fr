---
title: Planifier votre solution Azure Communication Services de téléphonie et SMS
titleSuffix: An Azure Communication Services concept document
description: Découvrez comment planifier efficacement l’utilisation des numéros de téléphone et de la téléphonie.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944241"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planifier votre solution de téléphonie et SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ce document décrit les différents plans de téléphonie et types de numéros qu’Azure Communication Services peut vous offrir. Nous allons parcourir les flux décisionnels pour vous aider à sélectionner un fournisseur de services vocaux, des types de numéros de téléphone, des plans et des fonctionnalités mis à votre disposition via Communication Services.

## <a name="about-phone-numbers-in-azure-communications-services"></a>À propos des numéros de téléphone dans Azure Communication Services

Azure Communication Services vous permet d’utiliser des numéros de téléphone pour passer et recevoir des appels téléphoniques et des messages SMS. Ces numéros de téléphone peuvent être utilisés pour configurer l’ID de l’appelant lors d’appels sortants émis par votre service.
  
Si vous n’avez pas de numéro de téléphone à importer dans votre solution Communication Services, la méthode la plus simple pour commencer consiste à obtenir un nouveau numéro de téléphone à partir d’Azure Communication Services, en quelques minutes.

Si vous disposez d’un numéro de téléphone existant que vous souhaitez continuer à utiliser dans votre solution (par exemple, 1-800–ENTREPRISE), vous pouvez porter ce numéro de téléphone du fournisseur existant dans Communication Services.

Le schéma suivant vous aide à parcourir les options disponibles :

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Schéma montrant comment prendre une décision concernant vos numéros de téléphone.":::

À présent, passons en revue les types de numéros de téléphone et les fonctionnalités disponibles via Communication Services. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Offre Microsoft directe de numéros de téléphone et de fonctionnalités

Azure Communication Services offre une excellente flexibilité aux développeurs. Sur la plupart des numéros de téléphone, nous vous autorisons à configurer un ensemble de plans « à la carte ». Certains développeurs ont uniquement besoin d’un plan d’appels entrants ; d’autres optent pour des plans d’appels entrants et de SMS sortants. Ces plans peuvent être sélectionnés lorsque vous louez et/ou portez vos numéros de téléphone dans Communication Services.

Les plans disponibles dépendent du pays et du type de numéro de téléphone dans lesquels vous opérez. Le schéma ci-dessous représente le flux décisionnel :    Les plans disponibles dépendent du pays et du type de numéro de téléphone dans lesquels vous opérez.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

Avant de pouvoir sélectionner un type de numéro de téléphone, nous allons passer en revue le plan de numérotage téléphonique international.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Lecture facultative. Plan de numérotage des télécommunications publiques internationales (E.164)

> [!NOTE]
> Nous vous recommandons de consulter ces informations même si vous connaissez bien le plan de numérotage téléphonique E.164, pour mieux comprendre les types de numéros et les fonctionnalités offerts par l’offre directe d’Azure Communication Services.

Le plan de numérotage des télécommunications publiques internationales est défini dans la recommandation E.164 de l’UIT (Union internationale des télécommunications). Les nombres conformes sont limités à un maximum de 15 chiffres.

Le numéro de téléphone est constitué par

-   Un préfixe « + »
-   Un indicatif de numérotation internationale ou un indicatif de pays/région (un, deux ou trois chiffres) 
-   *(Facultatif)* Un indicatif national de destination ou un plan de numérotage, communément appelé indicatif régional. La longueur de cet indicatif dépend du pays. Aux États-Unis, il est constitué de trois chiffres. En Australie et en Nouvelle-Zélande, il ne compte qu’un seul chiffre. L’Allemagne, le Japon, le Mexique et d’autres pays ont des indicatifs régionaux de longueur variable. Par exemple, en Allemagne, l’indicatif régional peut compter entre deux et cinq chiffres, tandis qu’au Japon, il peut compter entre un et cinq chiffres.
-   Un numéro d’abonné

> [!NOTE]
> La classification ci-dessus n’est pas pleinement conforme à la norme E.164 de l’UIT, et elle a pour but de fournir une description simplifiée. Par exemple, le numéro d’abonné est subdivisé dans cette norme. Si vous souhaitez découvrir plus en détail le plan de numérotage international, la [norme E.164 de l’UIT](https://www.itu.int/rec/T-REC-E.164) est un excellent point de départ.  

Voici quelques exemples qui vous aideront à mieux comprendre le plan de numérotage :

Numéro de téléphone régional aux États-Unis :

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Exemple de numéro de téléphone régional aux États-Unis":::

Numéro de téléphone régional au Canada :

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Exemple de numéro de téléphone régional au Canada":::

Numéro gratuit dans la région Amérique du Nord :

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Exemple de numéro gratuit en Amérique du Nord":::

Numéro de téléphone mobile au Royaume-Uni :

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Exemple de numéro mobile au Royaume-Uni":::

À présent, nous allons passer en revue les types de numéros de téléphone spécifiques disponibles dans Azure Communication Services.

## <a name="phone-number-types-in-azure-communication-services"></a>Types de numéros de téléphone dans Azure Communication Services

Microsoft propose des plans régionaux et gratuits pour les codes courts et les numéros mobiles dans les pays où cela est applicable.

Le tableau ci-dessous résume ces types de numéros de téléphone : 

| Type de numéro de téléphone | Exemple                              | Disponibilité dans le pays    | Cas d’utilisation courant                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Zones géographiques          | +1 (indicatif de zone géographique) XXX XX XX  | États-Unis, Canada, Porto Rico | Attribution de numéros de téléphone aux utilisateurs dans vos applications ou attribution à des systèmes/bots de réponse vocale interactive (IVR) |
| Gratuit         | \+ 1 (*indicatif* de zone d’appel gratuit) XXX XX XX | États-Unis, Canada, Porto Rico | Attribution à des systèmes/bots de réponse vocale interactive (IVR), applications SMS                                        |

## <a name="plans"></a>Plans 

Examinons les fonctionnalités que vous pouvez activer pour vos numéros de téléphone. Ces fonctionnalités varient selon le pays en raison des exigences réglementaires. Azure Communication Services offre les fonctionnalités suivantes :

- **SMS sortants unidirectionnels**, utiles pour les scénarios de notification et d’authentification à 2 facteurs.
- **SMS entrants et sortants bidirectionnels**, package prédéfini qui permet d’envoyer et de recevoir des SMS dans le cadre d’un seul plan.
- **Appels RTPC**, vous pouvez sélectionner un appel entrant et utiliser l’ID de l’appelant pour émettre des appels sortants.

## <a name="next-steps"></a>Étapes suivantes

### <a name="quickstarts"></a>Démarrages rapides

- [Obtenir un numéro de téléphone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Passer un appel](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Envoyer un SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentation conceptuelle

- [Concepts vocaux et vidéo](../voice-video-calling/about-call-types.md)
- [Flux d’appels et flux de SMS](../call-flows.md)
- [Tarification](../pricing.md)
