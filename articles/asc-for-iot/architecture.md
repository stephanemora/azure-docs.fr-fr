---
title: Présentation d’Azure Security Center pour l’architecture de solution IoT Preview | Microsoft Docs
description: En savoir plus sur le flux d’informations dans le centre de sécurité Azure pour le service IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862363"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center pour l’architecture IoT

Cet article explique l’architecture système fonctionnel d’Azure Security Center (ASC) pour la solution IoT. 

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC pour les composants de l’IoT

ASC pour IoT est constitué des composants suivants :
- Agents de l’appareil
- Envoyer le message de sécurité SDK
- Intégration d’IoT Hub
- Pipeline d’Analytique
 
### <a name="asc-for-iot-workflow"></a>ASC pour les flux de travail IoT

ASC pour les agents d’appareil IoT vous permet de recueillir des événements de sécurité brute à partir de vos appareils. Événements de sécurité brute peuvent inclure des connexions IP, la création du processus, les connexions utilisateur et autres informations liées à la sécurité. ASC pour les agents d’appareil IoT également gérer l’agrégation d’événements afin d’éviter le débit du réseau élevé. Les agents sont hautement personnalisables, vous permettant ainsi à les utiliser pour des tâches spécifiques, telles que l’envoi uniquement des informations importantes sur le contrat SLA le plus rapide, ou pour agréger les informations de sécurité étendues et le contexte en segments plus importants, en évitant les coûts de service plus élevés.
 
Agents de l’appareil et d’autres applications utilisent le **Azure ASC envoyer le message de sécurité SDK** pour envoyer des informations de sécurité dans Azure IoT Hub. IoT Hub récupère ces informations et la transfère à l’ASC pour le service IoT.

Une fois l’ASC pour le service IoT est activé, outre les données transférées, IoT Hub envoie également toutes ses données internes pour l’analyse par ASC pour IoT. Ces données incluent les journaux des opérations de cloud à l’appareil, les identités des appareils et la configuration du Hub. Toutes ces informations permet de créer l’ASC pour le pipeline d’analytique IoT.
 
ASC pour le pipeline d’analytique IoT reçoit également des flux d’intelligence des menaces supplémentaires à partir de diverses sources au sein de Microsoft et partenaires. L’ASC pour le pipeline d’analytique ensemble IoT fonctionne avec toutes les configurations client effectuées sur le service (par exemple, des alertes personnalisées et l’utilisation de l’envoi de messages de sécurité SDK).
 
À l’aide du pipeline d’analytique, ASC pour IoT combine tous les flux d’informations pour générer des alertes et des recommandations exploitables. Le pipeline contient deux règles personnalisées créées par les chercheurs en sécurité et les experts, ainsi que les modèles recherche d’écart par rapport à l’analyse de comportement et les risques de périphérique standard d’apprentissage.
 
ASC pour IoT recommandations et alertes (sortie du pipeline analytique) est écrit dans l’espace de travail Analytique de journal de chaque client. Y compris les événements bruts dans l’espace de travail, ainsi que les alertes et les recommandations permet les enquêtes de présentation approfondie et des requêtes à l’aide des informations précises concernant les activités suspectes détectées.  

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à l’architecture de base et le flux de travail de ASC solution IoT. Pour en savoir plus sur la configuration requise, la prise en main et activer votre solution de sécurité dans IoT Hub, consultez les articles suivants :

- [Prérequis du service](service-prerequisites.md)
- [Prise en main](getting-started.md)
- [Configurer votre solution](quickstart-configure-your-solution.md)
- [Activer la sécurité dans IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC pour IoT Forum aux questions](resources-frequently-asked-questions.md)
- [ASC pour les alertes de sécurité IoT](concept-security-alerts.md)

