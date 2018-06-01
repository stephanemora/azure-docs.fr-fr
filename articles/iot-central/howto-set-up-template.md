---
title: Configurer un modèle d’appareil dans une application Azure IoT Central | Microsoft Docs
description: Découvrez comment configurer un modèle d’appareil avec des mesures, des paramètres, des propriétés, des règles et un tableau de bord.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 52c6c8fe4375354d650f92b73bffc288c9a2ccfe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201507"
---
# <a name="set-up-a-device-template"></a>Configurer un modèle d’appareil

Un modèle d’appareil est un blueprint qui définit les caractéristiques et les comportements d’un type d’appareil qui se connecte à une application Microsoft Azure IoT Central.

Par exemple, un concepteur peut créer un modèle d’appareil pour un ventilateur connecté à IoT qui a :

- Une mesure de télémétrie de température

- Une mesure d’événement d’erreur du moteur du ventilateur

- Une mesure de l’état de fonctionnement du ventilateur

- Un paramètre de vitesse du ventilateur

- Une propriété d’emplacement

- Des règles qui envoient des alertes

- Un tableau de bord qui vous donne une vision globale de l’appareil

À partir de ce modèle d’appareil, un opérateur peut créer et connecter des appareils qui sont des ventilateurs réels avec des noms comme **ventilateur-1** et **ventilateur-2**. Tous ces ventilateurs ont des mesures, des paramètres, des propriétés, des règles et un tableau de bord que les utilisateurs de votre application peuvent surveiller et gérer.

> [!NOTE]
Seuls les concepteurs et les administrateurs peuvent créer, modifier et supprimer des modèles d’appareils. Tous les utilisateurs peuvent créer des appareils dans la page **Explorateur d’appareils** à partir de modèles d’appareils existants.

## <a name="create-a-new-device-template"></a>Créer un modèle d’appareil

1. Accédez à la page **Concepteur d’applications**.

1. Pour créer un modèle vide, choisissez **Créer un modèle d’appareil**, puis choisissez **Personnalisé**.

1. Entrez un nom pour votre nouveau modèle d’appareil, puis choisissez **Créer**.

    ![Page Détails de l’appareil](./media/howto-set-up-template/devicedetailspage.png)

1. Vous êtes maintenant dans la page **Détails de l’appareil** d’un nouvel appareil simulé. Un appareil simulé est automatiquement créé quand vous créez un modèle d’appareil. Il signale des données et peut être contrôlé tout comme un appareil réel.

Regardez maintenant chacun des onglets de la page **Détails de l’appareil**.

## <a name="measurements"></a>Mesures

Les mesures sont les données provenant de votre appareil. Vous pouvez ajouter plusieurs mesures à votre modèle d’appareil pour le faire correspondre aux fonctionnalités de votre appareil. Actuellement, la télémétrie et les événements sont les types de mesures pris en charge.

- Les mesures de type **télémétrie** sont les points de données numériques que votre appareil collecte au fil du temps ; elles sont représentées sous la forme d’un flux continu. Par exemple, la température.
- Les mesures de type **événement** sont des données ponctuelles qui représentent quelque chose ayant une signification sur l’appareil. Une gravité est associée aux événements, qui représente l’importance de l’événement. Par exemple, une erreur du moteur du ventilateur.
- Les mesures de type **état** représentent l’état de l’appareil ou de ses composants sur une période de temps. Par exemple, vous pouvez définir le mode d’un ventilateur avec deux états possibles : En fonctionnement et Arrêté.

### <a name="create-a-telemetry-measurement"></a>Créer une mesure de télémétrie
Pour ajouter une nouvelle mesure de télémétrie, cliquez sur le bouton **+ Nouvelle mesure** pour ouvrir un formulaire avec des choix pour sélectionner le type de mesure. Sélectionnez **Télémétrie** et entrez les détails dans le formulaire **Créer une télémétrie**.

