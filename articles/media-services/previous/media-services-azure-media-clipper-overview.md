---
title: Créer des clips avec Azure Media Clipper | Microsoft Docs
description: Vue d’ensemble d’Azure Media Clipper, outil de création de clips multimédias à partir d’actifs multimédias.
services: media-services
keywords: clip;sous-clip;encodage;média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685013"
---
# <a name="create-clips-with-azure-media-clipper"></a>Créer des clips avec Azure Media Clipper 

Azure Media Clipper est une bibliothèque JavaScript gratuite qui permet aux développeurs web de fournir à leurs utilisateurs une interface pour créer des clips multimédias. Cet outil peut être intégré à n’importe quelle page web. Il fournit des API pour le chargement d’actifs et l’envoi de travaux de découpage vidéo.

Azure Media Clipper vous permet d’effectuer les actions suivantes :
- Ajuster la pré-séquence et la post-séquence à partir d’archives en temps réel 
- Compiler des moments forts à partir d’événements en direct AMS, d’archives en temps réel ou de fichiers VOD fMP4 
- Concaténer des vidéos à partir de plusieurs sources 
- Produire des clips de synthèse à partir de vos actifs multimédias AMS 
- Découper des vidéos avec une précision au niveau de la trame 
- Générer des filtres de manifestes dynamiques sur des actifs VOD et en temps réel existants avec une précision de groupe d’images (GOP) 
- Effectuer des travaux d’encodage sur les actifs dans votre compte Media Services

Pour demander de nouvelles fonctionnalités ou nous faire part de vos idées ou commentaires, écrivez-nous sur [UserVoice pour Azure Media Services](https://aka.ms/amsvoice/). Si vous avez des problèmes ou des questions spécifiques, ou si vous détectez des bogues, contactez l’équipe Media Services à l’adresse amcinfo@microsoft.com.

L’image suivante illustre l’interface Clipper : ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Notes de publication
Affichez la liste suivante pour consulter le billet de blog Clipper, différents problèmes connus et le journal des modifications de la dernière version de Clipper :
- [Billet de blog](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Liste de problèmes connus](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Journal des modifications](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Prise en charge des navigateurs
Azure Media Clipper repose sur des technologies HTML5 modernes et prend en charge les navigateurs suivants :

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54+
- Safari 10+
- Firefox 50+

> [!NOTE]
> Seule la lecture HTML5 de flux à partir d’Azure Media Services est actuellement prise en charge.

## <a name="language-support"></a>Support multilingue
Le widget Clipper est disponible dans les 18 langues suivantes :
- Chinois (simplifié)
- Chinois (traditionnel)
- Tchèque
- Néerlandais, flamand
- Anglais
- Français
- Allemand
- Hongrois
- Italien
- Japonais
- Coréen
- Polonais
- Portugais (Brésil)
- Portugais (Portugal)
- Russe
- Espagnol
- Suédois
- Turc

## <a name="next-steps"></a>Étapes suivantes
Pour bien démarrer avec Azure Media Clipper et découvrir comment déployer le widget, lisez l’article de [prise en main](media-services-azure-media-clipper-getting-started.md).
