---
title: Déterminer votre stratégie de mise à jour pour Azure Percept DK
description: Avantages et inconvénients respectifs des mises à jour d’Azure Percept DK OTA ou via un câble USB. Recommandation pour choisir la meilleure approche de mise à jour pour différents utilisateurs.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c26de68bc30e8ebfa6de92c8e142e6ca69088b26
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223352"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>Déterminer votre stratégie de mise à jour pour Azure Percept DK

Pour maintenir votre logiciel Azure Percept DK à jour, Microsoft propose deux méthodes de mise à jour du kit de développement. **Mise à jour via un câble USB** ou **Mise à jour par voie hertzienne (Over-The-Air, OTA)** .

La mise à jour via un câble USB effectue une nouvelle installation du kit de développement. Les configurations existantes et toutes les données utilisateur dans chaque partition sont effacées après le déploiement de la nouvelle image. Pour ce faire, connectez le kit de développement à un système hôte à l’aide d’un câble USB de type C. Le système hôte peut être un ordinateur Windows ou Linux.  Vous pouvez également utiliser cette méthode de mise à jour pour effectuer une réinitialisation aux paramètres d’usine. Pour ce faire, redéployez exactement la même version vers le kit de développement. Pour plus de détails sur la mise à jour via câble USB, consultez [Mettre à jour Azure Percept DK via une connexion par câble USB-C](./how-to-update-via-usb.md).

La mise à jour OTA s’appuie sur le service Azure [Device Update pour IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-resources). Pour effectuer ce type de mise à jour, connectez le kit de développement au service Azure IoT Hub . Après une mise à jour OTA, les configurations et les données utilisateur sont conservées. Pour plus d’informations sur la mise à jour OTA, consultez [Mettre à jour votre Azure Percept DK par voie hertzienne (OTA)](./how-to-update-over-the-air.md).

Vérifiez les avantages et inconvénients respectifs des mises à jour via câble USB et OTA, puis suivez les recommandations de Microsoft pour les différents scénarios.

## <a name="usb-cable-update"></a>Mise à jour via câble USB

- Avantages
  - Vous n’avez pas besoin de connecter le kit de développement à Internet ou à Azure.
  - L’image la plus récente est toujours applicable, quelle que soient les versions du logiciel et du microprogramme actuellement chargées sur le kit de développement.
- Inconvénients
  - La mise à jour remplace l’image sur l’appareil et supprime les configurations ainsi que les données utilisateur.
  - Vous devez réexécuter OOBE et télécharger tout conteneur non préchargé.
  - La mise à jour ne peut pas être effectuée à distance.

## <a name="ota-update"></a>Mise à jour OTA

- Avantages
  - Préserve les données utilisateur, les configurations et les conteneurs téléchargés. Après une mise à jour OTA, le kit de développement continue de fonctionner comme avant.
  - La mise à jour peut être effectuée à distance.
  - Vous pouvez mettre à jour plusieurs appareils similaires en même temps. Vous pouvez également planifier les mises à jour, par exemple, pendant la nuit.
- Inconvénients
  - Certaines versions à arrêt forcé ne peuvent pas être ignorées. Consultez [Version à arrêt forcé de mise à jour OTA](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota).
  - L’appareil doit se connecter à un IoT Hub sur lequel la fonctionnalité « Device Update pour IoT Hub » est correctement configurée.
  - La mise à jour ne fonctionne pas bien pour passer à une version antérieure.

> [!IMPORTANT]
> Device Update pour IoT Hub ne bloque pas le déploiement d’une image dont la version est antérieure à celle du système d’exploitation en cours d’exécution. En revanche, ce mode de mise à jour du kit de développement entraîne une perte de données et de fonctionnalités.

## <a name="microsoft-recommendations"></a>Recommandations de Microsoft

|Type|Scénario|Update, méthode|
|:---:|---|:---:|
|Production|Maintien du kit de développement à jour sur le plan des correctifs et des correctifs de sécurité les plus récents quand il exécute déjà votre solution ou est déjà déployé sur le terrain.|OTA|
|Production/développement|Conversion unboxing d’un nouveau kit de développement et mise à jour vers le logiciel le plus récent.|USB|
|Production/développement|Mise à jour vers la dernière version du logiciel alors que vous avez ignoré plusieurs mises à jour mensuelles.|USB|
|Production/développement|Réinitialisation aux paramètres d’usine d’un kit de développement.|USB|
|Développer|Pendant le développement d’une solution, maintien à jour du système d’exploitation et F/W du kit de développement.|USB/OTA|
|Développer|Accès à une version spécifique (plus ancienne) pour le débogage ou l’investigation de problèmes.|USB|

## <a name="next-steps"></a>Étapes suivantes

Après avoir choisi la méthode de mise à jour de votre choix, visitez les pages suivantes pour vous préparer à la mise à jour :

Mise à jour via câble USB

- [Mettre à jour Azure Percept DK via une connexion par câble USB C](./how-to-update-via-usb.md)
- [Versions d’Azure Percept DK pour une mise à jour via un câble USB](./software-releases-usb-cable-updates.md)

OTA

- [Mettre à jour votre DK Azure Percept en OTA](./how-to-update-over-the-air.md)
- [Versions logicielles d’Azure Percept DK pour une mise à jour OTA](./software-releases-over-the-air-updates.md)
