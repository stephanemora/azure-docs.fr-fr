---
title: Azure Communication Services – Problèmes connus
description: Obtenir des informations sur les problèmes connus d’Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628113"
---
# <a name="known-issues-azure-communication-services"></a>Problèmes connus : Azure Communication Services

Cet article fournit des informations sur les problèmes connus associés à Azure Communication Services.

## <a name="video-streaming-quality-on-chromeandroid"></a>Qualité du streaming vidéo sur Chrome/Android 

Les performances du streaming vidéo peuvent être détériorées sur Chrome Android.

### <a name="possible-causes"></a>Causes possibles
La qualité des flux distants dépend de la résolution du convertisseur côté client qui a été utilisé pour initier ce streaming. Quand vous vous abonnez à un flux distant, un destinataire détermine sa propre résolution en fonction des dimensions du convertisseur côté client de l’expéditeur.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Les microphones des casques Bluetooth ne sont pas détectés

Vous pouvez rencontrer des problèmes lors de la connexion de votre casque Bluetooth à un appel via Communication Services.

### <a name="possible-causes"></a>Causes possibles
Il n’est pas possible de sélectionner un microphone Bluetooth sur iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>La commutation répétée de différents appareils vidéo peut entraîner l’arrêt temporaire du streaming vidéo

La commutation de différents appareils vidéo peut entraîner la suspension de votre streaming vidéo pendant l’acquisition du flux à partir de l’appareil sélectionné.

### <a name="possible-causes"></a>Causes possibles
Le streaming et la commutation entre différents appareils multimédias nécessitent beaucoup de ressources de calcul. La commutation fréquente peut entraîner une détérioration des performances. Il est recommandé aux développeurs d’arrêter le streaming sur un appareil avant de le démarrer sur un autre.
