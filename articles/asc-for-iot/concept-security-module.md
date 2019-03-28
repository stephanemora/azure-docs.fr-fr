---
title: Comprendre ASC pour jumeaux de module de sécurité IoT Preview | Microsoft Docs
description: En savoir plus sur le concept des jumeaux de module de sécurité et comment elles sont utilisées dans ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541841"
---
# <a name="security-module"></a>Module de sécurité

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment ASC pour IoT utilise les modules et les représentations d’appareil. 

## <a name="device-twins"></a>Jumeaux d’appareil

Pour les solutions de IoT créées dans Azure, les représentations d’appareil jouent un rôle clé dans la gestion des appareils et d’automatisation des processus.  

ASC pour IoT offre une intégration complète avec votre plateforme de gestion de périphérique IoT existant, ce qui vous permet de gérer l’état de sécurité de votre appareil, ainsi que la marque, utiliser des fonctionnalités de contrôle d’appareil existant. Intégration s’effectue à servant d’IoT Hub mécanisme de représentations réussies.  

En savoir plus sur le concept de [appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) jumeaux d’Azure IoT Hub. 

## <a name="security-module-twins"></a>Jumeaux de module de sécurité

ASC pour IoT conserve un jumeau de module de sécurité pour chaque appareil dans le service. La représentation de module de sécurité conserve toutes les informations relatives à la sécurité des appareils pour chaque périphérique spécifique dans votre solution. Propriétés de sécurité d’appareil sont conservées dans un jumeau de module de sécurité dédié pour la communication plus sûre et l’activation des mises à jour et maintenance nécessitant moins de ressources.  

Consultez [jumeau de module de sécurité créer](quickstart-create-security-twin.md) et [configurer des agents de sécurité](concept-agent-configuration.md) pour savoir comment créer, personnaliser et configurer la représentation. Consultez [jumeaux de module de présentation](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) pour en savoir plus sur le concept des jumeaux de module dans IoT Hub. 
 

## <a name="see-also"></a>Voir aussi
- [ASC pour IoT Preview](overview.md)
- [Déployer des agents de sécurité](select-deploy-agent.md)
- [Méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)