---
title: Plateforme Azure Industrial IoT
description: Cet article fournit une vue d’ensemble de la plateforme Azure Industrial IoT et de ses composants.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801551"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Qu’est-ce que la plateforme Azure Industrial IoT (IIoT) ?

La plateforme Azure Industrial IoT est une suite de modules et de services Microsoft déployés sur Azure. Ces modules et services ont intégré pleinement la transparence. Plus précisément, nous appliquons une offre de plateforme en tant que service (PaaS) gérée par Azure, un logiciel open source avec licence MIT, des normes internationales pour la communication (OPC UA, AMQP, MQTT, HTTP) et des interfaces (Open API) et des modèles de données industriels (OPC UA) à la périphérie et dans le cloud.

## <a name="enabling-shopfloor-connectivity"></a>Activation de la connectivité atelier 

La plateforme Azure Industrial IoT couvre la connectivité industrielle des ressources atelier (y compris la découverte des ressources OPC UA), normalise ses données au format OPC UA et transmet les données de télémétrie de ressources à Azure au format OPC UA PubSub. À partir de là, il stocke les données de télémétrie dans une base de données dans le cloud. En outre, la plateforme offre un accès sécurisé aux ressources atelier via OPC UA à partir du cloud. Les fonctionnalités de gestion des appareils (y compris la configuration de la sécurité) sont également intégrées. La fonctionnalité OPC UA a été créée à l’aide de la technologie de conteneur Docker pour faciliter le déploiement et la gestion. Pour les ressources non-OPC UA, nous avons conclu un partenariat avec les principaux fournisseurs de connectivité industriels et nous les avons aidés à déplacer leur logiciel d’adaptateur d’OPC UA vers Azure IoT Edge. Ces adaptateurs sont disponibles dans la Place de marché Azure.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Composants IoT industriels : modules IoT Edge et microservices cloud

Les services à la périphérie sont implémentés en tant que modules Azure IoT Edge et exécutés en local. Les microservices Cloud sont implémentés en tant que microservices ASP.NET avec une interface REST et exécutés sur des services Azure Kubernetes gérés ou autonomes sur Azure App Service. Pour les services à la périphérie et dans le cloud, nous avons fourni les conteneurs Docker prédéfinis dans le Microsoft Container Registry (MCR), en supprimant cette étape pour le client. Les services à la périphérie et dans le cloud s’exploitent l’un l’autre et doivent être utilisés ensemble. Nous avons également fourni des scripts de déploiement faciles à utiliser qui permettent de déployer l’ensemble de la plateforme à l’aide d’une seule commande.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris ce qu’est la plateforme Azure Industrial IoT, vous pouvez en savoir plus sur le serveur de publication OPC :

> [!div class="nextstepaction"]
> [Qu’est-ce qu’OPC Publisher ?](overview-what-is-opc-publisher.md)