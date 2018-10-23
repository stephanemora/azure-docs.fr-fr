---
title: Vue d’ensemble d’Azure Digital Twins | Microsoft Docs
description: En savoir plus sur Azure Digital Twins, une solution Azure IoT destinée à l’intelligence spatiale.
author: julieseto
ms.author: jseto
ms.date: 10/10/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 85b67683730c8352051b4d6b48f813576b11615b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322756"
---
# <a name="overview-of-azure-digital-twins"></a>Vue d’ensemble d’Azure Digital Twins

Azure Digital Twins est un service Azure IoT qui permet de créer des modèles complets de l’environnement physique. Parmi ses nombreuses fonctionnalités figure la possibilité de créer des graphiques d’intelligence spatiale afin de modéliser les relations et les interactions entre les personnes, les espaces et les appareils.

Azure Digital Twins permet d’interroger les données depuis un espace physique plutôt que depuis de nombreux capteurs dispersés. Ce service permet de créer des expériences réutilisables, très évolutives et adaptées à l’espace qui relient en permanence les données entre le monde numérique et le monde physique. Qu’il s’agisse de prévoir les besoins de maintenance d’une usine, d’analyser les besoins énergétiques en temps réel d’un réseau électrique ou d’optimiser l’utilisation des espace disponibles dans un bureau, ces caractéristiques contextuelles pertinentes uniques améliorent la qualité de vos applications.

Azure Digital Twins s’applique à tous les types d’environnements : entrepôts, bureaux, écoles, hôpitaux, banques, stades, usines, parkings, parcs, réseaux intelligents, villes, etc. Pensez à des scénarios comme le suivi de la température quotidienne dans plusieurs états, la surveillance des trajets de drones utilisés, l’identification des véhicules autonomes, l’analyse des taux d’occupation d’un bâtiment ou la recherche de la caisse enregistreuse qui a le plus fonctionné dans votre magasin. Quel que soit votre scénario métier dans le monde réel, Azure Digital Twins peut vous fournir une instance numérique correspondante.

La vidéo suivante fournit davantage de détails sur Digital Twins :

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Fonctionnalités clés

Voici quelques-unes des principales fonctionnalités d’Azure Digital Twins :

### <a name="spatial-intelligence-graph"></a>Graphique d’intelligence spatiale

Le [*graphique d’intelligence spatiale*](./concepts-objectmodel-spatialgraph.md) ou *graphique spatial* est une représentation virtuelle de l’environnement physique permettant de modéliser les relations entre les personnes, les lieux et les appareils.

Prenons l’exemple d’une application utilitaire intelligente pouvant comporter plusieurs compteurs d’électricité connectés dans un quartier. Pour surveiller et prévoir avec précision la consommation d’électricité et la facturation, l’entreprise du service public intelligent doit modéliser chaque appareil et capteur en tenant compte du contexte de l’emplacement et du client recevant la facture. Le graphique d’intelligence spatiale vous permet de modéliser ce type de relations complexes.

### <a name="digital-twin-object-models"></a>Modèles d’objets jumeaux numériques

Les [modèles d’objets jumeaux numériques](./concepts-objectmodel-spatialgraph.md) sont des protocoles d’appareils et des schémas de données prédéfinis qui adaptent les besoins spécifiques au domaine de votre solution pour en accélérer le développement et le simplifier.

Par exemple, une application d’occupation de salle peut utiliser des types d’espace prédéfinis comme campus, bâtiment, étage, salle, etc.

### <a name="multiple-and-nested-tenants"></a>Locataires multiples et imbriqués

Vous pouvez créer des solutions qui se mettent à l’échelle en toute sécurité et peuvent être réutilisées pour plusieurs locataires. Vous pouvez également créer plusieurs sous-locataires auxquels il est possible d’accéder et pouvant être utilisés de façon isolée et sécurisée.

Par exemple, une application d’utilisation de l’espace peut être configurée pour isoler les données d’un locataire des données d’autres locataires à l’intérieur d’un même bâtiment, ou pour regrouper les données concernant un locataire et plusieurs bâtiments.

### <a name="advanced-compute-capabilities"></a>Fonctionnalités de calcul avancées

