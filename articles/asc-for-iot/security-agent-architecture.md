---
title: Présentation de ASC pour l’architecture de l’agent de sécurité IoT Preview | Microsoft Docs
description: Comprendre l’architecture de l’agent de sécurité pour les agents utilisés dans l’ASC pour le service IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7c8f256cf91a479c45f21b933efdb6a5e0212796
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541871"
---
# <a name="security-agent-reference-architecture"></a>Architecture de référence de l’agent de sécurité

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC pour IoT fournit une architecture de référence pour les agents de sécurité qui se connecteront, traitement, agrègent et envoient des données de sécurité via IoT hub.

Agents de sécurité sont conçus pour fonctionner dans un environnement restreint de IoT et sont hautement personnalisables en termes de valeurs qu’ils fournissent par rapport aux ressources qu’ils utilisent.

Agents de sécurité prennent en charge les fonctionnalités de solution IoT suivantes :

- Collecter les événements de sécurité brute du système d’exploitation sous-jacent (Linux, Windows). Pour en savoir plus sur les collecteurs de données de sécurité disponibles, consultez [ASC pour configurer l’agent IoT](concept-agent-configuration.md).

- Regrouper les événements de sécurité brute dans les messages envoyés via IoT hub.

- S’authentifier avec l’identité d’appareil existant, ou une identité de module dédié. Consultez [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md) pour en savoir plus.

- Configurer à distance à l’aide de la **ascforiot** jumeau de module. Pour plus d’informations, consultez [configurer un ASC pour l’agent IoT](concept-agent-configuration.md).

ASC pour les agents de sécurité IoT sont développées en tant que projets open source et sont disponible à partir de GitHub : 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>Plateformes d’agent pris en charge

ASC pour IoT offre des agents de programme d’installation différent pour 32 bits et 64 bits de Windows et les mêmes pour 32 bits et 64 bits de Linux. Vérifiez que vous avez le programme d’installation d’agent appropriées pour chacun de vos appareils conformément au tableau suivant :

| 32 ou 64 bits | Linux | Windows |    Détails|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C#ou C           | C#      | Utilisez l’agent de C pour les appareils avec un minimum de ressources|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à ASC pour l’architecture de l’agent de sécurité IoT et les programmes d’installation disponibles.

Pour continuer la mise en route avec ASC pour un déploiement IoT, utilisez les articles suivants :


- Passez en revue l’ASC pour IoT [conditions préalables de Service](service-prerequisites.md)
- Découvrez comment [activer l’ASC pour le service IoT dans votre IoT Hub](quickstart-onboard-iot-hub.md)
- Utiliser le démarrage rapide pour [configurer votre solution](quickstart-configure-your-solution.md)
- Comprendre [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- Sélectionner et de déployer un [agent de sécurité](select-deploy-agent.md)
- En savoir plus sur le service à partir de la [ASC pour IoT FAQ](resources-frequently-asked-questions.md)