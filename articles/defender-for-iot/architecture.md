---
title: Architecture de solution
description: En savoir plus sur le flux d’informations dans le service Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930807"
---
# <a name="azure-defender-for-iot-architecture"></a>Architecture de Azure Defender pour IoT

Cet article explique l’architecture système fonctionnelle de la solution Defender pour IoT.

## <a name="defender-for-iot-components"></a>Composants de Defender pour IoT

Defender pour IoT est composé des éléments suivants :

- Intégration d’IoT Hub
- Agents Smart Device (facultatif)
- SDK d’envoi de message de sécurité
- Pipeline d’analytique

### <a name="defender-for-iot-workflows"></a>Flux de travail de Defender pour IoT

Defender pour IoT fonctionne dans l’un des deux flux de travail de fonctionnalités : intégré et amélioré

### <a name="built-in"></a>Intégré

En mode **intégré**, Defender pour IoT s’active lorsque vous choisissez d’activer l’option **Sécurité** dans votre IoT Hub. Grâce à sa supervision, ses suggestions et ses alertes en temps réel, le mode intégré offre une visibilité de l’appareil en une seule étape et une sécurité inégalée. Le mode de génération ne nécessite pas l’installation de l’agent sur un appareil et utilise des analyses avancées sur les activités journalisées pour analyser et protéger votre appareil de champ.

### <a name="enhanced"></a>Amélioré

En mode **étendu**, après l’activation de l’option **Sécurité** dans votre IOT Hub et l’installation des agents d’appareils Defender pour IoT sur vos appareils, les agents collectent, agrègent et analysent les événements de sécurité bruts de vos appareils. Les événements de sécurité bruts peuvent inclure des connexions IP, la création de processus, des connexions utilisateur et d’autres informations liées à la sécurité. Les agents d’appareil Defender pour IoT gèrent également l’agrégation d’événements afin d’éviter un débit réseau élevé. Les agents sont hautement personnalisables, vous permettant ainsi de les utiliser pour des tâches spécifiques, comme l’envoi d’informations importantes uniquement sur le contrat SLA le plus rapide, ou l’agrégation du contexte et des informations de sécurité étendues en segments plus volumineux, pour éviter des coûts de service plus élevés.

![Architecture de Defender pour IoT](./media/architecture/azure-iot-security-architecture.png)

Les agents d’appareil et d’autres applications utilisent le **SDK d’envoi de message de sécurité Azure** pour envoyer des informations de sécurité dans Azure IoT Hub. IoT Hub récupère ces informations et les transmet au service Defender pour IoT.

Une fois le service Defender pour IoT activé, outre les données transférées, IoT Hub envoie également toutes ses données internes à Defender pour IoT à des fins d’analyse. Ces données incluent les journaux des opérations appareil-cloud, les identités des appareils et la configuration du Hub. Toutes ces informations permettent de créer le pipeline d’analytique Defender pour IoT.

Le pipeline d’analytique Defender pour IoT reçoit également des flux supplémentaires d’informations sur les menaces à partir de diverses sources au sein de Microsoft et des partenaires de Microsoft. Le pipeline d’analytique entier Defender pour IoT fonctionne avec toutes les configurations client effectuées sur le service (par exemple, des alertes personnalisées et l’utilisation du SDK d’envoi de message de sécurité).

À l’aide du pipeline d’analytique, Defender pour IoT combine tous les flux d’informations pour générer des alertes et des suggestions exploitables. Le pipeline contient des règles personnalisées créées par les chercheurs et les experts en sécurité, ainsi que des modèles Machine Learning recherchant toute différence par rapport à l’analyse des risques et du comportement d’appareil standard.

Les recommandations et les alertes Defender pour IoT (sortie du pipeline d’analytique) sont écrites dans l’espace de travail Log Analytics de chaque client. Le fait d’inclure les événements bruts dans l’espace de travail, ainsi que les alertes et les recommandations permet de mettre en place des enquêtes et des requêtes approfondies à l’aide des informations précises concernant les activités suspectes détectées.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture de base et le flux de travail de la solution Defender pour IoT. Pour en savoir plus sur les prérequis, la prise en main et l’activation de votre solution de sécurité dans IoT Hub, consultez les articles suivants :

- [Prérequis du service](service-prerequisites.md)
- [Bien démarrer](getting-started.md)
- [Configurer votre solution](quickstart-configure-your-solution.md)
- [Activer la sécurité dans IoT Hub](quickstart-onboard-iot-hub.md)
- [FAQ de Defender pour IoT](resources-frequently-asked-questions.md)
- [Alertes de sécurité Defender pour IoT](concept-security-alerts.md)