Les fonctionnalités de calcul avancées appelées [fonctions définies par l’utilisateur](./concepts-user-defined-functions.md) vous permettent de définir et d’exécuter des fonctions personnalisées en fonction des [données des appareils](./concepts-device-ingress.md) en entrée afin d’envoyer des signaux aux points terminaux pré-définis. Cette fonctionnalité améliore la personnalisation et l’automatisation des tâches des appareils.

Par exemple, une application agricole intelligente peut inclure une fonction définie par l’utilisateur permettant d’évaluer les relevés des capteurs d’humidité du sol et les prévisions météorologiques, puis d’envoyer des signaux concernant les besoins en irrigation.

### <a name="built-in-access-control"></a>Contrôle d’accès intégré

Les fonctionnalités de gestion des accès et des identités telles que le [contrôle d’accès basé sur les rôles](./security-role-based-access-control.md) et [Azure Active Directory](./security-authenticating-apis.md) vous permettent de contrôler en toute sécurité l’accès des personnes et des appareils.

Par exemple, une application de gestion des installations peut être configurée de façon à permettre aux occupants d’une pièce de régler la température à l’intérieur d’une plage spécifiée, et aux gestionnaires d’installations de régler la température d’une pièce à une valeur quelconque.

### <a name="ecosystem"></a>Écosystème

Vous pouvez connecter une instance Azure Digital Twins à de nombreux services Azure performants, notamment : Azure Stream Analytics, AI, les services de stockage, Azure Maps, Microsoft Mixed Reality, Dynamics 365 et Office 365.

Par exemple, une application intelligente d’immeuble de bureaux peut utiliser Azure Digital Twins pour représenter des équipes et des appareils situés sur plusieurs étages. À mesure que les appareils transmettent les données en direct à l’instance Digital Twin fournie, Azure Stream Analytics peut les traiter afin d’obtenir des informations essentielles qui soient exploitables. Les données peuvent ensuite être stockées dans Stockage Azure et converties en un format de fichier partageable afin d’être distribuées dans toute l’organisation par le biais d’Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Les solutions qui profitent des avantages d’Azure Digital Twins

Azure Digital Twins est un outil pratique pour représenter le monde physique et ses nombreuses relations puisqu’il simplifie la modélisation IoT, le traitement des données, le traitement des événements et le suivi des appareils. Examinons quelques-uns des scénarios suivants qui relèvent de différents secteurs d’activité et qui tirent profit de son utilisation :

* Montrer à une société de gestion immobilière le taux d’occupation d’un espace au fil du temps afin de savoir quelle est la meilleure façon de configurer son immeuble de bureaux.
* Déclencher des tickets de bon de travail pour une application mobile qui pilote la répartition des agents de sécurité, la planification des services de conciergerie et tous les autres services dans un espace commercial ou sur un centre sportif.
* Afficher en temps réel à l’occupant d’un bâtiment les pièces occupées dans un bâtiment. Permettre ensuite à l’occupant de réserver des espaces de travail qui correspondent à ses besoins.
* Suivre la localisation des ressources au sein d’un espace.
* Optimiser la charge des véhicules électriques en modélisant les préférences des utilisateurs et les contraintes du réseau électrique.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Azure Digital Twins dans le cadre d’autres services IoT

Azure Digital Twins utilise Azure IoT Hub pour connecter les dispositifs et capteurs IoT qui actualisent l’ensemble avec le monde physique. Le diagramme suivant montre comment Azure Digital Twins se rapporte aux autres services Azure IoT :

![Azure Digital Twins est un service basé sur Azure IoT Hub](./media/overview/azure-digital-twins-in-iot-ecosystem.png)

Pour obtenir une description plus détaillée du reste de l’écosystème IoT, consultez [Technologies et solutions Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Étapes suivantes

Passez à une courte démonstration des fonctionnalités d’Azure Digital Twins :

> [!div class="nextstepaction"]
> [Démarrage rapide : Rechercher des salles disponibles avec Azure Digital Twins](./quickstart-view-occupancy-dotnet.md)

Découvrez en détail une application de gestion d’installations utilisant Azure Digital Twins :

> [!div class="nextstepaction"]
> [Didacticiel : Déployer Azure Digital Twins et configurer un graphique spatial](./tutorial-facilities-setup.md)

En savoir plus sur les concepts de base d’Azure Digital Twins :

> [!div class="nextstepaction"]
> [Comprendre le modèle d’objet Digital Twins et le graphique d’intelligence spatiale](./concepts-objectmodel-spatialgraph.md)
