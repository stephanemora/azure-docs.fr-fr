---
title: Technologies et solutions Azure pour l’Internet des objets (IoT)
description: Permet de présenter l’ensemble des technologies et solutions PaaS et SaaS disponibles pour créer une solution Azure IoT.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla; v-clay
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
ms.custom: ''
ms.openlocfilehash: 04b4079038ab0ef3f87cfceea521a1424e93cce1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2018
---
# <a name="azure-iot-technologies-and-solutions-paas-and-saas"></a>Technologies et solutions Azure IoT : PaaS et SaaS

Microsoft a développé un portefeuille qui prend en charge les besoins de tous les clients, pour permettre à tous d’accéder aux avantages de la transformation numérique. Le portefeuille de produits Azure IoT constitue une vue d’ensemble des technologies et solutions PaaS et SaaS disponibles. Il présente les deux possibilités offertes pour créer votre propre solution :
- Platform as a Service (PaaS) : concevez votre application en utilisant [Azure IoT Suite](https://www.azureiotsuite.com/), un ensemble de solutions d’entreprise préconfigurées qui vous permettent d’accélérer le développement de solutions IoT personnalisées.
- Software as a Service (SaaS) : pour une prise en main rapide avec [Azure IoT Central](https://azure.microsoft.com/services/iot-central/), la nouvelle solution SaaS conçue pour développer des applications IoT sans la complexité habituellement inhérente à ce genre de solution. Si votre organisation ne possède pas les ressources nécessaires pour concevoir sa propre solution IoT, Azure IoT Central peut l’y aider. En effet, cette solution IoT sans code est capable de créer des modèles d’appareil, des tableaux de bord et des règles en quelques minutes seulement.

![Le portefeuille de technologies et de solutions le plus complet du secteur][img-paas-saas-technologies-solutions]

## <a name="solutions"></a>solutions

Démarrez rapidement avec Azure IoT Suite et les offres SaaS. Pour créer une solution entièrement personnalisable, choisissez parmi les solutions préconfigurées disponibles, celles permettant d’activer des scénarios IoT types (par exemple, la surveillance à distance, la maintenance prédictive et l’usine connectée). Ou alors, utilisez Azure IoT Central, une solution de bout en bout intégralement gérée qui active de puissants scénarios IoT sans pour autant exiger une expertise en solutions cloud.

### <a name="azure-iot-suite-paas"></a>Azure IoT Suite (PaaS)

Azure IoT Suite est un ensemble de solutions PaaS d’entreprise personnalisables qui fournit un niveau élevé de contrôle sur votre solution IoT. Si votre entreprise met en œuvre l’Internet des objets pour ses opérations connectées ou possède des exigences de personnalisation spécifiques pour ses produits connectés, Azure IoT Suite lui offre contrôle dont elle a besoin. 

Les organisations disposant d’un grand nombre d’appareils ou de modèles d’appareil et les fabricants à la recherche de solutions d’usine connectée font partie des entreprises qui peuvent tirer parti d’Azure IoT Suite. En créant des solutions hautement personnalisables adaptées aux besoins complexes, Azure IoT Suite fournit :

- solutions préconfigurées
    - Surveillance à distance
    - Fabrique connectée
    - Maintenance prédictive
    - Simulation d’appareil
- Un déploiement en quelques minutes
- Un retour sur investissement accéléré
- Des solutions offrant un contrôle optimal 
 
### <a name="azure-iot-central-saas"></a>Azure IoT Central (SaaS)

Azure IoT Central est une solution SaaS intégralement gérée. Elle vous permet d’être rapidement opérationnel sans pour autant avoir beaucoup d’expérience dans le domaine de l’Internet des objets. Si votre entreprise recherche une personnalisation dans les plus brefs délais, les modèles SaaS sont la solution idéale pour parvenir à une mise en œuvre IoT. 

Les organisations comptant moins de modèles d’appareil, des scénarios plus prévisibles et des capacités limitées dans le domaine IoT et informatiques peuvent désormais bénéficier de l’Internet des objets grâce à une approche SaaS. Les entreprises qui manquaient auparavant de temps, d’argent et de savoir-faire pour développer des produits connectés peuvent maintenant être rapidement opérationnelles avec Azure IoT Central. Microsoft est une entreprise leader dans ce domaine. Elle fournit en effet une solution SaaS mature qui répond aux besoins de mise en œuvre IoT courants. 

- Solution SaaS IoT intégralement gérée
- Aucune expérience requise dans le domaine du développement de solutions cloud
- Configuration selon vos besoins
- Solution idéale pour les besoins IoT simples

### <a name="compare-azure-iot-suite-and-azure-iot-central"></a>Comparaison entre Azure IoT Suite et Azure IoT Central

Pour mettre en œuvre une [architecture de solution IoT](/azure/iot-fundamentals/iot-introduction#iot-solution-architecture) type, Azure IoT propose plusieurs options : [Azure IoT Suite](/azure/iot-suite) et [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions), chacune répondant à des besoins bien précis des clients.

[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) est la plateforme PaaS Azure principale utilisée à la fois par Azure IoT Central et Azure IoT Suite. IoT Hub permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et une solution cloud. IoT Hub vous aide à répondre aux défis de mise en œuvre de l’IoT telles que :

* Connectivité et gestion de haut volume d’appareils.
* Ingestion de télémétrie de haut volume.
* Commande et contrôle des appareils.
* Application de la sécurité des appareils.

Le choix de votre produit Azure IoT est une étape critique de la planification de votre solution IoT. IoT Hub est un service Azure individuel qui seul n’offre pas une solution IoT de bout en bout. IoT Hub peut être utilisé comme point de départ pour tout type de solution IoT. De plus, vous n’avez pas besoin d’utiliser Azure IoT Suite ni Azure IoT Central pour vous en servir. Azure IoT Suite et Azure IoT Central, ainsi que d’autres services Azure, utilisent IoT Hub. Le tableau suivant résume les principales différences entre Azure IoT Suite et Azure IoT Central pour vous aider à choisir la solution qui répond le mieux à vos besoins :

|                        | Azure IoT Suite | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Utilisation principale | Accélérer le développement d’une solution IoT personnalisée nécessitant une flexibilité maximale. | Accélérer la mise sur le marché de solutions IoT simples qui ne nécessitent pas une personnalisation de service complète. |
| Accès aux services PaaS sous-jacents          | Vous avez accès aux services Azure sous-jacents en vue de les gérer ou de les remplacer en fonction des besoins. | SaaS. Solution entièrement gérée, les services sous-jacents ne sont pas exposés. |
| Flexibilité            | Élevée. Le code des microservices est open source, et vous pouvez le modifier comme vous le souhaitez. En outre, vous pouvez personnaliser l’infrastructure de déploiement.| Moyenne. Vous pouvez utiliser l’expérience utilisateur intégrée basée sur un navigateur pour personnaliser le modèle de la solution et les aspects de l’interface utilisateur. L’infrastructure n’est pas personnalisable, car les différents composants ne sont pas exposés.|
| Niveau de compétence                 | Moyenne-élevée. Des compétences en Java ou en .NET sont nécessaires pour personnaliser le back end de la solution. Des compétences en JavaScript sont nécessaires pour personnaliser la visualisation. | Faible. Des compétences en modélisation sont nécessaires pour personnaliser la solution. Aucune compétence en codage n’est requise. |
| Expérience de démarrage | Des solutions pré-configurées mettent en œuvre des scénarios IoT courants. Déploiement possible en quelques minutes. | Les modèles d’application et les modèles de périphériques fournissent des modèles prédéfinis. Déploiement possible en quelques minutes. |
| Tarifs                | Vous pouvez affiner les services pour contrôler le coût. | Structure de tarification simple et prévisible. |

Le choix du produit à utiliser pour créer votre solution IoT est finalement déterminé par :

* Vos exigences métier.
* Le type de solution que vous souhaitez créer
* Les compétences de votre organisation pour créer et maintenir la solution à long terme.

## <a name="technologies-paas"></a>Technologies (PaaS)

En s’appuyant sur le portefeuille de services de plateforme IoT le plus complet du marché, les technologies PaaS adaptées à la plateforme Azure permettent de facilement créer, personnaliser et contrôler tous les aspects de votre solution IoT. Établissez des communications bidirectionnelles avec des milliards d’appareils IoT et gérez vos appareils IoT à grande échelle. Intégrez ensuite les données de vos appareils IoT avec d’autres services de plateforme, par exemple Azure Cosmos DB et Azure Time Series Insights, pour améliorer vos insights à l’échelle de la solution. 

### <a name="device-support"></a>Prise en charge des appareils

Démarrez votre projet IoT en toute confiance. Pour cela, utilisez les [Azure IoT Starter Kits](https://catalog.azureiotsuite.com/kits) ou choisissez parmi les centaines d’appareils certifiés pour IoT dans le [catalogue](http://catalog.azureiotsuite.com/). Tous ces appareils sont indépendants de la plateforme et testés pour se connecter en toute transparence à IoT Hub.
Connectez tous vos appareils à Azure IoT à l’aide des [Kits de développement logiciel (SDK) pour appareils](/azure/iot-hub/iot-hub-devguide-sdks). Ces SDK prennent en charge plusieurs systèmes d’exploitation, par exemple Linux, Windows et les systèmes d’exploitation en temps réel, ainsi que plusieurs langages de programmation, comme [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp) et [Python](https://github.com/Azure/azure-iot-sdk-python).

### <a name="iot"></a>IoT 
[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) est un service intégralement géré qui garantit des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un back end de solution. Le service Azure IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui permet d’effectuer un approvisionnement sans contact juste-à-temps sur le hub IoT approprié, sans intervention humaine. Les clients peuvent ainsi approvisionner des millions d’appareils de façon sécurisée et évolutive.

### <a name="edge"></a>Edge
[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) est un service Internet des objets (IoT) qui repose sur IoT Hub. Ce service est destiné aux clients qui souhaitent analyser les données sur des appareils, d’où « at the edge » (« en périphérie»). En déplaçant des parties de votre charge de travail en périphérie, vous bénéficiez d’une latence réduite et d’une possibilité de scénarios hors connexion.


### <a name="data-and-analytics"></a>Données et analyse
Sachez tirer profit des nombreuses données Azure et offres d’analyse dans votre solution IoT PaaS, du déplacement en périphérie de l’intelligence cloud avec Azure Machine Learning au stockage rentable des données d’appareils IoT avec Azure Data Lake en passant par la visualisation d’un volume important de données depuis les appareils IoT avec [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).

### <a name="visualization-and-integration"></a>Visualisation et intégration
Microsoft Azure offre une solution cloud complète. Celle-ci associe des services cloud intégrés en constante augmentation et un engagement envers la protection et la confidentialité de vos données, qui fait référence dans le secteur. En savoir plus sur [Microsoft Azure](https://azure.microsoft.com/).





## <a name="next-steps"></a>Étapes suivantes

Pour découvrir rapidement et facilement les fonctionnalités IoT, consultez la section [Prise en main](/azure/iot-hub/iot-hub-get-started). Ou encore, pour une expérience pratique plus approfondie, essayez l’un des [didacticiels](/azure/iot-edge/tutorial-simulate-device-windows).

[img-paas-saas-technologies-solutions]: media/iot-comparison/paas-saas-technologies-solutions.png

