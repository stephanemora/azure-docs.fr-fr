---
title: Comprendre l’architecture de l’agent de sécurité Azure Security Center pour IoT en préversion | Microsoft Docs
description: Comprenez l’architecture de l’agent de sécurité pour les agents utilisés dans Azure Security Center pour le service IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f4ca9a2689ab9e09b4ebff903e757f5c352b556
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616581"
---
# <a name="security-agent-reference-architecture"></a>Architecture de référence de l’agent de sécurité

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Security Center (ASC) pour IoT fournit une architecture de référence pour les agents de sécurité qui se connecteront, traiteront, rassembleront et enverront des données de sécurité via le hub IoT.

Les agents de sécurité sont conçus pour fonctionner dans un environnement IoT restreint et sont hautement personnalisables quant aux valeurs qu’ils fournissent par rapport aux ressources qu’ils utilisent.

Les agents de sécurité prennent en charge les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts à partir du système d’exploitation sous-jacent (Linux, Windows). Pour en savoir plus sur les collecteurs de données de sécurité disponibles, consultez [Configuration de l’agent ASC pour IoT](how-to-agent-configuration.md).

- Regroupement des événements de sécurité bruts dans des messages envoyés via IoT hub.

- Authentification avec une identité d’appareil existante, ou une identité de module dédiée. Consultez [Méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md) pour en savoir plus.

- Configuration à distance à l’aide du jumeau de module **azureiotsecurity**. Pour plus d’informations, consultez [Configurer un agent ASC pour IoT](how-to-agent-configuration.md).

Les agents de sécurité ASC pour IoT sont développés en tant que projets open source et sont disponibles sur GitHub : 

- [Agent ASC pour IoT basé sur C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Agent ASC pour IoT basé sur C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plateformes prises en charge

ASC pour IoT offre des agents de programme d’installation différents pour les versions 32 et 64 bits de Windows, et les mêmes pour les versions 32 et 64 bits de Linux. Vérifiez que vous avez le programme d’installation d’agent approprié pour chacun de vos appareils conformément au tableau suivant :

| 32 ou 64 bits | Linux | Windows |    Détails|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bits  | C  | C#  ||
| 64 bits  | C# ou C           | C#      | Utiliser l’agent C pour les appareils avec un minimum de ressources|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture de l’agent de sécurité ACS pour IoT et les programmes d’installation disponibles.

Pour continuer votre prise en main du déploiement d’ASC pour IoT, utilisez les articles suivants :

- Comprendre les [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- Sélectionner et déployer un [agent de sécurité](how-to-deploy-agent.md)
- Passer en revue les [Prérequis du service](service-prerequisites.md) pour ASC pour IoT
- Découvrez comment [Activer le service ASC pour l’IoT dans votre hub IoT](quickstart-onboard-iot-hub.md)
- Renseignez-vous sur le service dans la [FAQ ASC pour l’IoT](resources-frequently-asked-questions.md)
