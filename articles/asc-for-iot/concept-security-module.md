---
title: Comprendre Azure Security Center pour les jumeaux de module de sécurité IoT (préversion) | Microsoft Docs
description: En savoir plus sur le concept des jumeaux de module de sécurité et la manière dont ils sont utilisés dans Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 8f964fd2b121c6232081673666f5babb160e1721
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618305"
---
# <a name="security-module"></a>Module de sécurité

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment Azure Security Center (ASC) pour IoT utilise les jumeaux et les modules d’appareils. 

## <a name="device-twins"></a>Jumeaux d’appareil

Pour les solutions IoT intégrées à Azure, les jumeaux d’appareil jouent un rôle essentiel dans la gestion d’appareils et l’automatisation des processus.  

ASC pour IoT offre une intégration complète à votre plateforme de gestion d’appareils IoT existants, ce qui vous permet de gérer l’état de sécurité de vos appareils et de tirer parti des fonctionnalités de contrôle d’appareils existants. L’intégration est obtenue par l’utilisation du mécanisme de jumelage IoT Hub.  

En savoir plus sur le concept de [jumeaux d’appareils](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) dans Azure IoT Hub. 

## <a name="security-module-twins"></a>Jumeaux de module de sécurité

ASC pour IoT conserve un jumeau de module de sécurité pour chaque appareil du service.
Le jumeau de module de sécurité contient toutes les informations relatives à la sécurité de l’appareil pour chaque appareil spécifique de votre solution.
Les propriétés de sécurité d’appareil sont conservées dans un jumeau de module de sécurité dédié, pour une communication plus sûre et pour permettre des mises à jour et une maintenance nécessitant moins de ressources.  

Consultez [Créer un jumeau de module de sécurité](quickstart-create-security-twin.md) et [Configurer des agents de sécurité](how-to-agent-configuration.md) pour découvrir comment créer, personnaliser et configurer le jumeau. Consultez [Comprendre les jumeaux de module IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) pour en savoir plus sur le concept des jumeaux de module dans IoT Hub. 
 

## <a name="see-also"></a>Voir aussi
- [ASC pour IoT (préversion)](overview.md)
- [Déployer des agents de sécurité](how-to-deploy-agent.md)
- [Méthode d'authentification des agents de sécurité](concept-security-agent-authentication-methods.md)