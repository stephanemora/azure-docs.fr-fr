---
title: Présentation d’Azure IoT (Internet des objets)
description: Vue d’ensemble d’Azure IoT et des services et technologies associés.
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: 858124ca92ae80313749abbd22c479fef90ce873
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081521"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Présentation d’Azure et de l’Internet des objets

Azure IoT se compose de trois domaines de technologies et de solutions : solutions, services de plateforme et périphérie, tous conçus pour faciliter le développement de bout en bout des applications IoT. Cet article commence par décrire les caractéristiques communes d’une solution IoT dans le cloud ; il explique ensuite comment Azure IoT résout les problèmes des projets IoT et pourquoi il est intéressant d’adopter Azure IoT.

## <a name="iot-solution-architecture"></a>Architecture de solution IoT

Les solutions IoT nécessitent de sécuriser la communication bidirectionnelle entre les appareils, pouvant se chiffrer par millions, et un serveur principal de solution. Par exemple, une solution peut utiliser l’analyse prédictive automatisée pour découvrir des informations à partir de votre flux d’événements de l’appareil vers le cloud. 

Le diagramme suivant montre les éléments clés d’une architecture de solution IoT classique. Le diagramme est indépendant des détails de mise en œuvre spécifiques tels que les services Azure utilisés et systèmes d’exploitation des appareils. Dans cette architecture, les appareils IoT collectent des données qu’ils envoient à une passerelle cloud. La passerelle cloud met les données à la disposition d’autres services backend pour traitement. Ces services back-end peuvent fournir des données à :

* D’autres applications métier.
* Des opérateurs via un tableau de bord ou un autre appareil de présentation.

![Architecture de solution IoT][img-solution-architecture]

> [!NOTE]
> Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT : Architecture de référence).

### <a name="device-connectivity"></a>Connectivité des appareils

Dans une architecture de solution IoT, les appareils envoient généralement des données de télémétrie au cloud pour stockage et traitement. Par exemple, dans un scénario de maintenance prédictive, le serveur principal de solution peut utiliser le flux des données de capteur pour déterminer si une pompe spécifique nécessite une maintenance. Les appareils peuvent également recevoir des messages « cloud-à-appareil », et y répondre, en lisant les messages issus d’un point de terminaison cloud. Dans le même exemple, le serveur principal de solution peut envoyer des messages aux autres pompes de la station de pompage pour lancer le réacheminement des flux juste avant le début planifié de la maintenance. Cette procédure garantit que l’ingénieur de maintenance peut commencer la maintenance lors de son arrivée sur le site.

La connexion sécurisée et fiable des appareils est souvent le défi le plus important dans les solutions IoT. Cela est dû au fait que les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que des navigateurs et des applications mobiles. Plus précisément, les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain (contrairement à un téléphone) ;
* peuvent être déployés sur des sites distants avec un accès physique coûteux ;
* sont accessibles uniquement via le serveur principal de la solution ; Il n’existe aucun autre moyen d’interagir avec l’appareil.
* peuvent avoir des performances et/ou des ressources de traitement limitées ;
* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
* peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

En plus des contraintes précédentes, une solution IoT doit également être évolutive, sécurisée et fiable.

En fonction du protocole de communication et de la disponibilité du réseau, un appareil peut communiquer avec le cloud directement ou via une passerelle intermédiaire. Les architectures IoT combinent souvent ces deux modèles de communication.

### <a name="data-processing-and-analytics"></a>Traitement et analyse des données

Dans les solutions IoT modernes, le traitement des données peut se faire dans le cloud ou du côté de l’appareil. Le traitement côté appareil est appelé *bord informatique*. Le choix de l’emplacement de traitement des données dépend de facteurs tels que :

* Contraintes de réseau. Si la bande passante entre les appareils et le cloud est limitée, il serait intéressant d’effectuer plus de traitements de bord.
* Temps de réponse. S’il est nécessaire d’agir sur un appareil en temps quasi réel, il peut être préférable de traiter la réponse dans l’appareil. Par exemple, un bras de robot qui doit être arrêté en cas d’urgence.
* Environnement réglementaire. Certaines données ne peuvent pas être envoyées vers le cloud.

En général, le traitement des données à la fois dans le bord et dans le cloud combine les fonctionnalités suivantes :

* Recevoir des données de télémétrie à l'échelle de vos appareils et déterminer comment traiter et stocker ces données.
* Analyser les données de la télémétrie pour fournir des informations, que ce soit en temps réel ou différé.
* Envoyer des commandes à partir du cloud ou d’un appareil de passerelle à un appareil spécifique.

En outre, un back end cloud d’IoT doit fournir :

* Des fonctionnalités d’inscription d’appareils qui vous permettent de :
    * Mettre des appareils en service.
    * Contrôler les appareils qui sont autorisés à se connecter à votre infrastructure.
