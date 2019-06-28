---
title: Configurer un modèle d’appareil dans une application Azure IoT Central | Microsoft Docs
description: Découvrez comment configurer un modèle d’appareil avec des mesures, des paramètres, des propriétés, des règles et un tableau de bord.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d1704220a13b6d6b5a48b7167d7912a38057127d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65466510"
---
# <a name="set-up-a-device-template"></a>Configurer un modèle d’appareil

Un modèle d’appareil est un blueprint qui définit les caractéristiques et les comportements d’un type d’appareil se connectant à une application Azure IoT Central.

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

1. Accédez à la page **Modèles d’appareil**.

2. Pour créer un modèle, commencez par sélectionner **+Nouveau**.

3. Pour démarrer rapidement, choisissez parmi les modèles prédéfinis existants. Sinon, sélectionnez **Personnalisé**, entrez un nom, puis cliquez sur **Créer** pour créer votre propre modèle en partant de zéro.

   ![Bibliothèque des modèles d’appareils](./media/howto-set-up-template/newtemplate.png)

4. Lorsque vous créez un modèle personnalisé, vous voyez la page **Détails de l’appareil** de votre nouveau modèle d’appareil. IoT Central crée automatiquement un appareil simulé quand vous créez un modèle d’appareil. Un appareil simulé vous permet de tester le comportement de votre application avant de connecter un appareil physique.

Les sections suivantes décrivent chacun des onglets de la page **Modèle d’appareil**.

## <a name="measurements"></a>Mesures

Les mesures sont les données qui proviennent de votre appareil. Vous pouvez ajouter plusieurs mesures à votre modèle d’appareil pour le faire correspondre aux fonctionnalités de votre appareil.

- Les mesures de type **télémétrie** sont les points de données numériques que votre appareil collecte au fil du temps. Elles sont représentées sous la forme d’un flux continu. La température en est un exemple.
- Les mesures de type **événement** sont des données ponctuelles qui représentent quelque chose d’important sur l’appareil. Un niveau de gravité représente l’importance d’un événement. Il peut s’agir, par exemple, d’une erreur du moteur du ventilateur.
- Les mesures de type **état** représentent l’état de l’appareil ou de ses composants sur une période de temps. Par exemple, vous pouvez définir le mode d’un ventilateur avec deux états possibles : **En fonctionnement** et **Arrêté**.

### <a name="create-a-telemetry-measurement"></a>Créer une mesure de télémétrie

Pour ajouter une nouvelle mesure de télémétrie, sélectionnez **+ Nouvelle mesure**, choisissez **Télémétrie** comme type de mesure, puis entrez les détails dans le formulaire.

> [!NOTE]
> Les noms de champs du modèle d’appareil doivent correspondre aux noms de propriétés du code d’appareil correspondant, pour que les données de télémétrie soient affichées dans l’application quand un appareil physique y est connecté. Vous devrez faire de même lorsque vous configurerez les paramètres, les propriétés de l’appareil et les commandes dans les sections qui suivent.
.png Par exemple, vous pouvez ajouter une nouvelle mesure de télémétrie de température :

| Nom d’affichage        | Nom du champ    |  Units    | Min   |max|
| --------------------| ------------- |-----------|-------|---|
| Température         | temp          |  degC     |  0    |100|

