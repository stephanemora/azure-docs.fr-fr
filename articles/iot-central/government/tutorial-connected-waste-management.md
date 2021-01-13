---
title: 'Tutoriel : Créer une application de gestion connectée des déchets avec Azure IoT Central'
description: Découvrez comment générer une application de gestion connectée des déchets à l’aide de modèles d’application Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 8085409c3dc38d17b6fe0d3cb15857b2396e23b6
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881342"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Tutoriel : Créer une application connectée de gestion des déchets

Ce tutoriel vous montre comment utiliser Azure IoT Central pour créer une application de gestion connectée des déchets. 

Plus précisément, vous apprenez à : 

* Utiliser le modèle de *gestion connectée des déchets* Azure IoT Central pour créer votre application.
* Explorer et personnaliser le tableau de bord de l’opérateur 
* Explorer le modèle d’appareil pour conteneur connecté de déchets.
* Explorer les appareils simulés.
* Explorer et configurer les règles.
* Configurer des travaux.
* Personnaliser votre application.

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure est recommandé. Vous pouvez également utiliser une version d’évaluation gratuite pendant 7 jours. Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un sur la [page d’inscription à Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Créer votre application dans Azure IoT Central

Dans cette section, vous utilisez le modèle de gestion connectée des déchets pour créer votre application dans Azure IoT Central. Voici comment procéder :

1. Accédez à [Azure IoT Central](https://aka.ms/iotcentral).

    Si vous disposez d’un abonnement Azure, connectez-vous avec les informations d’identification que vous utilisez pour y accéder. Sinon, connectez-vous à l’aide d’un compte Microsoft :

    ![Capture d’écran de la connexion Microsoft.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Dans le volet gauche, sélectionnez **Générer**. Sélectionnez ensuite l’onglet **Administration**. La page relative au secteur public affiche plusieurs modèles d’application pour le secteur public.

    ![Capture d’écran de la page de création Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Sélectionnez le modèle d’application **Gestion connectée des déchets**. Ce modèle comprend un exemple de modèle d’appareil pour conteneur connecté de déchets, un appareil simulé, un tableau de bord d’opérateur et des règles de supervision préconfigurées.    

1. Sélectionnez **Créer une application**, ce qui ouvre la boîte de dialogue **Nouvelle application**. Renseignez les champs suivants :
    * **Application name** (Nom de l’application). Par défaut, le nom de l’application est **Gestion connectée des déchets** suivi d’une chaîne d’ID unique générée par IoT Central. Si vous le souhaitez, vous pouvez choisir un nom d’application convivial. Vous pouvez également modifier le nom de l’application.
    * **URL**. Si vous le souhaitez, vous pouvez spécifier l’URL de votre choix. Vous pourrez la changer plus tard. 
    * **Plan tarifaire**. Si vous avez un abonnement Azure, entrez votre annuaire, votre abonnement Azure et votre région dans les champs appropriés de la boîte de dialogue **Informations de facturation**. Si vous n’avez pas d’abonnement, sélectionnez **Gratuit** pour activer l’essai de 7 jours, puis indiquez les coordonnées nécessaires.  

    Pour plus d’informations sur les annuaires et les abonnements, consultez [Démarrage rapide : Créer une application Azure IoT Central](../core/quick-deploy-iot-central.md).

1. Au bas de la page, sélectionnez **Créer**. 

    ![Capture d’écran du dialogue Créer une application dans Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Capture d’écran du dialogue Informations de facturation dans Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Dans l’application que vous venez de créer, les éléments suivants sont préconfigurés :
* Exemples de tableaux de bord d’opérateur
* Exemples de modèles d’appareil préconfigurés pour conteneur connecté de déchets.
* Appareils simulés pour conteneur connecté de déchets.
* Règles et travaux
* Exemple de personnalisation. 

Cette application est la vôtre et vous pouvez la modifier à tout moment. Nous allons maintenant explorer l’application et effectuer certaines personnalisations.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Explorer et personnaliser le tableau de bord de l’opérateur 

Jetez un coup d’œil sur le **tableau de bord de gestion des déchets Wide World**, qui s’affiche une fois que vous avez créé votre application.

   ![Capture d’écran du tableau de bord de gestion des déchets Wide World.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

En tant que créateur, vous pouvez créer et personnaliser des vues dans le tableau de bord pour les opérateurs. Commençons par explorer le tableau de bord. 

>[!NOTE]
>Toutes les données figurant dans le tableau de bord sont basées sur des données d’appareils simulés, que vous allez découvrir de plus près dans la section suivante. 

Le tableau de bord est constitué de différentes vignettes :

* **Vignette de l’image du service public de gestion des déchets Wide World Waste** : La première vignette du tableau de bord contient le logo d’un service public de gestion des déchets fictif appelé « Wide World Waste ». Vous pouvez personnaliser cette vignette et fournir votre propre image, ou bien vous pouvez la supprimer. 

* **Vignette d’image de conteneur de déchets** : Vous pouvez utiliser des vignettes d’image et de contenu pour créer une représentation visuelle de l’appareil supervisé et y ajouter une description. 

* **Vignette de KPI du niveau de remplissage** : Cette vignette présente une valeur signalée par un capteur de *niveau de remplissage* dans un conteneur de déchets. Pour un conteneur de déchets, il est possible de superviser à distance le niveau de remplissage et d’autres capteurs, comme la *mesure des odeurs* ou le *poids*. Un opérateur peut effectuer une action, comme envoyer un camion de ramassage des ordures. 

* **Carte de la zone de supervision des déchets** : Cette vignette utilise Azure Maps, que vous pouvez configurer directement dans Azure IoT Central. La vignette de la carte présente l’emplacement des appareils. Essayez de survoler la carte et d’en tester les contrôles, comme le zoom avant, le zoom arrière ou l’agrandissement.

     ![Capture d’écran de la carte du tableau de bord du modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Histogramme du niveau de remplissage, de la mesure des odeurs et du poids** : Vous pouvez visualiser un ou plusieurs types de données de télémétrie d’appareil dans un histogramme. Vous pouvez également développer le graphique à barres.  

  ![Capture d’écran de l’histogramme du tableau de bord du modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Services de terrain** : Le tableau de bord inclut un lien pour l’intégration à des services de terrain Dynamics 365 à partir de votre application Azure IoT Central. Par exemple, vous pouvez utiliser des services de terrain pour créer des tickets d’affectation des services de ramassage des ordures. 


### <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord 

Vous pouvez personnaliser ce tableau de bord en sélectionnant le menu **Edit** (Modifier). Vous pouvez ensuite ajouter de nouvelles vignettes ou configurer des vignettes existantes. Voici à quoi ressemble le tableau de bord en mode d’édition : 

![Capture d’écran du tableau de bord du modèle de gestion connectée des déchets en mode d’édition.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Vous pouvez également sélectionner **+ Nouveau** pour créer un tableau de bord et le configurer à partir de zéro. Vous pouvez avoir plusieurs tableaux de bord et basculer de l’un à l’autre par le biais du menu Tableau de bord. 

## <a name="explore-the-device-template"></a>Explorer le modèle d’appareil

Un modèle d’appareil dans Azure IoT Central définit les fonctionnalités d’un appareil, qui peuvent être des données de télémétrie, des propriétés ou des commandes. En tant que créateur, vous pouvez définir des modèles d’appareil qui représenteront les fonctionnalités des appareils que vous connecterez. 

L’application Gestion connectée des déchets est fournie avec un exemple de modèle pour un appareil connecté de conteneur de déchets.

Pour afficher ce modèle d’appareil :

1. Dans Azure IoT Central, dans le volet gauche de votre application, sélectionnez **Modèles d’appareil**. 

    ![Capture d’écran montrant la liste des modèles d’appareil dans l’application.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Dans la liste **Modèles d’appareil**, sélectionnez **Connected Waste Bin** (Conteneur de déchets connecté).

1. Examinez les fonctionnalités du modèle d’appareil. Vous pouvez voir qu’il définit des capteurs comme le **niveau de remplissage**, la **mesure des odeurs**, le **poids**, l’**emplacement**, etc.

   ![Capture d’écran montrant les détails du modèle d’appareil de conteneur connecté de déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


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

  ![Capture d’écran des vues de modèles d’appareil de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publish 

Si vous avez apporté des modifications, n’oubliez pas de publier le modèle d’appareil. 

### <a name="create-a-new-device-template"></a>Créer un modèle d’appareil 

Pour créer un modèle d’appareil, sélectionnez **+ Nouveau** et suivez les étapes. Vous pouvez créer un modèle d’appareil personnalisé à partir de zéro ou choisir un modèle d’appareil dans le catalogue d’appareils Azure. 

## <a name="explore-simulated-devices"></a>Explorer les appareils simulés

Dans Azure IoT Central, vous pouvez créer des appareils simulés pour tester votre modèle d’appareil et votre application. 

L’application Connected waste management (Gestion connectée des déchets) a deux appareils simulés associés au modèle d’appareil de conteneur connecté de déchets. 

### <a name="view-the-devices"></a>Afficher les appareils

1. Dans le volet gauche d’Azure IoT Central, sélectionnez **Appareil**. 

   ![Capture d’écran des appareils du modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Sélectionnez l’appareil **Connected Waste Bin** (Conteneur connecté).  

     ![Capture d’écran des propriétés d’appareil du modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

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

   ![Capture d’écran des règles du modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Sélectionnez **Bin full alert** (Alerte de conteneur plein).

     ![Capture d’écran de l’alerte de conteneur plein.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

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

   ![Capture d’écran de la personnalisation de votre application utilisant le modèle de gestion connectée des déchets.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Vous pouvez également changer les images d’application. Sélectionnez **Administration** > **Paramètres d’application** > **Sélectionner une image** pour choisir une image à charger en tant qu’image de l’application.
1. Enfin, vous pouvez également changer le thème en sélectionnant **Paramètres** sur le titre de l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez votre application en procédant comme suit :

1. Sélectionnez **Administration** dans le volet gauche de votre application Azure IoT Central.
1. Sélectionnez **Paramètres d’application** > **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [concepts de la gestion connectée des déchets](./concepts-connectedwastemanagement-architecture.md)
