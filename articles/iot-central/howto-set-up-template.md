---
title: Configurer un modèle d’appareil dans une application Azure IoT Central | Microsoft Docs
description: Découvrez comment configurer un modèle d’appareil avec des mesures, des paramètres, des propriétés, des règles et un tableau de bord.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063033"
---
# <a name="set-up-a-device-template"></a>Configurer un modèle d’appareil

Un modèle d’appareil est un blueprint qui définit les caractéristiques et les comportements d’un type d’appareil qui se connecte à une application Microsoft Azure IoT Central.

Par exemple, un concepteur peut créer un modèle d’appareil pour un ventilateur connecté à IoT qui a :

- Une mesure de télémétrie de température

- Une mesure d’événement d’erreur du moteur du ventilateur

- Une mesure de l’état de fonctionnement du ventilateur

- Un paramètre de vitesse du ventilateur

- Une propriété d’emplacement

- Des règles qui envoient des alertes

- Un tableau de bord qui vous donne une vision globale de l’appareil

À partir de ce modèle d’appareil, un opérateur peut créer et connecter des appareils qui sont des ventilateurs réels avec des noms comme **ventilateur-1** et **ventilateur-2**. Tous ces ventilateurs ont des mesures, des paramètres, des propriétés, des règles et un tableau de bord que les utilisateurs de votre application peuvent surveiller et gérer.

> [!NOTE]
> Seuls les concepteurs et les administrateurs peuvent créer, modifier et supprimer des modèles d’appareils. Tous les utilisateurs peuvent créer des appareils dans la page **Device Explorer** à partir de modèles d’appareils existants.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

1. Accédez à la page **Concepteur d’applications**.

2. Pour créer un modèle vierge, sélectionnez **Créer un modèle d’appareil**, puis **Personnalisé**.

3. Attribuez un nom à votre nouveau modèle d’appareil, puis sélectionnez **Créer**.

   ![Page de détails de l’appareil ayant « Réfrigérateur » comme nom de modèle](./media/howto-set-up-template/devicedetailspage.png)

4. Vous êtes maintenant dans la page **Détails de l’appareil** d’un nouvel appareil simulé. Un appareil simulé est créé automatiquement quand vous créez un modèle d’appareil. Il signale des données et peut être contrôlé tout comme un appareil réel.

Regardez maintenant chacun des onglets de la page **Détails de l’appareil**.

## <a name="measurements"></a>Mesures

Les mesures sont les données qui proviennent de votre appareil. Vous pouvez ajouter plusieurs mesures à votre modèle d’appareil pour le faire correspondre aux fonctionnalités de votre appareil.

- Les mesures de type **télémétrie** sont les points de données numériques que votre appareil collecte au fil du temps. Elles sont représentées sous la forme d’un flux continu. La température en est un exemple.
- Les mesures de type **événement** sont des données ponctuelles qui représentent quelque chose d’important sur l’appareil. Un niveau de gravité représente l’importance d’un événement. Il peut s’agir, par exemple, d’une erreur du moteur du ventilateur.
- Les mesures de type **état** représentent l’état de l’appareil ou de ses composants sur une période de temps. Par exemple, vous pouvez définir le mode d’un ventilateur avec deux états possibles : **En fonctionnement** et **Arrêté**.

### <a name="create-a-telemetry-measurement"></a>Créer une mesure de télémétrie
Pour ajouter une nouvelle mesure de télémétrie, sélectionnez le bouton **+ Nouvelle mesure**. Sélectionnez **Télémétrie** comme type de mesure, puis entrez les détails dans le formulaire **Créer une télémétrie**.

> [!NOTE]
> Quand un appareil réel est connecté, soyez attentif au nom de la mesure signalée par l’appareil. Le nom doit correspondre exactement à l’entrée **Nom du champ** d’une mesure.

Par exemple, vous pouvez ajouter une nouvelle mesure de télémétrie de température :

