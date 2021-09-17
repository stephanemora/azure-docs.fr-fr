---
title: 'Tutoriel : analyse de compteurs intelligents Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application d’analyse de compteurs intelligents pour IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179457"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutoriel : déployer et découvrir pas à pas le modèle d’application d’analyse de compteurs intelligents 

Utilisez le modèle d’application d’*analyse de compteurs intelligents* IOT Central et les instructions de cet article pour développer une solution d’analyse de compteurs intelligents de bout en bout.

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="architecture des compteurs intelligents.":::

Cette architecture est constituée des composants suivants. Certaines solutions peuvent ne pas avoir besoin de tous les composants listés ici.

### <a name="smart-meters-and-connectivity"></a>Compteurs intelligents et connectivité

Un compteur intelligent est un des appareils les plus importants parmi l’ensemble des ressources énergétiques. Il enregistre et communique les données de consommation d’énergie aux services publics pour le suivi et d’autres cas d’usage, tels que la facturation et la réponse à la demande. Selon le type du compteur, il peut se connecter à IoT Central via des passerelles ou d’autres appareils ou systèmes intermédiaires, tels que des appareils de périmètre et des systèmes de tête de réseau. Créez un pont d’appareil IoT Central pour connecter les appareils qui ne peuvent pas être connectés directement. Le pont IoT Central est une solution open source ; vous pouvez trouver des informations complètes à son sujet [ici](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>Plateforme IoT Central

Azure IoT Central est une plateforme qui simplifie la création de votre solution IoT et contribue à réduire la charge et les coûts liés à la gestion, aux opérations et au développement IoT. Avec IoT Central, vous pouvez facilement connecter, surveiller et gérer facilement vos ressources Internet des objets (IoT) à grande échelle. Une fois que vous avez connecté vos compteurs intelligents à IoT Central, le modèle d’application utilise des fonctionnalités intégrées, comme des modèles d’appareil, des commandes et des tableaux de bord. Le modèle d’application utilise également le stockage IoT Central pour les scénarios de chemin à chaud tels que la surveillance, l’analyse, les règles et la visualisation des données de mesure en temps quasi-réel. 

### <a name="extensibility-options-to-build-with-iot-central"></a>Options d’extensibilité pour créer avec IoT Central

La plateforme IoT Central fournit deux options d’extensibilité : L’exportation de données continue (CDE) et des API. Les clients et partenaires peuvent choisir entre ces options pour personnaliser leurs solutions selon leurs besoins spécifiques. Par exemple, un de nos partenaires a configuré CDE avec Azure Data Lake Storage (ADLS). Ils utilisent ADLS pour la conservation à long terme des données et d’autres scénarios de stockage à froid, tels que le traitement par lots, l’audit et la création de rapports.

Dans ce tutoriel, vous allez apprendre à :

- Créer l’application de compteur intelligent gratuitement
- Découvrir l'application pas à pas
- Nettoyer les ressources

## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="create-a-smart-meter-monitoring-application"></a>Créer une application d’analyse de compteurs intelligents

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Énergie** :

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="Modèle de compteurs intelligents":::

1. Sélectionnez **Créer une application**, sous **Analyse de compteurs intelligents**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord

Une fois le modèle d’application correctement déployé, il comprend un échantillon d’appareil de compteur intelligent, un modèle d’appareil et un tableau de bord. 

Adatum est un fournisseur d’énergie fictif qui surveille et gère des compteurs intelligents. Le tableau de bord de surveillance de compteur intelligente, affiche des propriétés, des données et des exemples de commandes. Il permet aux opérateurs et aux équipes de support technique d'effectuer de manière proactive les activités suivantes avant l'apparition d'incidents : 
* Examiner les dernières informations du compteur et l’[emplacement](../core/howto-use-location-data.md) où celui-ci est installé sur la carte
* Vérifier de manière proactive le réseau du compteur et son état de la connexion 
* Surveiller les mesures de tensions minimale et maximale pour vérifier l’intégrité du réseau 
* Examiner les tendances en termes d’énergie, de puissance et de tension pour détecter d’éventuelles anomalies 
* Suivre la consommation énergétique totale à des fins de planification et de facturation
* Opérations de commande et de contrôle telles que la reconnexion du compteur et la mise à jour de la version du microprogramme. Dans le modèle, les boutons de commande affichent les fonctionnalités possibles et n'envoient pas de commandes réelles. 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="Tableau de bord d’analyse de compteurs intelligents.":::

### <a name="devices"></a>Appareils

L’application est fournie avec un exemple d’appareil de compteur intelligent. Vous pouvez consulter les détails de celui-ci en cliquant sur l'onglet **Appareils**.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="Appareils de compteurs intelligents.":::

Cliquez sur le lien de l’exemple d’appareil **SM0123456789** pour afficher les détails de celui-ci. Vous pouvez mettre à jour les propriétés accessibles en écriture de l’appareil dans la page **Mettre à jour les propriétés**, et visualiser les valeurs mises à jour sur le tableau de bord.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="Propriétés de compteurs intelligents.":::

### <a name="device-template"></a>Modèle d'appareil

Cliquez sur l’onglet **Modèles d’appareils** pour afficher le modèle d’appareil de compteur intelligent. Le modèle dispose d'une interface prédéfinie pour les données, les propriétés, les commandes et les affichages.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="Modèles d’appareil de compteurs intelligents.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous décidez de ne pas continuer à utiliser cette application, supprimez-la en procédant comme suit :

1. Dans le volet gauche, ouvrez l'onglet Administration.
1. Sélectionnez Paramètres de l'application, puis cliquez sur le bouton Supprimer en bas de la page. 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="Supprimer une application.":::

## <a name="next-steps"></a>Étapes suivantes

> [Tutoriel : déployer et parcourir un modèle d’application de panneaux solaires](tutorial-solar-panel-app.md)

