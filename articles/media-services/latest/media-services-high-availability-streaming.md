---
title: Diffusion en continu à haute disponibilité d’Azure Media Services
description: Découvrez comment basculer vers un compte Media Services secondaire en cas de panne ou de défaillance d’un centre de données régional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898987"
---
# <a name="media-services-high-availability-streaming"></a>Diffusion en continu à haute disponibilité de Media Services

Actuellement, Azure Media Services n’assure pas de basculement instantané du service en cas de panne ou de défaillance d’un centre de données régional du composant sous-jacent ou des services dépendants. Cet article fournit des conseils sur la création d’une diffusion en continu de vidéo à la demande entre régions.

## <a name="prerequisites"></a>Prérequis

Passez en revue [Comment créer un système d’encodage interrégion](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Comment générer un streaming vidéo à la demande interrégion 

* Le streaming vidéo à la demande interrégion implique la duplication des [actifs multimédias](assets-concept.md), des [stratégies de clé de contenu](content-key-policy-concept.md) (si elles sont utilisées), des [stratégies de streaming](streaming-policy-concept.md) et des [localisateurs de streaming](streaming-locators-concept.md). 
* Vous devrez créer les stratégies dans les deux régions et les tenir à jour. 
* Quand vous créez les localisateurs de streaming, vous devez utiliser les mêmes valeurs d’ID de localisateur, d’ID de ContentKey et de ContentKey.  
* Si vous encodez le contenu, nous vous recommandons de l’encoder dans la région A et de le publier, puis de copier le contenu encodé dans la région B et de le publier en utilisant les mêmes valeurs que celles de la région A.
* Vous pouvez utiliser Traffic Manager sur les noms d’hôtes pour l’origine et le service de remise de clés (dans la configuration Media Services, cela ressemble à une URL de serveur de clés personnalisée).

## <a name="next-steps"></a>Étapes suivantes

Consulter :

* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu](stream-files-dotnet-quickstart.md)
* [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
