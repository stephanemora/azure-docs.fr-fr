---
title: Les modèles d’applications dans Azure IoT Central | Microsoft Docs
description: Les modèles d’applications Azure IoT Central permettent de passer rapidement au développement de solutions IoT.
author: ankitscribbles
ms.author: ankitgup
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 26f9c41b23353343c5533c83db59320698824233
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470883"
---
# <a name="what-are-application-templates"></a>Présentation des modèles d’application

Les modèles d'applications d'Azure IoT Central constituent un outil très utile pour se lancer dans le développement d'une solution IoT. Les modèles d’applications peuvent avoir plusieurs utilités : vous faire une idée des possibilités offertes ou bien personnaliser entièrement votre application pour la revendre à vos clients.

Les modèles d’applications se décomposent ainsi :

- Exemples de tableaux de bord
- Exemples de modèles d’appareils
- Appareils simulés produisant des données en temps réel
- Règles et travaux préconfigurés
- Documentation complète comprenant des tutoriels et des guides pratiques

Vous choisissez le modèle d’application lorsque vous créez votre application. Vous ne pouvez pas changer le modèle une fois l’application créée.

## <a name="custom-templates"></a>Modèles personnalisés

Si vous souhaitez créer votre application à partir de zéro, choisissez le modèle **Application personnalisée**. L’ID du modèle d’application personnalisé est `iotc-pnp-preview`.

## <a name="industry-focused-templates"></a>Modèles sectoriels

Azure IoT Central est une plateforme d’applications indépendante du secteur. Les modèles d'applications sont des exemples disponibles actuellement dans ces secteurs :

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="connected-logistics"></a>Logistique connectée

Les coûts logistiques mondiaux devraient atteindre 10,6 billions de dollars en 2020. La plus grande partie de ces dépenses est imputable au transport des marchandises, et les transporteurs sont soumis à de fortes contraintes, ainsi qu’à une forte concurrence.

Vous pouvez utiliser des capteurs IoT afin de collecter et de superviser des conditions ambiantes, telles que la température, l’humidité, l’inclinaison, les chocs, la lumière et l’emplacement d’une marchandise. Vous pouvez combiner les données de télémétrie collectées à partir des capteurs et des appareils IoT avec d’autres sources de données, telles que la météo et le trafic dans les systèmes décisionnels basés sur le cloud.

Les avantages d’une solution de logistique connectée sont les suivants :

* Supervision de la cargaison à l’aide d’un suivi et d’un traçage en temps réel 
* Intégrité de la cargaison à l’aide d’une supervision en temps réel des conditions ambiantes
* Sécurité de la cargaison en lien avec le vol, la perte ou les dommages
* Délimitation géographique, optimisation des itinéraires, gestion des flottes et analytique des véhicules
* Prévisions concernant le départ et l’arrivée de la cargaison

Les captures d’écran suivantes montrent le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions :

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-1.png" alt-text="Capture d’écran montrant la moitié supérieure du tableau de bord des opérations logistiques connectées.":::

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-2.png" alt-text="Capture d’écran montrant la moitié inférieure du tableau de bord des opérations logistiques connectées.":::

Pour plus d’informations, consultez le tutoriel [Déployer et découvrir un modèle d’application de logistique connectée](../retail/tutorial-iot-central-connected-logistics.md).

## <a name="digital-distribution-center"></a>Centre de distribution numérique

À mesure que les fabricants et détaillants établissent leur présence dans le monde entier, leurs chaînes logistiques se ramifient et deviennent de plus en plus complexes. Les consommateurs s’attendent à ce que de vastes éventails de produits soient disponibles et à ce que leurs achats leur soient livrés dans un délai d’un ou deux jours après l’achat. Les centres de distribution doivent s’adapter à ces tendances tout en éliminant des inefficacités existantes.

