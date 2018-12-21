---
title: "Configuration d'Azure Time Series Insights (préversion) - Tutoriel : Configurer un environnement Azure Time Series Insights (préversion) | Microsoft Docs"
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
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322616"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutoriel : Configurer un environnement Azure Time Series Insights (préversion)

Ce tutoriel vous guidera tout au long du processus de création d'un environnement Azure Time Series Insights (préversion) avec paiement à l'utilisation. Ce tutoriel vous montre comment effectuer les opérations suivantes :

* Créer un environnement Azure Time Series Insights (préversion)
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements dans Azure Event Hubs
* Simuler un parc éolien pour transmettre des données en continu à l'environnement Azure Time Series Insights (préversion)
* Effectuer une analyse de base des données
* Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

# <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Dans cette section, vous allez créer trois appareils simulés qui enverront des données à un hub IoT.

1. Accédez à la [page d'accueil des accélérateurs de solution Azure IoT](https://www.azureiotsolutions.com/Accelerators). La page d'accueil des accélérateurs de solution Azure IoT contient plusieurs exemples prédéfinis. Connectez-vous à l'aide de votre compte Azure. Puis sélectionnez **Simulation d'appareil**.

   ![Page d'accueil des accélérateurs de solution Azure IoT][1]

   Enfin, cliquez sur **Essayer maintenant**.

1. Entrez les paramètres requis sur la page **Créer une solution de simulation d'appareil** :

   | Paramètre | Description |
   | --- | --- |
   | Nom de la solution |    Valeur unique, utilisée pour la création d’un nouveau groupe de ressources. Les ressources Azure répertoriées sont | créées et attribuées au groupe de ressources. |
   | Abonnement | Spécifiez le même abonnement que celui utilisé pour la création de votre environnement Time Series Insights. |
   | Région |   Spécifiez la même région que celle utilisée pour la création de votre environnement Time Series Insights. |
   | Déployer des ressources Azure facultatives    | Ne décochez pas l'option IoT Hub, car les appareils simulés l'utiliseront pour se connecter/transmettre des données en continu. |

   Après avoir entré les paramètres requis, cliquez sur **Créer une solution**. Attendez 10 à 15 minutes, le temps que votre solution soit déployée.

   ![Créer une solution de simulation d'appareil][2]

1. Sur le **tableau de bord Accélérateur de solution**, cliquez sur le bouton **Lancer** :

   ![Lancer la solution de simulation d'appareil][3]

1. Vous êtes redirigé vers la page **Simulation d'appareil Microsoft Azure IoT**. Cliquez sur **+ Nouvelle simulation** en haut à droite de l'écran.

   ![Page Simulation Azure IoT][4]

1.  Renseignez les paramètres requis comme suit :

    ![Paramètres à renseigner][5]

    |||
    | --- | --- |
    | **Nom** | Entrez le nom unique d'un simulateur |
    | **Description** | Entrez une définition |
    | **Durée de la simulation** | Paramètre à définir sur `Run indefinitely` |
    | **Modèle de l'appareil** | **Nom** : Entrez `Chiller` **Quantité** : Entrez `3` |
    | **IoT Hub cible** | Paramètre à définir sur `Use pre-provisioned IoT Hub` |

    Après avoir renseigné les paramètres requis, cliquez sur **Démarrer la simulation**.

1. Sur le tableau de bord Simulation d'appareil, consultez **Appareils actifs** et **Messages par seconde**.

    ![Tableau de bord Simulation Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Répertorier les propriétés de la simulation d'appareil

Avant de créer un environnement Azure Time Series Insights, vous devez vous procurer les noms de votre hub IoT, de votre abonnement et de votre groupe de ressources.

1. Accédez au **tableau de bord Accélérateur de solution** et connectez-vous à l'aide du même compte d'abonnement Azure. Recherchez la simulation d'appareil que vous avez créée aux étapes précédentes.

1. Cliquez sur votre simulateur d'appareil, puis sur **Lancer**. Cliquez sur le lien **Portail de gestion Azure** affiché à droite.

    ![Listes de simulateurs][7]

1. Notez les noms du groupe IoT Hub, de l'abonnement et du groupe de ressources.

    ![Portail Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Créer un environnement Time Series Insights (préversion) avec paiement à l'utilisation

Cette section explique comment créer un environnement Azure Time Series Insights (préversion) via le [portail Azure](https://portal.azure.com/).

1. Connectez-vous au portail Azure à l'aide de votre compte d'abonnement.

1. Sélectionnez **Créer une ressource**.

1. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**.

   ![Sélectionner Créer une ressource, puis Internet des objets et Time Series Insights][9]

1. Renseignez les champs de la page comme suit :

   | | |
   | --- | ---|
   | **Nom de l’environnement** | Choisissez un nom unique pour l'environnement Azure Time Series Insights (préversion). |
   | **Abonnement** | Entrez l'abonnement pour lequel vous souhaitez créer l'environnement Azure Time Series Insights (préversion). Nous vous recommandons d'utiliser le même abonnement que pour les autres ressources IoT créées par le simulateur d'appareil. |
   | **Groupe de ressources** | Un groupe de ressources est un conteneur pour les ressources Azure. Choisissez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Azure Time Series Insights (préversion). Nous vous recommandons d'utiliser le même groupe de ressources que pour les autres ressources IoT créées par le simulateur d'appareil. |
   | **Lieu** | Choisissez une région de centre de données pour votre environnement Azure Time Series Insights (préversion). Pour éviter des coûts de bande passante et une latence supplémentaires, il est préférable de maintenir l'environnement Azure Time Series Insights (préversion) dans la même région que les autres ressources IoT. |
   | **Niveau** |  Sélectionnez `PAYG`, qui signifie « paiement à l'utilisation ». Il s'agit de la référence SKU du produit Azure Time Series Insights (préversion). |
   | **ID de propriété** | Identifie de façon unique votre série chronologique. Notez que ce champ est immuable et qu'il ne pourra pas être modifié ultérieurement. Pour ce tutoriel, définissez le champ sur `iothub-connection-device-id`. Pour en savoir plus sur l'ID Time Series, reportez-vous à [Comment choisir un ID Time Series](./time-series-insights-update-how-to-id.md). |
   | **Nom du compte de stockage** | Entrez un nom unique global pour le nouveau compte de stockage à créer. |

   Après avoir renseigné les champs ci-dessus, cliquez sur **Suivant : Source de l'événement**.

   ![Cliquez sur Suivant : Source de l’événement][10]

1. Sur la page, renseignez les champs comme suit :

   | | |
   | --- | --- |
   | **Créer une source d'événement ?** | Entrez `Yes`|
   | **Nom** | Requiert une valeur unique, utilisée pour nommer la source d’événement.|
   | **Type de source** | Entrez `IoT Hub` |
   | **Sélectionner un hub ?** | Entrez `Select Existing` |
   | **Abonnement** | Entrez l'abonnement que vous avez utilisé pour le simulateur d'appareil. |
   | **Nom du hub IoT** | Entrez le nom du hub IoT que vous avez créé pour le simulateur d'appareil. |
   | **Stratégie d'accès IoT Hub** | Entrez `iothubowner` |
   | **Groupe de consommateurs IoT Hub** | Vous devez disposer d'un groupe de consommateurs unique pour l'instance d'Azure Time Series Insights (préversion). |
   | **Timestamp** | Ce champ est utilisé pour identifier la propriété d'horodatage dans vos données de télémétrie entrantes. Pour ce tutoriel, ne le renseignez pas. Ce simulateur utilise l'horodatage entrant du hub IoT auquel Time Series Insights est associé par défaut.|

   Pour créer un groupe de consommateurs unique :

   1. Cliquez sur **Nouveau** en regard du champ **Groupe de consommateurs IoT Hub** :

      ![Cliquez sur Suivant : Source de l’événement][11]

   1. Donnez un nom unique au groupe de consommateurs et cliquez sur **Ajouter** :

      ![Cliquez sur Ajouter.][12]

   Après avoir renseigné les champs ci-dessus, cliquez sur **Examiner et créer**.

      ![Examiner et créer][13]

1. Passez en revue tous les champs de la page Examiner, puis cliquez sur **Créer**.

   ![Créer][14]

1. Vous pouvez voir l'état de votre déploiement.

   ![Déploiement terminé][15]

1. Si le locataire vous appartient, vous devez avoir accès à votre environnement Time Series. Pour être sûr d'y avoir accès :

   * Accédez à l'environnement Azure Time Series Insights (préversion) que vous venez de créer. Pour ce faire, recherchez votre groupe de ressources. Puis cliquez sur votre environnement Time Series :

      ![Déploiement terminé][16]

   * Sur la page Azure Time Series Insights (préversion), accédez à **Stratégies d'accès aux données**.

     ![Stratégies d’accès aux données][17]

   * Vérifiez que vos informations d'identification sont répertoriées.

     ![Vérifier vos informations d’identification][18]

   Si vos informations d'identification ne sont pas répertoriées, vous devez vous octroyer l'autorisation d'accéder à l'environnement. Pour en savoir plus sur la définition des autorisations, reportez-vous à [Octroyer l'accès aux données](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analyser les données dans votre environnement

Dans cette section, vous procédez à l'analyse de base de vos données de séries chronologiques à l'aide de [l'explorateur Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md).

1. Accédez à l'explorateur Azure Time Series Insights (préversion) en cliquant sur l'URL de la page des ressources sur le [portail Azure](https://portal.azure.com/).

   ![URL de l'explorateur Azure Time Series Insights][19]

1. Dans l'explorateur, sélectionnez les nœuds **Instances non apparentées** pour afficher toutes les instances Azure Time Series Insights (préversion) de l'environnement.

   ![Liste des instances non apparentées][20]

1. Dans la série chronologique indiquée, cliquez sur la première instance. Cliquez ensuite sur **Afficher la pression moyenne**.

   ![Afficher la pression moyenne][21]

1. Un graphique de séries chronologiques doit apparaître à droite :

   ![Graphique de séries chronologiques][22]

1. Répétez l'**étape 3** avec les deux autres séries chronologiques. Toutes les séries chronologiques peuvent ensuite être affichées comme indiqué ci-dessous :

   ![Graphique de toutes les séries chronologiques][23]

1. Modifiez l'**intervalle de temps** pour voir les tendances relatives aux séries chronologiques au cours de la dernière heure. Sélectionnez la case d'option **De** comme indiqué ci-dessous :

   ![Sélectionner l'option De][24]

1. Modifiez l'heure dans la case d'option **De** pour afficher les événements de la dernière heure :

   ![Sélectionner l'option De][25]

1. Vous pouvez ensuite comparer la pression enregistrée sur les trois appareils au cours de la dernière heure :

   ![Sélectionner l'option De][26]

## <a name="define-and-apply-a-model"></a>Définir et appliquer un modèle

Dans cette section, vous allez appliquer un modèle pour structurer vos données. Pour finaliser le modèle, vous allez définir les types, les hiérarchies et les instances. Pour en savoir plus sur la modélisation des données, accédez à [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

1. Dans l'explorateur, sélectionnez l'onglet **Modèle** :

   ![Sélectionner l'onglet Modèle][27]

1. Cliquez ensuite sur **+ Ajouter** pour ajouter un type. Sur le côté droit, un éditeur de type s'ouvre.

   ![Cliquez sur Ajouter.][28]

1. Définissez ensuite trois variables : Pression, Température et Humidité dans un Type. Renseignez les champs suivants :

   | | |
   | --- | ---|
   | **Nom** | Entrez `Chiller` |
   | **Description** | Entrez `This is a type definition of Chiller` |

   * À présent, définissez la pression avec trois variables :

      | | |
      | --- | ---|
      | **Nom** | Entrez `Avg Pressure` |
      | **Valeur** | Sélectionnez **Pression (Double)**. Remarque : quelques minutes peuvent être nécessaires pour renseigner ce champ après le début de la réception des événements par Azure Time Series Insights. |
      | **Opération d'agrégation** | Sélectionnez `AVG`. |

      ![Ajouter une variable][29]

      Cliquez sur **+ Variable** pour ajouter la variable suivante.

   * Définissez maintenant la température :

      | | |
      | --- | ---|
      | **Nom** | Entrez `Avg Temperature` |
      | **Valeur** | Sélectionnez **Température (Double)**. Remarque : quelques minutes peuvent être nécessaires pour renseigner ce champ après le début de la réception des événements par Azure Time Series Insights. |
      | **Opération d'agrégation** | Sélectionnez `AVG`.|

      ![Définir la température][30]

   * À présent, définissez l'humidité :

      | | |
      | --- | ---|
      | **Nom** | Entrez `Max Humidity` |
      | **Valeur** | Sélectionnez **Humidité (Double)**. Remarque : quelques minutes peuvent être nécessaires pour renseigner ce champ après le début de la réception des événements par Azure Time Series Insights. |
      | **Opération d'agrégation** | Sélectionnez `MAX`.|

      ![Définir la température][31]

   Après avoir défini les variables, cliquez sur **Créer**.

1. Vous pouvez constater que votre type a été ajouté :

   ![Voir le type ajouté][32]

1. L'étape suivante consiste à ajouter une hiérarchie. Dans la section **Hiérarchies**, sélectionnez **+ Ajouter** pour créer une hiérarchie :

   ![Ajouter une hiérarchie][33]

1. Définissez la hiérarchie. Renseignez les champs comme suit :

   | | |
   | --- | ---|
   | **Nom** | Entrez `Location Hierarchy` |
   | **Niveau 1** | Entrez `Country` |
   | **Niveau 2** | Entrez `City` |
   | **Niveau 3** | Entrez `Building` |

   Après avoir renseigné les champs ci-dessus, cliquez sur **Créer**.

   ![Définir une hiérarchie][34]

1. Vous pouvez voir la hiérarchie qui a été créée :

   ![Voir votre hiérarchie][35]

1. Après avoir défini votre hiérarchie, cliquez sur **Instances** à gauche. Une fois les instances affichées, cliquez sur la première d'entre elles et sélectionnez **Modifier** :

   ![Modifier une instance][36]

1. Sur le côté droit, un éditeur de texte s'affiche. Ajoutez les champs suivants :

   | | |
   | --- | --- |
   | **Type** | Sélectionnez `Chiller`. |
   | **Description** | Entrez `Instance for Chiller-01.1` |
   | **Hiérarchies** | Activez `Location Hierarchy` |
   | **Pays** | Entrez `USA` |
   | **Ville** | Entrez `Seattle` |
   | **Immeuble** | Entrez `Space Needle` |

    Après avoir renseigné les champs ci-dessus, cliquez sur **Enregistrer**.

   ![Enregistrer un condensateur][37]

1. Répétez l'étape précédente pour les autres capteurs. Utilisez les champs suivants :

   * Pour le condensateur 01.2 :

     | | |
     | --- | --- |
     | **Type** | Sélectionnez `Chiller`. |
     | **Description** | Entrez `Instance for Chiller-01.2` |
     | **Hiérarchies** | Activez `Location Hierarchy` |
     | **Pays** | Entrez `USA` |
     | **Ville** | Entrez `Seattle` |
     | **Immeuble** | Entrez `Pacific Science Center` |

   * Pour le condensateur 01.3 :

     | | |
     | --- | --- |
     | **Type** | Sélectionnez `Chiller`. |
     | **Description** | Entrez `Instance for Chiller-01.1` |
     | **Hiérarchies** | Activez `Location Hierarchy` |
     | **Pays** | Entrez `USA` |
     | **Ville** | Entrez `New York` |
     | **Immeuble** | Entrez `Empire State Building` |

1. Accédez à l'onglet **Analyser** et actualisez la page. Développez tous les niveaux hiérarchiques pour trouver les séries chronologiques.

   ![Afficher l'onglet Analyser][38]

1. Pour explorer les séries chronologiques de la dernière heure, remplacez **Durées courtes** par la dernière heure:

   ![Explorer la dernière heure][39]

1. Cliquez sur les séries chronologiques sous **Centre des sciences du Pacifique**, puis sélectionnez **Afficher l'humidité maximale**.

   ![Afficher l'humidité maximale][40]

1. La série chronologique correspondant à l'**Humidité maximale** et à une taille d'intervalle de 1 minute s'ouvre. Cliquez sur une région pour filtrer une plage. Puis cliquez sur le bouton droit et effectuez un zoom avant pour analyser les événements de la plage de temps :

   ![Afficher, filtrer et zoomer][41]

   ![Afficher, filtrer et zoomer][42]

1. Vous pouvez également cliquer sur une région avec le bouton gauche de la souris, puis cliquer sur le bouton droit pour afficher les détails de l'événement :

   ![Afficher, filtrer et zoomer][43]

   ![Afficher, filtrer et zoomer][44]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :  

* Créer et utiliser un accélérateur de simulation d'appareil
* Créer un environnement Azure Time Series Insights (préversion) avec paiement à l'utilisation
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub d'événements
* Simuler un parc éolien pour transmettre des données en continu à l'environnement Azure Time Series Insights (préversion)
* Effectuer une analyse de base des données
* Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances

Maintenant que vous savez créer votre propre environnement Azure Time Series Insights (préversion), penchez-vous sur les concepts clés d'Azure Time Series Insights.

Découvrez-en plus sur la configuration du stockage d'Azure Time Series Insights :

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
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
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