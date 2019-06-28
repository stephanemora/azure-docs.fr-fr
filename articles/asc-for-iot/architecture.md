---
title: Comprendre l’architecture de la solution Azure Security Center pour IoT en préversion | Microsoft Docs
description: En savoir plus sur le flux d’informations dans le service Azure Security Center pour IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 3c775c8cb99b056c7f468c028a80dd0b27ae6438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200691"
---
# <a name="azure-security-center-for-iot-architecture"></a>Architecture d’Azure Security Center pour IoT

Cet article explique l’architecture système fonctionnelle de la solution Azure Security Center (ASC) pour IoT. 

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>Composants d’ASC pour IoT

ASC pour IoT est composé des éléments suivants :
- Agents d’appareil
- SDK d’envoi de message de sécurité
- Intégration d’IoT Hub
- Pipeline d’analytique
 
### <a name="asc-for-iot-workflow"></a>Flux de travail d’ASC pour IoT

Les agents d’appareil ASC pour IoT vous permettent de recueillir facilement des événements de sécurité bruts à partir de vos appareils. Les événements de sécurité bruts peuvent inclure des connexions IP, la création de processus, des connexions utilisateur et d’autres informations liées à la sécurité. Les agents d’appareil ASC pour IoT gèrent également l’agrégation d’événements afin d’éviter un débit réseau élevé. Les agents sont hautement personnalisables, vous permettant ainsi de les utiliser pour des tâches spécifiques, comme l’envoi d’informations importantes uniquement sur le contrat SLA le plus rapide, ou l’agrégation du contexte et des informations de sécurité étendues en segments plus volumineux, pour éviter des coûts de service plus élevés.
 
Les agents d’appareil et d’autres applications utilisent le **SDK d’envoi de message de sécurité Azure ASC** pour envoyer des informations de sécurité dans Azure IoT Hub. IoT Hub récupère ces informations et les transmet au service ASC pour IoT.

Une fois le service ASC pour IoT activé, outre les données transférées, IoT Hub envoie également toutes ses données internes à ASC pour IoT à des fins d’analyse. Ces données incluent les journaux des opérations appareil-cloud, les identités des appareils et la configuration du Hub. Toutes ces informations permettent de créer le pipeline d’analytique ASC pour IoT.
 
Le pipeline d’analytique ASC pour IoT reçoit également des flux supplémentaires d’information sur les menaces à partir de diverses sources au sein de Microsoft et des partenaires de Microsoft. Le pipeline d’analytique entier d’ASC pour IoT fonctionne avec toutes les configurations client effectuées sur le service (par exemple, des alertes personnalisées et l’utilisation du SDK d’envoi de message de sécurité).
 
À l’aide du pipeline d’analytique, ASC pour IoT combine tous les flux d’informations pour générer des alertes et des recommandations exploitables. Le pipeline contient des règles personnalisées créées par les chercheurs et les experts en sécurité, ainsi que des modèles Machine Learning recherchant toute différence par rapport à l’analyse des risques et du comportement d’appareil standard.
 
Les recommandations et les alertes ASC pour IoT (sortie du pipeline d’analytique) sont écrites dans l’espace de travail Log Analytics de chaque client. Le fait d’inclure les événements bruts dans l’espace de travail, ainsi que les alertes et les recommandations permet de mettre en place des enquêtes et des requêtes approfondies à l’aide des informations précises concernant les activités suspectes détectées.  

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture de base et le flux de travail de la solution ASC pour IoT. Pour en savoir plus sur les prérequis, la prise en main et l’activation de votre solution de sécurité dans IoT Hub, consultez les articles suivants :

- [Prérequis du service](service-prerequisites.md)
- [Prise en main](getting-started.md)
- [Configurer votre solution](quickstart-configure-your-solution.md)
- [Activer la sécurité dans IoT Hub](quickstart-onboard-iot-hub.md)
- [FAQ ASC pour IoT](resources-frequently-asked-questions.md)
- [Alertes de sécurité ASC pour IoT](concept-security-alerts.md)