Aujourd’hui, le prélèvement et le conditionnement manuels représentent de 55 à 65 % des coûts des centres de distribution. Le prélèvement et le conditionnement manuels sont généralement plus lents que lorsqu’ils sont automatisés. En outre, la fluctuation rapide des besoins en personnel fait qu’il est encore plus difficile de traiter les volumes élevés des marchandises à expédier. Cette fluctuation saisonnière entraîne un taux élevé de renouvellement du personnel et augmente la probabilité d’erreurs coûteuses.

Des solutions basées sur des caméras compatibles IoT peuvent offrir des avantages en permettant la mise en place d’une boucle de rétroaction numérique. Les données du centre de distribution se transforment en insights actionnables qui, à leur tour, produisent de meilleures données.

Les avantages d’un centre de distribution numérique sont les suivants :

* Supervision à l’aide de caméras des marchandises à mesure qu’elles arrivent et transitent par le système d’acheminement
* Identification automatique des marchandises défectueuses
* Suivi efficace des commandes
* Réduction des coûts, amélioration de la productivité et optimisation de l’utilisation

La capture d’écran suivante montre le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions : 

:::image type="content" source="media/concepts-app-templates/digital-distribution-center-dashboard.png" alt-text="Tableau de bord de centre de distribution numérique":::

Pour plus d’informations, consultez le tutoriel [Déployer et parcourir un modèle d’application du centre de distribution numérique](../retail/tutorial-iot-central-digital-distribution-center.md).

## <a name="in-store-analytics---condition-monitoring"></a>Analytique en magasin – Monitorage des conditions

Pour de nombreux détaillants, les bonnes conditions ambiantes dont bénéficient leurs magasins est ce qui les distingue de leurs concurrents. Les détaillants ont intérêt à maintenir des conditions ambiantes agréables dans leurs magasins pour le bien-être de leurs clients.  

Vous pouvez utiliser le modèle d’application « Analytique dans le magasin – Surveillance des conditions » d’IoT Central afin de créer une solution de bout en bout. Le modèle d’application vous permet de connecter et de superviser numériquement l’environnement d’un magasin de détail en utilisant différentes sortes de capteurs. Ces capteurs génèrent des données de télémétrie que vous pouvez convertir en insights métier afin d’aider le détaillant à réduire ses coûts d’exploitation et à fournir une expérience optimale pour ses clients.

Utilisez le modèle d’application pour :

* Connecter différents genres de capteurs IoT à une instance d’application IoT Central
* Surveiller et gérer l’intégrité du réseau de capteurs et les éventuels appareils de passerelle de l’environnement
* Créer des règles personnalisées en lien avec les conditions ambiantes au sein d’un magasin afin de déclencher des alertes à l’intention des responsables magasin
* Convertir les conditions ambiantes du magasin en insights que l’équipe du magasin peut utiliser pour améliorer l’expérience du client
* Exporter les insights agrégés dans des applications métier existantes ou nouvelles afin de fournir des informations pertinentes aux vendeurs

Le modèle d’application est fourni avec un ensemble de modèles d’appareils, et utilise un ensemble d’appareils simulés pour renseigner le tableau de bord. 

La capture d’écran suivante montre le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions : 

:::image type="content" source="media/concepts-app-templates/in-store-analytics-condition-dashboard.png" alt-text="Surveillance des conditions d’analytique en magasin":::

