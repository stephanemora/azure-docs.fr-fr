---
title: 'Didacticiel : Configurer un environnement Azure Time Series Insights (préversion) | Microsoft Docs'
description: Apprenez à configurer votre environnement dans Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 1b09c0e31b217d7d67f936aefe9045d190241389
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633111"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutoriel : Configurer un environnement Azure Time Series Insights (préversion)

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights (préversion) avec paiement à l’utilisation. Ce tutoriel vous montre comment effectuer les opérations suivantes :

* Créer un environnement Azure Time Series Insights (préversion)
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements dans Azure Event Hubs
* Exécuter un exemple d’accélérateur de solution pour transmettre des données à l’environnement Azure Time Series Insights (préversion)
* Effectuer une analyse de base des données.
* Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

# <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Dans cette section, vous créez trois appareils simulés qui enverront des données à un hub IoT.

1. Accédez à la [page des accélérateurs de solution Azure IoT](https://www.azureiotsolutions.com/Accelerators). La page affiche plusieurs exemples prédéfinis. Connectez-vous à l’aide de votre compte Azure. Puis sélectionnez **Simulation d'appareil**.

   ![Page des accélérateurs de solution Azure IoT][1]

   Sélectionnez **Essayer maintenant**.

1. Entrez les paramètres requis sur la page **Créer une solution de simulation d’appareil** :

   | Paramètre | Description |
   | --- | --- |
   | **Nom de la solution** |    Entrez une valeur unique pour la création d’un groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources. |
   | **Abonnement** | Spécifiez le même abonnement que celui utilisé pour la création de votre environnement Time Series Insights. |
   | **Région** |   Spécifiez la même région que celle utilisée pour la création de votre environnement Time Series Insights. |
   | **Déployer des ressources Azure facultatives**    | Ne désélectionnez pas l’option IoT Hub, car les appareils simulés utiliseront cet hub pour se connecter et effectuer le streaming des données. |

   Ensuite, sélectionnez **Créer la solution**. Attendez 10 à 15 minutes, le temps que votre solution soit déployée.

   ![Page pour créer une solution de simulation d’appareil][2]

1. Dans le tableau de bord de l’accélérateur de solution, sélectionnez le bouton **Lancer** :

   ![Lancer la solution de simulation d'appareil][3]

1. Vous êtes redirigé vers la page **Simulation d’appareil Microsoft Azure IoT**. Sélectionnez **+ Nouvelle simulation** dans le coin supérieur droit de la page.

   ![Page Simulation Azure IoT][4]

1.  Renseignez les paramètres requis comme suit :

    ![Paramètres à renseigner][5]

    |||
    | --- | --- |
    | **Nom** | Entrez un nom unique pour le simulateur. |
    | **Description** | Entrez une définition. |
    | **Durée de la simulation** | Définissez ce paramètre sur **Run indefinitely** (Exécuter indéfiniment). |
    | **Modèle de l’appareil** | **Nom** : Entrez **Chiller**. </br>**Quantité** : Entrez **3**. |
    | **IoT Hub cible** | Définissez ce paramètre sur **Use pre-provisioned IoT Hub** (Utiliser le hub IoT préprovisionné). |

    Ensuite, sélectionnez **Démarrer la simulation**.

1. Dans le tableau de bord de simulation d’appareil, consultez **Appareils actifs** et **Messages par seconde**.

    ![Tableau de bord Simulation Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Répertorier les propriétés de la simulation d'appareil

Avant de créer un environnement Azure Time Series Insights, vous devez obtenir les noms de votre hub IoT, de votre abonnement et de votre groupe de ressources.

1. Accédez au tableau de bord des accélérateurs de solution et connectez-vous à l’aide du même compte d’abonnement Azure. Recherchez la simulation d'appareil que vous avez créée aux étapes précédentes.

1. Sélectionnez votre simulateur d’appareil et sélectionnez **Lancer**. Sélectionnez le lien **Portail de gestion Azure** situé à droite.

    ![Listes de simulateurs][7]

1. Notez les noms du hub IoT, de l’abonnement et du groupe de ressources.

    ![Portail Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Créer un environnement Time Series Insights (préversion) avec paiement à l'utilisation

Cette section explique comment créer un environnement Azure Time Series Insights (préversion) via le [portail Azure](https://portal.azure.com/).

1. Connectez-vous au portail Azure à l'aide de votre compte d'abonnement.

1. Sélectionnez **Créer une ressource**.

1. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**.

   ![Sélectionnez Internet des objets, puis Time Series Insights][9]

1. Renseignez les champs de la page de la façon suivante :

   | | |
   | --- | ---|
   | **Nom de l’environnement** | Choisissez un nom unique pour l'environnement Azure Time Series Insights (préversion). |
   | **Abonnement** | Entrez l'abonnement pour lequel vous souhaitez créer l'environnement Azure Time Series Insights (préversion). Une bonne pratique est d’utiliser le même abonnement que pour les autres ressources IoT créées par le simulateur d’appareil. |
   | **Groupe de ressources** | Un groupe de ressources est un conteneur pour les ressources Azure. Choisissez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Azure Time Series Insights (préversion). Une bonne pratique est d’utiliser le même groupe de ressources que pour les autres ressources IoT créées par le simulateur d’appareil. |
   | **Lieu** | Choisissez une région de centre de données pour votre environnement Azure Time Series Insights (préversion). Pour éviter des coûts de bande passante et une latence supplémentaires, il est préférable de maintenir l'environnement Azure Time Series Insights (préversion) dans la même région que les autres ressources IoT. |
   | **Niveau** |  Sélectionnez **PAYG**, qui signifie « paiement à l’utilisation ». Il s’agit de la référence SKU du produit Azure Time Series Insights (préversion). |
   | **ID de propriété** | Entrez un identificateur qui identifie de façon unique votre série chronologique. Notez que ce champ est immuable et qu’il ne pourra pas être modifié ultérieurement. Pour ce tutoriel, utilisez l’identificateur **iothub-connection-device-id**. Pour en savoir plus sur l’ID Time Series, consultez [Choisir un ID Time Series](./time-series-insights-update-how-to-id.md). |
   | **Nom du compte de stockage** | Entrez un nom global unique pour le compte de stockage à créer. |

   Ensuite, sélectionnez **Suivant : Source de l'événement**.

   ![Page pour la création d’un environnement Time Series Insights][10]

1. Dans la page de la source de l’événement, renseignez les champs comme suit :

   | | |
   | --- | --- |
   | **Créer une source d’événement ?** | Entrez **Yes**.|
   | **Nom** | Entrez une valeur unique à utiliser comme nom de la source d’événement.|
   | **Type de source** | Entrez **IoT Hub**. |
   | **Sélectionner un hub ?** | Entrez **Sélection un élément existant**. |
   | **Abonnement** | Entrez l’abonnement que vous avez utilisé pour le simulateur d’appareil. |
   | **Nom du hub IoT** | Entrez le nom du hub IoT que vous avez créé pour le simulateur d’appareil. |
   | **Stratégie d'accès IoT Hub** | Entrez **iothubowner**. |
   | **Groupe de consommateurs IoT Hub** | Vous avez besoin d’un groupe de consommateurs unique pour l’instance Azure Time Series Insights (préversion). Sélectionnez **Nouveau**, entrez un nom unique, puis sélectionnez **Ajouter**. |
   | **Propriété timestamp** | Ce champ est utilisé pour identifier la propriété d'horodatage dans vos données de télémétrie entrantes. Pour ce tutoriel, ne le renseignez pas. Ce simulateur utilise le timestamp entrant du hub IoT auquel Time Series Insights est associé par défaut.|

   Ensuite, sélectionnez **Vérifier + créer**.

   ![Page de configuration d’une source d’événement][13]

1. Vérifiez tous les champs de la page de vérification, puis sélectionnez **Créer**.

   ![Page Vérifier + créer, avec le bouton Créer][14]

1. Vous pouvez voir l'état de votre déploiement.

   ![Notification de la fin du déploiement][15]

1. Si le locataire vous appartient, vous avez normalement accès à votre environnement Azure Time Series Insights (préversion). Pour être sûr d'y avoir accès :

   a. Recherchez votre groupe de ressources et sélectionnez votre environnement Azure Time Series Insights (préversion) :

      ![Environnement sélectionné][16]

   b. Dans la page Azure Time Series Insights (préversion), accédez à **Stratégies d’accès aux données**.

     ![Stratégies d’accès aux données][17]

   c. Vérifiez que vos informations d'identification sont répertoriées.

     ![Informations d’identification listées][18]

   Si vos informations d’identification ne sont pas listées, vous devez vous octroyer l’autorisation d’accéder à l’environnement. Pour en savoir plus sur la définition des autorisations, consultez [Octroyer l’accès aux données](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analyser les données dans votre environnement

Dans cette section, vous procédez à l'analyse de base de vos données de séries chronologiques à l'aide de [l'explorateur Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md).

1. Accédez à l’explorateur Azure Time Series Insights (préversion) en sélectionnant l’URL dans la page des ressources sur le [portail Azure](https://portal.azure.com/).

   ![URL de l’explorateur Time Series Insights (préversion)][19]

1. Dans l’explorateur, sélectionnez le nœud **Instances non apparentées** pour afficher toutes les instances Azure Time Series Insights (préversion) de l’environnement.

   ![Liste des instances non apparentées][20]

1. Dans la série chronologique affichée, sélectionnez la première instance. Ensuite, sélectionnez **Show Avg pressure** (Afficher la pression moyenne).

   ![Instance sélectionnée avec la commande de menu pour afficher la pression moyenne][21]

   Un graphique de séries chronologiques doit apparaître à droite :

   ![Graphique de séries chronologiques][22]

1. Répétez l’étape 3 pour les deux autres séries chronologiques. Vous pouvez ensuite afficher toutes les séries chronologiques, comme illustré dans ce graphique :

   ![Graphique affichant toutes les séries chronologiques][23]

1. Changez l’intervalle de temps pour afficher les tendances des séries chronologiques au cours de la dernière heure. 

   a. Sélectionnez la case d’option **De** :

      ![Case d’option De][24]

   b. Changez l’heure dans la case pour afficher les événements de la dernière heure :

      ![Ajustements de l’heure][25]

1. Vous pouvez ensuite comparer la pression enregistrée sur les trois appareils au cours de la dernière heure :

   ![Comparaison des trois appareils][26]

## <a name="define-and-apply-a-model"></a>Définir et appliquer un modèle

Dans cette section, vous appliquez un modèle pour structurer vos données. Pour finaliser le modèle, vous définissez les types, les hiérarchies et les instances. Pour en savoir plus sur la modélisation des données, accédez à [Modèle de série chronologique](./time-series-insights-update-tsm.md).

1. Dans l'explorateur, sélectionnez l'onglet **Modèle** :

   ![Onglet Modèle dans l’Explorateur][27]

1. Sélectionnez **+ Ajouter** pour ajouter un type. Un éditeur de type s’ouvre sur le côté droit.

   ![Bouton Ajouter pour les types][28]

1. Définissez trois variables pour le type : la pression, la température et l’humidité. Entrez les informations suivantes :

   | | |
   | --- | ---|
   | **Nom** | Entrez **Chiller**. |
   | **Description** | Entrez **This is a type definition of Chiller** (C’est une définition du type de refroidisseur). |

   * Définissez la pression avec trois variables :

      | | |
      | --- | ---|
      | **Nom** | Entrez **Avg Pressure** (Pression moyenne). |
      | **Valeur** | Sélectionnez **Pression (Double)**. Notez que le remplissage de ce champ peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
      | **Opération d'agrégation** | Sélectionnez **AVG**. |

      ![Sélections pour définir la pression][29]

      Sélectionnez **+ Ajouter une variable** pour ajouter la variable suivante.

   * Définissez la température :

      | | |
      | --- | ---|
      | **Nom** | Entrez **Avg Temperature** (Température moyenne). |
      | **Valeur** | Sélectionnez **Température (Double)**. Notez que le remplissage de ce champ peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
      | **Opération d'agrégation** | Sélectionnez **AVG**.|

      ![Sélections pour définir la température][30]

   * Définissez l’humidité :

      | | |
      | --- | ---|
      | **Nom** | Entrez **Max Humidity** (Humidité maximale). |
      | **Valeur** | Sélectionnez **Humidité (Double)**. Notez que le remplissage de ce champ peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements. |
      | **Opération d'agrégation** | Sélectionnez **MAX**.|

      ![Sélections pour définir la température][31]

   Sélectionnez ensuite **Create** (Créer).

1. Vous pouvez constater que votre type a été ajouté :

   ![Informations sur le type ajouté][32]

1. L’étape suivante consiste à ajouter une hiérarchie. Dans la section **Hiérarchies**, sélectionnez **+ Ajouter** :

   ![Onglet Hiérarchies avec le bouton Ajouter][33]

1. Définissez la hiérarchie. Renseignez les champs comme suit :

   | | |
   | --- | ---|
   | **Nom** | Entrez **Location Hierarchy** (Hiérarchie d’emplacement). |
   | **Niveau 1** | Entrez **Country** (Pays). |
   | **Niveau 2** | Entrez **City** (Ville). |
   | **Niveau 3** | Entrez **Building** (Immeuble). |

   Sélectionnez ensuite **Create** (Créer).

   ![Champs de la hiérarchie avec le bouton Créer][34]

1. Voici la hiérarchie que vous avez créée :

   ![Informations sur la hiérarchie][35]

1. Sélectionnez **Instances** sur le côté gauche. Quand les instances sont affichées, sélectionnez la première d’entre elles, puis sélectionnez **Modifier** :

   ![Sélection du bouton Modifier pour une instance][36]

1. Un éditeur de texte s’affiche sur le côté droit. Ajoutez les informations suivantes :

   | | |
   | --- | --- |
   | **Type** | Sélectionnez **Chiller**. |
   | **Description** | Entrez **Instance for Chiller-01.1** (Instance pour le refroidisseur-01.1). |
   | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
   | **Pays** | Entrez **USA**. |
   | **Ville** | Entrez **Seattle**. |
   | **Immeuble** | Entrez **Space Needle**. |

    Sélectionnez ensuite **Enregistrer**.

   ![Champs de l’instance avec le bouton Enregistrer][37]

1. Répétez l'étape précédente pour les autres capteurs. Utilisez les champs suivants :

   * Pour le refroidisseur 01.2 :

     | | |
     | --- | --- |
     | **Type** | Sélectionnez **Chiller**. |
     | **Description** | Entrez **Instance for Chiller-01.2** (Instance pour le refroidisseur-01.2). |
     | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
     | **Pays** | Entrez **USA**. |
     | **Ville** | Entrez **Seattle**. |
     | **Immeuble** | Entrez **Pacific Science Center**. |

   * Pour le refroidisseur 01.3 :

     | | |
     | --- | --- |
     | **Type** | Sélectionnez **Chiller**. |
     | **Description** | Entrez **Instance for Chiller-01.1** (Instance pour le refroidisseur-01.1). |
     | **Hiérarchies** | Sélectionnez **Location Hierarchy** (Hiérarchie d’emplacement). |
     | **Pays** | Entrez **USA**. |
     | **Ville** | Entrez **New York**. |
     | **Immeuble** | Entrez **Empire State Building**. |

1. Accédez à l’onglet **Analyser** et actualisez la page. Développez tous les niveaux hiérarchiques pour trouver les séries chronologiques.

   ![Onglet Analyser][38]

1. Pour explorer les séries chronologiques de la dernière heure, changez l’option **Durées courtes** à **Dernière heure** :

   ![Option Durées courtes, avec Dernière heure sélectionnée][39]

1. Sélectionnez la série chronologique sous **Pacific Science Center**, puis sélectionnez **Show Max Humidity** (Afficher l’humidité maximale).

   ![Série chronologique sélectionnée avec l’option de menu Show Max Humidity (Afficher l’humidité maximale)][40]

1. La série chronologique correspondant à **Show Max Humidity** (Afficher l’humidité maximale) et à un intervalle de 1 minute s’ouvre. Sélectionnez une région pour filtrer une plage. Ensuite, cliquez avec le bouton droit et sélectionnez **Zoom** pour analyser les événements de la plage de temps :

   ![Plage sélectionnée avec la commande Zoom d’un menu contextuel][41]

1. Vous pouvez également sélectionner une région et cliquer avec le bouton droit pour afficher les détails d’un événement :

   ![Liste détaillée des événements][44]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :  

* Créer et utiliser un accélérateur de simulation d'appareil
* Créer un environnement Azure Time Series Insights (préversion) avec paiement à l'utilisation
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements
* Exécuter un exemple d’accélérateur de solution pour transmettre des données à l’environnement Azure Time Series Insights (préversion)
* Effectuer une analyse de base des données
* Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

Maintenant que vous savez créer votre propre environnement Azure Time Series Insights (préversion), penchez-vous sur les concepts clés d'Azure Time Series Insights.

Découvrez-en plus sur la configuration du stockage d’Azure Time Series Insights :

> [!div class="nextstepaction"]
> [Azure Time Series Insights (préversion) - Stockage et entrée](./time-series-insights-update-storage-ingress.md)

En savoir plus sur les modèles Time Series :

> [!div class="nextstepaction"]
> [Azure Time Series Insights (préversion) - Modélisation de données](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-seven-dashboard.png
[7]: media/v2-update-provision/device-six-listings.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png