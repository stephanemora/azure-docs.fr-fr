---
title: Options IoT Microsoft Azure | Microsoft Docs
description: Choisissez comment implémenter votre solution Azure IoT à l’aide d’Azure IoT Central, IoT Suite ou de IoT Hub.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201456"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparer les options de Azure IoT Central et de Azure IoT

Pour implémenter une solution IoT, Microsoft Azure IoT Central et Azure IoT offrent plusieurs options, chacune étant adaptée à différents ensembles d’exigences du client :

* [Azure IoT Central](overview-iot-central.md) est une solution SaaS (logiciel en tant que service) qui utilise une approche basée sur les modèles pour vous permettre de créer des solutions IoT de niveau entreprise sans nécessiter de compétences en développement de solutions cloud.

* [Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/) est une collection de niveau entreprise de [solutions pré-configurées](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions) basées sur la plateforme en tant que service (PaaS) Azure qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub est la PasS Azure principale, utilisée aussi bien par Azure IoT Central que par Azure IoT Suite. IoT Hub permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et une solution cloud. IoT Hub vous aide à répondre aux défis de mise en œuvre de l’IoT telles que :

* Connectivité et gestion de haut volume d’appareils.
* Ingestion de télémétrie de haut volume.
* Commande et contrôle des appareils.
* Application de la sécurité des appareils.

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Comparer Azure IoT Central et Azure IoT Suite

Le choix de votre produit Azure IoT est une étape critique de la planification de votre solution IoT. IoT Hub est un service Azure individuel qui seul n’offre pas une solution IoT de bout en bout. IoT Hub peut être utilisé comme point de départ pour tout type de solution IoT. De plus, vous n’avez pas besoin d’utiliser Azure IoT Suite ni Azure IoT Central pour vous en servir. IoT Suite et Azure IoT Central utilisent IoT Hub avec d’autres services Azure. Le tableau suivant résume les principales différences entre IoT Suite et Azure IoT Central pour vous aider à choisir la solution convenant le mieux à vos besoins :

|     | Azure IoT Central | Azure IoT Suite |
| --- | ----------- | --------- |
| Utilisation principale                      | Accélérer la mise sur le marché de solutions IoT simples qui ne nécessitent pas une personnalisation de service complète.                                                    | Accélérer le développement d’une solution IoT personnalisée nécessitant une flexibilité maximale.                                                                                                                             |
| Accès aux services PaaS sous-jacents | SaaS. Solution entièrement gérée, les services sous-jacents ne sont pas exposés.                                                                                            | Vous avez accès aux services Azure sous-jacents en vue de les gérer ou de les remplacer en fonction des besoins.                                                                                                                    |
| Flexibilité                        | Moyenne. Vous pouvez utiliser l’expérience utilisateur intégrée basée sur un navigateur pour personnaliser le modèle de la solution et les aspects de l’interface utilisateur. L’infrastructure n’est pas personnalisable, car les différents composants ne sont pas exposés. | Élevée. Le code des microservices est open source, et vous pouvez le modifier comme vous le souhaitez. En outre, vous pouvez personnaliser l’infrastructure de déploiement.                                               |
| Niveau de compétence                        | Faible. Des compétences en modélisation sont nécessaires pour personnaliser la solution. Aucune compétence en codage n’est requise.                                                                          | Moyenne-élevée. Des compétences en Java ou en .NET sont nécessaires pour personnaliser le back end de la solution. Des compétences en JavaScript sont nécessaires pour personnaliser la visualisation.                                                                       |
| Expérience de démarrage             | Les modèles d’application et les modèles de périphériques fournissent des modèles prédéfinis. Déploiement possible en quelques minutes.                                                                                                  | Des solutions pré-configurées mettent en œuvre des scénarios IoT courants. Déploiement possible en quelques minutes.                                                                                                                            |
| Tarifs                            | Structure de tarification simple et prévisible.                                                                                                                           | Vous pouvez affiner les services pour contrôler le coût.                                                                                                                                                            |

Le choix du produit à utiliser pour créer votre solution IoT est finalement déterminé par :

* Vos exigences métier.
* Le type de solution que vous souhaitez créer
* Les compétences de votre organisation pour créer et maintenir la solution à long terme.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suggérées en fonction du produit et de l’approche choisis, sont :

* **Azure IoT Central** : [Azure IoT Central](overview-iot-central.md).
* **IoT Suite** : [Quelles sont les solutions préconfigurées Azure IoT ?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions).
* **IoT Hub** : [Présentation du service Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).