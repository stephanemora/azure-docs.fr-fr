---
title: 'Tutoriel : Gestion des déchets connectée Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application de gestion des déchets connectée pour IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1ed898b02f2c30c3dcb043903bd3c3261d088539
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183230"
---
# <a name="tutorial-deploy-and-walk-through-the-connected-waste-management-application-template"></a>Tutoriel : Déployer et découvrir un modèle d’application de gestion des déchets connectée

Utilisez le modèle d’application de *gestion des déchets connecté* IOT central et les conseils de cet article pour développer une solution de gestion des déchets connectée de bout en bout.

:::image type="content" source="media/tutorial-connectedwastemanagement/concepts-connected-waste-management-architecture-1.png" alt-text="Architecture de gestion des déchets connectée.":::

### <a name="devices-and-connectivity"></a>Appareils et connectivité

Les appareils, tels que les poubelles, qui sont utilisés dans les environnements ouverts peuvent généralement être connectés via des réseaux longue distance à faible puissance LPWAN ou via un opérateur réseau tiers. Pour ces types d’appareils, utilisez [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) pour envoyer les données de votre appareil à votre application IoT dans Azure IoT Central. Vous pouvez également utiliser des passerelles d’appareil qui sont compatibles IP et capables de se connecter directement à IoT Central.

### <a name="iot-central"></a>IoT Central

Azure IoT Central est une plateforme d’applications IoT qui vous permet de générer et de déployer rapidement une solution IoT. Vous pouvez personnaliser et intégrer votre solution à des services tiers.

Lorsque vous connectez vos systèmes de déchets intelligents à IoT Central, l’application permet de contrôler les appareils, d’effectuer le suivi et de déclencher des alertes, d’accéder à l’interface utilisateur à laquelle est intégré RBAC et d’accéder aux tableaux de bord configurables, ainsi qu’aux options d’extensibilité.

### <a name="extensibility-and-integrations"></a>Extensibilité et intégrations

Vous pouvez étendre votre application IoT dans IoT Central et si vous le souhaitez :

* Transformer et intégrer vos données IoT en vue d’une analyse avancée, par exemple en effectuant l'apprentissage de modèles Machine Learning via l’exportation continue de données à partir d’une application IoT Central.
* Automatiser des workflows dans d’autres systèmes en déclenchant des actions via Power Automate ou des webhooks à partir d’une application IoT Central.
* Accéder programmatiquement à votre application IoT dans IoT Central via des API IoT Central.

### <a name="business-applications"></a>Applications métier

Vous pouvez utiliser des données IoT pour alimenter diverses applications métier au sein d’un utilitaire de gestion des déchets. Par exemple, dans une solution de gestion des déchets connectée, vous pouvez optimiser l’envoi de camions de ramassage d’ordures. L’optimisation peut être effectuée en fonction des données de capteurs IoT à partir de poubelles connectées. Dans votre [application de gestion des déchets IoT Central connectée](./tutorial-connected-waste-management.md), vous pouvez configurer des règles et des actions et les paramétrer pour créer des alertes dans le [Service de terrain connecté](/dynamics365/field-service/connected-field-service). Configurez Power Automate dans les règles d’IoT Central pour automatiser les workflows entre des applications et des services. Par ailleurs, selon les activités de service dans le service de terrain connecté, des informations peuvent être renvoyées à Azure IoT Central.

Vous pouvez facilement configurer les processus d’intégration suivants avec IoT Central et le service de terrain connecté :

* Azure IoT Central peut envoyer des informations relatives à des anomalies de périphériques à un service de terrain connecté à des fins de diagnostic.
* Ce service de terrain connecté peut créer des cas de support ou des ordres de travail déclenchés à partir d’anomalies de périphériques.
* Il peut également planifier des techniciens pour l’inspection afin d’éviter les temps d’arrêt.
* Le tableau de bord d’appareil Azure IoT Central peut être mis à jour avec les informations de planification et de service appropriées.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]

> * Utiliser le modèle de *gestion connectée des déchets* Azure IoT Central pour créer votre application.
> * Explorer et personnaliser le tableau de bord. 
> * Explorer le modèle d’appareil pour conteneur connecté de déchets.
> * Explorer les appareils simulés.
> * Explorer et configurer les règles.
> * Configurer des travaux.
> * Personnaliser votre application.

## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="create-connected-waste-management-application"></a>Créer une application de gestion des déchets connectée

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Build** dans la barre de navigation de gauche et sélectionnez l’onglet **Gouvernement** : :::image type="content" source="media/tutorial-connectedwastemanagement/iot-central-government-tab-overview.png" alt-text="Modèle de gestion des déchets connectée":::

1. Sélectionnez **Créer une application** sous **Gestion des déchets connectée**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord 

Une fois que vous avez déployé le modèle d’application, votre tableau de bord par défaut est le **Tableau de bord de gestion des déchets du monde entier**.

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-1.png" alt-text="Capture d’écran du tableau de bord de gestion des déchets Wide World.":::


En tant que créateur, vous pouvez créer et personnaliser des vues dans le tableau de bord pour les opérateurs. Commençons par explorer le tableau de bord. 

>[!NOTE]
>Toutes les données figurant dans le tableau de bord sont basées sur des données d’appareils simulés, que vous allez découvrir de plus près dans la section suivante. 

Le tableau de bord est constitué de différentes vignettes :

* **Vignette de l’image du service public de gestion des déchets Wide World Waste** : La première vignette du tableau de bord contient le logo d’un service public de gestion des déchets fictif appelé « Wide World Waste ». Vous pouvez personnaliser cette vignette et fournir votre propre image, ou bien vous pouvez la supprimer. 

* **Vignette d’image de conteneur de déchets** : Vous pouvez utiliser des vignettes d’image et de contenu pour créer une représentation visuelle de l’appareil supervisé et y ajouter une description. 

* **Vignette de KPI du niveau de remplissage** : Cette vignette présente une valeur signalée par un capteur de *niveau de remplissage* dans un conteneur de déchets. Pour un conteneur de déchets, il est possible de superviser à distance le niveau de remplissage et d’autres capteurs, comme la *mesure des odeurs* ou le *poids*. Un opérateur peut effectuer une action, comme envoyer un camion de ramassage des ordures. 

* **Carte de la zone de supervision des déchets** : Cette vignette utilise Azure Maps, que vous pouvez configurer directement dans Azure IoT Central. La vignette de la carte présente l’[emplacement](../core/howto-use-location-data.md) des appareils. Essayez de survoler la carte et d’en tester les contrôles, comme le zoom avant, le zoom arrière ou l’agrandissement.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-map.png" alt-text="Capture d’écran de la carte du tableau de bord du modèle de gestion connectée des déchets.":::



* **Histogramme du niveau de remplissage, de la mesure des odeurs et du poids** : Vous pouvez visualiser un ou plusieurs types de données de télémétrie d’appareil dans un histogramme. Vous pouvez également développer le graphique à barres.  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-bar-chart.png" alt-text="Capture d’écran de l’histogramme du tableau de bord du modèle de gestion des déchets connectée.":::


* **Services de terrain** : Le tableau de bord inclut un lien pour l’intégration à des services de terrain Dynamics 365 à partir de votre application Azure IoT Central. Par exemple, vous pouvez utiliser des services de terrain pour créer des tickets d’affectation des services de ramassage des ordures. 

### <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord 

Vous pouvez personnaliser ce tableau de bord en sélectionnant le menu **Edit** (Modifier). Vous pouvez ensuite ajouter de nouvelles vignettes ou configurer des vignettes existantes. Voici à quoi ressemble le tableau de bord en mode d’édition : 

:::image type="content" source="media/tutorial-connectedwastemanagement/edit-dashboard.png" alt-text="Capture d’écran du tableau de bord du modèle de gestion connectée des déchets en mode d’édition.":::


Vous pouvez également sélectionner **+ Nouveau** pour créer un tableau de bord et le configurer à partir de zéro. Vous pouvez avoir plusieurs tableaux de bord et basculer de l’un à l’autre par le biais du menu Tableau de bord. 

### <a name="explore-the-device-template"></a>Explorer le modèle d’appareil

Un modèle d’appareil dans Azure IoT Central définit les fonctionnalités d’un appareil, qui peuvent être des données de télémétrie, des propriétés ou des commandes. En tant que créateur, vous pouvez définir des modèles d’appareil qui représenteront les fonctionnalités des appareils que vous connecterez. 

L’application Gestion connectée des déchets est fournie avec un exemple de modèle pour un appareil connecté de conteneur de déchets.

