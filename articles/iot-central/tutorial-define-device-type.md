---
title: Définir un nouveau type d’appareil dans Azure IoT Central | Microsoft Docs
description: Ce didacticiel vous indique comment définir un nouveau type d’appareil dans votre application Azure IoT Central en votre qualité de générateur. Vous définissez la télémétrie, l’état, les propriétés et les paramètres pour votre type d’appareil.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7121c83aea75f3b23820a7b0504fa704ec9f3016
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984303"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Didacticiel : définir un nouveau type d’appareil dans votre application Azure IoT Central

Ce didacticiel vous indique comment utiliser un modèle d’appareil pour définir un nouveau type d’appareil dans votre application Microsoft Azure IoT Central en votre qualité de générateur. Un modèle d’appareil définit la télémétrie, l’état, les propriétés et les paramètres pour votre type d’appareil.

Pour vous permettre de tester votre application avant de connecter un appareil réel, Azure IoT Central génère un appareil simulé à partir du modèle d’appareil lorsque vous créez ce dernier.

Dans ce didacticiel, vous allez créer le modèle d’appareil **Climatiseur connecté**. Un climatiseur connecté :

* envoie des données de télémétrie comme la température et l’humidité ;
* signale son état, par exemple allumé ou éteint ;
* présente diverses propriétés, telles que la version du microprogramme et le numéro de série ;
* comporte divers paramètres, comme la température et la vitesse du ventilateur cibles.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’appareil
> * Ajouter des mesures de télémétrie à votre appareil
> * Visualiser la télémétrie simulée
> * Définir une mesure d’événement
> * Visualiser des événements simulés
> * Définir une mesure d’état
> * Visualiser un état simulé
> * Utiliser les propriétés de l’appareil
> * Utiliser les paramètres de l’appareil
> * Utiliser des commandes

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer d’une application Azure IoT Central. Si vous avez suivi le guide de démarrage rapide [Créer votre application Azure IoT Central](quick-deploy-iot-central.md), vous pouvez réutiliser l’application que vous avez créée dans le cadre de ce guide. Dans le cas contraire, créez une application Azure IoT Central vide en procédant comme suit :