Pour plus d’information, consultez le tutoriel [Créer une application d’analytique en magasin dans Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="in-store-analytics---checkout"></a>Analytique en magasin – Validation de l’achat

Pour certains détaillants, c’est l’expérience de paiement en magasin qui les distingue de la concurrence. Les détaillants veulent offrir une expérience simplifiée de paiement en magasin afin d’inciter les clients à revenir.  

Vous pouvez utiliser le modèle d’application « Analytique dans le magasin – Paiement » d’IoT Central afin de créer une solution qui fournit aux vendeurs des insights concernant les caisses. Par exemple, les capteurs peuvent fournir des informations sur la longueur des files d’attente et les temps d’attente moyens pour chaque caisse.

Utilisez le modèle d’application pour :

* Connecter différents genres de capteurs IoT à une instance d’application IoT Central
* Surveiller et gérer l’intégrité du réseau de capteurs et les éventuels appareils de passerelle de l’environnement
* Créer des règles personnalisées concernant le paiement en magasin afin de déclencher des alertes à l’intention des vendeurs
* Convertir les conditions de paiement en magasin en insights, que l’équipe du magasin peut utiliser pour améliorer l’expérience du client
* Exporter les insights agrégés dans des applications métier existantes ou nouvelles afin de fournir des informations pertinentes aux vendeurs

Le modèle d’application est fourni avec un ensemble de modèles d’appareils, et utilise un ensemble d’appareils simulés pour renseigner le tableau de bord avec les données concernant la longueur des files d’attente de chaque caisse. 

La capture d’écran suivante montre le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions : 

:::image type="content" source="media/concepts-app-templates/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Caisse d’analytique en magasin":::

Pour plus d’information, consultez le tutoriel [Créer une application d’analytique en magasin dans Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="smart-inventory-management"></a>Gestion intelligente des stocks

Le stock correspond aux marchandises que détient un détaillant. La gestion des stocks est essentielle pour s’assurer que le bon produit se trouve au bon endroit au bon moment. Un détaillant doit trouver un équilibre entre les coûts liés au stockage d’un trop grand nombre d’articles et les coûts liés au fait de ne pas avoir suffisamment d’articles en stock pour répondre à la demande.

Les données IoT obtenues à partir d’étiquettes RFID, de balises et de caméras permettent d’améliorer les processus impliqués dans la gestion des stocks. Vous pouvez combiner les données de télémétrie collectées à partir des capteurs et des appareils IoT avec d’autres sources de données, telles que la météo et le trafic dans les systèmes décisionnels basés sur le cloud.

Les avantages de la gestion des stocks intelligente sont les suivants :

* Réduction du risque de rupture de stock des articles et maintien du niveau de service client souhaité 
* Analyse approfondie et insights sur la précision du stock en quasi temps réel
* Outils permettant de déterminer le niveau de stock adapté pour répondre aux commandes des clients

Ce modèle d’application se concentre sur la connectivité des appareils, ainsi que sur la configuration et la gestion des lecteurs RFID et Bluetooth basse énergie.

La capture d’écran suivante montre le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions :

:::image type="content" source="media/concepts-app-templates/smart-inventory-management-dashboard.png" alt-text="Tableau de bord de gestion intelligente des stocks":::

Pour plus d’informations, consultez le tutoriel [Déployer et découvrir un modèle de gestion intelligente des stocks IoT pour Azure IoT Central](../retail/tutorial-iot-central-smart-inventory-management.md).

## <a name="micro-fulfillment-center"></a>Centre de microtraitement

En raison d’une concurrence de plus en plus accrue, les détaillants se doivent de répondre le plus rapidement possible à la demande. Une nouvelle tendance est apparue pour répondre à la demande croissante des consommateurs. Celle-ci consiste à héberger un stock proche des clients finaux et des magasins dans lesquels ils se rendent.

Le modèle d’application Centre de microtraitement d’IoT Central vous permet de superviser et de gérer tous les aspects de vos centres de traitement entièrement automatisés. Ce modèle comprend des capteurs pour la supervision des conditions ambiantes, ainsi que des robots de transport pour accélérer le processus de développement de la solution. Ces capteurs collectent des signaux significatifs qui peuvent être convertis en insights métier permettant aux détaillants de réduire leurs coûts d’exploitation et d’offrir des expériences appréciées des clients.

Ce modèle d’application vous permet de : 

- Connecter de façon continue différentes sortes de capteurs IoT comme des robots ou des capteurs de supervision des conditions ambiantes à une instance d’application IoT Central
- Superviser et gérer l’intégrité du réseau de capteurs et les éventuels appareils de passerelle de l’environnement
- Créer des règles personnalisées en lien avec les conditions ambiantes au sein d’un centre de traitement afin de déclencher les alertes nécessaires
- Convertir les conditions ambiantes du centre de traitement en insights exploitables par l’équipe de l’entrepôt
- Exporter des insights agrégés dans des applications métier existantes ou nouvelles à l’intention des vendeurs

La capture d’écran suivante montre le tableau de bord prêt à l’emploi du modèle d’application. Le tableau de bord est entièrement personnalisable afin de mieux répondre à vos besoins en solutions :

:::image type="content" source="media/concepts-app-templates/MFC-Dashboard.png" alt-text="Centre de microtraitement":::

Pour plus d’informations, consultez le tutoriel [Déployer et parcourir un modèle d’application du Centre de microtraitement](../retail/tutorial-micro-fulfillment-center.md).

## <a name="video-analytics---object-and-motion-detection"></a>Analytique vidéo - Détection d’objets et de mouvements

Le *modèle d’application d’analytique vidéo pour la détection d’objet et de mouvement Azure IoT Central* vous permet d’expérimenter rapidement la manière de déployer, gérer et surveiller une solution qui utilise des caméras de périphérie intelligentes pour la détection d’objet et de mouvement.

L’application d’analytique vidéo utilise un module d’[analytique vidéo en direct](#live-video-analytics) s’exécutant dans IoT Edge. Le module d’analytique vidéo en direct fournit une plateforme vous permettant de créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. Vous pouvez utiliser la plateforme pour améliorer des solutions IoT, telles que l’application d’analytique vidéo, avec la détection d’objet et de mouvement.

Le modèle d’application comprend quatre tableaux de bord d’application :

* **Bien démarrer** fournit des liens vers des ressources pour vous aider à commencer à utiliser le modèle d’application.

- **Tableau de bord de démonstration** fournit une illustration des types d’informations que vous pouvez afficher à partir de vos caméras connectées.
- **(Exemple) Gestion de caméras réelles** utilise des caméras simulées pour montrer comment vous pouvez gérer vos caméras à partir de l’application.
- **(Exemple) Supervision de caméras réelles** utilise des caméras simulées pour montrer comment vous pouvez superviser vos caméras à partir de l’application.

:::image type="content" source="media/concepts-app-templates/live-video-analytics.png" alt-text="Analytique vidéo - Détection d’objets et de mouvements":::

Pour savoir comment déployer la solution, consultez le tutoriel [Créer une application d’analytique vidéo dans Azure IoT Central](../retail/tutorial-video-analytics-deploy.md).

### <a name="live-video-analytics"></a>Analytique vidéo en direct

L’[analytique vidéo en direct](https://github.com/Azure/live-video-analytics) fournit une plateforme pour créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. La plateforme offre la possibilité de capturer, d’enregistrer, d’analyser des vidéos en direct et de publier les résultats, tels qu’une analytique vidéo, dans les services Azure. Les services Azure peuvent s’exécuter dans le cloud ou à la périphérie. La plateforme permet d’améliorer des solutions IoT avec une analytique vidéo.

## <a name="smart-meter-monitoring"></a>Monitorage des compteurs intelligents

 Les compteurs intelligents permettent d'automatiser la facturation et offrent des fonctionnalités avancées, telles que les relevés en temps réel et la communication bidirectionnelle. Le modèle d'application de suivi des compteurs intelligents permet aux fournisseurs d'énergie et aux partenaires de suivre l'état et les données des compteurs intelligents, et de définir des alarmes et des notifications. Il propose des exemples de commandes, pour déconnecter les compteurs et mettre à jour les logiciels par exemple. Les données des compteurs peuvent être configurées pour être transférées vers d'autres applications métier et pour développer des solutions personnalisées. 

Fonctionnalités clés de l’application :

- Modèle d'exemple de compteur
- Informations sur le compteur et état en temps réel
- Relevés des compteurs (énergie, puissance et tension, par exemple)
- Exemples de commandes de compteur 
- Visualisations et tableaux de bord intégrés
- Extensibilité pour le développement de solutions personnalisées

Vous pouvez essayer [gratuitement l'application de suivi des compteurs intelligents](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) sans abonnement Azure et sans engagement.

Une fois l'application déployée, les données simulées des compteurs apparaissent sur le tableau de bord, comme illustré par la figure ci-dessous. Ce modèle est un exemple d'application que vous pouvez facilement étendre et personnaliser pour vos cas d'usage spécifiques.

:::image type="content" source="media/concepts-app-templates/smart-meter-app-dashboard.png" alt-text="Tableau de bord de l'application de suivi des compteurs intelligents":::

## <a name="solar-panel-monitoring"></a>Monitorage des panneaux solaires

L'application de suivi des panneaux solaires permet aux fournisseurs d'énergie et aux partenaires de suivre quasiment en temps la production d'énergie et l'état de connexion des panneaux solaires. Elle peut envoyer des notifications en fonction de critères de seuil définis. Elle propose des exemples de commandes, pour mettre à jour le microprogramme et pour définir d'autres propriétés par exemple. Les données des panneaux solaires peuvent être configurées pour être transférées vers d'autres applications métier et pour développer des solutions personnalisées. 

Fonctionnalités clés de l’application : 

- Modèle d'exemple de panneau solaire 
- Informations sur le panneau solaire et état en temps réel
- Production d'énergie solaire et autres relevés
- Exemples de commandes et de contrôles
- Visualisations et tableaux de bord intégrés
- Extensibilité pour le développement de solutions personnalisées

Vous pouvez essayer [gratuitement l'application de suivi des panneaux solaires](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) sans abonnement Azure et sans engagement.

Une fois l'application déployée, les données simulées des panneaux solaires apparaissent au bout d'une à deux minutes, comme illustré sur le tableau de bord ci-dessous. Ce modèle est un exemple d'application que vous pouvez facilement étendre et personnaliser pour vos cas d'usage spécifiques. 

:::image type="content" source="media/concepts-app-templates/solar-panel-app-dashboard.png" alt-text="Tableau de bord de l'application de suivi des panneaux solaires":::

## <a name="water-quality-monitoring"></a>Suivi de la qualité de l’eau

La surveillance de la qualité de l’eau repose sur des techniques d’échantillonnage manuelles et l’analyse en laboratoire de terrain, ce qui est laborieux et coûteux. La supervision à distance de la qualité de l’eau en temps réel permet de gérer les problèmes de qualité de l’eau avant que des citoyens soient affectés. De plus, avec l’analytique avancée, les distributeurs d’eau et les agences environnementales peuvent réagir à des avertissements précoces concernant des problèmes potentiels de qualité de l’eau et planifier le traitement de l’eau.  

L’application Suivi de la qualité de l’eau est un modèle d’application IoT Central destiné à vous aider à démarrer rapidement le développement de votre solution IoT et à permettre aux distributeurs d’eau de surveiller numériquement la qualité de l’eau dans des villes intelligentes.

:::image type="content" source="media/concepts-app-templates/water-quality-monitoring-dashboard-full.png" alt-text="Modèle d’application Suivi de la qualité de l’eau":::

Le modèle d’application se compose des éléments suivants :

- Exemples de tableaux de bord
- Exemples de modèles d’appareil de surveillance de la qualité de l’eau
- Appareils de surveillance de la qualité de l’eau simulés
- Règles et travaux préconfigurés
- Personnalisation à l’aide d’un étiquetage blanc 

Prise en main avec le [didacticiel d’application Suivi de la qualité de l’eau](../government/tutorial-water-quality-monitoring.md).

## <a name="water-consumption-monitoring"></a>Suivi de la consommation d’eau

Le suivi traditionnel de la consommation d’eau repose sur les distributeurs d’eau qui relèvent manuellement les compteurs de consommation d’eau sur le terrain. De plus en plus de villes remplacent les compteurs traditionnels par des compteurs intelligents avancés permettant une supervision à distance de la consommation, ainsi qu’un contrôle à distance des vannes pour maîtriser le débit d’eau. Le suivi de la consommation d’eau combiné avec un message de commentaire numérique adressé au consommateur peut contribuer à sensibiliser à la consommation d’eau et à la réduction de celle-ci. 

L’application Suivi de la consommation d’eau est un modèle d’application IoT Central destiné à vous aider à démarrer rapidement le développement de votre solution IoT et à permettre aux distributeurs d’eau et aux villes de surveiller et contrôler à distance le début d’eau afin de réduire la consommation. 

:::image type="content" source="media/concepts-app-templates/water-consumption-monitoring-dashboard-full.png" alt-text="Modèle d’application Suivi de la consommation d’eau":::

Le modèle d’application Suivi de la consommation d’eau est constitué des composants préconfigurés suivants :

- Exemples de tableaux de bord
- Exemples de modèles d’appareil de surveillance de la qualité de l’eau
- Appareils de surveillance de la qualité de l’eau simulés
- Règles et travaux préconfigurés
- Personnalisation à l’aide d’un étiquetage blanc

 Prise en main avec le [didacticiel d’application Suivi de la consommation d’eau](../government/tutorial-water-consumption-monitoring.md).

## <a name="connected-waste-management"></a>Gestion des déchets connectée 

L’application Gestion connectée des déchets est un modèle d’application IoT Central destiné à vous aider à démarrer rapidement le développement de votre solution IoT afin de permettre aux villes intelligentes d’effectuer une surveillance à distance afin d’optimiser la collecte des déchets. 

:::image type="content" source="media/concepts-app-templates/connected-waste-management-dashboard.png" alt-text="Modèle d’application Gestion connectée des déchets":::

Le modèle d’application Gestion connectée des déchets est constitué de composants préconfigurés :

- Exemples de tableaux de bord
- Exemples de modèles de conteneur de déchets connectés
- Conteneur de déchets connectés simulés
- Règles et travaux préconfigurés
- Personnalisation à l’aide d’un étiquetage blanc 

Prise en main avec le [didacticiel d’application Gestion connectée des déchets](../government/tutorial-connected-waste-management.md).

## <a name="continuous-patient-monitoring"></a>Monitorage continu des patients 

Dans l'espace IoT dédié à la santé, la Surveillance continue des patients est un élément clé pour réduire le risque de réadmission, gérer plus efficacement les maladies chroniques et améliorer les résultats des patients. La surveillance continue des patients peut être divisée en deux grandes catégories :

1. La **surveillance des patients hospitalisés** : grâce à des dispositifs médicaux portables et à d'autres appareils disponibles à l'hôpital, les équipes de soins peuvent surveiller les constantes vitales et l'état de santé des patients. Il n'est ainsi plus nécessaire d'envoyer une infirmière au chevet du patient plusieurs fois par jour. Et des notifications permettent aux équipes de soins de déterminer quand un patient a besoin d'une attention particulière, ce qui les aide à mieux hiérarchiser leur temps.
1. La **surveillance à distance des patients** : l’utilisation de dispositifs médicaux portables et des résultats rapportés par les patients pour suivre ceux-ci en dehors de l’hôpital peut réduire le risque de réadmission. Les données des patients atteints de maladies chroniques et des patients en rééducation peuvent être recueillies pour s'assurer qu'ils respectent leur traitement et pour veiller à ce que les alertes de détérioration de leur état soient transmises aux équipes de soins avant que cet état ne devienne critique.

Ce modèle d'application peut être utilisé pour créer des solutions dans les deux catégories de surveillance continue des patients. Voici les avantages :

- Connecter de façon continue différentes sortes de dispositifs médicaux portables à une instance IoT Central
- Surveiller et gérer les appareils pour garantir leur intégrité
- Créer des règles personnalisées pour les données des appareils afin de déclencher les alertes appropriées
- Exporter les données médicales des patients vers l'API Azure pour FHIR, un magasin de données conforme
- Exporter les insights agrégés vers des applications métier existantes ou nouvelles

:::image type="content" source="media/concepts-app-templates/in-patient-dashboard.png" alt-text="CPM-dashboard":::


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les modèles d’applications IoT Central, lancez-vous en [créant une application IoT Central](quick-deploy-iot-central.md).
