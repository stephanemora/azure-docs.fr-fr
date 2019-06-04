---
title: 'Didacticiel : Configurer un environnement Azure Time Series Insights (préversion) | Microsoft Docs'
description: Apprenez à configurer votre environnement dans Azure Time Series Insights (préversion).
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: 77b7b90b63ffebc14498183fc179b9c8ae76a722
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237845"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutoriel : Configurer un environnement Azure Time Series Insights (préversion)

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights (préversion) avec paiement à l’utilisation. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

* Créer un environnement Azure Time Series Insights (préversion)
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements dans Azure Event Hubs
* Exécuter un exemple d’accélérateur de solution pour transmettre des données à l’environnement Azure Time Series Insights (préversion)
* Effectuer une analyse de base des données.
* Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

## <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Dans cette section, vous créez trois appareils simulés qui envoient des données à une instance Azure IoT Hub.

1. Accédez à la [page des accélérateurs de solution Azure IoT](https://www.azureiotsolutions.com/Accelerators). La page affiche plusieurs exemples prédéfinis. Connectez-vous à l’aide de votre compte Azure. Puis sélectionnez **Simulation d'appareil**.

   [![Page des accélérateurs de solution Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Sélectionnez **Essayer maintenant**.

1. Dans la page **Créer une solution de simulation d’appareil**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | --- |
    | **Nom de la solution** | Entrez une valeur unique pour un nouveau groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources. |
    | **Abonnement** | Sélectionnez l’abonnement que vous avez utilisé pour créer votre environnement Time Series Insights. |
    | **Région** | Sélectionnez la région que vous avez utilisée pour créer votre environnement Time Series Insights. |
    | **Déployer des ressources Azure facultatives** | Laissez la case **IoT Hub** cochée. Les appareils simulés utilisent IoT Hub pour se connecter et pour diffuser des données en continu. |
 
    Sélectionnez **Créer la solution**. Attendez 10 à 15 minutes, le temps que votre solution soit déployée.

    [![Page pour créer une solution de simulation d’appareil](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Dans le tableau de bord de l’accélérateur de solution, sélectionnez **Lancer** :

    [![Lancer la solution de simulation d'appareil](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Vous êtes redirigé vers la page **Simulation d’appareil Microsoft Azure IoT**. Dans le coin supérieur droit de la page, sélectionnez **Nouvelle simulation**.

    [![Page Simulation Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Dans le volet **Configuration de la simulation**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | --- |
    | **Nom** | Entrez un nom unique pour le simulateur. |
    | **Description** | Entrez une définition. |
    | **Durée de la simulation** | Définissez ce paramètre sur **Run indefinitely** (Exécuter indéfiniment). |
    | **Modèle de l’appareil** | **Nom** : Entrez **Chiller**. <br />**Quantité** : Entrez **3**. |
    | **IoT Hub cible** | Définissez ce paramètre sur **Use pre-provisioned IoT Hub** (Utiliser le hub IoT préprovisionné). |

    [![Paramètres à définir](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Sélectionnez **Démarrer la simulation**.

    Sur le tableau de bord Simulation d’appareil, notez les informations affichées dans les zones **Appareils actifs** et **Messages par seconde**.

    [![Tableau de bord Simulation Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Répertorier les propriétés de la simulation d'appareil

Avant de créer un environnement Azure Time Series Insights, vous devez obtenir les noms de votre hub IoT, de votre abonnement et du groupe de ressources.

1. Accédez au tableau de bord de l’accélérateur de solution. Connectez-vous à l’aide du même compte d’abonnement Azure. Recherchez la simulation d'appareil que vous avez créée dans la section précédente.

1. Sélectionnez votre simulateur d’appareil, puis choisissez **Lancer**. Dans le volet de l’accélérateur de solution du simulateur de solution de droite, sélectionnez l’option **Portail de gestion Azure**.

    [![Listes de simulateurs](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Notez les noms du hub IoT, de l’abonnement et du groupe de ressources.

    [![Détails du tableau de bord du simulateur d’appareil sur le portail Azure](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Créer un environnement Time Series Insights (préversion) avec paiement à l'utilisation

Cette section explique comment créer un environnement Azure Time Series Insights (préversion) via le [portail Azure](https://portal.azure.com/).

1. Connectez-vous au portail Azure à l'aide de votre compte d'abonnement.

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **Time Series Insights**.

   [![Sélectionnez Internet des objets, puis Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Dans le volet **Créer un environnement Time Series Insights**, dans l’onglet **De base**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | ---|
    | **Nom de l’environnement** | Entrez un nom unique pour l'environnement Azure Time Series Insights (préversion). |
    | **Abonnement** | Entrez l'abonnement pour lequel vous souhaitez créer l'environnement Azure Time Series Insights (préversion). Nous vous recommandons d'utiliser le même abonnement que pour les autres ressources IoT créées par le simulateur d'appareil. |
    | **Groupe de ressources** | Sélectionnez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Azure Time Series Insights (préversion). Un groupe de ressources est un conteneur pour les ressources Azure. Une bonne pratique consiste à utiliser le même groupe de ressources que pour les autres ressources IoT créées par le simulateur d’appareil. |
    | **Lieu** | Choisissez une région de centre de données pour votre environnement Azure Time Series Insights (préversion). Pour éviter des coûts de bande passante et une latence supplémentaires, il est préférable de créer votre environnement Azure Time Series Insights (préversion) dans la même région que vos autres ressources IoT. |
    | **Niveau** |  Sélectionnez **PAYG** (*Paiement à l'utilisation*). Il s’agit de la référence SKU du produit Azure Time Series Insights (préversion). |
    | **ID de propriété** | Entrez une valeur qui identifie de façon unique votre instance Time Series. La valeur que vous entrez dans la zone **ID de propriété** est immuable. Vous ne pouvez pas la changer ultérieurement. Pour ce tutoriel, utilisez l’identificateur **iothub-connection-device-id**. Pour en savoir plus sur l’ID Time Series, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md). |
    | **Nom du compte de stockage** | Entrez un nom global unique pour le compte de stockage à créer. |
   
   Sélectionnez **Suivant : Source de l'événement**.

   [![Volet pour la création d’un environnement Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Dans l’onglet **Source de l'événement**, définissez les paramètres suivants :

   | Paramètre | Action |
   | --- | --- |
   | **Créer une source d’événement ?** | Sélectionnez **Oui**.|
   | **Nom** | Entrez une valeur unique pour le nom de la source d’événement. |
   | **Type de source** | Sélectionnez **IoT Hub**. |
   | **Sélectionner un hub** | Choisissez **Sélection d’un élément existant**. |
   | **Abonnement** | Sélectionnez l’abonnement que vous avez utilisé pour le simulateur d’appareil. |
   | **Nom du hub IoT** | Entrez le nom du hub IoT que vous avez créé pour le simulateur d’appareil. |
   | **Stratégie d'accès IoT Hub** | sélectionnez **iothubowner**. |
   | **Groupe de consommateurs IoT Hub** | Sélectionnez **Nouveau**, entrez un nom unique, puis sélectionnez **Ajouter**. Le groupe de consommateurs doit représenter une valeur unique dans Azure Time Series Insights (préversion). |
   | **Propriété timestamp** | Cette valeur utilisée pour identifier la propriété d'**horodatage** dans vos données de télémétrie entrantes. Pour ce didacticiel, laissez cette zone vide. Ce simulateur utilise le timestamp entrant du hub IoT auquel Time Series Insights est associé par défaut. |

   Sélectionnez **Revoir + créer**.

   [![Configurer une source d’événements](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Dans l’onglet **Vérifier + créer**, passez en revue vos sélections, puis choisissez **Créer**.

    [![Page Vérifier + Créer, avec le bouton Créer](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Vous pouvez voir l'état de votre déploiement :

    [![Notification de la fin du déploiement](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Si le locataire vous appartient, vous avez accès à votre environnement Azure Time Series Insights (préversion). Pour être sûr d'y avoir accès :

   1. Recherchez votre groupe de ressources et sélectionnez votre environnement Azure Time Series Insights (préversion) :

      [![Environnement sélectionné](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Dans la page Azure Time Series Insights (préversion), sélectionnez **Stratégies d’accès aux données** :

      [![Stratégies d’accès aux données](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Vérifiez que vos informations d'identification sont répertoriées :

      [![Informations d’identification listées](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Si vos informations d’identification ne sont pas listées, vous devez vous octroyer l’autorisation d’accéder à l’environnement. Pour en savoir plus sur la définition des autorisations, consultez [Octroyer l’accès aux données](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analyser les données dans votre environnement

Dans cette section, vous procédez à l'analyse de base de vos données de séries chronologiques à l'aide de [l'explorateur Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md).

1. Accédez à l’explorateur Azure Time Series Insights (préversion) en sélectionnant l’URL dans la page des ressources sur le [portail Azure](https://portal.azure.com/).

    [![URL de l’explorateur Time Series Insights (préversion)](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Dans l’explorateur, sélectionnez le nœud **Instances non apparentées** pour afficher toutes les instances Azure Time Series Insights (préversion) de l’environnement.

    [![Liste des instances non apparentées](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Sélectionnez la première instance de série chronologique. Ensuite, sélectionnez **Show pressure** (Afficher la pression).

    [![Instance de série chronologique sélectionnée avec la commande de menu pour afficher la pression moyenne](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Un graphique des séries chronologiques apparaît. Définissez l’**intervalle** sur **15s**.

    [![Graphique de séries chronologiques](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Répétez l’étape 3 pour les deux autres instances de séries chronologiques. Vous pouvez afficher toutes les instances de séries chronologiques, comme illustré dans ce graphique :

    [![Graphique affichant toutes les séries chronologiques](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Dans la case d’option **Intervalle de temps**, changez l’intervalle de temps pour afficher les tendances des séries chronologiques au cours de la dernière heure :

    [![Définissez la plage horaire à une certaine heure](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Définir et appliquer un modèle

Dans cette section, vous appliquez un modèle pour structurer vos données. Pour finaliser le modèle, vous définissez les types, les hiérarchies et les instances. Pour en savoir plus sur la modélisation des données, consultez [Modèle de série chronologique](./time-series-insights-update-tsm.md).

1. Dans l'explorateur, sélectionnez l'onglet **Modèle** :

   [![Onglet Modèle dans l’Explorateur](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Sélectionnez **Ajouter** pour ajouter un type :

   [![Bouton Ajouter pour les types](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Définissez ensuite trois variables pour le type : la *pression*, la *température* et l’*humidité*. Dans le volet **Ajouter un type**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | ---|
    | **Nom** | Entrez **Chiller**. |
    | **Description** | Entrez **This is a type definition of Chiller** (C’est une définition du type de refroidisseur). |

   * Pour définir la *pression*, sous **Variables**, définissez les paramètres suivants :

     | Paramètre | Action |
     | --- | ---|
     | **Nom** | Entrez **Avg Pressure** (Pression moyenne). |
     | **Valeur** | Sélectionnez **Pression (Double)** . Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
     | **Opération d'agrégation** | Sélectionnez **AVG**. |

      [![Sélections pour définir la pression](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Pour ajouter la variable suivante, sélectionnez **Ajouter une variable**.

   * Définissez la *température* :

     | Paramètre | Action |
     | --- | ---|
     | **Nom** | Entrez **Avg Temperature** (Température moyenne). |
     | **Valeur** | Sélectionnez **Température (Double)** . Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
     | **Opération d'agrégation** | Sélectionnez **AVG**.|

      [![Sélections pour définir la température](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Pour ajouter la variable suivante, sélectionnez **Ajouter une variable**.

   * Définissez l’*humidité* :

      | | |
      | --- | ---|
      | **Nom** | Entrer **Max Humidity** (Humidité maximale) |
      | **Valeur** | Sélectionnez **Humidité (Double)** . Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
      | **Opération d'agrégation** | Sélectionnez **MAX**.|

      [![Sélections pour définir la température](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Sélectionnez **Créer**.

    Vous pouvez voir le type que vous avez ajouté :

    [![Informations sur le type ajouté](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. L’étape suivante consiste à ajouter une hiérarchie. Sous **Modifier la hiérarchie**, sélectionnez **Ajouter** :

    [![Onglet Hiérarchies avec le bouton Ajouter](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Dans le volet **Modifier la hiérarchie**, définissez les paramètres suivants :

   | Paramètre | Action |
   | --- | ---|
   | **Nom** | Entrez **Location Hierarchy** (Hiérarchie d’emplacement). |
   | **Niveau 1** | Entrez **Country** (Pays). |
   | **Niveau 2** | Entrez **City** (Ville). |
   | **Niveau 3** | Entrez **Building** (Immeuble). |

   Sélectionnez **Enregistrer**.

    [![Champs de la hiérarchie avec le bouton Créer](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Voici la hiérarchie que vous avez créée :

    [![Informations sur la hiérarchie](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Sélectionnez **Instances**. Sélectionnez la première instance, puis choisissez **Modifier** :

    [![Sélection du bouton Modifier pour une instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Dans le volet **Modifier les instances**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | --- |
    | **Type** | Sélectionnez **Chiller**. |
    | **Description** | Entrez **Instance for Chiller-01.1** (Instance pour le refroidisseur-01.1). |
    | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
    | **Pays** | Entrez **USA**. |
    | **Ville** | Entrez **Seattle**. |
    | **Immeuble** | Entrez **Space Needle**. |

    [![Champs de l’instance avec le bouton Enregistrer](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Sélectionnez **Enregistrer**.

1. Répétez l’étape précédente pour les autres capteurs. Mettez à jour les valeurs suivantes :

   * Pour le condensateur 01.2 :

     | Paramètre | Action |
     | --- | --- |
     | **Type** | Sélectionnez **Chiller**. |
     | **Description** | Entrez **Instance for Chiller-01.2** (Instance pour le refroidisseur-01.2). |
     | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
     | **Pays** | Entrez **USA**. |
     | **Ville** | Entrez **Seattle**. |
     | **Immeuble** | Entrez **Pacific Science Center**. |

   * Pour le condensateur 01.3 :

     | Paramètre | Action |
     | --- | --- |
     | **Type** | Sélectionnez **Chiller**. |
     | **Description** | Entrez **Instance for Chiller-01.3** (Instance pour le refroidisseur-01.3). |
     | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
     | **Pays** | Entrez **USA**. |
     | **Ville** | Entrez **New York**. |
     | **Immeuble** | Entrez **Empire State Building**. |

1. Sélectionnez l’onglet **Analyser**, puis actualisez la page. Sous **Location Hierarchy** (Hiérarchie d’emplacement), développez tous les niveaux de hiérarchie pour afficher les instances de séries chronologiques :

   [![Onglet Analyser](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Pour explorer les instances de séries chronologiques de la dernière heure, changez l’option **Durées courtes** à **Dernière heure** :

    [![Option Durées courtes, avec Dernière heure sélectionnée](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Sous **Pacific Science Center**, sélectionnez l’instance de série chronologique, puis choisissez **Show Max Humidity** (Afficher l’humidité maximale).

    [![Instance de série chronologique sélectionnée avec l’option de menu Show Max Humidity (Afficher l’humidité maximale)](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. La série chronologique correspondant à **Show Max Humidity** (Afficher l’humidité maximale) et à un intervalle de **1 minute** s’ouvre. Pour filtrer une plage, sélectionnez une région. Pour analyser les événements de la plage de temps, cliquez avec le bouton droit sur le graphique, puis sélectionnez **Zoom** :

   [![Plage sélectionnée avec la commande Zoom d’un menu contextuel](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Pour afficher les détails d’un événement, sélectionnez une région, puis cliquez avec le bouton droit sur le graphique :

   [![Liste détaillée des événements](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :  

> [!div class="checklist"]
> * Créer et utiliser un accélérateur de simulation d'appareil
> * Créer un environnement Azure Time Series Insights (préversion) avec paiement à l'utilisation
> * Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements
> * Exécuter un exemple d’accélérateur de solution pour transmettre des données à l’environnement Azure Time Series Insights (préversion)
> * Effectuer une analyse de base des données
> * Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

Maintenant que vous savez créer votre propre environnement Azure Time Series Insights (préversion), penchez-vous sur les concepts clés d'Azure Time Series Insights.

Découvrez-en plus sur la configuration du stockage d’Azure Time Series Insights :

> [!div class="nextstepaction"]
> [Azure Time Series Insights (préversion) - Stockage et entrée](./time-series-insights-update-storage-ingress.md)

En savoir plus sur les modèles Time Series :

> [!div class="nextstepaction"]
> [Azure Time Series Insights (préversion) - Modélisation de données](./time-series-insights-update-tsm.md)