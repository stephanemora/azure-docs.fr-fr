---
title: Bien démarrer avec Azure Security Center (ASC) pour l’IoT (préversion) | Microsoft Docs
description: Découvrez le flux de travail de base des fonctionnalités et du service Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 39f448f258923d23bdcba75a0a1987777ce0492e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616860"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Bien démarrer avec Azure Security Center pour l’IoT 

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article donne une explication des différents blocs de construction du service Azure Security Center (ASC) pour l’IoT et des premiers pas pour [activer le service](quickstart-onboard-iot-hub.md). 

ASC pour l’IoT s’intègre parfaitement aux hubs IoT pour assurer l’analyse de sécurité de la configuration du hub, de l’identité d’appareil et des modèles de communication hub-appareil.
Pour offrir des fonctionnalités de sécurité améliorées, il effectue une collecte de données de sécurité basée sur les agents auprès des appareils IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>Intégration parfaite entre ASC pour l’IoT et IoT Hub

Quand il s’agit de protéger différents appareils IoT, la possibilité de collecter des données directement sur les appareils ou sur leur réseau est incontournable. C’est dans cette optique qu’ASC pour l’IoT propose tout un arsenal d’agents de sécurité à faible empreinte permettant le monitoring et la sécurisation renforcée des appareils.

Dans la préversion d’ASC pour l’IoT, l’architecture de référence des agents de sécurité Linux et Windows est fournie à la fois en C# et en C.
Les agents gèrent la collecte d’événements bruts auprès du système d’exploitation, l’agrégation d’événements pour réduire les coûts et la configuration grâce à un jumeau de module.
Les messages de sécurité sont envoyés aux services d’analyse d’ASC pour l’IoT par le hub IoT.

## <a name="asc-for-iot-basics"></a>Informations de base sur ASC pour l’IoT

Choisissez le scénario de flux de travail qui répond le mieux aux besoins de votre environnement et de vos appareils IoT :

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Bien démarrer avec l’intégration parfaite entre ASC pour l’IoT et IoT Hub 

>[!Note]
>Ce flux de travail permet d’utiliser le service sans recourir aux agents de sécurité ASC pour l’IoT. 

Pour permettre le monitoring de vos modèles de communication cloud-appareil, appareil-cloud et gestion des identités d'appareil, suivez le flux de travail de base ci-dessous afin de tester et de lancer le service : 

1. [Activer le service ASC pour l’IoT sur un hub IoT](quickstart-onboard-iot-hub.md)
1. Si aucun appareil n’est inscrit sur votre hub IoT, [Enregistrez un nouvel appareil](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Créez un module de sécurité azureiotsecurity](quickstart-create-security-twin.md) pour vos appareils. 
1. Définissez le comportement normal des appareils et du système avec des [alertes personnalisées](quickstart-create-custom-alerts.md). 
1. Effectuez un test système pour vérifier l’état du service et des appareils. 
1. Explorez les [alertes](concept-security-alerts.md), les [recommandations](concept-recommendations.md) et la [présentation approfondie de Log Analytics](how-to-security-data-access.md) avec IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Bien démarrer avec les agents de sécurité ASC pour l’IoT

Utilisez les fonctionnalités de sécurité améliorées d’ASC pour l’IoT, comme le monitoring des connexions à distance, les applications actives, les événements de connexion et les meilleures pratiques de configuration du système d’exploitation suivant le flux de travail ci-dessous pour tester et activer le service : 

1. [Activer le service ASC pour l’IoT sur un hub IoT](quickstart-onboard-iot-hub.md)
1. Si aucun appareil n’est inscrit sur votre hub IoT, [Enregistrez un nouvel appareil](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Créez un module de sécurité azureiotsecurity](quickstart-create-security-twin.md) pour vos appareils.
1. Pour installer l’agent sur un appareil simulé Azure au lieu d’un appareil réel, [lancez une nouvelle Machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) dans une zone disponible. 
1. [Déployez un l’agent de sécurité ASC pour l’IoT](how-to-deploy-linux-cs.md) sur votre appareil IoT ou une nouvelle machine virtuelle.
1. Suivez les instructions de [trigger_events](https://aka.ms/iot-security-github-trigger-events) pour exécuter une simulation d’attaque sans danger.
1. Vérifiez les alertes ASC pour l’IoT qui surviennent en réponse à l’attaque simulée à l’étape précédente. Commencez la vérification cinq minutes après avoir exécuté le script.
1. Explorez les [alertes](concept-security-alerts.md), les [recommandations](concept-recommendations.md) et la [présentation approfondie de Log Analytics](how-to-security-data-access.md) avec IoT Hub. 

## <a name="next-steps"></a>Étapes suivantes

- Activer [ASC pour l’IoT](quickstart-onboard-iot-hub.md)
- Configurer une [solution](quickstart-configure-your-solution.md)
- [Créer des modules de sécurité](quickstart-create-security-twin.md)
- Configurer des [alertes personnalisées](quickstart-create-custom-alerts.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
