---
title: 'Tutoriel : Analyse de panneaux solaires Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application d’analyse de panneaux solaires pour IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182783"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutoriel : déployer et découvrir pas à pas le modèle d'application d’analyse de panneaux solaires 

Utilisez le modèle d’application d’*analyse de panneaux solaires* IOT Central solaire et les instructions de cet article pour développer une solution d’analyse de panneaux solaires de bout en bout.

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="Architecture des panneaux solaires.":::

Cette architecture est constituée des composants suivants. Certaines applications peuvent ne pas avoir besoin de tous les composants listés ici.

### <a name="solar-panels-and-connectivity"></a>Panneaux solaires et connectivité

Les panneaux solaires sont l’une des principales sources d’énergie renouvelable. Selon le type de panneau solaire et sa configuration, vous pouvez le connecter via des passerelles ou d’autres appareils intermédiaires et systèmes propriétaires. Vous pouvez être amené à créer un pont d’appareil IoT Central pour connecter les appareils qui ne peuvent pas être connectés directement. Le pont IoT Central est une solution open source ; vous pouvez trouver des informations complètes à son sujet [ici](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>Plateforme IoT Central

Azure IoT Central est une plateforme qui simplifie la création de votre solution IoT et contribue à réduire la charge et les coûts liés à la gestion, aux opérations et au développement IoT. Avec IoT Central, vous pouvez facilement connecter, surveiller et gérer facilement vos ressources Internet des objets (IoT) à grande échelle. Une fois que vous avez connecté vos panneaux solaires à IoT Central, le modèle d’application utilise des fonctionnalités intégrées, comme des modèles d’appareil, des commandes et des tableaux de bord. Le modèle d’application utilise également le stockage IoT Central pour les scénarios de chemin à chaud tels que la surveillance, l’analyse, les règles et la visualisation des données de mesure en temps quasi-réel.

### <a name="extensibility-options-to-build-with-iot-central"></a>Options d’extensibilité pour créer avec IoT Central

La plateforme IoT Central fournit deux options d’extensibilité : L’exportation de données continue (CDE) et des API. Les clients et partenaires peuvent choisir entre ces options pour personnaliser leurs solutions selon leurs besoins spécifiques. Par exemple, un de nos partenaires a configuré CDE avec Azure Data Lake Storage (ADLS). Il utilise ADLS pour la conservation à long terme des données et d’autres scénarios de stockage à froid, tels que le traitement par lots, l’audit et la création de rapports. 


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]

> * Créer gratuitement une application de suivi des panneaux solaires
> * examiner l’application
> * Nettoyer les ressources


## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.


## <a name="create-a-solar-panel-monitoring-application"></a>Créer une application d’analyse de panneaux solaires


1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Énergie** :

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="Modèle de compteurs intelligents":::

1. Sélectionnez **Créer une application**, sous **Analyse de panneaux solaires**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord

Une fois que vous avez déployé le modèle d’application, vous allez examiner l’application de plus près. Remarquez qu’elle est fournie avec un exemple de compteur intelligent, un modèle d’appareil et un tableau de bord.

Adatum est un fournisseur d’énergie fictif qui assure le suivi et la gestion de panneaux solaires. Le tableau de bord de suivi des panneaux solaires contient des propriétés, des données et des exemples de commandes. Ce tableau de bord vous permet, à vous ou à votre équipe de support, d’effectuer les activités suivantes de manière proactive, avant toute opération de support technique supplémentaire visant à résoudre un problème :
* Consulter les dernières informations relatives à un panneau et l’[emplacement](../core/howto-use-location-data.md) où celui-ci est installé sur la carte.
* Vérifier l’état du panneau et l’état de la connexion.
* Examiner les tendances en matière de production d’énergie et de température pour détecter d’éventuelles anomalies.
* Suivre la production totale d’énergie à des fins de planification et de facturation.
* Activer un panneau et mettre à jour la version du microprogramme, si nécessaire. Dans le modèle, les boutons de commande montrent les fonctionnalités possibles, mais n’envoient pas de commandes réelles.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="Capture d’écran du tableau de bord de suivi des panneaux solaires.":::

### <a name="devices"></a>Appareils

L'application est fournie avec un exemple de panneau solaire. Pour voir les détails de l’appareil, sélectionnez **Appareils**.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="Capture d’écran des appareils du modèle de suivi des panneaux solaires.":::

Sélectionnez l’exemple d’appareil, **SP0123456789**. Sous l’onglet **Update Properties** (Mettre à jour les propriétés), vous pouvez mettre à jour les propriétés accessibles en écriture de l’appareil et voir les valeurs mises à jour sur le tableau de bord. 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="Capture d’écran de l’onglet de mise à jour des propriétés du modèle de suivi des panneaux solaires.":::


### <a name="device-template"></a>Modèle d’appareil

Pour voir le modèle de panneau solaire, sélectionnez l’onglet **Modèles d’appareil**. Le modèle a des interfaces prédéfinies pour les données, les propriétés, les commandes et les vues.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="Capture d’écran des modèles d’appareil du modèle de suivi des panneaux solaires.":::


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous décidez de ne pas continuer à utiliser cette application, supprimez-la en effectuant les étapes suivantes :

1. Dans le volet gauche, sélectionnez **Administration**.
1. Sélectionnez **Paramètres d’application** > **Supprimer**. 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="Capture d’écran de l’option Administration du modèle de suivi des panneaux solaires.":::