![Formulaire « Créer une télémétrie » contenant des détails sur la mesure de température](./media/howto-set-up-template/measurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure **Température** figure dans la liste des mesures. Un opérateur peut afficher la visualisation des données de température recueillies par l’appareil.

![Graphique Mesure](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Créer une mesure d’événement
Pour ajouter une nouvelle mesure d’événement, sélectionnez le bouton **+ Nouvelle mesure**. Sélectionnez **Événement** comme type de mesure, puis entrez les détails dans le formulaire **Créer un événement**.

Indiquez les détails de l’événement dans les champs **Nom d’affichage**, **Nom du champ** et **Gravité**. Vous pouvez choisir parmi les trois niveaux de gravité disponibles : **Erreur**, **Avertissement** et **Information**.  

Par exemple, vous pouvez ajouter un nouvel événement **Erreur du moteur du ventilateur**.

![Formulaire « Créer un événement » contenant des détails sur un événement du moteur du ventilateur](./media/howto-set-up-template/eventmeasurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure **Erreur du moteur du ventilateur** figure dans la liste des mesures. Un opérateur peut afficher la visualisation des données d’événement envoyées par l’appareil.

![Graphique de mesure d’événement](./media/howto-set-up-template/eventmeasurementschart.png)

Pour afficher des détails supplémentaires sur l’événement, sélectionnez l’icône de l’événement sur le graphique :

![Détails sur l’événement « Erreur du moteur du ventilateur »](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Créer une mesure d’état
Pour ajouter une nouvelle mesure d’état, sélectionnez le bouton **+ Nouvelle mesure**. Sélectionnez **État** comme type de mesure, puis entrez les détails dans le formulaire **Créer un état**.

Indiquez les détails de l’état dans les champs **Nom d’affichage**, **Nom du champ** et **Valeurs**. Chaque valeur peut aussi être assortie d’un nom d’affichage qui sera utilisé quand la valeur s’affichera dans les graphiques et les tableaux.

Par exemple, vous pouvez ajouter un nouvel état **Mode du ventilateur**, dont les deux valeurs possibles que peut envoyer l’appareil sont **En fonctionnement** et **Arrêté**.

![Formulaire « Modifier l’état » contenant des détails sur le mode du ventilateur](./media/howto-set-up-template/statemeasurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure d’état **Mode du ventilateur** figure dans la liste des mesures. L’opérateur peut afficher la visualisation des données d’état envoyées par l’appareil.

![Graphique de mesure d’état](./media/howto-set-up-template/statemeasurementschart.png)

Si l’appareil envoie un trop grand nombre de points de données sur une courte période, la mesure d’état s’affiche avec un visuel différent, comme le montre la capture d’écran suivante. Si vous cliquez sur le graphique, tous les points de données de cette période sont affichés par ordre chronologique. Vous pouvez également réduire l’intervalle de temps pour voir la mesure tracée sur le graphique.

![Détails sur la mesure d’état « Mode du ventilateur statique »](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Paramètres

Les paramètres contrôlent un appareil. Ils permettent aux opérateurs de votre application de fournir des entrées à l’appareil. Vous pouvez ajouter plusieurs paramètres à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Paramètres**, que les opérateurs peuvent utiliser. Vous pouvez ajouter six types de paramètres : nombre, texte, date, bascule, liste de choix et étiquette de section.

> [!NOTE]
> Quand un appareil réel est connecté, soyez attentif au nom du paramètre signalé par l’appareil. Le nom doit correspondre exactement à l’entrée **Nom du champ** d’un paramètre.

Les paramètres peuvent avoir un parmi trois états. L’appareil signale ces états.

- **Synchronisé** : l’appareil a changé pour refléter la valeur du paramètre.

- **En attente** : l’appareil est en train de prendre la valeur du paramètre.

- **Erreur** : l’appareil a retourné une erreur.

Par exemple, vous pouvez ajouter un nouveau paramètre de vitesse du ventilateur :

![Formulaire « Configurer le nombre » contenant des détails sur les paramètres de vitesse](./media/howto-set-up-template/settingsform.png)

Une fois que vous avez sélectionné **Enregistrer**, le paramètre **Vitesse du ventilateur** apparaît sous forme de vignette et peut être utilisé pour changer la vitesse du ventilateur de l’appareil.

Après avoir créé une vignette, vous pouvez essayer votre nouveau paramètre. Désactivez d’abord le mode Conception en haut à droite de l’écran.

![Onglet « Paramètres » avec le commutateur « Mode Conception » pour la vignette](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Propriétés

Les propriétés sont les métadonnées associées à l’appareil, comme l’emplacement et le numéro de série de l’appareil. Vous pouvez ajouter plusieurs propriétés à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Propriétés**. Un opérateur peut spécifier les valeurs des propriétés quand il crée un appareil et les modifier à tout moment. Vous pouvez ajouter six types de propriété : nombre, texte, date, bascule, propriété d’appareil et étiquette.

Il existe deux catégories de propriété :

- Les propriétés de type **Appareil** qui sont signalées par l’appareil.
- Les propriétés de type **Application** qui sont stockées seulement dans l’application. L’appareil ne reconnaît pas les propriétés d’application.

> [!NOTE]
> Pour les propriétés d’appareil, quand un appareil réel est connecté, soyez attentif au nom de la propriété signalée par l’appareil. Le nom doit correspondre exactement à l’entrée **Nom du champ** de la propriété. Pour les propriétés d’application, le nom du champ est à votre libre choix dès lors qu’il est unique dans le modèle d’appareil.

Par exemple, vous pouvez ajouter l’emplacement de l’appareil en tant que nouvelle propriété :

![Formulaire « Configurer le texte » sous l’onglet « Propriétés »](./media/howto-set-up-template/propertiesform.png)

Une fois que vous avez sélectionné **Enregistrer**, l’emplacement de l’appareil apparaît sous forme de vignette :

![Vignette Emplacement](./media/howto-set-up-template/propertiestile.png)

Après avoir créé une vignette, vous pouvez changer la valeur de la propriété. Désactivez d’abord le mode Conception en haut à droite de l’écran.

### <a name="create-a-location-property-through-azure-maps"></a>Créer une propriété d’emplacement via Azure Maps
Vous pouvez indiquer le contexte géographique des données d’emplacement dans Azure IoT Central et cartographier les coordonnées de latitude et de longitude d’une adresse postale. Vous pouvez aussi simplement cartographier les coordonnées de latitude et de longitude. Azure Maps active cette fonctionnalité dans IoT Central.

Vous pouvez ajouter deux types de propriété d’emplacement :
- L’**emplacement en tant que propriété d’application**, qui est stockée uniquement dans l’application. L’appareil ne reconnaît pas les propriétés d’application.
- L’**emplacement en tant que propriété d’appareil**, qui est signalée par l’appareil.

#### <a name="add-location-as-an-application-property"></a>Ajouter un emplacement en tant que propriété d’application 
Vous pouvez créer une propriété d’emplacement sous forme de propriété d’application en utilisant Azure Maps dans votre application Azure IoT Central. Par exemple, vous pouvez ajouter l’adresse d’installation de l’appareil. 

1. Sous l’onglet **Propriétés**, vérifiez que le **Mode Conception** est **Activé**.

   ![Onglet « Propriétés » avec le mode Conception activé](./media/howto-set-up-template/locationcloudproperty1.png)

2. Dans la bibliothèque, sélectionnez **Emplacement**.
3. Configurez les champs **Nom d’affichage**, **Nom du champ** et éventuellement **Valeur initiale** pour l’emplacement. 

   ![Formulaire « Configurer l’emplacement » contenant les détails de l’emplacement](./media/howto-set-up-template/locationcloudproperty2.png)

   Vous pouvez ajouter un emplacement dans les deux formats suivants :
   - **Emplacement sous forme d’adresse**
   - **Emplacement sous forme de coordonnées** 

4. Sélectionnez **Enregistrer**. 

   ![Propriété d’emplacement avec l’adresse d’installation ajoutée](./media/howto-set-up-template/locationcloudproperty3.png)

Un opérateur peut désormais mettre à jour la valeur de l’emplacement dans le formulaire du champ de l’emplacement. 

#### <a name="add-location-as-a-device-property"></a>Ajouter un emplacement en tant que propriété d’appareil 

Vous pouvez créer une propriété d’emplacement sous forme de propriété d’appareil signalée par cet appareil. Par exemple, si vous voulez effectuer le suivi de l’emplacement de l’appareil :

1. Sous l’onglet **Propriétés**, vérifiez que le **Mode Conception** est **Activé**.

   ![Onglet « Propriétés » avec le mode Conception activé](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Cliquez **Propriété de l’appareil** dans la bibliothèque.
3. Configurez le nom d’affichage et le nom du champ, puis sélectionnez **Emplacement** comme type de données. 

   > [!NOTE]
   > Le nom du champ doit correspondre exactement au nom de la propriété signalée par l’appareil. 

   ![Formulaire « Configurer les propriétés de l’appareil » contenant les détails de l’emplacement](./media/howto-set-up-template/locationdeviceproperty2.png)

Maintenant que vous avez configuré votre propriété d’emplacement, vous pouvez [ajouter une carte pour visualiser l’emplacement dans le tableau de bord de l’appareil](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Commandes

Les commandes permettent de gérer un appareil à distance. Cela permet aux opérateurs de votre application d’exécuter instantanément les commandes sur l’appareil. Vous pouvez ajouter plusieurs commandes à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Commandes**, que les opérateurs peuvent utiliser. En tant que créateur de l’appareil, vous avez la possibilité de définir des commandes en fonction de vos besoins.

En quoi une commande est-elle différente d’un paramètre ? 

* **Paramètre** : un paramètre est une configuration que vous souhaitez appliquer à un appareil et que celui-ci doit conserver jusqu’à ce que vous décidiez de le modifier. Par exemple, vous pouvez définir la température de votre congélateur et faire en sorte que ce réglage reste actif même quand le congélateur redémarre. 

* **Commande** : les commandes vous permettent d’exécuter instantanément une commande sur l’appareil à distance à partir d’IoT Central. Si aucun appareil n’est connecté, la commande expire et échoue. Tel peut être le cas si vous souhaitez redémarrer un appareil.  

Quand vous exécutez une commande, vous pouvez obtenir trois états différents, selon que l’appareil a reçu la commande ou non. 

Par exemple, vous pouvez ajouter un nouvelle commande **Écho** :

![Formulaire « Configurer la commande » contenant les détails de l’écho](./media/howto-set-up-template/commandsecho.png)

Après avoir sélectionné **Enregistrer**, la commande **Écho** s’affiche sous forme de vignette et est prête à être utilisée pour envoyer un écho à l’appareil.

Après avoir créé une vignette, vous pouvez essayer votre nouvelle commande.

## <a name="rules"></a>Règles

Les règles permettent aux opérateurs de surveiller des appareils quasi en temps réel. Les règles appellent automatiquement des actions, comme l’envoi d’un e-mail quand la règle se déclenche. Un seul type de règle est actuellement disponible :

- La **règle de télémétrie**, qui se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié. [Découvrez plus en détail les règles de télémétrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Tableau de bord

Le tableau de bord est l’endroit où un opérateur peut accéder à des informations sur un appareil. Comme concepteur, vous pouvez ajouter des vignettes à cette page pour aider les opérateurs à comprendre comment l’appareil se comporte. Vous pouvez ajouter plusieurs vignettes de tableau de bord à votre modèle d’appareil. Vous pouvez ajouter six types de vignette de tableau de bord : image, graphique en courbes, graphique à barres, indicateur de performance clé (KPI), paramètres et propriétés, puis étiquette.

Par exemple, vous pouvez ajouter une vignette **Paramètres et propriétés** pour montrer une sélection des valeurs actuelles de paramètres et de propriétés :

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Maintenant, quand un opérateur visualise le tableau de bord, il peut voir cette vignette qui affiche les propriétés et les paramètres de l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Ajouter un emplacement Azure Maps dans le tableau de bord

Si vous avez configuré une propriété d’emplacement précédemment dans la section [Créer une propriété d’emplacement via Azure Maps](#create-a-location-property-through-azure-maps), vous pouvez visualiser cet emplacement en utilisant une carte dans le tableau de bord de votre appareil.

1. Sous l’onglet **Tableau de bord**, vérifiez que le **Mode Conception** est **Activé**.

   ![Onglet « Tableau de bord » avec le mode Conception activé](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Dans le tableau de l’appareil, sélectionnez **Carte** dans la bibliothèque. 
3. Donnez un titre et choisissez la propriété d’emplacement que vous avez configurée précédemment dans les propriétés de votre appareil.

   ![Formulaire « Configurer la carte » contenant les détails de la vignette et les propriétés](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Sélectionnez **Enregistrer**. La vignette de la carte présente maintenant l’emplacement que vous avez sélectionné. 

   ![Vignette de la carte avec l’emplacement sélectionné](./media/howto-set-up-template/locationcloudproperty6map.png) 

Vous pouvez redimensionner la carte à la taille souhaitée.

Dès lors, quand un opérateur consulte le tableau de bord, il voit toutes les vignettes de tableau de bord que vous avez configurées, y compris la carte d’emplacement.

![Vignettes dans le tableau de bord](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer un modèle d’appareil dans votre application Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Créer une version d’un modèle d’appareil](howto-version-devicetemplate.md)
