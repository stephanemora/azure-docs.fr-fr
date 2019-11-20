---
title: Introduction à l’Internet des objets Azure (IoT)
description: Introduction expliquant les principes de base d’Azure IoT et des services IoT avec notamment des exemples qui illustrent l’utilisation de l’IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ce82a2cc4cc936d2e0a7a8b82cbc0ed7e5c6eb52
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048649"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Qu’est-ce que l’Internet des objets Azure (IoT) ?

L’Internet des objets Azure (IoT) est une collection de services cloud gérés par Microsoft qui permettent de connecter, superviser et contrôler des milliards de ressources IoT. Plus simplement, une solution IoT est constituée d’un ou plusieurs appareils IoT et d’un ou plusieurs services back-end exécutés dans le cloud qui communiquent entre eux. 

Cet article présente les principes de base de l’IoT, aborde les cas d’usage et décrit brièvement les huit différents services disponibles. En comprenant ce qui est à votre disposition, vous arriverez à savoir ce que vous souhaitez étudier de plus près pour vous permettre de concevoir votre scénario.

## <a name="introduction"></a>Introduction

Les principaux éléments d’une solution IoT sont : les appareils, les services back-end et les communications entre les deux. 

### <a name="iot-devices"></a>Appareils IoT

Les appareils sont généralement constitués d’un circuit avec des capteurs attachés qui se connectent à Internet. De nombreux appareils communiquent via une puce Wi-Fi. Voici quelques exemples d’appareils IoT :

* capteurs de pression sur une pompe à huile distante
* capteurs de température et d’humidité sur un climatiseur
* accéléromètres dans un ascenseur
* capteurs de présence dans une salle

Les deux appareils fréquemment utilisés pour le prototypage sont le kit de base MX Chip IoT Devkit de Microsoft et les appareils de Raspberry PI. Le kit MX Chip IoT Devkit contient des capteurs intégrés pour la température, la pression, l’humidité, ainsi qu’un gyroscope, un accéléromètre, un magnétomètre et une puce Wi-Fi. Raspberry PI est un appareil IoT auquel vous pouvez attacher de nombreux types différents de capteurs, ce qui vous permet de sélectionner exactement ce dont vous avez besoin pour votre scénario. 

