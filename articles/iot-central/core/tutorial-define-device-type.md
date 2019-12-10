---
title: Tutoriel - Définir un nouveau type d’appareil dans Azure IoT Central
description: Ce didacticiel vous indique comment définir un nouveau type d’appareil dans votre application Azure IoT Central en votre qualité de générateur. Vous définissez la télémétrie, l’état, les propriétés et les paramètres pour votre type d’appareil.
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d986ddcf4b8eccfbd4ac1819f96157b5dfe6fe91
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705882"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Didacticiel : Définir un nouveau type d’appareil dans votre application Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ce didacticiel vous indique comment utiliser un modèle d’appareil pour définir un nouveau type d’appareil dans votre application Microsoft Azure IoT Central en votre qualité de générateur. Un modèle d’appareil définit la télémétrie, l’état, les propriétés et les paramètres pour votre type d’appareil.

Pour vous permettre de tester votre application avant de connecter un appareil réel, IoT Central génère un appareil simulé à partir du modèle d’appareil lorsque vous créez ce dernier.

Dans ce didacticiel, vous allez créer le modèle d’appareil **Climatiseur connecté**. Un climatiseur connecté :

* envoie des données de télémétrie comme la température et l’humidité ;
* signale son état, par exemple, allumé ou éteint ;
* Il présente diverses propriétés d’appareil, telles que la version du microprogramme et le numéro de série.
* Il comporte divers paramètres, comme la température de la cible.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’appareil
> * Ajouter des mesures de télémétrie à votre appareil
> * Visualiser la télémétrie simulée
> * Définir une mesure d’événement
> * Visualiser des événements simulés
> * Définir une mesure d’état
> * Visualiser un état simulé
> * Utiliser les paramètres et propriétés
> * Utiliser des commandes
> * Affichez votre appareil simulé dans le tableau de bord

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une application Azure IoT Central créée à partir du modèle d’**application personnalisée**. Si vous n’avez pas d’application, suivez le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md), en veillant à choisir le modèle d’**application personnalisée**.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

En tant que générateur, vous pouvez créer et modifier les modèles d’appareil dans votre application. Lorsque vous créez un modèle d’appareil, Azure IoT Central génère un appareil simulé à partir du modèle. L’appareil simulé génère la télémétrie qui vous permet de tester le comportement de votre application avant de connecter un appareil réel.

Pour ajouter un nouveau modèle d’appareil à votre application, vous devez accéder à la page **Modèles d’appareil**. Pour ce faire, sélectionnez **Modèles d’appareil** dans le volet de gauche.

