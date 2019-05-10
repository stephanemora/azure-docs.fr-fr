---
title: Recommandations de tableau microphone Speech SDK d’appareils - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Recommandations de tableau microphone Speech SDK d’appareils. Les géométries de tableau suivantes sont recommandées pour une utilisation avec la pile Audio de Microsoft. Emplacement des sources de l’audio et rejet de bruit ambiant sont améliorés avec un plus grand nombre de microphones avec des dépendances des applications, les scénarios utilisateur et le facteur de forme de périphérique.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 63dd64e900cf68e708032569ca75ac2e8b221491
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237008"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recommandations de tableau vocale Microphone du Kit de développement logiciel des appareils

Dans cet article, vous allez apprendre à concevoir un tableau de microphone pour les appareils de Speech SDK.

Les appareils de Speech SDK fonctionne mieux avec un tableau de microphone a été conçu selon les consignes suivantes, y compris la sélection de composant et de la géométrie du microphone. Est également fournie sur l’intégration et de considérations électriques.

## <a name="microphone-geometry"></a>Géométrie du microphone

Les géométries de tableau suivantes sont recommandées pour une utilisation avec la pile Audio de Microsoft. Emplacement des sources de l’audio et rejet de bruit ambiant sont améliorés avec un plus grand nombre de microphones avec des dépendances des applications, les scénarios utilisateur et le facteur de forme de périphérique.

|          | Tableau circulaire    |       |  Tableau linéaire              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Micros  | 7                 | 4                 | 4              | 2              |
| Géométrie | 6 1 externe, centre, Radius = mm 42,5, espacées de manière égale| 3 1 externe, centre, Radius = mm 42,5, espacées de manière égale | Longueur = 120 mm, espacement = 40 mm | Espacement = 40 mm |

Les canaux de microphone doivent être classés en fonction de la numérotation mentionnés pour chaque au-dessus de tableau, l’augmentation de 0.  La pile Audio Microsoft nécessiteront un flux de référence supplémentaires de la lecture audio pour effectuer l’annulation de l’écho.

## <a name="component-selection"></a>Sélection de composant

Composants du microphone doivent être sélectionnés pour reproduire un signal gratuit de bruit et distorsion de manière précise.

Les propriétés recommandées lors de la sélection des microphones sont :

| Paramètre                         | Recommandé                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz signal 94 dBSPL, pondéré de bruit)   |
| Mise en correspondance amplitude                | + 1 dB @ 1 kHz                     |
| Phase de correspondance                    | + 2° @ 1 kHz                       |
| Point de surcharge acoustiques (AOP)     | \> dBSPL 120 (THD = 10 %)          |
| Vitesse de transmission                          | Au moins 24 bits                    |
| Taux d’échantillonnage                     | Minimum 16 kHz\*                   |
| Directivité                       | Omnidirectionnels                   |
| Réponse en fréquence                | + 3 dB, 8000-200 Hz flottante masque\*|
| Fiabilité                       | Plage de température de stockage-40 ° C à 70 ° C<br />Température de fonctionnement-20 ° C à 55 ° C  |

*\*Taux d’échantillonnage plus élevés ou des plages de fréquence « plus larges » peuvent être nécessaires pour les applications de haute qualité communications VoIP)*

Sélection de composant doit être associée à une bonne intégration électroacoustique afin d’éviter ce qui altère les performances des composants utilisés. Cas d’usage unique peuvent nécessiter également des exigences supplémentaires (par exemple : fonctionnement des plages de température).

## <a name="microphone-array-integration"></a>Intégration de tableau microphone

Les performances des tableaux lorsque intégrées à un appareil et après une gain fixe ou une EQ doivent respecter les recommandations suivantes :

|  Paramètre        |    Recommandé |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz signal 94 dBSPL, pondéré de bruit) |
|  Sensibilité de sortie  | -26 dBFS/Pa @ 1 kHz (recommandé) |
|  Mise en correspondance amplitude  | + 2 dB, 8000-200 Hz |
|  Phase de correspondance      | + 5°, 8000-200 Hz |
| THD %                 | ≤ 1 %, 8000-200 Hz, dBSPL 94, ordre 5 |
|  Réponse en fréquence  | + 6 dB, 8000-200 Hz flottante masque\* |