Pour plus d’informations sur les appareils IoT disponibles, consultez le [catalogue d’appareils certifiés pour IoT](https://catalog.azureiotsolutions.com/alldevices) le plus grand du secteur.

Avec les kits [IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md), vous pouvez créer des applications qui s’exécutent sur vos appareils pour pouvoir effectuer les tâches dont vous avez besoin. Les kits SDK vous permettent d’envoyer la télémétrie à votre hub IoT, de recevoir des messages et des mises à jour à partir du hub IoT, et ainsi de suite.

### <a name="communication"></a>Communication

Votre appareil peut communiquer avec les services back-end dans les deux sens. Voici quelques exemples de modes de communication de l’appareil avec la solution back-end.

#### <a name="examples"></a>Exemples 

* Votre appareil peut envoyer la température d’un camion réfrigéré mobile à un hub IoT toutes les 5 minutes. 

* Le service back-end peut demander à l’appareil d’envoyer la télémétrie plus fréquemment pour aider à diagnostiquer un problème. 

* Votre appareil peut envoyer des alertes basées sur les valeurs lues de ses capteurs. Par exemple, si vous surveillez un réacteur discontinu dans une usine chimique, vous souhaiterez peut-être envoyer une alerte quand la température dépasse une certaine valeur.

* Votre appareil peut envoyer des informations à un tableau de bord pour que des opérateurs humains puissent les voir. Par exemple, une salle de contrôle dans une raffinerie peut montrer la température et la pression de chaque tuyau, ainsi que le volume transitant via ces tuyaux, ce qui permet aux opérateurs de les voir. 

Ces tâches, et d’autres, peuvent être implémentées à l’aide des kits [IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Considérations relatives à la connexion

La connexion sécurisée et fiable des appareils est souvent le défi le plus important dans les solutions IoT. Cela est dû au fait que les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que les navigateurs et les applications mobiles. Plus précisément, les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain (contrairement à un téléphone) ;

* peuvent être déployés sur des sites distants avec un accès physique coûteux ;

* sont accessibles uniquement via le serveur principal de la solution ; Il n’existe aucun autre moyen d’interagir avec l’appareil.

* peuvent avoir des performances et/ou des ressources de traitement limitées ;

* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;

* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.

### <a name="back-end-services"></a>Services back-end 

Voici quelques-unes des fonctions que peut fournir un service back-end.

* Recevoir des données de télémétrie à grande échelle de vos appareils et déterminer comment traiter et stocker ces données.

* Analyser la télémétrie pour fournir des insights en temps réel ou en différé.

* Envoyer des commandes depuis le cloud à un appareil spécifique. 

* Provisionner les appareils et contrôler ceux qui peuvent se connecter à votre infrastructure.

* Contrôler l’état de vos appareils et surveiller leurs activités.

Par exemple, dans un scénario de maintenance prédictive, le back-end cloud stocke les données de télémétrie historiques. La solution utilise ces données pour identifier un comportement anormal potentiel au niveau de pompes spécifiques avant qu’un problème réel ne puisse survenir. À l’aide de l’analyse des données, on peut identifier que la solution préventive consiste à envoyer une commande à l’appareil pour effectuer une action corrective. Ce processus génère une boucle de rétroaction automatisée entre l’appareil et le cloud qui augmente considérablement l’efficacité de la solution.

## <a name="an-iot-example"></a>Exemple IoT

Voici un exemple où une entreprise utilise IoT pour économiser des millions de dollars. 

Une ferme dispose d’un énorme bétail composé de centaines de milliers de vaches. Il est très important d’effectuer le suivi de toutes ces vaches et de savoir comment elles vont, ce qui nécessite beaucoup de déplacements. Les propriétaires ont attaché des capteurs à chacune des vaches, lesquels envoient des informations, telles que les coordonnées GPS et la température, à un service back-end pour qu’elles soient écrites dans une base de données.

Ils disposent d’un service analytique qui analyse les données entrantes et les données de chaque vache pour pouvoir répondre aux questions comme les suivantes :

* La vache a-t-elle de la température ? Depuis combien de temps la vache a de la température ? Si cela fait plus d’un jour, relevez les coordonnées GPS et partez retrouver la vache, puis traitez-la avec des antibiotiques le cas échéant. 

* Est-ce que la vache n’a pas bougé depuis plus d’un jour ? Si oui, relevez les coordonnées GPS et partez retrouver la vache. La vache est-elle tombée d’une falaise ? La vache est-elle blessée ? La vache a-t-elle besoin d’aide ? 

En implémentant cette solution IoT, les propriétaires de la ferme peuvent examiner et traiter les vaches rapidement et réduire le temps qu’ils devaient passer à les chercher et les localiser, ce qui leur fait économiser beaucoup d’argent. Pour plus d’exemples concrets d’entreprises qui utilisent IoT, consultez [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Services IoT

Il existe plusieurs services IoT dans Azure et il peut être difficile de déterminer celui que vous souhaitez utiliser. Certains, comme IoT Central et les accélérateurs de solution IoT, fournissent des modèles qui vous aident à créer votre propre solution et à devenir opérationnel rapidement. Vous pouvez aussi développer vos propres solutions de A à Z en utilisant d’autres services disponibles. Tout dépend de l’aide et du contrôle que vous souhaitez avoir. Voici une liste des services disponibles et dans quel but vous pourriez les utiliser.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md) : Il s'agit d'une plateforme IoT qui simplifie la création de vos solutions IoT et contribue à réduire la charge et le coût liés à la gestion, aux opérations et au développement IoT. Pour commencer, sélectionnez un modèle pour votre type d’appareil, puis créez et testez une application IoT Central simple qu’utiliseront les opérateurs de l’appareil. L’application IoT Central vous permettra aussi de surveiller les appareils et d’en provisionner de nouveaux. Ce service est pour les solutions simples qui ne nécessitent pas une personnalisation avancée du service.

2. [**Accélérateurs de solution IoT**](/azure/iot-suite) : il s’agit d’un ensemble de solutions PaaS que vous pouvez utiliser pour accélérer le développement d’une solution IoT. Commencez avec une solution IoT qui vous est fournie, puis personnalisez cette solution dans son intégralité selon vos besoins. Vous devez avoir des compétences en Java ou .NET pour personnaliser le back-end et des compétences en JavaScript pour personnaliser la visualisation. 

3. [**IoT Hub**](/azure/iot-hub/) : ce service vous permet de vous connecter depuis vos appareils à un hub IoT et de surveiller et contrôler des milliards d’appareils IoT. Il est particulièrement utile si vous avez besoin d’une communication bidirectionnelle entre vos appareils IoT et votre back-end. Il s’agit du service sous-jacent d’IoT Central et des accélérateurs de solution IoT. 

4. [**Service IoT Hub Device Provisioning**](/azure/iot-dps/) : il s’agit d’un service d’assistance pour IoT Hub que vous pouvez utiliser pour provisionner des appareils sur votre hub IoT de façon sécurisée. Avec ce service, vous pouvez facilement provisionner des millions d’appareils rapidement plutôt que de le faire un par un. 

5. [**IoT Edge**](/azure/iot-edge/) : ce service repose sur IoT Hub. Il peut être utilisé pour analyser les données sur les appareils IoT plutôt que dans le cloud. En déplaçant des parties de votre charge de travail à la périphérie, moins de messages doivent être envoyés au cloud. 

6. [**Azure Digital Twins**](../digital-twins/index.yml) : ce service vous permet de créer des modèles complets de l’environnement physique. Vous pouvez modéliser les relations et les interactions entre les personnes, les espaces et les appareils. Par exemple, vous pouvez prévoir les besoins de maintenance d’une usine, analyser les besoins énergétiques en temps réel d’un réseau électrique ou optimiser l’utilisation de l’espace disponible d’un bureau.

7. [**Time Series Insights**](/azure/time-series-insights) : ce service vous permet de stocker, de visualiser et d’interroger de grandes quantités de données de série chronologique générées par les appareils IoT. Vous pouvez utiliser ce service avec IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps) : ce service fournit des informations géographiques aux applications web et mobiles. Vous pouvez utiliser un ensemble complet d’API REST et un contrôle JavaScript web pour créer des applications souples qui fonctionnent sur des applications de bureau ou mobiles pour les appareils Apple et Windows.

## <a name="next-steps"></a>Étapes suivantes

Pour des cas d’entreprise concrets et l’architecture utilisée, consultez [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Pour des exemples de projets que vous pouvez essayer avec un kit IoT DevKit, consultez [IoT DevKit Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Pour une explication plus complète des différents services et de leur utilisation, consultez [Services et technologies Azure IoT](iot-services-and-technologies.md).

Pour une étude approfondie de l’architecture IoT, consultez [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Architecture de référence Microsoft Azure IoT).