![Formulaire « Créer une télémétrie » contenant des détails sur la mesure de température](./media/howto-set-up-template/measurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure **Température** figure dans la liste des mesures. Au bout de quelques instants, vous voyez la visualisation des données de température de l’appareil simulé.

Lors de l’affichage des données de télémétrie, vous pouvez choisir parmi les options d’agrégation suivantes : Moyenne, Minimum, Maximum, Somme et Nombre. **Moyenne** est sélectionnée en tant qu’agrégation par défaut sur le graphique. 

> [!NOTE]
> La mesure de télémétrie est représentée par un nombre à virgule flottante.

### <a name="create-an-event-measurement"></a>Créer une mesure d’événement

Pour ajouter une nouvelle mesure d’événement, sélectionnez **+ Nouvelle mesure** et sélectionnez **Événement** comme type de mesure. Entrez les détails dans le formulaire **Créer un événement**.

Indiquez les détails de l’événement dans les champs **Nom d’affichage**, **Nom du champ** et **Gravité**. Vous pouvez choisir un des trois niveaux de gravité disponibles : **Erreur**, **Avertissement** et **Information**.

Par exemple, vous pouvez ajouter un nouvel événement **Erreur du moteur du ventilateur**.

| Nom d’affichage        | Nom du champ    |  Gravité par défaut |
| --------------------| ------------- |-----------|
| Erreur du moteur du ventilateur     | fanmotorerror |  Error    |

![Formulaire « Créer un événement » contenant des détails sur un événement du moteur du ventilateur](./media/howto-set-up-template/eventmeasurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure **Erreur du moteur du ventilateur** figure dans la liste des mesures. Au bout de quelques instants, vous voyez la visualisation des données d’événement de l’appareil simulé.

Pour afficher des détails supplémentaires sur un événement, sélectionnez l’icône de l’événement sur le graphique :

![Détails sur l’événement « Erreur du moteur du ventilateur »](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Le type de données de la mesure d’événement est « chaîne ».

### <a name="create-a-state-measurement"></a>Créer une mesure d’état

Pour ajouter une nouvelle mesure d’état, sélectionnez le bouton **+ Nouvelle mesure** et sélectionnez **État** comme type de mesure. Entrez les détails dans le formulaire **Créer un état**.

Indiquez les détails de l’état dans les champs **Nom d’affichage**, **Nom du champ** et **Valeurs**. Chaque valeur peut aussi être assortie d’un nom d’affichage qui sera utilisé quand la valeur s’affichera dans les graphiques et les tableaux.

Par exemple, vous pouvez ajouter un nouvel état **Mode du ventilateur**, dont les deux valeurs possibles que peut envoyer l’appareil sont **En fonctionnement** et **Arrêté**.

| Nom d’affichage | Nom du champ    |  Valeur 1   | Nom d’affichage | Valeur 2    |Nom d’affichage  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Mode du ventilateur     | fanmode       |  1         | En fonctionnement    |     0      | Arrêté      |

![Formulaire « Modifier l’état » contenant des détails sur le mode du ventilateur](./media/howto-set-up-template/statemeasurementsform.png)

Après avoir sélectionné **Enregistrer**, la mesure d’état **Mode du ventilateur** figure dans la liste des mesures. Au bout de quelques instants, vous voyez la visualisation des données d’état de l’appareil simulé.

Si l’appareil envoie un trop grand nombre de points de données sur une courte période, la mesure d’état s’affiche avec un visuel différent. Sélectionnez le graphique pour voir tous les points de données de cette période par ordre chronologique. Vous pouvez également réduire l’intervalle de temps pour voir la mesure tracée sur le graphique.

> [!NOTE]
> Le type de données de la mesure d’état est « chaîne ».

## <a name="settings"></a>Paramètres

Les paramètres contrôlent un appareil. Ils permettent aux opérateurs de fournir des entrées à l’appareil. Vous pouvez ajouter plusieurs paramètres à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Paramètres**, que les opérateurs peuvent utiliser. Vous pouvez ajouter six types de paramètres : nombre, texte, date, bascule, liste de choix et étiquette de section.

Les paramètres peuvent avoir un parmi trois états. L’appareil signale ces états.

- **Synchronisé** : l'appareil a changé pour refléter la valeur du paramètre.

- **Pending** : l'appareil est en train de prendre la valeur du paramètre.

- **Erreur** : l'appareil a renvoyé une erreur.

Par exemple, vous pouvez ajouter un nouveau paramètre de vitesse du ventilateur en sélectionnant **Paramètres** et en entrant le nouveau paramètre **Nombre** :

| Nom d’affichage  | Nom du champ    |  Units  | Décimales |Initial|
| --------------| ------------- |---------| ---------|---- |
| Vitesse du ventilateur     | fanSpeed      | TR/MIN     | 2        | 0   |

![Formulaire « Configurer le nombre » contenant des détails sur les paramètres de vitesse](./media/howto-set-up-template/settingsform.png)

Une fois que vous avez sélectionné **Enregistrer**, le paramètre **Vitesse du ventilateur** apparaît sous forme de vignette. Un opérateur peut utiliser le paramètre dans la page **Device Explorer** pour changer la vitesse du ventilateur de l’appareil.

## <a name="properties"></a>properties

Les propriétés sont les métadonnées associées à l’appareil, comme l’emplacement et le numéro de série de l’appareil. Ajoutez plusieurs propriétés à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Propriétés**. Une propriété peut avoir un type, tel que nombre, texte, date, activer/désactiver, propriété de l’appareil, étiquette ou emplacement. Un opérateur peut spécifier les valeurs des propriétés quand il crée un appareil et les modifier à tout moment. Les propriétés d’appareil sont en lecture seule et elles sont envoyées par l’appareil à l’application. Un opérateur ne peut pas changer les propriétés d’appareil. Quand un appareil physique se connecte, la vignette de propriété d’appareil est mise à jour dans l’application.

Il existe deux catégories de propriété :

- Les _propriétés d’appareil_, que l’appareil signale à l’application IoT Central. Les propriétés d’appareil sont des valeurs en lecture seule signalées par l’appareil et mises à jour dans l’application quand un appareil physique est connecté.
- Les _propriétés d’application_, qui sont stockées dans l’application et peuvent être modifiées par l’opérateur. L’appareil ne reconnaît pas les propriétés d’application.

Par exemple, vous pouvez ajouter la date du dernier entretien de l’appareil en tant que nouvelle propriété **Date** (une propriété d’application) sous l’onglet **Propriétés** :

| Nom d’affichage  | Nom du champ | Valeur initiale   |
| --------------| -----------|-----------------|
| Dernière utilisation      | lastServiced        | 29/01/2019     |

![Formulaire « Configurer le dernier entretien » sous l’onglet « Propriétés »](./media/howto-set-up-template/propertiesform.png)

Une fois que vous avez sélectionné **Enregistrer**, la date du dernier entretien de l’appareil apparaît sous forme de vignette.

Après avoir créé la vignette, vous pouvez changer la valeur de la propriété d’application dans **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Créer une propriété d’emplacement via Azure Maps

Vous pouvez indiquer le contexte géographique des données d’emplacement dans Azure IoT Central et cartographier les coordonnées de latitude et de longitude d’une adresse postale. Vous pouvez aussi cartographier les coordonnées de latitude et de longitude. Azure Maps active cette fonctionnalité dans IoT Central.

Vous pouvez ajouter deux types de propriété d’emplacement :

- L’**emplacement en tant que propriété d’application**, qui est stocké dans l’application. L’appareil ne reconnaît pas les propriétés d’application.
- L’**emplacement en tant que propriété d’appareil**, qui est signalée par l’appareil à l’application.

#### <a name="add-location-as-an-application-property"></a>Ajouter un emplacement en tant que propriété d’application

Vous pouvez créer une propriété d’emplacement sous forme de propriété d’application en utilisant Azure Maps dans votre application Azure IoT Central. Par exemple, vous pouvez ajouter l’adresse d’installation de l’appareil :

1. Accédez à l’onglet **Propriétés**.

2. Dans la bibliothèque, sélectionnez **Emplacement**.

3. Configurez les champs **Nom d’affichage**, **Nom du champ** et éventuellement **Valeur initiale** pour l’emplacement.

    | Nom d’affichage  | Nom du champ | Valeur initiale |
    | --------------| -----------|---------| 
    | Adresse de l’installation | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulaire « Configurer l’emplacement » contenant les détails de l’emplacement](./media/howto-set-up-template/locationcloudproperty2.png)

   Vous pouvez ajouter un emplacement dans les deux formats suivants :
   - **Emplacement sous forme d’adresse**
   - **Emplacement sous forme de coordonnées**

4. Sélectionnez **Enregistrer**. Un opérateur peut mettre à jour la valeur de l’emplacement dans **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Ajouter un emplacement en tant que propriété d’appareil

Vous pouvez créer une propriété d’emplacement sous forme de propriété d’appareil signalée par cet appareil. Par exemple, si vous voulez effectuer le suivi de l’emplacement de l’appareil :

1. Accédez à l’onglet **Propriétés**.

2. Cliquez **Propriété de l’appareil** dans la bibliothèque.

3. Configurez le nom d’affichage et le nom du champ, puis sélectionnez **Emplacement** comme type de données :

    | Nom d’affichage  | Nom du champ | Type de données |
    | --------------| -----------|-----------|
    | Emplacement de l’appareil | deviceLocation | location  |

   > [!NOTE]
   > Les noms de champs doivent correspondre aux noms de propriétés situés dans le code d’appareil correspondant.

   ![Formulaire « Configurer les propriétés de l’appareil » contenant les détails de l’emplacement](./media/howto-set-up-template/locationdeviceproperty2.png)

Une fois l’appareil physique connecté, l’emplacement que vous avez ajouté en tant que propriété d’appareil est mis à jour avec la valeur envoyée par l’appareil. Maintenant que vous avez configuré votre propriété d’emplacement, vous pouvez [ajouter une carte pour visualiser l’emplacement dans le tableau de bord de l’appareil](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Commandes

Les commandes permettent de gérer un appareil à distance. Elles permettent aux opérateurs d’exécuter des commandes sur l’appareil. Vous pouvez ajouter plusieurs commandes à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Commandes**, que les opérateurs peuvent utiliser. En tant que créateur de l’appareil, vous avez la possibilité de définir des commandes en fonction de vos besoins.

En quoi une commande est-elle différente d’un paramètre ?

* **Paramètre** : un paramètre est une configuration que vous souhaitez appliquer à un appareil. Vous souhaitez que l’appareil conserve cette configuration jusqu’à ce que vous la changiez. Par exemple, vous pouvez définir la température de votre congélateur et faire en sorte que ce réglage reste actif même quand le congélateur redémarre.

* **Commande** : les commandes vous permettent d'exécuter instantanément une commande sur l'appareil à distance à partir d'IoT Central. Si aucun appareil n’est connecté, la commande expire et échoue. Tel peut être le cas si vous souhaitez redémarrer un appareil.

Par exemple, vous pouvez ajouter une nouvelle commande **Echo** en sélectionnant l’onglet **Commandes**, puis en sélectionnant **+ Nouvelle commande** et en entrant les détails de la nouvelle commande :

| Nom d’affichage  | Nom du champ | Délai d’expiration par défaut | Type de données |
| --------------| -----------|---------------- | --------- |
| Commande echo  | echo       |  30             | text      |

![Formulaire « Configurer la commande » contenant les détails de l’écho](./media/howto-set-up-template/commandsecho1.png)

Une fois que vous avez sélectionné **Enregistrer**, la commande **Echo** apparaît sous forme de vignette et peut être utilisée à partir de **Device Explorer** quand votre appareil physique se connecte. Pour que les commandes puissent être exécutées correctement, les noms de champs doivent correspondre aux noms de propriétés situés dans le code d’appareil correspondant.

## <a name="rules"></a>Règles

Les règles permettent aux opérateurs de surveiller des appareils quasi en temps réel. Les règles appellent automatiquement des actions, comme l’envoi d’un e-mail quand la règle se déclenche. Un seul type de règle est actuellement disponible :

- La **règle de télémétrie**, qui se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié. [Découvrez plus en détail les règles de télémétrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>tableau de bord

Le tableau de bord est l’endroit où un opérateur peut accéder à des informations sur un appareil. Comme concepteur, vous pouvez ajouter des vignettes à cette page pour aider les opérateurs à comprendre comment l’appareil se comporte. Vous pouvez ajouter plusieurs vignettes de tableau de bord à votre modèle d’appareil. Vous pouvez ajouter de nombreux types de vignettes de tableau de bord : image, graphique en courbes, graphique à barres, indicateur de performance clé (KPI), paramètres, propriétés et étiquette.

Par exemple, vous pouvez ajouter une vignette **Paramètres et propriétés** pour afficher une sélection des valeurs actuelles de paramètres et de propriétés en sélectionnant l’onglet **Tableau de bord** et la vignette dans la bibliothèque :

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Désormais, quand un opérateur affiche le tableau de bord dans **Device Explorer**, il peut voir la vignette.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Ajouter un emplacement Azure Maps dans le tableau de bord

Si vous avez configuré une propriété d’emplacement, vous pouvez visualiser l’emplacement en utilisant une carte dans le tableau de bord de votre appareil.

1. Accédez à l’onglet **Tableau de bord**.

1. Dans le tableau de l’appareil, sélectionnez **Carte** dans la bibliothèque.

1. Donnez un titre à la carte. L’exemple suivant s’intitule **Emplacement d’installation**. Ensuite, choisissez la propriété d’emplacement que vous avez configurée sous l’onglet **Propriétés**. Dans l’exemple suivant, **Adresse d’installation** est sélectionnée.

   ![Formulaire « Configurer la carte » contenant les détails de la vignette et les propriétés](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Sélectionnez **Enregistrer**. La vignette de la carte présente maintenant l’emplacement que vous avez sélectionné.

Vous pouvez redimensionner la carte à la taille souhaitée. Désormais, quand un opérateur consulte le tableau de bord dans **Device Explorer**, toutes les vignettes de tableau de bord que vous avez configurées, y compris la carte d’emplacement, sont visibles.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer un modèle d’appareil dans votre application Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Créer une nouvelle version de modèle d’appareil](howto-version-devicetemplate.md)
> [Connecter un appareil IoT DevKit MXChip à votre application Azure IoT Central](howto-connect-devkit.md)
> [Connecter une application cliente générique à votre application Azure IoT Central (Node.js)](howto-connect-nodejs.md)