* Une gestion des appareils pour surveiller l’état de vos appareils et leurs activités.

Par exemple, dans le scénario de maintenance prédictive, le serveur principal cloud stocke les données de télémétrie historiques. La solution utilise ces données pour identifier un comportement anormal potentiel au niveau de pompes spécifiques avant qu’un problème réel ne puisse survenir. À l’aide de l’analyse des données, on peut identifier que la solution préventive consiste à envoyer une commande à l’appareil pour effectuer une action corrective. Ce processus génère une boucle de rétroaction automatisée entre l’appareil et le cloud qui augmente considérablement l’efficacité de la solution.

### <a name="presentation-and-business-connectivity"></a>Présentation et connectivité d’entreprise

Grâce à la couche de présentation et de connectivité d’entreprise, les utilisateurs finaux peuvent interagir avec la solution IoT et les appareils. Les utilisateurs peuvent ainsi afficher et analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports BI qui affichent les données historiques ou les données quasiment en temps réel. Par exemple, un opérateur peut vérifier l’état d’une station de pompage spécifique et voir les alertes éventuellement déclenchées par le système. Cette couche permet également d’intégrer le serveur principal de solution IoT aux applications métier existantes pour une adaptation optimale aux flux de travail et processus d’entreprise. Par exemple, une solution de maintenance prédictive peut intégrer un système de planification qui prévoit une visite d’un ingénieur sur une station de pompage quand elle identifie un besoin de maintenance sur une pompe.

## <a name="why-azure-iot"></a>Pourquoi opter pour Azure IoT ?

Azure IoT réduit la complexité des projets IoT et résout les problèmes de sécurité, d’incompatibilité de l’infrastructure et d’évolutivité des solutions IoT. Voici comment :

**Agile** <br>
Accélérez votre migration vers l’IoT
* Évolutif : commencez petit et allez aussi loin que vous le souhaitez : des millions d’appareils et des téraoctets de données, dans la plupart des régions du monde.

* Ouvert : utilisez l’existant, ou modernisez-vous en vous connectant à n’importe quel appareil, logiciel ou service.

* Hybride : selon vos besoins, déployez votre solution IoT en périphérie, dans le cloud ou un mélange des deux.

* Rapide : accélérez le déploiement et la mise sur le marché, tout en devançant la concurrence avec le chef de file des accélérateurs de solutions et de l’innovation rapide dans l’IoT.

**Exhaustif** <br>
Changez la donne de votre entreprise

* Complet : Microsoft est le seul fournisseur de solutions IoT à offrir une plateforme complète couvrant les appareils et le cloud (Big Data et analytique avancée), avec des services gérés.

* Partenaire de réussite : exploitez toute la puissance du plus grand écosystème de partenaires au monde et concrétisez technologies et systèmes cœur de métier, tous secteurs confondus et dans le monde entier.

* Piloté par les données : l’IoT est une affaire de données ; la meilleure solution IoT est celle qui réunit tous les outils nécessaires pour stocker, interpréter, transformer, analyser et présenter les données dont l’utilisateur a besoin, à l’endroit qui convient et au bon moment.

* Centré sur les appareils : Microsoft IoT permet de tout connecter (des équipements d’ancienne génération à un vaste écosystème de matériel certifié), et vous offre la possibilité de créer vos propres appareils sur des systèmes périphériques, mobiles et embarqués.

**Sécuriser** <br>
Résolvez le plus grand défi de l’IoT : la sécurité

* Puissant : avec Microsoft IoT, vous pouvez atteindre vos objectifs, avec les technologies, les meilleures pratiques et les fonctionnalités nécessaires pour résoudre le plus grand défi de l’IoT : la sécurité.

* Concret : sécurisez vos données IoT et gérez les risques grâce à la gestion des identités et des accès, la protection des informations, la protection contre les menaces et la gestion de la sécurité.

* Tranquillisant : garantissez la sécurité des informations sensibles sur les différents périphériques, logiciels, applications et services cloud, ainsi que les environnements locaux.

* Conforme : chef de file du secteur, Microsoft a établi des exigences de sécurité qui répondent à un large éventail de normes internationales et sectorielles concernant les appareils, les données et les services IoT.

## <a name="next-steps"></a>Étapes suivantes

Explorez les domaines suivants de technologies et de solutions, ou consultez la table des matières à gauche pour voir la liste des services Azure IoT.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>solutions</h3>
                        <a href="/azure/iot-suite">Accélérateurs de solution IoT</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Services de plateforme</h3>
                        <a href="/azure/iot-hub">IoT Hub</a><br/>
                        <a href="/azure/iot-dps">Service IoT Hub Device Provisioning</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">Présentation d’IoT Edge</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../iot-hub/about-iot-hub.md
[lnk-iot-sa]: ../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: https://aka.ms/iotrefarchitecture