1. Accédez à la page [Application Manager](https://aka.ms/iotcentral) (Gestionnaire d’application) d’Azure IoT Central.

2. Entrez l’adresse e-mail et le mot de passe que vous utilisez pour accéder à votre abonnement Azure :

   ![Saisie de votre compte d’entreprise](./media/tutorial-define-device-type/sign-in.png)

3. Pour commencer à créer une application Azure IoT Central, choisissez **Nouvelle application** :

    ![Page du Gestionnaire d’applications d’Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Pour créer une application Azure IoT Central :

    * Choisissez un nom d’application convivial, tel que **Climatiseurs Contoso**. Azure IoT Central génère un préfixe d’URL unique pour vous. Vous pouvez remplacer ce préfixe d’URL par une chaîne plus facile à mémoriser.
    
    * Choisissez un service Azure Active Directory et un abonnement Azure à utiliser. Pour plus d’informations sur les répertoires et les abonnements, consultez l’article [Créer votre application Azure IoT Central](howto-create-application.md).
    
    * Utilisez un groupe de ressources existant ou créez un groupe de ressources en lui attribuant le nom de votre choix. Par exemple, **contoso-rg**.
    
    * Choisissez la région correspondant à votre emplacement géographique.
    
    * Choisissez le modèle d’application **Custom Application** (Application personnalisée).
    
    * Choisissez le plan de paiement **Free 30 Day Trial Application** (Version d’évaluation gratuite de 30 jours).
    
    * Cliquez sur **Créer**.

    ![Page de création d’une application Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

Pour plus d’informations, consultez l’article [Créer votre application Azure IoT Central](howto-create-application.md).

## <a name="create-a-new-custom-device-template"></a>Créer un modèle d’appareil personnalisé

En tant que générateur, vous pouvez créer et modifier les modèles d’appareil dans votre application. Lorsque vous créez un modèle d’appareil, Azure IoT Central génère un appareil simulé à partir du modèle. L’appareil simulé génère la télémétrie qui vous permet de tester le comportement de votre application avant de connecter un appareil physique.

Pour ajouter un nouveau modèle d’appareil à votre application, vous devez accéder à la page **Application Builder** (Générateur d’applications). Pour ce faire, choisissez **Application Builder** (Générateur d’applications) dans le menu de navigation gauche.

![Page Générateur d’applications](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Ajouter un appareil et définir la télémétrie

La procédure ci-après vous explique comment créer un modèle d’appareil **Climatiseur connecté** pour les appareils qui envoient des données de télémétrie de température à votre application :

1. Sur la page **Application Builder** (Générateur d’applications), choisissez **Create Device Template** (Créer un modèle d’appareil) :

    ![Option de création d’un modèle d’appareil sur la page du générateur d’applications](./media/tutorial-define-device-type/builderhomedevices.png)

2. Sur la page **Device Templates** (Modèles d’appareil), choisissez **Custom** (Personnalisé). Un modèle d’appareil **Custom** (Personnalisé) vous permet de définir l’ensemble des caractéristiques et des comportements de votre climatiseur connecté :

    ![Appareils](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Sur la page **New Device Template** (Nouveau modèle d’appareil), entrez **Climatiseur connecté** comme nom pour votre appareil, puis choisissez **Create** (Créer). Vous pouvez également charger une image de votre appareil qui est visible par les opérateurs dans Device Explorer :

    ![Appareil personnalisé](./media/tutorial-define-device-type/createcustomdevice.png)

4. Dans le modèle d’appareil **Climatiseur connecté**, vérifiez que vous vous trouvez sur la page **Measurements** (Mesures) dans laquelle vous définissez la télémétrie. Chaque modèle d’appareil que vous définissez comporte différentes pages vous permettant de :

    * spécifier les mesures, telles que les données de télémétrie, d’événement et d’état, qui sont envoyées par l’appareil ;
    
    * définir les paramètres permettant de contrôler l’appareil ;
    
    * définir les propriétés permettant d’enregistrer des informations sur l’appareil ;
    
    * définir les règles associées à l’appareil ;
    
    * personnaliser le tableau de bord de l’appareil pour vos opérateurs.

    ![Mesures de climatiseur](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Pour renommer l’appareil ou le modèle d’appareil, cliquez sur le texte en haut de la page.

5. Pour ajouter la mesure de télémétrie de température, choisissez **New Measurement** (Nouvelle mesure). Puis choisissez **Telemetry** (Télémétrie) comme type de mesure :

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

    Vous pouvez également choisir une couleur pour l’affichage de la télémétrie. Pour enregistrer la définition de la télémétrie, choisissez **Enregistrer** :

    ![Configuration de la simulation de température](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Après un court instant, la page **Measurements** (Mesures) affiche un graphique de la télémétrie de température de votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou l’agrégation, ou pour modifier la définition de télémétrie :

    ![Visualisation de la simulation de température](./media/tutorial-define-device-type/viewsimulation.png)

8. Vous pouvez également personnaliser le graphique à l’aide des contrôles **Ligne**, **Empilé** et **Modifier l’intervalle de temps** :

    ![Personnalisation du graphique](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Définir une mesure d’événement

La mesure de type Event (Événement) vous permet de définir des données ponctuelles qui sont envoyées par l’appareil pour signaler une situation significative, telle qu’une erreur ou une défaillance de composant. De même que les mesures de télémétrie, Azure IoT Central peut simuler des événements d’appareil pour vous permettre de tester le comportement de votre application avant de connecter un appareil physique. Vous définissez des mesures d’événement pour votre type d’appareil dans la vue **Measurements** (Mesures).

1. Pour ajouter la mesure d’événement **Erreur du moteur du ventilateur**, choisissez **New Measurement** (Nouvelle mesure). Puis choisissez **Event** (Événement) comme type de mesure :

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
    | Severity             | Error             |

    Pour enregistrer la définition de l’événement, choisissez **Enregistrer** :

    ![Configuration de la mesure d’événement](./media/tutorial-define-device-type/eventconfiguration.png)

3. Après un court instant, la page **Measurements** (Mesures) affiche un graphique des événements générés aléatoirement par votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou pour modifier la définition d’événement :

    ![Visualisation de la simulation d’événement](./media/tutorial-define-device-type/eventview.png)

1. Pour voir des détails supplémentaires sur l’événement, cliquez sur l’événement dans le graphique :

    ![Visualisation des détails de l’événement](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Définir une mesure d’état

Vous pouvez utiliser la mesure État pour définir et visualiser l’état de l’appareil ou de son composant sur une période spécifique. De même que les mesures de télémétrie, Azure IoT Central peut simuler un état d’appareil pour vous permettre de tester le comportement de votre application avant de connecter un appareil physique. Vous définissez des mesures d’état pour votre type d’appareil dans la vue **Measurements** (Mesures).

1. Pour ajouter la mesure **Mode du ventilateur**, choisissez **New Measurement** (Nouvelle mesure). Puis choisissez **État** comme type de mesure :

    ![Mesures d’état de climatiseur connecté](./media/tutorial-define-device-type/statenew.png)

2. Chaque type d’état que vous définissez pour un modèle d’appareil inclut des [options de configuration](howto-set-up-template.md) telles que :

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

    Pour enregistrer la définition de l’état, choisissez **Enregistrer** :

    ![Configuration de la mesure d’état](./media/tutorial-define-device-type/stateconfiguration.png)

3. Après un court instant, la page **Measurements** (Mesures) affiche un graphique des états générés aléatoirement par votre climatiseur connecté simulé. Utilisez les contrôles pour gérer la visibilité ou pour modifier la définition d’état :

    ![Visualisation de la simulation d’état](./media/tutorial-define-device-type/stateview.png)

4. Si l’appareil envoie un nombre de points de données excessif sur une courte période, la mesure d’état s’affiche avec un visuel différent, comme illustré ci-dessous. Si vous cliquez sur le graphique, tous les points de données de cette période s’affichent dans l’ordre chronologique. Vous pouvez également réduire l’intervalle de temps pour voir la mesure tracée sur le graphique.

    ![Visualisation des détails de l’état](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Paramètres, propriétés et commandes

Les propriétés, les commandes, les propriétés d’appareil et les paramètres constituent des valeurs distinctes qui sont définies dans un modèle d’appareil et qui sont associées à chaque appareil :

* Les _paramètres_ vous permettent d’envoyer des données de configuration à un appareil à partir de votre application. Par exemple, un opérateur peut utiliser un paramètre pour remplacer l’intervalle de télémétrie de deux secondes par un intervalle de cinq secondes. Lorsqu’un opérateur modifie un paramètre, ce dernier est marqué comme étant en attente dans l’interface utilisateur jusqu’à ce que l’appareil confirme la prise en compte de la modification du paramètre.

* Les _propriétés_ vous permettent d’enregistrer des informations concernant votre appareil dans votre application. Par exemple, vous pouvez utiliser les propriétés pour enregistrer le numéro de série d’un appareil ou le numéro de téléphone du constructeur d’un appareil. Les propriétés sont stockées dans l’application et ne se synchronisent pas avec l’appareil. Un opérateur peut assigner des valeurs aux propriétés.

* Les _propriétés d’appareil_ vous permettent d’autoriser un appareil à envoyer des valeurs de propriété à votre application. Ces propriétés sont uniquement modifiables par l’appareil. Les propriétés d’appareil sont en lecture seule pour un opérateur.

* Vous utilisez des _commandes_ pour gérer à distance votre appareil à partir de votre application. Vous pouvez exécuter directement des commandes sur l’appareil à partir du cloud pour contrôler les appareils. Par exemple, un opérateur peut exécuter des commandes telles que le redémarrage, pour redémarrer l’appareil immédiatement.

## <a name="use-settings"></a>Utiliser les paramètres

Vous utilisez les *paramètres* pour autoriser un opérateur à envoyer des données de configuration à un appareil. Dans cette section, vous allez ajouter un paramètre à votre modèle d’appareil **Climatiseur connecté** pour permettre à un opérateur de définir la température cible du climatiseur connecté.

1. Accédez à la page **Settings** (Paramètres) de votre modèle d’appareil **Climatiseur connecté** :

    ![Préparation de l’ajout d’un paramètre](./media/tutorial-define-device-type/deviceaddsetting.png)

    Vous pouvez créer différents types de paramètres, tels que des valeurs numériques ou du texte.

2. Choisissez **Number** (Nombre) pour ajouter un paramètre de type valeur numérique à votre appareil.

3. Pour configurer votre paramètre **Température réglée**, utilisez les informations du tableau suivant :

    | Champ                | Valeur           |
    | -------------------- | -----------     |
    | Nom d’affichage         | Température définie |
    | Nom du champ           | setTemperature  |
    | Unité de mesure  | F               |
    | Nombre de décimales       | 1               |
    | Valeur minimale        | 20              |
    | Valeur maximale        | 200             |
    | Valeur initiale        | 80              |
    | Description          | Température cible du climatiseur |

    Puis choisissez **Enregistrer** :

    ![Configuration du paramètre Température réglée](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Lorsque l’appareil accuse réception d’une modification de paramètre, le paramètre prend l’état **synchronisé**.

4. Vous pouvez personnaliser la disposition de la page **Settings** (Paramètres) en déplaçant et en redimensionnant les vignettes de paramètres :

    ![Personnalisation de la disposition des paramètres](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties--device-properties"></a>Utiliser des propriétés/propriétés d’appareil

Les *propriétés* vous permettent de stocker des informations concernant votre appareil dans l’application. Dans cette section, vous allez ajouter des propriétés cloud à votre modèle d’appareil **Climatiseur connecté** pour stocker l’emplacement de l’appareil et la dernière date de service. Notez que ces deux éléments sont des propriétés modifiables de l’appareil. Il existe également des propriétés en lecture seule consignées par l’appareil et qui ne sont pas modifiables, par exemple le numéro de série de l’appareil et la version du microprogramme.
 
1. Accédez à la page **Propriétés** de votre modèle d’appareil **Climatiseur connecté** :

    ![Préparation de l’ajout d’une propriété](./media/tutorial-define-device-type/deviceaddproperty.png)

    Vous pouvez créer différents types de propriétés d’appareil, comme des nombres ou du texte. Pour ajouter une propriété d’emplacement à votre modèle d’appareil, choisissez **Emplacement**.

2. Pour configurer votre propriété d’emplacement, utilisez les informations du tableau suivant :

    | Champ                | Valeur                |
    | -------------------- | -------------------- |
    | Nom d’affichage         | Lieu             |
    | Nom du champ           | location             |
    | Valeur initiale        | Seattle, WA          |
    | Description          | Emplacement de l’appareil      |

    Laissez les autres champs définis sur leur valeur par défaut.

    ![Configuration des propriétés de l’appareil](./media/tutorial-define-device-type/configureproperties.png)

    Choisissez **Enregistrer**.

3. Pour ajouter une propriété de dernière date de service à votre modèle d’appareil, choisissez **Date**.

4. Pour configurer votre propriété de dernière date de service, utilisez les informations du tableau suivant :

    | Champ                | Valeur                   |
    | -------------------- | ----------------------- |
    | Nom d’affichage         | Dernière date de service       |
    | Nom du champ           | serviceDate             |
    | Valeur initiale        | 1/1/2018                |
    | Description          | Dernière utilisation           |

    ![Configuration des propriétés de l’appareil](./media/tutorial-define-device-type/configureproperties2.png)

    Choisissez **Enregistrer**.

5. Vous pouvez personnaliser la disposition de la page **Propriétés** en déplaçant et en redimensionnant les vignettes de propriétés :

    ![Personnalisation de la mise en page des propriétés](./media/tutorial-define-device-type/propertieslayout.png)


## <a name="use-commands"></a>Utiliser des commandes

Vous utilisez les _commandes_ pour permettre à un opérateur d’exécuter des commandes directement sur l’appareil. Dans cette section, vous ajoutez une commande à votre modèle d’appareil **Climatiseur connecté** afin d’activer un opérateur, lequel renvoie un message à l’écran du climatiseur connecté (fonctionne avec l’exemple de code MxChip).

1. Accédez à la page **Commandes** de votre modèle d’appareil **Climatiseur connecté** :

    ![Préparation de l’ajout d’un paramètre](media/tutorial-define-device-type/commandsecho.png)

    Vous pouvez créer des commandes de différents types, selon vos besoins. 

1. Cliquez sur **Nouvelle commande** pour ajouter une commande à votre appareil.

1. Pour configurer votre nouvelle commande, utilisez les informations du tableau suivant :

    | Champ                | Valeur           |
    | -------------------- | -----------     |
    | Nom d’affichage         | Commande echo    |
    | Nom du champ           | echo            |
    | Délai d’expiration par défaut      | 30              |
    | Type d’affichage         | texte            |
    | Description          | Commande d’appareil  |  

Vous pouvez ajouter des entrées supplémentaires à la commande en cliquant sur **+** pour les entrées.

2. Choisissez **Enregistrer**.

3. Vous pouvez personnaliser la disposition de la page **Commandes** en déplaçant et en redimensionnant les vignettes de commandes :

    ![Personnalisation de la disposition des paramètres](media/tutorial-define-device-type/commandstileresize.png)

## <a name="view-your-simulated-device"></a>Visualiser votre appareil simulé

Après avoir défini votre modèle d’appareil **Climatiseur connecté**, vous pouvez personnaliser son **Dashboard** (Tableau de bord) pour y inclure les mesures, les paramètres et les propriétés que vous avez définis. Vous pouvez visualiser un aperçu du tableau de bord en tant qu’opérateur :

1. Accédez à la page **Dashboard** (Tableau de bord) de votre modèle d’appareil **Climatiseur connecté** :

    ![Tableau de bord de climatiseur connecté](./media/tutorial-define-device-type/aircondashboards.png)

2. Choisissez **Graphique en courbes** pour ajouter ce composant sur le **Dashboard** (Tableau de bord) :

    ![Composants de tableau de bord](./media/tutorial-define-device-type/dashboardcomponents1.png)

3. Configurez le composant **Graphique en courbes** en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Température |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures | Température (choisissez **Visibilité** en regard de **Température**) |

    ![Paramètres de graphique en courbes](./media/tutorial-define-device-type/linechartsettings.png)

    Puis choisissez **Enregistrer**.

4. Configurez le composant **Event Chart** (Graphique d’événements) en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Événements |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures | Erreur du moteur du ventilateur (choisissez **Visibilité** en regard de **Erreur du moteur du ventilateur**) |

    ![Paramètres de graphique en courbes](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Puis choisissez **Enregistrer**.

5. Configurez le composant **State Chart** (Graphique d’états) en utilisant les informations du tableau suivant :

    | Paramètre      | Valeur       |
    | ------------ | ----------- |
    | Intitulé        | Mode du ventilateur |
    | Intervalle de temps   | 30 dernières minutes |
    | Mesures | Mode du ventilateur (choisissez **Visibilité** en regard de **Mode du ventilateur**) |

    ![Paramètres de graphique en courbes](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Puis choisissez **Enregistrer**.

6. Pour ajouter le paramètre de température réglée au tableau de bord, choisissez **Settings and Properties** (Paramètres et propriétés) :

    ![Composants de tableau de bord](./media/tutorial-define-device-type/dashboardcomponents4.png)

7. Configurez le composant **Settings and Properties** (Paramètres et propriétés) en utilisant les informations du tableau suivant :

    | Paramètre                 | Valeur         |
    | ----------------------- | ------------- |
    | Intitulé                   | Température cible réglée |
    | Settings and Properties (Paramètres et propriétés) | Température réglée |

    ![Paramètres de la propriété de numéro de série](./media/tutorial-define-device-type/propertysettings3.png)

    Puis choisissez **Enregistrer**.

8. Pour ajouter le numéro de série d’appareil au tableau de bord, choisissez **Settings and Properties** (Paramètres et propriétés) :

    ![Composants de tableau de bord](./media/tutorial-define-device-type/dashboardcomponents3.png)

9. Configurez le composant **Settings and Properties** (Paramètres et propriétés) en utilisant les informations du tableau suivant :

    | Paramètre                 | Valeur         |
    | ----------------------- | ------------- |
    | Intitulé                   | Numéro de série |
    | Settings and Properties (Paramètres et propriétés) | Numéro de série |

    ![Paramètres de la propriété de numéro de série](./media/tutorial-define-device-type/propertysettings3.png)

    Puis choisissez **Enregistrer**.

10. Pour ajouter la version de microprogramme d’appareil au tableau de bord, choisissez **Settings and Properties** (Paramètres et propriétés) :

    ![Composants de tableau de bord](./media/tutorial-define-device-type/dashboardcomponents4.png)

11. Configurez le composant **Settings and Properties** (Paramètres et propriétés) en utilisant les informations du tableau suivant :

    | Paramètre                 | Valeur            |
    | ----------------------- | ---------------- |
    | Intitulé                   | Version du microprogramme |
    | Settings and Properties (Paramètres et propriétés) | Version du microprogramme |

    ![Paramètres de la propriété de numéro de série](./media/tutorial-define-device-type/propertysettings3.png)

    Puis choisissez **Enregistrer**.

12. Pour visualiser le tableau de bord en tant qu’opérateur, désactivez le **mode Conception** dans la partie supérieure droite de la page.

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
> * Utiliser les propriétés de l’appareil
> * Utiliser les paramètres de l’appareil

Maintenant que vous avez défini un modèle d’appareil dans votre application Azure IoT Central, voici les étapes suivantes suggérées :

* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md)
* [Personnaliser la vue de l’opérateur](tutorial-customize-operator.md)
