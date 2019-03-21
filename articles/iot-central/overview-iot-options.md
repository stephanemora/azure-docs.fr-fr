---
title: Options IoT Microsoft Azure | Microsoft Docs
description: Choisissez comment implémenter votre solution Azure IoT à l’aide d’Azure IoT Central, d’accélérateurs de solution IoT ou d’IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 571c703609bac2e4414baf19edc39cf5e9f8820a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759774"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparer les options de Azure IoT Central et de Azure IoT

Microsoft Azure IoT Central et Azure IoT offrent plusieurs options pour créer une solution IoT. Ces options répondent à différents besoins des clients :

* [Azure IoT Central](overview-iot-central.md) est une solution SaaS (software as a service) qui utilise une approche basée sur les modèles pour vous permettre de créer des solutions IoT de niveau entreprise sans nécessiter de compétences en développement de solutions cloud.

* Les [accélérateurs de solution Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) sont une collection de niveau entreprise d’[accélérateurs de solution](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) basés sur la plateforme en tant que service (PaaS) Azure et qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub est la plateforme en tant que service (PaaS) de base utilisée par Microsoft IoT Central et les accélérateurs de solution Azure IoT. IoT Hub prend en charge des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et une solution cloud. IoT Hub vous aide à répondre aux défis de l’implémentation IoT comme les suivants :

* Connectivité et gestion d’appareils à haut volume
* Ingestion de données de télémétrie volumineuses
* Commande et contrôle des appareils
* Sécurisation des appareils

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Comparer Azure IoT Central et les accélérateurs de solution Azure IoT

Le choix de votre produit Azure IoT est une étape critique de la planification de votre solution IoT. IoT Hub est un service Azure individuel qui seul n’offre pas une solution IoT de bout en bout. Vous pouvez utiliser IoT Hub en tant que point de départ d’une solution IoT. Vous n’avez pas besoin d’utiliser également des accélérateurs de solution Azure IoT ou Azure IoT Central pour utiliser IoT Hub. Les accélérateurs de solution IoT et Azure IoT Central, ainsi que d’autres services Azure, utilisent IoT Hub.

Le tableau suivant résume les principales différences entre les accélérateurs de solution IoT et Azure IoT Central pour vous aider à choisir ce qui convient le mieux à vos besoins :

|     | Azure IoT Central | Accélérateurs de solution Azure IoT |
| --- | ----------- | --------- |
| Utilisation principale                      | Accélérer la mise sur le marché de solutions IoT simples qui ne nécessitent pas une personnalisation de service complète.                                                    | Accélérer le développement d’une solution IoT personnalisée nécessitant une flexibilité maximale.                                                                                                                             |
| Accès aux services PaaS sous-jacents | SaaS. Étant donné qu’il s’agit d’une solution complètement managée, les services sous-jacents ne sont pas exposés.                                                                                            | Vous avez accès aux services Azure sous-jacents en vue de les gérer ou de les remplacer en fonction des besoins.                                                                                                                    |
| Flexibilité                        | Moyenne. Vous pouvez utiliser l’expérience utilisateur intégrée basée sur un navigateur pour personnaliser le modèle de la solution et les aspects de l’interface utilisateur. L’infrastructure n’est pas personnalisable, car les différents composants ne sont pas exposés. | Élevée. Le code des microservices est open source et vous pouvez le modifier comme vous le souhaitez. En outre, vous pouvez personnaliser l’infrastructure de déploiement.                                               |
| Niveau de compétence                        | Faible. Des compétences en modélisation sont nécessaires pour personnaliser la solution. Aucune compétence en codage n’est requise.                                                                          | Moyenne-élevée. Des compétences en Java ou en .NET sont nécessaires pour personnaliser le back-end de la solution. Des compétences en JavaScript sont nécessaires pour personnaliser la visualisation.                                                                       |
| Expérience de prise en main             | Les modèles d’application et les modèles d’appareil fournissent des modèles prédéfinis. Déploiement possible en quelques minutes.                                                                                                  | Des solutions pré-configurées mettent en œuvre des scénarios IoT courants. Déploiement possible en quelques minutes.                                                                                                                            |
| Tarifs                            | Structure de tarification simple et prévisible.                                                                                                                           | Vous pouvez affiner les services pour contrôler le coût.                                                                                                                                                            |

Le produit à utiliser pour créer votre solution IoT dépend de ce qui suit :

* Vos exigences métier.
* Le type de solution que vous souhaitez créer.
* Les compétences de votre organisation pour créer et maintenir la solution à long terme.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suggérées en fonction du produit et de l’approche choisis, sont :

* **Azure IoT Central** : [Qu’est-ce que Azure IoT Central ?](overview-iot-central.md)
* **Accélérateurs de solution IoT** : [Que sont les accélérateurs de solution IoT ?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **Hub IoT** : [Qu’est-ce qu’Azure IoT Hub ?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)