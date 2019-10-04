---
title: Comprendre les coûts d’Azure Security Center pour IoT | Microsoft Docs
description: En savoir plus sur les coûts associés à Azure Security Center pour IoT et la manière dont vous pouvez les contrôler.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: e742848df56e4e2be1b9edf42586e642f0c938cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933806"
---
# <a name="pricing-and-associated-costs"></a>Tarifs et coûts associés

Cet article explique le modèle de tarification d’Azure Security Center (ASC) pour IoT, récapitule tous les coûts associés et explique comment les gérer.

## <a name="pricing"></a>Tarifs

Le modèle de tarification Azure Security Center pour IoT se compose de deux parties et est facturé une fois qu’un IoT Hub est [activé](quickstart-onboard-iot-hub.md) dans Azure Security Center pour IoT :

- Coût par appareil : fonctionnalités de sécurité intégrées basées sur l’analyse des journaux IoT Hub.

- Coût par message : fonctionnalités de sécurité améliorées basées sur les messages de sécurité à partir d’appareils IoT Edge ou leaf.

  >[!Note]
  > Les messages de sécurité entraînent également une consommation du quota sur IoT Hub.

Pour plus d’informations, consultez la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Coûts associés

Deux types de coûts, qui ne font pas partie de la tarification directe, sont associés à Azure Security Center pour IoT :

- Consommation de quota IoT Hub

- Coûts de stockage Log Analytics

Vous pouvez réduire les coûts associés en désactivant certaines fonctionnalités, en modifiant vos paramètres.

Pour changer vos paramètres :

1. Ouvrez IoT Hub.

2. Sous **Sécurité**, cliquez sur **Vue d’ensemble**.

3. Cliquez sur **Settings**.

Le tableau suivant fournit un récapitulatif des coûts associés et des implications de chaque option.

|     | Usage | Commentaire |
| --- | --- | --- |
| **Consommation de quota IoT Hub** |  |
| Travail [Exporter l’appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) (exportation de représentation) | 1 par jour | Désactiver la _collection de métadonnées de représentations_ |
| **Stockage Log Analytics** |  |
| Alertes et recommandations de l’appareil| Alertes et recommandations de sécurité générées par le service | Obligatoire |
| Données de sécurité brutes| Données de sécurité brutes des appareils IoT, collectées par des agents de sécurité | Désactiver le _stockage d’événements bruts de sécurité d’appareil_ |
|

>[!Important]
> La désactivation a des implications graves pour la disponibilité des fonctionnalités de sécurité Azure Security Center pour IoT. 
  
| Désactiver | Implications |
| --- | --- |
| _Collection de métadonnées de représentations_ | Désactiver les [alertes personnalisées](quickstart-create-custom-alerts.md) |
| | Désactiver les recommandations de manifeste IoT Edge |
| | Désactiver les alertes et les recommandations basées sur l’identité d’appareil |
| _Stocker les événements bruts de sécurité d’appareil_ | Les détails des recommandations de base du système d’exploitation d’appareil ne sont pas disponibles |
| | Les détails sur les enquêtes relatives à [l’alerte](concept-security-alerts.md) et à la [recommandation](concept-recommendations.md) ne sont pas disponibles |
|


## <a name="see-also"></a>Voir aussi

- Accéder à vos [données de sécurité brutes](how-to-security-data-access.md)
- [Investiguer un appareil](how-to-investigate-device.md)
- Comprendre et explorer les [recommandations de sécurité](concept-recommendations.md)
- Comprendre et explorer les [alertes de sécurité](concept-security-alerts.md)