> [!NOTE]
> Quand un appareil réel est connecté, soyez attentif au nom de la mesure signalée par l’appareil. Le nom doit correspondre exactement au **Nom du champ** d’une mesure.

Par exemple, vous pouvez ajouter une nouvelle mesure de télémétrie de température :

![Formulaire Mesures](./media/howto-set-up-template/measurementsform.png)

Une fois que vous avez choisi **Enregistrer**, la mesure **Température** apparaît dans la liste des mesures ; un opérateur peut dès lors visualiser les données de température collectées par l’appareil.

![Graphique des mesures](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Créer une mesure d’événement
Pour ajouter une nouvelle mesure d’événement, cliquez sur le bouton **+ Nouvelle mesure** pour ouvrir un formulaire avec des choix pour sélectionner le type de mesure. Sélectionnez **Événement** et entrez les détails dans le formulaire **Créer un événement**.

Dans ce formulaire, spécifiez le **Nom d’affichage**, le **Nom du champ**et la **Gravité** de l’événement. Vous pouvez choisir parmi les trois niveaux de gravité disponibles : **Erreur**, **Avertissement** et **Information**.  

Par exemple, vous pouvez ajouter un nouvel événement « Erreur du moteur du ventilateur ».

![Formulaire Mesures d’événement](./media/howto-set-up-template/eventmeasurementsform.png)

Une fois que vous avez choisi **Enregistrer**, la mesure **Erreur du moteur du ventilateur** apparaît dans la liste des mesures ; un opérateur peut dès lors visualiser les données d’événement envoyées par l’appareil.

![Graphique Mesures d’événement](./media/howto-set-up-template/eventmeasurementschart.png)

Pour voir des détails supplémentaires sur l’événement, cliquez sur l’icône de l’événement sur le graphique :

![Détail des mesures d’événement](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Créer une mesure d’état
Pour ajouter une nouvelle mesure d’état, cliquez sur le bouton **+ Nouvelle mesure** pour ouvrir un formulaire avec des choix pour sélectionner le type de mesure. Sélectionnez **État** et entrez les détails dans le formulaire **Créer un état**.

Dans ce formulaire, spécifiez le **Nom d’affichage**, le **Nom du champ**et les **valeurs** possibles de l’état. Chaque **valeur** peut également avoir un nom d’affichage qui est utilisé lors de l’affichage de la valeur sur les graphiques et les tableaux.

Par exemple, vous pouvez ajouter un nouvel état « Mode du ventilateur », qui a deux valeurs possibles que l’appareil peut envoyer, **En fonctionnement** et **Arrêté**.

![Formulaire Mesures d’état](./media/howto-set-up-template/statemeasurementsform.png)

Une fois que vous avez choisi **Enregistrer**, la mesure **Mode du ventilateur** apparaît dans la liste des mesures ; un opérateur peut dès lors visualiser les données d’état envoyées par l’appareil.

![Graphique Mesures d’état](./media/howto-set-up-template/statemeasurementschart.png)

Au cas où trop de points de données sont envoyés par l’appareil sur une courte période de temps, la mesure d’état est affichée avec un visuel différent, comme illustré ci-dessous. Si vous cliquez sur le graphique, tous les points de données de cette période sont affichés par ordre chronologique. Vous pouvez également réduire l’intervalle de temps pour voir la mesure tracée sur le graphique.

![Détail des mesures d’état](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Paramètres

Les paramètres contrôlent un appareil. Ils permettent aux opérateurs de votre application de fournir des entrées à l’appareil. Vous pouvez ajouter plusieurs paramètres à votre modèle d’appareil qui apparaissent sous forme de vignettes sous l’onglet **Paramètres**, que les opérateurs peuvent utiliser. Vous pouvez ajouter six types de paramètres : nombre, texte, date, bascule, liste de choix et étiquette de section.

> [!NOTE]
> Quand un appareil réel est connecté, soyez attentif au nom du paramètre signalé par l’appareil. Le nom doit correspondre exactement au **Nom du champ** d’un paramètre.

Les paramètres peuvent avoir un parmi trois états. Ces états sont signalés par l’appareil.

- **Synchronisé** : l’appareil a changé pour refléter la valeur du paramètre.

- **En attente** : l’appareil passe actuellement à la valeur du paramètre.

- **Erreur** : l’appareil a retourné une erreur.

Par exemple, vous pouvez ajouter un nouveau paramètre de vitesse du ventilateur :

![Formulaire Paramètres](./media/howto-set-up-template/settingsform.png)

Une fois que vous avez choisi **Enregistrer**, le paramètre **Vitesse du ventilateur** apparaît sous forme de vignette et peut être utilisé pour changer la vitesse du ventilateur de l’appareil.

> [!NOTE]
> Après avoir créé une vignette, vous pouvez essayer votre nouveau paramètre. Désactivez d’abord le mode Conception en haut à droite de l’écran :

![Vignette Paramètres](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Propriétés

Les propriétés sont les métadonnées associées à l’appareil, comme l’emplacement et le numéro de série. Vous pouvez ajouter plusieurs propriétés à votre modèle d’appareil ; elles apparaissent sous forme de vignettes sous l’onglet **Propriétés**. Un opérateur peut spécifier les valeurs des propriétés quand il crée un appareil et les modifier à tout moment. Les propriétés que vous pouvez ajouter sont de six types : nombre, texte, date, bascule, propriété d’appareil et étiquette.

Il existe deux types de propriétés :

- Les **propriétés d’appareil** sont signalées par l’appareil.
- Les **propriétés d’application** sont celles qui sont stockées seulement dans l’application. L’appareil n’a aucune connaissance des propriétés d’application.

> [!NOTE]
> Pour les propriétés d’appareil, quand un appareil réel est connecté, soyez attentif au nom de la propriété signalée par l’appareil. Le nom doit correspondre exactement au **Nom du champ** de la propriété. Pour les propriétés d’application, le nom du champ est à votre libre choix dès lors qu’il est unique dans le modèle d’appareil.

Par exemple, vous pouvez ajouter l’emplacement de l’appareil en tant que nouvelle propriété :

![Formulaire Propriétés](./media/howto-set-up-template/propertiesform.png)

Une fois que vous avez choisi **Enregistrer**, l’emplacement de l’appareil apparaît sous forme de vignette :

![Vignette Propriétés](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Après avoir créé une vignette, vous pouvez changer la valeur de la propriété. Désactivez d’abord le mode Conception en haut à droite de l’écran.

## <a name="rules"></a>Règles

Les règles permettent aux opérateurs de surveiller des appareils quasi en temps réel. Les règles appellent automatiquement des **actions**, comme l’envoi d’un e-mail quand la règle se déclenche. Un seul type de règle est actuellement disponible :

- **Règle de télémétrie :** une règle de télémétrie se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié. Découvrez plus en détail les [règles de télémétrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Tableau de bord

Le tableau de bord est l’endroit où un opérateur peut accéder à des informations sur un appareil. Comme concepteur, vous pouvez ajouter des vignettes à cette page pour aider les opérateurs à comprendre comment l’appareil se comporte. Vous pouvez ajouter plusieurs vignettes de tableau de bord à votre modèle d’appareil. Les vignettes de tableau de bord que vous pouvez ajouter sont de six types : image, graphique en courbes, graphique à barres, indicateur de performance clé, paramètres et propriétés, et étiquette.

Par exemple, vous pouvez ajouter une vignette **Paramètres et propriétés** pour montrer une sélection des valeurs actuelles de paramètres et de propriétés :

![Formulaire Détails de l’appareil du tableau de bord](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Maintenant, quand un opérateur visualise le tableau de bord, il peut voir cette vignette qui affiche les propriétés et les paramètres de l’appareil :

![Vignette du tableau de bord](./media/howto-set-up-template/dashboardtile.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer un modèle d’appareil dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Créer une version d’un modèle d’appareil](howto-version-devicetemplate.md)