Pour afficher ce modèle d’appareil :

1. Dans Azure IoT Central, dans le volet gauche de votre application, sélectionnez **Modèles d’appareil**. 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template.png" alt-text="Capture d’écran montrant la liste des modèles d’appareil dans l’application.":::


1. Dans la liste **Modèles d’appareil**, sélectionnez **Connected Waste Bin** (Conteneur de déchets connecté).

1. Examinez les fonctionnalités du modèle d’appareil. Vous pouvez voir qu’il définit des capteurs comme le **niveau de remplissage**, la **mesure des odeurs**, le **poids**, l’**emplacement**, etc.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-connected-bin.png" alt-text="Capture d’écran montrant les détails du modèle d’appareil de poubelles connectées.":::


### <a name="customize-the-device-template"></a>Personnaliser le modèle d’appareil

Essayez de personnaliser les éléments suivants :

1. Dans le menu du modèle d’appareil, sélectionnez **Customize** (Personnaliser).
1. Recherchez le type de données de télémétrie **Odor meter** (mesure des odeurs).
1. Mettez à jour le **nom d’affichage** de **Odor meter** en le remplaçant par **Niveau d’odeur**.
1. Essayez de mettre à jour l’unité de mesure ou définissez la **valeur minimale** et la **valeur maximale**.
1. Sélectionnez **Enregistrer**. 

### <a name="add-a-cloud-property"></a>Ajouter une propriété cloud 

Voici comment procéder :

1. Dans le menu du modèle d’appareil, sélectionnez **Cloud properties** (Propriétés cloud).
1. Sélectionnez **+ Ajouter une propriété cloud**. Dans Azure IoT Central, vous pouvez ajouter une propriété qui s’applique à l’appareil, mais qui n’est pas censée être envoyée par un appareil. Par exemple, une propriété cloud peut être un seuil d’alerte propre à la zone d’installation, aux informations sur les ressources ou aux informations de maintenance. 
1. Sélectionnez **Enregistrer**. 
 
### <a name="views"></a>Les vues 

Le modèle d’appareil de conteneur connecté de déchets est fourni avec des vues prédéfinies. Explorez les vues et mettez-les à jour si vous le souhaitez. Les vues définissent la façon dont les opérateurs voient les données d’appareil et entrent les propriétés cloud. 

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-views.png" alt-text="Capture d’écran des vues de modèles d’appareil du modèle de gestion des déchets connectée.":::


### <a name="publish"></a>Publish 

Si vous avez apporté des modifications, n’oubliez pas de publier le modèle d’appareil. 

### <a name="create-a-new-device-template"></a>Créer un modèle d’appareil 

Pour créer un modèle d’appareil, sélectionnez **+ Nouveau** et suivez les étapes. Vous pouvez créer un modèle d’appareil personnalisé à partir de zéro ou choisir un modèle d’appareil dans le catalogue d’appareils Azure. 

### <a name="explore-simulated-devices"></a>Explorer les appareils simulés

Dans Azure IoT Central, vous pouvez créer des appareils simulés pour tester votre modèle d’appareil et votre application. 

L’application Connected waste management (Gestion connectée des déchets) a deux appareils simulés associés au modèle d’appareil de conteneur connecté de déchets. 

### <a name="view-the-devices"></a>Afficher les appareils

1. Dans le volet gauche d’Azure IoT Central, sélectionnez **Appareil**. 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices.png" alt-text="Capture d’écran des appareils du modèle de gestion connectée des déchets.":::


1. Sélectionnez l’appareil **Connected Waste Bin** (Conteneur connecté).  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices-bin-1.png" alt-text="Capture d’écran des propriétés d’appareil du modèle de gestion connectée des déchets.":::


1. Accédez à l’onglet **Cloud Properties** (Cloud Properties). Mettez à jour la valeur **Bin full alert threshold** (Seuil d’alerte de conteneur rempli) en la faisant passer de **95** à **100**. 

Explorez les onglets **Device Properties** (Propriétés de l’appareil) et **Device Dashboard** (Tableau de bord de l’appareil). 

> [!NOTE]
> Tous les onglets ont été configurés à partir des vues du modèle d’appareil.

### <a name="add-new-devices"></a>Ajouter de nouveaux appareils

Vous pouvez ajouter de nouveaux appareils en sélectionnant **+ Nouveau** sous l’onglet **Appareils**. 

