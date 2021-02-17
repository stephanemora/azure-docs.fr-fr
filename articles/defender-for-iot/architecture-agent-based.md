---
title: Architecture de solution basée sur un agent
description: Découvrez l’architecture basée sur un agent et le flux d’informations d’Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 1729a20825eb554a4fd2ee60141994e57e62f030
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809612"
---
# <a name="agent-based-solution-for-device-builders"></a>Solution basée sur un agent pour les fabricants d’appareils

Cet article décrit l’architecture système fonctionnelle de la solution Defender pour IoT basée sur un agent. Azure Defender pour IoT offre deux ensembles de fonctionnalités pour répondre aux besoins de votre environnement, une solution sans agent pour les organisations, et une solution basée sur un agent pour les fabricants d’appareils.

## <a name="iot-hub-built-in-security"></a>Sécurité intégrée d’IoT Hub

Defender pour IoT est activé par défaut dans chaque nouveau hub IoT créé. Defender for IoT fournit une analyse en temps réel, des recommandations et des alertes, sans nécessiter l’installation d’un agent sur des appareils, et utilise une analytique avancée des métadonnées IoT Hub journalisées afin d’analyser et de protéger les appareils et les hubs IoT. 

## <a name="defender-for-iot-micro-agent"></a>Micro-agent Defender pour IoT 

Le micro-agent Defender pour IoT fournit un niveau approfondi de protection et de visibilité de la sécurité du comportement des appareils. collecte, agrège et analyse les événements de sécurité bruts provenant de vos appareils. Les événements de sécurité bruts peuvent inclure des connexions IP, la création de processus, des connexions utilisateur et d’autres informations liées à la sécurité. Les agents d’appareil Defender pour IoT gèrent également l’agrégation d’événements afin d’éviter un débit réseau élevé. Les agents sont hautement personnalisables, vous permettant ainsi de les utiliser pour des tâches spécifiques, comme l’envoi d’informations importantes uniquement sur le contrat SLA le plus rapide, ou l’agrégation du contexte et des informations de sécurité étendues en segments plus volumineux, pour éviter des coûts de service plus élevés.

Les agents d’appareil et d’autres applications utilisent le **SDK d’envoi de message de sécurité Azure** pour envoyer des informations de sécurité dans Azure IoT Hub. IoT Hub obtient ces informations et les transmet au service Defender pour IoT.

Une fois le service Defender pour IoT activé, outre les données transférées, IoT Hub envoie également toutes ses données internes à Defender pour IoT à des fins d’analyse. Ces données incluent les journaux des opérations appareil-cloud, les identités des appareils et la configuration du hub. Toutes ces informations permettent de créer le pipeline d’analytique Defender pour IoT.

Le pipeline d’analytique Defender pour IoT reçoit également d’autres flux d’informations sur les menaces à partir de diverses sources au sein de Microsoft et des partenaires de Microsoft. Le pipeline d’analytique entier Defender pour IoT fonctionne avec toutes les configurations client effectuées sur le service (par exemple, des alertes personnalisées et l’utilisation du SDK d’envoi de message de sécurité).

À l’aide du pipeline d’analytique, Defender pour IoT combine tous les flux d’informations pour générer des alertes et des suggestions exploitables. Le pipeline contient des règles personnalisées créées par les chercheurs et les experts en sécurité, ainsi que des modèles Machine Learning recherchant toute différence par rapport à l’analyse des risques et du comportement d’appareil standard.

Les recommandations et les alertes Defender pour IoT (sortie du pipeline d’analytique) sont écrites dans l’espace de travail Log Analytics de chaque client. Le fait d’inclure les événements bruts dans l’espace de travail, ainsi que les alertes et les recommandations, permet de mettre en place des enquêtes et des requêtes approfondies à l’aide des informations précises concernant les activités suspectes détectées.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Architecture du micro-agent.":::

## <a name="see-also"></a>Voir aussi

[FAQ de Defender pour IoT](resources-frequently-asked-questions.md)

[Prérequis système](quickstart-system-prerequisites.md)
