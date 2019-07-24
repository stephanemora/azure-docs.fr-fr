---
title: Architecture de référence des accélérateurs de solution IoT - Azure | Microsoft Docs
description: Familiarisez-vous avec l’architecture de référence des accélérateurs de solution Azure IoT. Les accélérateurs de solution existants reposent sur cette architecture de référence. Vous pouvez également utiliser l'architecture de référence pour créer vos propres solutions IoT personnalisées.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: 01349dbc5fe98fd69ce7219ef6f2029d7bb815d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067906"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Introduction à l'architecture de référence Azure IoT

Cet article présente l'[architecture de référence Azure IoT](https://aka.ms/iotrefarchitecture) et fournit des exemples sur la façon dont les [accélérateurs de solution Azure IoT](about-iot-accelerators.md) suivent ses recommandations.

Les accélérateurs de solution open source [Supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md) et [Usine connectée](iot-accelerators-connected-factory-features.md) suivent la plupart des recommandations de l'architecture de référence. Vous pouvez utiliser les accélérateurs de solution comme point de départ de votre propre solution IoT ou comme outils d'apprentissage.

## <a name="overview"></a>Vue d'ensemble

L'architecture de référence décrit les éléments qui constituent une solution IoT, comme les principes technologiques, la composition des services Azure IoT et les appareils. L'architecture effectue également des recommandations sur les technologies d'implémentation.

Au plus haut niveau, vous pouvez considérer qu'une solution IoT est constituée de ce qui suit :

* Appareils qui génèrent et envoient des données de télémétrie telles que des mesures et des événements. Par exemple, dans l’accélérateur de solution de supervision à distance, les camions et les ascenseurs envoient des données de télémétrie.
* Insights générés à partir des données de télémétrie envoyées par les appareils. Dans l’accélérateur de solution de supervision à distance, une règle génère un insight. Par exemple, une règle peut identifier le moment où la température atteint un certain seuil dans un moteur.
* Actions, basées sur les insights, qui aident à améliorer une activité ou un processus. Dans l’accélérateur de solution de supervision à distance, une action par e-mail peut avertir un opérateur d’un problème potentiel sur un moteur.

L'[architecture de référence Azure IoT](https://aka.ms/iotrefarchitecture) est un document évolutif mis à jour à mesure que la technologie progresse.

## <a name="core-subsystems"></a>Sous-systèmes principaux

L'architecture de référence identifie les sous-systèmes principaux représentés dans le diagramme suivant :

![Sous-systèmes principaux](media/iot-accelerators-architecture-overview/coresubsystems1.png)

Les sections suivantes décrivent la façon dont les composants de l’accélérateur de solution de supervision à distance sont mis en correspondance avec les sous-systèmes principaux.

### <a name="iot-devices"></a>Appareils IoT

Une solution IoT doit permettre une communication sécurisée, efficace et robuste entre pratiquement tous les types d'appareils et une passerelle cloud. Les appareils sont des ressources d'entreprise allant de simples capteurs de température à des chaînes de production complexes comportant des centaines de composants et de capteurs.

Une passerelle locale, ou appareil de périmètre, est un appareil spécialisé ou un logiciel à usage général qui peut agir en tant que :

* Facilitateur de communication pour gérer la conversion de protocole.
* Système local de contrôle des appareils et centre de traitement des données de télémétrie des appareils. Un appareil de périmètre peut traiter les données de télémétrie localement pour contrôler les appareils sans communiquer avec le cloud. Un appareil de périmètre peut également filtrer ou agréger les données de télémétrie des appareils afin de réduire le volume de données de télémétrie transférées vers le cloud.

Dans la solution de supervision à distance, les appareils se connectent à un hub IoT et envoient des données de télémétrie à des fins de traitement. La solution de supervision à distance permet également aux opérateurs de gérer les appareils à l’aide de travaux ou d’un processus automatique. Vous pouvez utiliser les kits Azure IoT device SDK pour implémenter vos appareils.

La solution de supervision à distance peut déployer et gérer des appareils IoT Edge. Le traitement en périphérie permet de réduire le volume des données de télémétrie transférées vers le cloud et d'accélérer les réponses fournies aux événements des appareils.

### <a name="cloud-gateway"></a>Passerelle cloud

Une passerelle cloud permet la communication vers et depuis les appareils et appareils de périmètre. Ces appareils peuvent se trouver sur de nombreux sites distants.

La passerelle gère les communications des appareils, notamment la gestion des connexions, la protection du chemin de communication, l'authentification et les autorisations. La passerelle applique également des quotas de connexion et de débit, et collecte les données de télémétrie utilisées pour la facturation, le diagnostic et d'autres tâches de supervision.