## <a name="explore-and-configure-rules"></a>Explorer et configurer les règles

Dans Azure IoT Central, vous pouvez créer des règles pour superviser automatiquement les données de télémétrie des appareils et déclencher des actions quand une ou plusieurs conditions sont remplies. Ces actions peuvent inclure l’envoi d’e-mails de notification, le déclenchement d’une action dans Power Automate ou le démarrage d’une action webhook pour envoyer des données à d’autres services.

L’application Connected Waste Management (Gestion connectée des déchets) possède quatre exemples de règles.

### <a name="view-rules"></a>Afficher les règles

1. Dans le volet gauche d’Azure IoT Central, sélectionnez **Règles**.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-rules.png" alt-text="Capture d’écran des règles du modèle de gestion connectée des déchets.":::


1. Sélectionnez **Bin full alert** (Alerte de conteneur plein).

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-bin-full-alert.png" alt-text="Capture d’écran de l’alerte de conteneur plein.":::


 1. L’**alerte de conteneur plein** vérifie la condition suivante : **Le niveau de remplissage est supérieur ou égal au seuil d’alerte de conteneur plein**.

    Le **seuil d’alerte de conteneur plein** est une propriété cloud définie dans le modèle d’appareil de conteneur de déchets connecté. 

À présent, nous allons créer une action de messagerie.

### <a name="create-an-email-action"></a>Créer une action de messagerie

Dans la liste **Actions** de la règle, vous pouvez configurer une action e-mail :
1. Sélectionnez **+ E-mail**. 
1. Pour **Nom d’affichage**, entrez **Alerte de pH élevé**.
1. Dans **À**, entrez l’adresse e-mail associée à votre compte Azure IoT Central. 
1. Si vous le souhaitez, entrez une note à inclure dans le texte de l’e-mail.
1. Sélectionnez **Terminé** > **Enregistrer**. 

Vous allez à présent recevoir un e-mail quand la condition configurée sera remplie.

>[!NOTE]
>L’application enverra un e-mail chaque fois qu’une condition sera remplie. Désactivez (Disable) la règle pour cesser de recevoir des e-mails à partir de la règle automatisée. 
  
Pour créer une règle, dans le volet gauche de **Règles**, sélectionnez **+ Nouveau**.

## <a name="configure-jobs"></a>Configuration des travaux

Dans Azure IoT Central, les travaux vous permettent de déclencher des mises à jour des propriétés d’appareil ou cloud sur plusieurs appareils. Vous pouvez également utiliser des travaux pour déclencher des commandes d’appareil sur plusieurs appareils. Azure IoT Central automatise le flux de travail pour vous. 

1. Dans le volet gauche d’Azure IoT Central, sélectionnez **Travaux**. 
1. Sélectionnez **+ Nouveau** et configurez un ou plusieurs travaux. 

## <a name="customize-your-application"></a>Personnaliser votre application 

En tant que réalisateur de logiciel, vous pouvez modifier plusieurs paramètres pour personnaliser l’expérience utilisateur dans votre application.

### <a name="change-the-application-theme"></a>Modifier le thème de l’application

Voici comment procéder :
1. Accédez à **Administration** > **Personnaliser votre application**.
1. Sélectionnez **Modifier** pour choisir une image à charger pour le **logo de l’application**.
1. Sélectionnez **Modifier** pour choisir une image à charger pour l’**icône du navigateur** (une image qui s’affiche sous les onglets du navigateur).
1. Vous pouvez également remplacer les couleurs du navigateur par défaut en ajoutant des codes couleur hexadécimaux HTML. Pour ce faire, utilisez les champs **En-tête** et **Accent**.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-customize-your-application.png" alt-text="Capture d’écran de la personnalisation de votre application utilisant le modèle de gestion connectée des déchets.":::


1. Vous pouvez également changer les images d’application. Sélectionnez **Administration** > **Paramètres d’application** > **Sélectionner une image** pour choisir une image à charger en tant qu’image de l’application.
1. Enfin, vous pouvez également changer le thème en sélectionnant **Paramètres** sur le titre de l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez votre application en procédant comme suit :

1. Sélectionnez **Administration** dans le volet gauche de votre application Azure IoT Central.
1. Sélectionnez **Paramètres d’application** > **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts de la consommation des déchets connectée](./tutorial-water-consumption-monitoring.md)