![Page Modèles d’appareil](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

La procédure ci-après vous explique comment créer un modèle d’appareil **Climatiseur connecté** pour les appareils qui envoient des données de télémétrie de température à votre application :

1. Dans la page **Modèles d’appareil**, sélectionnez **+ Nouveau** :

    ![Page Modèles d’appareil, Créer un modèle d’appareil](./media/tutorial-define-device-type/newtemplate.png)

2. La page affiche les modèles que vous pouvez choisir.

    ![Bibliothèque des modèles d’appareil](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Sélectionnez **Personnalisé**, entrez **Climatiseur connecté** comme nom pour votre appareil, puis sélectionnez **Créer**. Vous pouvez également charger une image de votre appareil qui est visible par les opérateurs dans Device Explorer :

    ![Appareil personnalisé](./media/tutorial-define-device-type/createcustomdevice.png)

4. Dans le modèle d’appareil **Climatiseur connecté**, vérifiez que vous vous trouvez sous l’onglet **Mesures** où vous définissez la télémétrie. Chaque modèle d’appareil que vous définissez comporte différents onglets dans lesquels vous pouvez :

   * Spécifier les _mesures_, telles que les données de télémétrie, d’événement et d’état, qui sont envoyées par l’appareil.

   * Définir les _paramètres_ de contrôle de l’appareil.

   * Définir les _propriétés_ qui sont les métadonnées de l’appareil.

   * Définir les _commandes_ à exécuter directement sur l’appareil.

   * Définir les _règles_ associées à l’appareil.

   * Personnaliser le _tableau de bord_ de l’appareil pour vos opérateurs.

     ![Mesures de climatiseur](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Pour renommer le modèle d’appareil, sélectionnez son nom en haut de la page.

5. Pour ajouter la mesure des données de télémétrie de température, sélectionnez **+ Nouvelle mesure**. Puis choisissez **Telemetry** (Télémétrie) comme type de mesure :

    ![Mesures de climatiseur connecté](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Chaque type de télémétrie que vous définissez pour un modèle d’appareil inclut des [options de configuration](howto-set-up-template.md) telles que :

   * les options d’affichage ;

   * les détails de la télémétrie ;

   * les paramètres de simulation.

     Pour configurer la télémétrie de **température**, utilisez les informations du tableau suivant :

     | Paramètre              | Valeur         |
     | -------------------- | -----------   |
     | Nom d’affichage         | Température   |
     | Nom du champ           | température   |
     | Units                | F             |
     | Min                  | 60            |
     | max                  | 110           |
     | Nombre de décimales       | 0             |

     Vous pouvez également choisir une couleur pour l’affichage de la télémétrie. Pour enregistrer la définition de télémétrie, sélectionnez **Enregistrer** :

     ![Configuration de la simulation de température](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Après un court instant, l’onglet **Mesures** affiche un graphique de la télémétrie de température de votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou l’agrégation, ou pour modifier la définition de télémétrie :
 
    > [!NOTE]
    > Pour les données de télémétrie, l’agrégation est définie sur **Moyenne** par défaut. 

    ![Visualisation de la simulation de température](./media/tutorial-define-device-type/viewsimulation.png)

8. Vous pouvez également personnaliser le graphique à l’aide des contrôles **Ligne**, **Empilé** et **Modifier l’intervalle de temps** :

    ![Personnalisation du graphique](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Ajouter une mesure d’événement

Utilisez des événements pour définir les données dans le temps que l’appareil envoie quand un événement tel qu’une erreur ou une défaillance d’un composant se produit. Azure IoT Central peut simuler des événements d’appareil pour vous permettre de tester le comportement de votre application avant de connecter un appareil réel. Définissez les mesures d’événement pour votre modèle d’appareil dans la vue **Mesures**.

1. Pour ajouter la mesure d’événement **Erreur du moteur du ventilateur**, sélectionnez **+ Nouvelle mesure**. Puis choisissez **Event** (Événement) comme type de mesure :

    ![Mesures de climatiseur connecté](./media/tutorial-define-device-type/eventnew.png)

2. Chaque type d’événement que vous définissez pour un modèle d’appareil inclut des [options de configuration](howto-set-up-template.md) telles que :

   * le nom d’affichage ;

   * le nom du champ ;

   * la gravité.

     Pour configurer l’événement **Erreur du moteur du ventilateur**, utilisez les informations du tableau suivant :

     | Paramètre              | Valeur             |
     | -------------------- | -----------       |
     | Nom d’affichage         | Erreur du moteur du ventilateur   |
     | Nom du champ           | fanmotorerr       |
     | severity             | Error             |

     Pour enregistrer la définition de l’événement, sélectionnez **Enregistrer** :

     ![Configuration de la mesure d’événement](./media/tutorial-define-device-type/eventconfiguration.png)

3. Après un court instant, l’onglet **Mesures** affiche un graphique des événements générés aléatoirement par votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou pour modifier la définition d’événement :

    ![Visualisation de la simulation d’événement](./media/tutorial-define-device-type/eventview.png)

1. Pour voir des détails supplémentaires sur l’événement, sélectionnez l’événement dans le graphique :

    ![Visualisation des détails de l’événement](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Définir une mesure d’état

Vous pouvez utiliser la mesure d’état pour définir et visualiser l’état de l’appareil ou de son composant sur une période spécifique. Azure IoT Central peut simuler un état d’appareil pour vous permettre de tester le comportement de votre application avant de connecter un appareil réel. Vous définissez des mesures d’état pour votre type d’appareil dans la vue **Measurements** (Mesures).

1. Pour ajouter une mesure d’état **Mode du ventilateur**, sélectionnez **+ Nouvelle mesure**. Puis choisissez **État** comme type de mesure :

    ![Mesures d’état de climatiseur connecté](./media/tutorial-define-device-type/statenew.png)

2. Chaque type d’état que vous définissez pour un modèle d’appareil inclut des [options de configuration](howto-set-up-template.md) telles que :

   * le nom d’affichage ;

   * le nom du champ ;

   * les valeurs avec des étiquettes d’affichage facultatives ;

   * la couleur de chaque valeur.

     Pour configurer l’état **Mode du ventilateur**, utilisez les informations du tableau suivant :

     | Paramètre              | Valeur             |
     | -------------------- | -----------       |
     | Nom d’affichage         | Mode du ventilateur          |
     | Nom du champ           | fanmode           |
     | Valeur                | 1                 |
     | Étiquette d’affichage        | En fonctionnement         |
     | Valeur                | 0                 |
     | Étiquette d’affichage        | Arrêté           |

     Pour enregistrer la définition de la mesure d’état, sélectionnez **Enregistrer** :

     ![Configuration de la mesure d’état](./media/tutorial-define-device-type/stateconfiguration.png)

3. Après un court instant, l’onglet **Mesures** affiche un graphique des états générés aléatoirement par votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou pour modifier la définition d’état :

    ![Visualisation de la simulation d’état](./media/tutorial-define-device-type/stateview.png)

4. Si l’appareil envoie un nombre de points de données excessif sur une courte période, la mesure d’état s’affiche avec un visuel différent. Sélectionnez le graphique pour voir tous les points de données de cette période, affichés par ordre chronologique. Vous pouvez également réduire l’intervalle de temps pour voir les mesures de manière plus détaillée.

## <a name="settings-properties-and-commands"></a>Paramètres, propriétés et commandes

Les propriétés, les commandes et les paramètres constituent des valeurs distinctes qui sont définies dans un modèle d’appareil et qui sont associées à chaque appareil :

* Les _paramètres_ vous permettent d’envoyer des données de configuration à un appareil à partir de votre application. Par exemple, un opérateur peut utiliser un paramètre pour remplacer l’intervalle de télémétrie de deux secondes par un intervalle de cinq secondes. Quand un opérateur modifie un paramètre, ce dernier est marqué comme étant en attente dans l’interface utilisateur jusqu’à ce que l’appareil retourne une confirmation.

* Les _propriétés_ vous permettent de définir les métadonnées qui sont associées à votre appareil. Il existe deux catégories de propriété :
    
  * Les _propriétés d’application_ vous permettent d’enregistrer des informations concernant votre appareil dans votre application. Par exemple, vous pouvez utiliser les propriétés d’application pour enregistrer l’emplacement d’un appareil ainsi que sa dernière date de service. Ces propriétés sont stockées dans l’application et ne se synchronisent pas avec l’appareil. Un opérateur peut assigner des valeurs aux propriétés.

  * Les _propriétés d’appareil_ vous permettent d’autoriser un appareil à envoyer des valeurs de propriété à votre application. Ces propriétés sont uniquement modifiables par l’appareil. Les propriétés d’appareil sont en lecture seule pour un opérateur. Dans le cas d’un climatiseur connecté, la version du microprogramme et le numéro de série de l’appareil sont des propriétés d’appareil signalées par ce dernier.
    
    Pour plus d’informations, consultez la section [Propriétés](howto-set-up-template.md#properties) dans le guide pratique de configuration d’un modèle d’appareil.

* Vous utilisez des _commandes_ pour gérer à distance votre appareil à partir de votre application. Vous pouvez exécuter directement des commandes sur l’appareil à partir du cloud pour contrôler les appareils. Par exemple, un opérateur peut exécuter des commandes telles que le redémarrage, pour redémarrer l’appareil immédiatement.

## <a name="use-settings"></a>Utiliser les paramètres

Vous utilisez les *paramètres* pour autoriser un opérateur à envoyer des données de configuration à un appareil. Dans cette section, vous allez ajouter un paramètre à votre modèle d’appareil **Climatiseur connecté** pour permettre à un opérateur de définir la température cible du climatiseur connecté.

1. Accédez à l’onglet **Paramètres** de votre modèle d’appareil **Climatiseur connecté**.

2. Vous pouvez créer différents types de paramètres, tels que des valeurs numériques ou du texte. Sélectionnez **Nombre** pour ajouter un paramètre de type valeur numérique à votre appareil.

3. Pour configurer votre paramètre **Température réglée**, utilisez les informations du tableau suivant :

    | Champ                | Valeur           |
    | -------------------- | -----------     |
    | Nom d’affichage         | Température définie |
    | Nom du champ           | setTemperature  |
    | Unité de mesure      | F               |
    | Nombre de décimales       | 1               |
    | Valeur minimale        | 20              |
    | Valeur maximale        | 200             |
    | Valeur initiale        | 80              |
    | Description          | Température cible du climatiseur |

    Sélectionnez ensuite **Enregistrer** :

    ![Configuration du paramètre Température réglée](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Lorsque l’appareil accuse réception d’une modification de paramètre, le paramètre prend l’état **synchronisé**.

4. Vous pouvez personnaliser la disposition de l’onglet **Paramètres** en déplaçant et en redimensionnant les vignettes de paramètres :

    ![Personnalisation de la disposition des paramètres](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Utiliser les propriétés

Les *propriétés d’application* vous permettent de stocker des informations concernant votre appareil dans l’application. Dans cette section, vous allez ajouter des propriétés d’application à votre modèle d’appareil **Climatiseur connecté** pour stocker l’emplacement de l’appareil et la dernière date de service. Ces propriétés sont modifiables dans l’application. L’appareil indique également des propriétés telles que le numéro de série et la version du microprogramme qui sont en lecture seule dans l’application.

1. Accédez à l’onglet **Propriétés** de votre modèle d’appareil **Climatiseur connecté**.

1. Vous pouvez créer différents types de propriétés d’appareil, comme des nombres ou du texte. Pour ajouter une propriété d’emplacement à votre modèle d’appareil, choisissez **Emplacement**. Pour configurer votre propriété d’emplacement, utilisez les informations du tableau suivant :

    | Champ                | Valeur                |
    | -------------------- | -------------------- |
    | Nom d’affichage         | Location             |
    | Nom du champ           | location             |
    | Valeur initiale        | Seattle, WA          |
    | Description          | Emplacement de l’appareil      |

    Laissez les autres champs définis sur leur valeur par défaut.

    ![Configuration des propriétés de l’appareil](./media/tutorial-define-device-type/configureproperties.png)

    Sélectionnez **Enregistrer**.

1. Pour ajouter une propriété de dernière date de service à votre modèle d’appareil, choisissez **Date**.

1. Pour configurer votre propriété de dernière date de service, utilisez les informations du tableau suivant :

    | Champ                | Valeur                   |
    | -------------------- | ----------------------- |
    | Nom d’affichage         | Dernière date de service       |
    | Nom du champ           | serviceDate             |
    | Valeur initiale        | 1/1/2019                |
    | Description          | Dernière utilisation           |

    ![Configuration des propriétés de l’appareil](./media/tutorial-define-device-type/configureproperties2.png)

    Sélectionnez **Enregistrer**.

1. Vous pouvez personnaliser la disposition de l’onglet **Propriétés** en déplaçant et en redimensionnant les vignettes de propriétés.

1. Pour ajouter une propriété à l’appareil, par exemple, pour ajouter une version du microprogramme au modèle d’appareil, choisissez **Propriété de l’appareil**.

1. Pour configurer votre version du microprogramme, utilisez les informations du tableau suivant :

    | Champ                | Valeur                   |
    | -------------------- | ----------------------- |
    | Nom d’affichage         | Version du microprogramme        |
    | Nom du champ           | firmwareVersion         |
    | Type de données            | text                    |
    | Description          | La version du microprogramme du climatiseur |

    ![Configurer la version du microprogramme](./media/tutorial-define-device-type/configureproperties3.png)

    Sélectionnez **Enregistrer**.

1. Pour ajouter une propriété à l’appareil, par exemple, pour ajouter un numéro de série au modèle d’appareil, choisissez **Propriété de l’appareil**.

1. Pour configurer le numéro de série, utilisez les informations du tableau suivant :

    | Champ                | Valeur                   |
    | -------------------- | ----------------------- |
    | Nom d’affichage         | Numéro de série           |
    | Nom du champ           | serialNumber            |
    | Type de données            | text                    |
    | Description          | Le numéro de série du climatiseur  |

    ![Configurer le numéro de série](./media/tutorial-define-device-type/configureproperties4.png)

    Sélectionnez **Enregistrer**.

    > [!NOTE]
    > Les propriétés de l’appareil sont envoyées de l’appareil vers l’application. Les valeurs de version du microprogramme et du numéro de série seront mises à jour lorsque vous connecterez votre véritable appareil à IoT Central.

## <a name="use-commands"></a>Utiliser des commandes

Vous utilisez les _commandes_ pour permettre à un opérateur d’exécuter des commandes directement sur l’appareil. Dans cette section, vous ajoutez une commande à votre modèle d’appareil **Climatiseur connecté** afin d’activer un opérateur, lequel renvoie un message à l’écran du climatiseur connecté.

1. Accédez à l’onglet **Commandes** de votre modèle d’appareil **Climatiseur connecté** pour modifier le modèle.

1. Sélectionnez **+ Nouvelle commande** pour ajouter une commande à votre appareil et commencer à configurer votre nouvelle commande.

1. Pour configurer votre nouvelle commande, utilisez les informations du tableau suivant :

    | Champ                | Valeur           |
    | -------------------- | -----------     |
    | Nom d’affichage         | Commande echo    |
    | Nom du champ           | echo            |
    | Délai d’expiration par défaut      | 30              |
    | Type de données         | text            |
    | Description          | Commande d’appareil  |  

    Vous pouvez ajouter des entrées supplémentaires à la commande en sélectionnant **+** pour **Champs d’entrée**.

    ![Préparation de l’ajout d’un paramètre](./media/tutorial-define-device-type/commandsecho1.png)

     Sélectionnez **Enregistrer**.

1. Vous pouvez personnaliser la disposition de l’onglet **Commandes** en déplaçant et en redimensionnant les vignettes de commandes.

## <a name="view-your-simulated-device"></a>Visualiser votre appareil simulé

Après avoir défini votre modèle d’appareil **Climatiseur connecté**, vous pouvez personnaliser son **tableau de bord** pour y inclure les mesures, les paramètres et les propriétés que vous avez définis. Vous pouvez visualiser un aperçu du tableau de bord en tant qu’opérateur :

1. Accédez à l’onglet **Tableau de bord** de votre modèle d’appareil **Climatiseur connecté**.

1. Sélectionnez **Graphique en courbes** pour ajouter ce composant au **tableau de bord**.

1. Configurez le composant **Graphique en courbes** en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Température |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures     | Température (sélectionnez **Visibilité** à côté de **Température**) |

    ![Paramètres de graphique en courbes](./media/tutorial-define-device-type/linechartsettings.png)

    Ensuite, sélectionnez **Enregistrer**.

1. Sélectionnez le composant **Historique des événements** en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Événements du moteur de ventilateur |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures     | Erreur du moteur du ventilateur (sélectionnez **Visibilité** à côté de **Erreur du moteur du ventilateur**) |

    ![Paramètres du graphique des événements](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Ensuite, sélectionnez **Enregistrer**.

1. Configurez le composant **Historique des états** en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Mode du ventilateur |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures | Mode du ventilateur (sélectionnez **Visibilité** à côté de **Mode du ventilateur**) |

    ![Paramètres de graphique en courbes](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Ensuite, sélectionnez **Enregistrer**.

1. Pour ajouter les paramètres et propriétés de l’appareil au tableau de bord, choisissez **Paramètres et propriétés**. Sélectionnez **Ajouter/Supprimer** pour ajouter les paramètres ou les propriétés que vous aimeriez voir dans le tableau de bord.

1. Configurez le composant **Settings and Properties** (Paramètres et propriétés) en utilisant les informations du tableau suivant :

    | Paramètre                 | Valeur         |
    | ----------------------- | ------------- |
    | Intitulé                   | Propriétés de l’appareil |
    | Settings and Properties (Paramètres et propriétés) | Température définie<br/>Numéro de série<br/>Version du microprogramme |

    Les paramètres et propriétés que vous avez précédemment définis dans les pages **Paramètres et propriétés** sont affichés dans **Colonnes disponibles**.

    ![Définir des paramètres de propriété de température](./media/tutorial-define-device-type/propertysettings4.png)

    Ensuite, sélectionnez **Enregistrer**.

1. Vous voyez maintenant des données simulées pour votre climatiseur connecté dans le tableau de bord. Vous pouvez modifier les vignettes et la disposition dans le tableau de bord :

    ![Afficher le tableau de bord](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Créer un modèle d’appareil
> * Ajouter des mesures de télémétrie à votre appareil
> * Visualiser la télémétrie simulée
> * Définir les événements d’appareil
> * Visualiser des événements simulés
> * Définir votre état
> * Visualiser un état simulé
> * Utiliser les paramètres et propriétés
> * Utiliser des commandes
> * Affichez votre appareil simulé dans le tableau de bord

Maintenant que vous avez défini un modèle d’appareil dans votre application Azure IoT Central, voici les étapes suivantes suggérées :

* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md)
* [Personnaliser la vue de l’opérateur](tutorial-customize-operator.md)