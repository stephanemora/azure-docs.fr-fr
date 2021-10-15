---
title: Notes de publication du Conteneur Azure Percept DK
description: Informations sur les modifications et les correctifs des versions de conteneur Azure Percept DK.
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5b4e67197e5d43e929ca01c8c81e297e134edf47
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271609"
---
# <a name="azure-percept-dk-container-release-notes"></a>Notes de publication du Conteneur Azure Percept DK

Cette page fournit des informations sur les modifications et les correctifs des versions de conteneur Azure Percept DK.

Pour télécharger les mises à jour du conteneur, accédez à [Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview), sélectionnez Appareils dans le volet de navigation de gauche, choisissez l’appareil spécifique, puis sélectionnez les onglets Vision et Speech pour lancer les téléchargements du conteneur. 

## <a name="august-2108-release"></a>Version d’août (2108)

- Azureyemodule (mcr.microsoft.com/azureedgedevices/azureeyemodule:2108-1)
    - Mise à jour vers la dernière version d’Intel (mai) du microprogramme de la caméra MyriadX. 
    - Activation de la caméra UVC (USB Video Class) comme source d’entrée. Consultez [Développement avancé sur GitHub](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source) pour savoir comment utiliser la caméra UVC comme source d’entrée. 
    - Correction du plantage de module lors de l’utilisation d’un flux RTSP brut H.264.

## <a name="june-2106-release"></a>Version de juin (2106)

- Azureyemodule
    - Cette version ajoute la prise en charge de l’alignement du temps des inférences des réseaux neuronaux lents avec les flux vidéo. Elle ajoute une latence dans le flux vidéo approximativement égale à la latence du réseau neuronal, mais fait en sorte que les inférences (par exemple les zones englobantes) soient dessinées sur la vidéo aux emplacements appropriés. 
    - Pour activer cette fonctionnalité, ajoutez `TimeAlignRTSP: true` à votre jumeau de module dans le portail Azure IoT Hub.
- Azureearspeechclientmodule
    - Intégration du [kit SDK Speech 1.16](../cognitive-services/speech-service/devices-sdk-release-notes.md) dans le module Speech, qui comprend des correctifs pour la prise en charge des jetons vocaux et intègre EAR SOM comme appareil pris en charge par défaut dans la bibliothèque de bas niveau.
    - Correction d’un problème de détection PnP lorsque la sécurité MCU a été supprimée mais pas le codec.
    - Résolution des problèmes de délai d’expiration du service Speech avec les fonctions de bouton PTT/PTS.
    - Correctifs de sécurité
        - Lecture hors limites lors de la réception de données TLS dans le module Speech.
        - Les ports USB Codec et MCU sont à nouveau exposés lors de la mise à niveau du microprogramme de l’appareil (mode DFU).
        - Gestion des exceptions lors de l’analyse JSON.
        - Activation de tous les indicateurs de sécurité renforcés du compilateur.
        - Lecture hors limites lors de la réception de données TLS dans le module Speech.
        - Versioning des dépendances SSL et libcurl, et prévention de la version vulnérable.
        - Mise en œuvre du protocole HTTPS et épinglage d’une autorité de certification TLS lors des connexions curl.

## <a name="april-2104-release"></a>Version d’avril (2104)

- Azureyemodule
    - Correction du format de message IoT Hub (passage du format encodé 64 bits au format JSON encodé en UTF-8).
    - Correction d’un bogue lié au classifieur Custom Vision (auparavant, les modèles de classifieur Custom Vision ne fonctionnaient pas correctement ; ils interprétaient la mauvaise dimension du tenseur de sortie comme étant les confiances de la classe, ce qui entraînait toujours la prédiction d’une classe unique, indépendamment de la confiance).
    - Mise à jour de H.264 de façon à utiliser TCP plutôt d’UDP, pour l’intégration d’Azure Video Analyzer.

## <a name="next-steps"></a>Étapes suivantes

- [Comment déterminer votre stratégie de mise à jour](./how-to-determine-your-update-strategy.md)