*\*Les plages de fréquence « Plus larges » peuvent s’avérer nécessaires pour les applications de haute qualité communications VoIP)*

## <a name="speaker-integration-recommendations"></a>Recommandations de l’intégration de l’orateur

Annulation de l’écho est nécessaire pour les appareils de reconnaissance vocale qui contiennent des haut-parleurs, des recommandations supplémentaires sont fournies pour l’intégration et de sélection de l’intervenant.

| Paramètre                         | Recommandé                       |
|-----------------------------------|-----------------------------------|
| Considérations relatives à la linéarité          | Aucun traitement non linéaire après la référence de l’orateur, sinon un flux de données de référence de bouclage en fonction du matériel est requis  |
| Bouclage de l’orateur                  | Fourni par WASAPI, l’API privées personnalisé ALSA plug-in (Linux), ou fourni via un canal du microprogramme      |
| THD %                              | 3e octave bandes 5e commande minimale, 70 dBA lecture @ 0,8 ≤ 6.3 m%, 315-500 Hz ≤ 5 %, 630-5000 Hz                 |
| COUPLAGE d’écho à Microphones      | \> -10 dB TCLw à l’aide de la méthode ITU-T G.122 annexe B.4, normalisées au niveau de mic<br />TCLw = TCLwmeasured \+ (mesurée niveau - de cibler la sensibilité de sortie)<br />TCLw = TCLwmeasured \+ (mesurée niveau - (-26)) |

## <a name="integration-design-architecture"></a>Architecture d’intégration de conception

Les instructions suivantes pour l’architecture sont nécessaires lors de l’intégration de microphones dans un appareil :

| Paramètre                         | Recommandation                    |
|-----------------------------------|-----------------------------------|
| Similarité de Port de MIC               | Tous les ports de microphone sont de même longueur de tableau    |
| Dimensions de Port de MIC               | Port taille Ø0.8-1.0 mm. Longueur de port / Port diamètre \< 2              |
| Scellement de MIC                       | Joint à uniformément implémentée dans le haut de la pile. Recommandons \> 70 % des taux de compression pour joints de mousse     |
| Fiabilité de MIC                   | Maille doit être utilisé pour empêcher la poussière et entrée (entre la carte de circuits imprimés pour bas porté microphones et la fermeture de joint/capot supérieur)  |
| Isolation de MIC                     | Joints de caoutchouc et le découplage de vibration via la structure, en particulier pour isoler les chemins de vibration en raison des haut-parleurs intégrés      |
| Horloge de l’échantillonnage                    | Audio du périphérique doit être libre d’instabilité et les abandons avec dérive faible    |
| Possibilité d’enregistrement                 | L’appareil doit être capable d’enregistrer le flux de données brutes de canal individuelle simultanément |
| USB                               | Tous les périphériques d’entrée audio USB doivent définir les descripteurs conformément à la [spécification de Rev3 périphériques Audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Géométrie du microphone               | Pilotes doivent implémenter [Microphone tableau Geometry descripteurs](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) correctement  |
| Détectabilité                   | Les appareils ne doivent pas avoir n’importe quel matériel indétectables ou incontrôlable, de microprogramme ou des algorithmes de traitement audio non linéaire basé sur logiciel tiers 3e vers/à partir de l’appareil|
| Format de capture                    | Formats de capture doivent utiliser un taux de minimales d’échantillonnage de 16 kHz et profondeur de 24 bits recommandée      |

## <a name="electrical-architecture-considerations"></a>Considérations relatives à une architecture électrique

Le cas échéant, les tableaux peuvent être connectés à un hôte USB (par exemple, SoC qui exécute la pile Audio de Microsoft) et des interfaces pour les services de reconnaissance vocale ou d’autres applications.

Composants matériels tels que de la conversion de PDM-TDM devraient vous assurer que la plage dynamique et le rapport signal-bruit des microphones est conservé au sein des échantillonneurs de nouveau.

Haute vitesse 2.0 de classe Audio USB doit être pris en charge dans n’importe quel MCU audio afin de fournir la bande passante nécessaire pour jusqu'à sept canaux au taux d’échantillonnage plus élevées et la résolution en bits.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les appareils de Speech SDK](speech-devices-sdk.md)
