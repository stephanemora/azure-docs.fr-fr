---
title: Liste des fonctionnalités du Lecteur multimédia Azure
description: Document de référence sur les fonctionnalités du Lecteur multimédia Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 41e090b9e1d4c091bd3972afd296c5751e6b8c58
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082713"
---
# <a name="feature-list"></a>Liste des fonctionnalités #
Voici la liste des fonctionnalités testées et des fonctionnalités non prises en charge :

| Fonctionnalité | TESTÉE | PARTIELLEMENT TESTÉE | NON TESTÉE | NON PRISE EN CHARGE | REMARQUES |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| Lecture                                |        |                  |          |             |                                                                                                                      |
| Lecture à la demande de base                | X      |                  |          |             | Prend uniquement en charge les flux en provenance d'Azure Media Services                                                                      |
| Lecture en direct de base                     | X      |                  |          |             | Prend uniquement en charge les flux en provenance d'Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Prend en charge le service de remise de clés d'Azure Media Services                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Prend en charge le service de remise de clés d'Azure Media Services                                                                   |
| Widevine                                |        | X                |          |             | Prend en charge les zones PSSH Widevine décrites dans le manifeste                                                                    |
| FairPlay                                |        | X                |          |             | Prend en charge le service de remise de clés d'Azure Media Services                                                                   |
| Technologies                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback (FlashSS)                | X      |                  |          |             | Toutes les fonctionnalités ne sont pas disponibles avec cette technologie.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Toutes les fonctionnalités ne sont pas disponibles avec cette technologie.                                                                         |
| Transmission directe HLS native (HTML5)         |        | X                |          |             | Toutes les fonctionnalités ne sont pas disponibles avec cette technologie pour cause de restrictions de plateforme.                                            |
| Fonctionnalités                                |        |                  |          |             |                                                                                                                      |
| Prise en charge des API                             | X      |                  |          |             | Voir la liste des problèmes connus                                                                                                |
| Interface utilisateur de base                                | X      |                  |          |                                                                                                                                    |
| Initialisation via JavaScript       | X      |                  |          |             |                                                                                                                      |
| Initialisation par balise vidéo        |        | X                |          |             |                                                                                                                      |
| Adressage de segment - Basé sur l'heure         | X      |                  |          |             |                                                                                                                      |
| Adressage de segment - Basé sur un index        |        |                  |          | X           |                                                                                                                      |
| Adressage de segment - Basé sur les octets         |        |                  |          | X           |                                                                                                                      |
| Réécriture d'URL Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Accessibilité - Légendes et sous-titres  |        | X                |          |             |  WebVTT pris en charge pour les requêtes à la demande, CEA-708 en direct partiellement testé                                                       |
| Accessibilité - Touches d'accès rapide                 | X      |                  |          |             |                                                                                                                      |
| Accessibilité - Contraste élevé           |        | X                |          |             |                                                                                                                      |
| Accessibilité - Focus touche Tab               |        | X                |          |             |                                                                                                                      |
| Messages d'erreur                         |        | X                |          |             | Messages d'erreur non cohérents entre les technologies                                                                         |
| Déclenchement d'événements                        | X      |                  |          |             |                                                                                                                      |
| Diagnostics                             |        | X                |          |             | Les informations de diagnostic sont uniquement disponibles avec la technologie AzureHtml5JS, et partiellement disponibles avec la technologie SilverlightSS. |
| Ordre des technologies personnalisable                 |        | X                |          |             |                                                                                                                      |
| Heuristique - De base                      | X      |                  |          |             |                                                                                                                      |
| Heuristique - Personnalisation              |        |                  | X        |             | La personnalisation est uniquement disponible avec la technologie AzureHtml5JS.                                                          |
| Discontinuités                         | X      |                  |          |             |                                                                                                                      |
| Sélectionner la vitesse de transmission                          | X      |                  |          |             | Cette API est uniquement disponible avec les technologies AzureHtml5JS et FlashSS.                                                    |
| Flux audio multiples                      |        | X                |          |             | Le commutateur audio programmatique est pris en charge avec les technologies AzureHtml5JS et FlashSS, et est disponible par sélection sur l'interface utilisateur avec AzureHtml5JS, FlashSS et HTML5 natif (dans Safari).  La plupart des plateformes requièrent les mêmes données privées de codec pour basculer les flux audio (même codec, canal, taux d'échantillonnage, etc.). |
| Localisation de l'interface utilisateur                         |        | X                |          |             |                                                                                                                      |
| Lecture multi-instance                 |        |                  |          | X           | Ce scénario peut fonctionner avec certaines technologies mais il n'est actuellement pas pris en charge et n'a pas été testé. Vous pouvez également en bénéficier avec des iframes. |
| Prise en charge des publicités                             |        | x                |          |             | Le Lecteur multimédia Azure prend en charge l'insertion de publicités linéaires pré-, mi- et post- déploiement à partir de serveurs publicitaires compatibles VAST pour la VOD avec la technologie AzureHtml5JS |
| Analytics                               |        | X                |          |             | Le Lecteur multimédia Azure permet d'écouter des événements d'analyse et de diagnostic afin de les envoyer au backend Analytics de votre choix.  Tous les événements et propriétés ne sont pas disponibles avec certaines technologies pour cause de limitations de plateforme.                                                                            |
| Apparences personnalisées                            |        |                  | X        |             | Attribuez aux contrôles la valeur false dans le Lecteur multimédia Azure et utilisez votre propre code HTML ou CSS.           |
| Lecture à vitesse variable sur la barre de recherche                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Audio uniquement                              |        |                  |          | X           | Cette fonctionnalité peut fonctionner avec certaines technologies pour la diffusion en continu adaptative, mais elle n'est actuellement pas prise en charge et ne fonctionne pas avec AzureHtml5JS. La lecture MP3 progressive peut fonctionner avec la technologie HTML5 si la plateforme la prend en charge.                                                                                                        |
| Vidéo uniquement                              |        |                  |          | X           | Cette fonctionnalité peut fonctionner avec certaines technologies pour la diffusion en continu adaptative, mais elle n'est actuellement pas prise en charge et ne fonctionne pas avec AzureHtml5JS.      |
| Présentation sur plusieurs périodes               |        |                  |          | X                                                                                                                                  |
| Angles de vue multiples                  |        |                  |          | X           |                                                                                                                      |
| Vitesse de lecture                          |        | X                |          |             | La vitesse de lecture est prise en charge dans la plupart des scénarios, à l'exception du scénario mobile en raison d'un bogue partiel de Chrome                 |

## <a name="next-steps"></a>Étapes suivantes ##
- [Démarrage rapide du Lecteur multimédia Azure](azure-media-player-quickstart.md)