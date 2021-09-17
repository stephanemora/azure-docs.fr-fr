---
title: 'Tutoriel : analyse de la qualité de l’eau Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application d’analyse de la qualité de l’eau pour IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 481a085b1fd5fec55cd34f885dfcda40fec6f5e7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179081"
---
# <a name="tutorial-deploy-and-walk-through-the-water-quality-monitoring-application"></a>Tutoriel : déployer et parcourir l’application d’analyse de la qualité de l’eau

Utilisez le modèle d’application d’*analyse de la qualité de l’eau* IOT Central et les instructions de cet article pour développer une solution d’analyse de la qualité de l’eau de bout en bout.


![Architecture du suivi de la qualité de l’eau](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### <a name="devices-and-connectivity"></a>Appareils et connectivité

Les solutions de gestion de l’eau utilisent des systèmes d’eau intelligents tels que des débitmètres, des analyses de la qualité de l’eau, des vannes intelligentes, des détecteurs de fuite.

Les appareils des solutions d’eau intelligentes peuvent se connecter via des réseaux longue distance à faible puissance (LPWAN) ou via un opérateur réseau tiers. Pour ces types d’appareils, utilisez [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) pour envoyer les données de votre appareil à votre application IoT dans Azure IoT Central. Vous pouvez également utiliser des passerelles d’appareil qui sont compatibles IP et capables de se connecter directement à IoT Central.

### <a name="iot-central"></a>IoT Central

Azure IoT Central est une plateforme d’applications IoT qui vous permet de générer et de déployer rapidement une solution IoT. Vous pouvez personnaliser et intégrer votre solution à des services tiers.

Lorsque vous connectez vos systèmes d’eau intelligents à IoT Central, l’application permet de contrôler les appareils, d’effectuer le suivi et de déclencher des alertes, d’accéder à l’interface utilisateur à laquelle est intégré RBAC et d’accéder aux tableaux de bord configurables, ainsi qu’aux options d’extensibilité.

### <a name="extensibility-and-integrations"></a>Extensibilité et intégrations

Vous pouvez étendre votre application IoT dans IoT Central et si vous le souhaitez :

* Transformer et intégrer vos données IoT en vue d’une analyse avancée, par exemple en effectuant l'apprentissage de modèles Machine Learning via l’exportation continue de données à partir d’une application IoT Central.
* Automatiser des workflows dans d’autres systèmes en déclenchant des actions via Power Automate ou des webhooks à partir d’une application IoT Central.
* Accéder programmatiquement à votre application IoT dans IoT Central via des API IoT Central.

### <a name="business-applications"></a>Applications métier

Vous pouvez utiliser des données IoT pour alimenter diverses applications métier au sein d’une solution pour l’eau. Dans votre [application d’analyse de la consommation d’eau IoT Central](tutorial-water-consumption-monitoring.md), vous pouvez configurer des règles et des actions et les définir pour créer des alertes dans le [Service de terrain connecté](/dynamics365/field-service/connected-field-service). Configurez Power Automate dans les règles d’IoT Central pour automatiser les workflows entre des applications et des services. Par ailleurs, selon les activités de service dans le service de terrain connecté, des informations peuvent être renvoyées à Azure IoT Central.

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Utiliser le modèle **Water quality monitoring** (Suivi de la qualité de l’eau) pour créer une application de suivi de la qualité de l’eau.
> * Explorer et personnaliser un tableau de bord.
> * Explorer un modèle d’appareil de suivi de la qualité de l’eau.
> * Explorer les appareils simulés.
> * Explorer et configurer les règles.
> * Configurer des travaux.
> * Personnaliser le marquage de l’application à l’aide d’une étiquette blanche.

## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="create-water-quality-monitoring-application"></a>Créer une application d’analyse de la qualité de l’eau

Créez une application à l’aide de la procédure suivante :

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Gouvernement** : :::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="Modèle d’application":::

1. Sélectionnez **Créer une application** sous **Analyse de la qualité de l’eau**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord

Une fois l’application créée, le volet **Wide World water quality dashboard** (Tableau de bord de qualité de l’eau dans le monde) s’ouvre.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="Tableau de bord d’analyse de la qualité de l’eau.":::

En tant que créateur, vous pouvez créer et personnaliser des vues sur le tableau de bord, que les opérateurs pourront utiliser. Avant d’essayer de le personnaliser, commençons par explorer le tableau de bord.

Toutes les données affichées dans le tableau de bord sont basées sur des données d’appareils simulés, qui sont décrites dans la section suivante.

Le tableau de bord inclut les types de vignettes suivants :

* **Vignette d’image Wide World water utility** (Utilitaire Wide World Water) : la première vignette en haut à gauche du tableau de bord est une vignette d’image représentant l’utilitaire fictif « Wide World Water ». Vous pouvez personnaliser cette vignette et fournir votre propre image, ou la supprimer.

* **Vignettes d’indicateur de performance clé de pH moyen** : des vignettes de KPI comme **pH moyen au cours des 30 dernières minutes** figurent en haut du volet de tableau de bord. Vous pouvez personnaliser les vignettes de KPI et définir chacune sur un type et une plage de temps différents.

* **Carte des zones de suivi de l’eau** : Azure IoT Central utilise Azure Maps, que vous pouvez configurer directement dans votre application pour afficher l’[emplacement](../core/howto-use-location-data.md) des appareils. Vous pouvez également mapper les informations d’emplacement de votre application sur votre appareil, puis utiliser Azure Maps pour afficher les informations sur une carte. Survolez la carte et essayez les contrôles.

* **Carte thermique de distribution moyenne du pH** : Vous pouvez sélectionner différents graphiques de visualisation pour afficher les données de télémétrie des appareils de la manière la plus appropriée à votre application.

* **Graphique en courbes des indicateurs de qualité critique** : vous pouvez visualiser les données de télémétrie des appareils sous la forme d’un graphique en courbes sur une plage de temps.  

* **Graphique à barres de concentration des agents chimiques** : vous pouvez visualiser les données de télémétrie des appareils dans un graphique à barres.

* **Vignette de réinitialisation des paramètres des capteurs** : le tableau de bord comprend une vignette pour les actions qu’un opérateur peut lancer directement à partir du tableau de bord de suivi. La réinitialisation des propriétés d’un appareil est un exemple des actions possibles.

* **Vignettes de liste de propriétés** : le tableau de bord comporte plusieurs vignettes de propriétés qui représentent les informations de seuil, les informations d’intégrité d’appareil et les informations de maintenance.

### <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord

En tant que créateur, vous pouvez personnaliser les vues figurant sur le tableau de bord, que les opérateurs pourront utiliser.

1. Sélectionnez **Edit** (Modifier) pour personnaliser le volet du **Wide World water quality dashboard** (Tableau de bord de qualité de l’eau dans le monde). Vous pouvez personnaliser ce tableau de bord en sélectionnant des commandes dans le menu **Edit** (Modifier). Une fois le tableau de bord en mode d’édition, vous pouvez ajouter de nouvelles vignettes ou configurer les vignettes existantes.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="Modifiez votre tableau de bord.":::

1. Sélectionnez **+ New** (Nouveau) pour créer un tableau de bord et le configurer. Vous pouvez avoir plusieurs tableaux de bord et naviguer entre eux via le menu Dashboard (Tableau de bord).

## <a name="explore-a-water-quality-monitoring-device-template"></a>Explorer un modèle d’appareil de suivi de la qualité de l’eau

Un modèle d’appareil dans Azure IoT Central définit les fonctionnalités d’un appareil. Les fonctionnalités disponibles sont la télémétrie, les propriétés et les commandes. En tant que créateur, vous pouvez définir des modèles d’appareil dans Azure IoT Central qui représenteront les fonctionnalités des appareils connectés. Vous pouvez également créer des appareils simulés pour tester le modèle et l’application de vos appareils.

L’application de suivi de la qualité de l’eau que vous avez créée est fournie avec un modèle d’appareil de suivi de la qualité de l’eau.

Pour afficher ce modèle d’appareil :

1. Sélectionnez **Device templates** (Modèles d’appareil) dans le volet le plus à gauche de votre application dans Azure IoT Central.
1. Dans la liste des modèles d’appareil, sélectionnez **Water Quality Monitor** (Contrôleur de qualité de l’eau) pour ouvrir ce modèle d’appareil.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="Le modèle d’appareil.":::

### <a name="customize-the-device-template"></a>Personnaliser le modèle d’appareil

Exercez-vous à personnaliser les paramètres de modèle d’appareil suivants :

1. Dans le menu du modèle d’appareil, sélectionnez **Customize** (Personnaliser).
1. Accédez au type de télémétrie **Temperature** (Température).
1. Remplacez le nom d’affichage (**Display name**) par **Température signalée**.
1. Changez l’unité de mesure ou définissez **Min value** (Valeur minimale) et **Max value** (Valeur maximale).
1. Sélectionnez **Enregistrer**.

#### <a name="add-a-cloud-property"></a>Ajouter une propriété cloud

1. Dans le menu du modèle d’appareil, sélectionnez **Cloud properties** (Propriétés cloud).
1. Pour ajouter une nouvelle propriété cloud, sélectionnez **+ Add Cloud Property** (Ajouter une propriété cloud). Dans Azure IoT Central, vous pouvez ajouter une propriété qui s’applique à un appareil, mais qui n’est pas censée être envoyée par l’appareil. Un seuil d’alerte spécifique à la zone d’installation, aux informations sur les ressources ou aux informations de maintenance est un exemple d’une telle propriété.
1. Entrez **Zone d’installation** comme **Nom d’affichage** et choisissez **Chaîne** comme **Schéma**.
1. Sélectionnez **Enregistrer**.

### <a name="explore-views"></a>Explorer les vues

Le modèle d’appareil de suivi de la qualité de l’eau est fourni avec des vues prédéfinies. Les vues définissent la façon dont les opérateurs verront les données de l’appareil et définiront les propriétés cloud. Explorez les vues et exercez-vous à apporter des modifications.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="Affichages du modèle d’appareil.":::

### <a name="publish-the-device-template"></a>Publier le modèle d’appareil

Si vous avez apporté des modifications, veillez à sélectionner **Publish** (Publier) pour publier le modèle d’appareil.

### <a name="create-a-new-device-template"></a>Créer un modèle d’appareil

1. Dans la page **Device templates** (Modèles d’appareil), sélectionnez **+ New** (+ Nouveau) pour créer un modèle d’appareil et suivre le processus de création.
1. Créez un modèle d’appareil personnalisé ou choisissez un modèle d’appareil dans le catalogue d’appareils Azure IoT.

## <a name="explore-simulated-devices"></a>Explorer les appareils simulés

L’application de suivi de la qualité de l’eau que vous avez créée à partir du modèle d’application a deux appareils simulés. Ces appareils sont mappés au modèle d’appareil de suivi de la qualité de l’eau.

### <a name="view-the-devices"></a>Afficher les appareils

1. Sélectionnez **Devices** (Appareils) dans le volet le plus à gauche de votre application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="Appareils":::

1. Sélectionnez un appareil simulé.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="Sélection de l’appareil 1":::

1. Sous l’onglet **Cloud Properties** (Propriétés cloud), modifiez la valeur **Acidity (pH) threshold** (Seuil d’acidité (pH)) en remplaçant **8** par **9**, puis sélectionnez **Save**.
1. Explorez l’onglet **Device Properties** (Propriétés de l’appareil) et l’onglet **Device Dashboard** (Tableau de bord de l’appareil).

> [!NOTE]
> Tous les onglets ont été configurés à partir des **vues du modèle d’appareil**.

### <a name="add-new-devices"></a>Ajouter de nouveaux appareils

1. Sous l’onglet **Devices** (Appareils), sélectionnez **+ New** (Nouveau) pour ajouter un nouvel appareil.
1. Utilisez l’**ID d’appareil** suggéré ou entrez votre propre ID d’appareil. Vous pouvez également entrer un **nom** pour votre nouvel appareil.
1. Sélectionnez **Water Quality Monitor** (Contrôleur de qualité de l’eau) comme **Device template** (Modèle d’appareil). 
1. Vérifiez que l’option **Simulate this device** (Simuler cet appareil) est définie sur **Yes** si vous voulez créer un appareil simulé. 
1. Sélectionnez **Create** (Créer).  

## <a name="explore-and-configure-rules"></a>Explorer et configurer les règles

Dans Azure IoT Central, vous pouvez créer des règles pour superviser automatiquement les données de télémétrie des appareils. Ces règles déclenchent une action lorsque l’une de leurs conditions est remplie. L’une des actions possibles consiste à envoyer des e-mails de notification. D’autres actions possibles incluent une action Power Automate ou webhook pour envoyer des données à d’autres services.

L’application de suivi de la qualité de l’eau que vous avez créée comporte deux règles préconfigurées.

### <a name="view-rules"></a>Afficher les règles

1. Sélectionnez **Rules** (Règles) dans le volet le plus à gauche de votre application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="Règles":::

1. Sélectionnez **High pH alert** (Alerte de pH élevé), qui est l’une des règles préconfigurées dans l’application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="Règle d’alerte de pH élevé.":::

   La règle **High pH alert** (Alerte de pH élevé) est configurée pour vérifier si la condition d’acidité (pH) est supérieure à 8.

Ensuite, ajoutez une action de messagerie à la règle :

1. Sélectionnez **+ E-mail**.
1. Dans la zone **Display name** (Nom d’affichage), entrez **Alerte de pH élevé**.
1. Dans la zone **To** (À), entrez l’adresse e-mail associée à votre compte Azure IoT Central.
1. Si vous le souhaitez, entrez une note à inclure dans le texte de l’e-mail.
1. Sélectionnez **Terminé** pour terminer l’action.
1. Définissez la règle sur **Enabled** (Activée), puis sélectionnez **Save**.

Après quelques minutes, vous devrez recevoir un e-mail quand la condition configurée sera remplie.

> [!NOTE]
> L’application enverra un e-mail chaque fois qu’une condition sera remplie. Sélectionnez **Disable** (Désactiver) pour cesser de recevoir un e-mail automatisé à partir de cette règle.
  
Pour créer une nouvelle règle, sélectionnez **Rules** (Règles) dans le volet le plus à gauche de votre application, puis sélectionnez **+New** (Nouveau).

## <a name="configure-jobs"></a>Configuration des travaux

Les travaux Azure IoT Central vous permettent de déclencher des mises à jour des propriétés d’appareil ou cloud sur plusieurs appareils. Vous pouvez également utiliser des travaux pour déclencher des commandes d’appareil sur plusieurs appareils. Azure IoT Central automatise le flux de travail pour vous.

1. Sélectionnez **Jobs** (Travaux) dans le volet le plus à gauche de votre application.
1. Sélectionnez **+ New job** (+ Nouveau travail) et configurez un ou plusieurs travaux.

## <a name="customize-your-application"></a>Personnaliser votre application

En tant que réalisateur de logiciel, vous pouvez modifier plusieurs paramètres pour personnaliser l’expérience utilisateur dans votre application.

1. Sélectionnez **Administration** > **Personnaliser votre application**.
1. Sous le **logo Masthead**, sélectionnez **Modifier** pour choisir l’image à charger comme logo.
1. Sous **Icône du navigateur**, sélectionnez **Modifier** pour choisir l’image qui apparaîtra dans les onglets du navigateur.
1. Sous **Couleurs du navigateur**, vous pouvez remplacer les valeurs par défaut par des codes couleur hexadécimaux HTML.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="Personnaliser votre application":::

### <a name="update-the-application-image"></a>Mettre à jour l’image de l’application

1. Sélectionnez **Administration** > **Votre application**.

1. Sélectionnez **Modifier** pour choisir une image à charger en tant qu’image de l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez l’application en procédant comme suit :

1. Ouvrez l’onglet **Administration** dans le volet le plus à gauche de votre application.
1. Sélectionnez **Votre application**, puis le bouton **Supprimer**.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="Supprimer votre application.":::
