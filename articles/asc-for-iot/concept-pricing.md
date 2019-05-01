---
title: Comprendre Azure Security Center pour IoT coûte Preview | Microsoft Docs
description: En savoir plus sur les coûts associés à Azure Security Center pour IoT et comment vous pouvez les contrôler.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 00666e27c9251248aa6ecff75d88908baabf71f3
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919724"
---
# <a name="pricing-and-associated-costs"></a>Tarifs et coûts associés

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique Azure Security Center (ASC) pour le modèle de tarification IoT, récapitule tous les coûts associés et explique comment les gérer.

## <a name="pricing"></a>Tarifs

L’ASC pour IoT, modèle de tarification se compose de deux parties et est facturée une fois un IoT Hub [activé](quickstart-onboard-iot-hub.md) dans ASC pour IoT :

- Coût par appareil - fonctionnalités de sécurité intégrées basées sur l’analyse des journaux de IoT Hub.

- Coût par message - les capacités améliorées de sécurité basées sur les messages de sécurité à partir d’appareils IoT Edge ou feuille.

  >[!Note]
  > Messages de sécurité entraîne également la consommation de quota sur IoT Hub.

Pour plus d’informations, consultez [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Coûts associés

ASC pour IoT a deux types de coûts associés, qui ne font pas partie de la tarification directe :

- Consommation de quota de IoT Hub

- Les coûts de stockage Analytique de journal

Vous pouvez réduire les coûts associés en n’optant pas certaines fonctionnalités, en modifiant vos paramètres.

Pour modifier vos paramètres :

1. Ouvrez IoT Hub.

2. Sous **sécurité**, cliquez sur **vue d’ensemble**.

3. Cliquez sur **Settings**.

Le tableau suivant fournit un récapitulatif des coûts associés et les implications de chaque option.

|     | Usage | Commentaire |
| --- | --- | --- |
| **Consommation de quota de IoT Hub** |  |
| [Exporter l’appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) travail (exportation de représentation) | 1 par jour | Désactiver _collection de métadonnées de représentations réussies_ |
| **Stockage d’Analytique de journal** |  |
| Alertes et des recommandations de l’appareil| Recommandation de sécurité et les alertes générées par le service | Non facultatif |
| Données de sécurité brute| Données de sécurité brute à partir d’appareils IoT, collectées par les agents de sécurité | Désactiver _stocker les événements de sécurité de périphérique brut_ |

>[!Important]
> Opt-out a des conséquences importantes pour les fonctionnalités de sécurité disponibles.
  
| Désactiver | Implications |
| --- | --- |
| _Collection de métadonnées de représentation_ | Désactiver [alertes personnalisées](quickstart-create-custom-alerts.md) |
| | Désactiver les recommandations de manifeste IoT Edge |
| | Désactiver les alertes et les recommandations basée sur l’identité d’appareil |
| _Événements de sécurité de périphérique brut Store_ | Plus d’informations sur les recommandations de périphérique du système d’exploitation de base ne sont pas disponibles |
| | Détails sur [alerte](concept-security-alerts.md) et [recommandation](concept-recommendations.md) enquêtes ne sont pas disponibles |


## <a name="see-also"></a>Voir aussi

- Accès votre [les données de sécurité brute](how-to-security-data-access.md)
- [Examiner un appareil](how-to-investigate-device.md)
- Comprendre et Explorer [recommandations de sécurité](concept-recommendations.md)
- Comprendre et Explorer [alertes de sécurité](concept-security-alerts.md)
