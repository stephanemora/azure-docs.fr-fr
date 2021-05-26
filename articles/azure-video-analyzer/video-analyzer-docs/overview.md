---
title: Qu’est-ce qu’Azure Video Analyzer
description: Cette rubrique fournit une vue d’ensemble d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: overview
ms.date: 03/11/2021
ms.openlocfilehash: c1d556662794de6127b0c455ac812b94b5a05742
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386132"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Qu’est-ce qu’Azure Video Analyzer ? (préversion)

Azure Video Analyzer fournit une plateforme pour créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. La plateforme se compose d’un module IoT Edge et d’un service Azure associé. Elle offre la possibilité de capturer, d’enregistrer et d’analyser des vidéos en direct, et de publier les résultats : vidéo et/ou analytique vidéo. La vidéo peut être publiée à la périphérie ou dans le service cloud Video Analyzer, tandis que l’analytique vidéo peut être publiée dans les services Azure (dans le cloud et/ou à la périphérie). La plateforme peut être utilisée pour améliorer les solutions IoT avec analyse vidéo. La fonctionnalité Video Analyzer peut être associée à d’autres modules Azure IoT Edge, comme Stream Analytics sur IoT Edge, Cognitive Services sur IoT Edge et à des services Azure dans le cloud, comme Event Hub, Cognitive Services, etc. pour créer des applicables hybrides puissantes (par exemple, périphérie + cloud).

Le module périphérique Video Analyzer est conçu comme une plateforme extensible qui vous permet de connecter différents modules périphériques d’analyse vidéo (par exemple, des conteneurs Cognitive Services, des modules périphériques personnalisés que vous avez générés avec des modèles Machine Learning open source ou des modèles personnalisés entraînés avec vos propres données) et de les utiliser pour analyser la vidéo en direct sans avoir à créer et exécuter un pipeline vidéo en direct, qui s’avère être une tâche complexe. Le service cloud Video Analyzer vous permet de lire les vidéos et les analyses vidéo à partir de ces workflows.

## <a name="accelerate-iot-solutions-development"></a>Accélérer le développement de solutions IoT 

Les solutions IoT qui combinent l’analytique vidéo avec des signaux d’autres capteurs IoT et/ou des données métier peuvent vous aider à automatiser ou semi-automatiser des décisions métier, et ainsi améliorer la productivité. Video Analyzer vous permet de créer des solutions de ce type plus rapidement. Vous pouvez vous concentrer sur la création des modules d’analyse vidéo et de la logique propre à votre entreprise, et laisser le soin à la plateforme de masquer les complexités liées à la gestion et à l’exécution d’un pipeline vidéo.

Avec Video Analyzer, vous pouvez continuer à utiliser vos [caméras de télésurveillance](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) avec vos [systèmes de gestion vidéo](https://en.wikipedia.org/wiki/Video_management_system) existants et créer des applications d’analytique vidéo de manière indépendante. Vous pouvez utiliser Video Analyzer avec les kits de ressources et les SDK Vision par ordinateur pour créer des solutions IoT de pointe. Le diagramme ci-dessous illustre ceci.

![Développer des solutions IoT avec Video Analyzer](./media/overview/product-diagram.svg)

### <a name="concepts"></a>Concepts

* [Pipeline](pipeline.md)
* [Enregistrement de vidéo](video-recording.md)
* [Enregistrement vidéo en continu](continuous-video-recording.md)
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)
* [Video Analyzer sans enregistrement vidéo](analyze-live-video-without-recording.md)

## <a name="compliance-privacy-and-security"></a>Conformité, confidentialité et sécurité

Rappelez-vous que vous devez respecter toutes les lois applicables dans le cadre de votre utilisation de Video Analyzer, et que vous n’êtes pas autorisé à l’utiliser ou à utiliser tout autre service Azure d’une façon qui porte atteinte aux droits d’autrui ou qui soit préjudiciable pour autrui.

Pour pouvoir traiter une vidéo avec Video Analyzer, vous devez avoir tous les droits appropriés pour utiliser la vidéo, y compris, lorsque la loi l’exige, tous les consentements nécessaires des personnes (le cas échéant) qui apparaissant dans la vidéo ou l’image, pour utiliser, traiter et stocker leurs données dans Video Analyzer et Azure. Certaines juridictions peuvent imposer des exigences légales spéciales pour la collecte, le traitement en ligne et le stockage de certaines catégories de données, comme les données biométriques. Avant d’utiliser Video Analyzer et Azure pour le traitement et le stockage de données soumises à des exigences légales spéciales, vous devez veiller à respecter toutes les exigences légales applicables.

Pour en savoir plus sur la conformité, la confidentialité et la sécurité dans Video Analyzer, visitez le [Centre de confidentialité](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) Microsoft. Pour connaître les obligations de confidentialité de Microsoft et les bonnes pratiques de gestion et de conservation de vos données, y compris comment supprimer vos données, consultez la [Déclaration de confidentialité](https://privacy.microsoft.com/PrivacyStatement), les [Conditions des services en ligne](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (« OST ») et l’[Avenant au traitement des données](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (« DPA ») de Microsoft. En utilisant Video Analyzer, vous acceptez de respecter l’OST, le DPA et la Déclaration de confidentialité.

## <a name="next-steps"></a>Étapes suivantes

* Suivez l’article [Démarrage rapide : Bien démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md) pour voir comment exécuter la détection de mouvement sur un flux vidéo en direct.
* Passer en revue la [terminologie](terminology.md)