La solution de supervision à distance déploie un hub IoT pour fournir un point de terminaison sécurisé aux appareils auxquels les données de télémétrie doivent être envoyées. En outre, le hub IoT :

* inclut un magasin d'identités d'appareils pour gérer les appareils autorisés à se connecter à la solution ;
* permet à la solution d'envoyer des commandes aux appareils. Par exemple, pour ouvrir une vanne afin de relâcher la pression ;
* permet la gestion en bloc des appareils. Par exemple, pour procéder à la mise à niveau du microprogramme sur un ensemble d'appareils.

### <a name="stream-processing"></a>Traitement des flux de données

Au fur et à mesure que la solution ingère des données de télémétrie, il est impératif de comprendre de quelle façon le flux de traitement des données de télémétrie peut varier. En fonction du scénario, les enregistrements de données peuvent passer par différentes étapes :

* Stockage, comme les caches en mémoire, les files d'attente temporaires et les archives permanentes.

* Analyse, pour exécuter les données de télémétrie d'entrée en fonction d'un ensemble de conditions, ce qui peut produire différents enregistrements de données de sortie. Par exemple, des données de télémétrie d'entrée codées en Avro peuvent renvoyer des données de télémétrie de sortie codées en JSON.

* Les données de télémétrie d'entrée d'origine et les enregistrements de sortie d'analyse sont généralement stockés et mis à disposition pour l'affichage. Les données de télémétrie d'entrée et les enregistrements de sortie peuvent également déclencher des actions telles que des e-mails, des tickets d'incident et des commandes d'appareil.

Le routage peut acheminer les données de télémétrie vers un(e) ou plusieurs points de terminaison de stockage, processus d'analyse et actions. Une solution peut combiner les étapes dans des ordres différents et les traiter avec des tâches parallèles simultanées.

La solution de supervision à distance utilise [Azure Stream Analytics](/azure/stream-analytics/) pour le traitement de flux. Le moteur de règles de la solution utilise des requêtes Stream Analytics pour générer les alertes et les actions. Par exemple, la solution peut utiliser une requête pour identifier le moment où, sur cinq minutes, la température moyenne du compartiment de stockage d'un camion passe en dessous des 2 degrés Celsius.

### <a name="storage"></a>Stockage

Les solutions IoT peuvent générer de gros volumes de données, généralement des données de séries chronologiques. Ces données doivent être stockées là où elles peuvent être utilisées pour la visualisation et la création de rapports. Une solution peut également avoir besoin d'accéder ultérieurement aux données à des fins d'analyse ou de traitement supplémentaire. Les données sont souvent réparties en deux magasins : le magasin de données chaudes et le magasin de données froides. Le magasin de données chaudes contient les données récentes pour un accès à faible latence. Le magasin de données froides stocke généralement les données historiques.

La solution de supervision à distance utilise [Azure Time Series Insights](/azure/time-series-insights/) comme magasin de données chaudes et Cosmos DB comme magasin de données froides.

### <a name="ui-and-reporting-tools"></a>Interface utilisateur et outils de création de rapports

L'interface utilisateur de la solution peut fournir ce qui suit :

* Accès aux données de l'appareil et aux résultats d'analyse, et visualisation de ceux-ci
* Détection des appareils via le registre
* Commande et contrôle des appareils.
* Flux de travail de provisionnement des appareils
* Notifications et alertes
* Intégration à des tableaux de bord interactifs en temps réel pour afficher les données d'un grand nombre d'appareils  
* Géolocalisation et services géo-conscients

La solution de supervision à distance comprend une interface utilisateur web pour fournir cette fonctionnalité. L'interface utilisateur web inclut ce qui suit :

* Une carte interactive pour désigner l'emplacement des appareils
* Un accès à l'explorateur de Time Series Insights pour interroger et analyser les données de télémétrie

### <a name="business-integration"></a>Intégration d’entreprise

La couche d'intégration d'entreprise gère l'intégration de la solution IoT à des systèmes d'entreprise tels que les applications CRM, ERP et métier. Exemples : facturation des services, assistance clientèle et fourniture de pièces de rechange.

La solution de supervision à distance utilise des règles pour envoyer des e-mails lorsqu’une condition est remplie. Par exemple, la solution peut avertir un opérateur lorsque la température d'un camion passe en dessous des 2 degrés Celsius.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté l’architecture de référence Azure IoT et fourni quelques exemples sur la façon dont l’accélérateur de solution de supervision à distance suit ses recommandations. L'étape suivante consiste à lire l'article [Architecture de référence Microsoft Azure IoT](https://aka.ms/iotrefarchitecture).