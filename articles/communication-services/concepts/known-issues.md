---
title: Azure Communication Services – Questions fréquentes (FAQ) / Problèmes connus
description: En savoir plus sur Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493641"
---
# <a name="faq--known-issues"></a>Questions fréquentes (FAQ) / Problèmes connus
Cet article fournit des informations sur les problèmes connus et les questions fréquentes concernant Azure Communication Services.

## <a name="faq"></a>Forum aux questions

### <a name="why-is-the-quality-of-my-video-degraded"></a>Pourquoi la qualité de ma vidéo est-elle dégradée ?

La qualité des flux vidéo est déterminée par la taille du renderer côté client qui a été utilisé pour lancer ce flux. Quand vous vous abonnez à un flux distant, un destinataire détermine sa propre résolution en fonction des dimensions du convertisseur côté client de l’expéditeur.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Pourquoi n’est-il pas possible d’énumérer/sélectionner des appareils micro/haut-parleur sur Safari ?

Les applications ne peuvent pas énumérer/sélectionner des appareils micro/haut-parleur (comme Bluetooth) sur Safari sur iOS/iPad. Il s’agit d’une limitation du système d’exploitation : il n’y a toujours qu’un seul appareil.

Pour Safari sur MacOS - l’application ne peut pas énumérer/sélectionner le haut-parleur via le Gestionnaire d’appareils de Communication Services : celui-ci doit être sélectionné via le système d’exploitation. Si vous utilisez Chrome sur MacOS, l’application peut énumérer/sélectionner des appareils via le Gestionnaire d’appareils de Communication Services.

## <a name="known-issues"></a>Problèmes connus

Cette section fournit des informations sur les problèmes connus associés à Azure Communication Services.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>La commutation répétée de différents appareils vidéo peut entraîner l’arrêt temporaire du streaming vidéo

La commutation de différents appareils vidéo peut entraîner la suspension de votre streaming vidéo pendant l’acquisition du flux à partir de l’appareil sélectionné.

#### <a name="possible-causes"></a>Causes possibles
Le streaming et la commutation entre différents appareils multimédias nécessitent beaucoup de ressources de calcul. La commutation fréquente peut entraîner une détérioration des performances. Il est recommandé aux développeurs d’arrêter le streaming sur un appareil avant de le démarrer sur un autre.
