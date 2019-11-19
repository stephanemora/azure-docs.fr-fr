---
title: 'Didacticiel : Configurer un environnement Azure Time Series Insights (préversion)'
description: 'Didacticiel : Apprenez à configurer votre environnement dans Azure Time Series Insights (préversion).'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114575"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutoriel : Configurer un environnement Azure Time Series Insights (préversion)

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights (préversion) avec paiement à l’utilisation.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Créer un environnement Azure Time Series Insights (préversion)
> * Connecter l'environnement Azure Time Series Insights (préversion) à une instance IoT Hub
> * Exécuter un exemple d’accélérateur de solution pour transmettre des données à l’environnement Azure Time Series Insights (préversion)
> * Effectuer une analyse de base des données.
> * Définir une hiérarchie et un type de modèle Time Series, et les associer à vos instances
> * Utiliser le connecteur Power BI et visualiser les données dans Power BI.

>[!TIP]
> [Les accélérateurs de solution IoT](https://www.azureiotsolutions.com/Accelerators) fournissent des solutions préconfigurées de classe entreprise, qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

Souscrivez un [abonnement Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.

## <a name="prerequisites"></a>Prérequis

* Au minimum, vous devez disposer du rôle **Contributeur** pour l’abonnement Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide d’un contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Dans cette section, vous allez créer trois appareils simulés qui envoient des données à une instance Azure IoT Hub.

1. Accédez à la [page des accélérateurs de solution Azure IoT](https://www.azureiotsolutions.com/Accelerators). La page affiche plusieurs exemples prédéfinis. Connectez-vous à l’aide de votre compte Azure. Puis sélectionnez **Simulation d'appareil**.

   [![Page des accélérateurs de solution Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Dans la page suivante, sélectionnez **Essayer maintenant**.

   [![Page de simulation d’appareil](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Dans la page **Créer une solution de simulation d’appareil**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | --- |
    | **Nom du déploiement** | Entrez une valeur unique pour un nouveau groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources. |
    | **Abonnement Azure** | Sélectionnez l’abonnement où vous allez créer votre environnement Time Series Insights. |
    | **Emplacement Azure** | Sélectionnez la région où vous envisagez de stocker votre environnement Time Series Insights. Notez que le simulateur d’appareil est uniquement proposé dans un nombre limité de régions. Dès lors, si la région souhaitée ne s'affiche pas, vous pouvez choisir d’utiliser un emplacement uniquement pour le didacticiel, puis créer un nouvel environnement TSI lorsque vous êtes prêt à passer à la phase suivante de l’étape d'intégration.  |
    | **Options de déploiement** | Sélectionnez **Approvisionner nouveau IoT Hub**. |

    1. Sélectionnez **Create** (Créer).
    [![Page de simulation d’appareil](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Au bout d'une vingtaine de minutes, votre accélérateur de solution est prêt.

    [![Page de simulation d’appareil](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Créer un environnement PAYG (préversion)

Cette section décrit comment créer un environnement Azure Time Series Insights (préversion) et le connecter à l’IoT hub créé par l’accélérateur de solution IoT à l’aide du [portail Azure](https://portal.azure.com/).

1. Connectez-vous au portail Azure à l'aide de votre compte d'abonnement.

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **Time Series Insights**.

   [![Sélectionnez Internet des objets, puis Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Dans le volet **Créer un environnement Time Series Insights**, dans l’onglet **De base**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | ---|
    | **Nom de l’environnement** | Entrez un nom unique pour l'environnement Azure Time Series Insights (préversion). |
    | **Abonnement** | Entrez l'abonnement pour lequel vous souhaitez créer l'environnement Azure Time Series Insights (préversion). Nous vous recommandons d'utiliser le même abonnement que pour les autres ressources IoT créées par le simulateur d'appareil. |
    | **Groupe de ressources** | Sélectionnez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Azure Time Series Insights (préversion). Un groupe de ressources est un conteneur pour les ressources Azure. Une bonne pratique consiste à utiliser le même groupe de ressources que pour les autres ressources IoT créées par le simulateur d’appareil. |
    | **Lieu** | Sélectionnez une région de centre de données pour votre environnement Azure Time Series Insights (préversion). Pour éviter toute latence supplémentaire, il est préférable de créer votre environnement Azure Time Series Insights (préversion) dans la même région que le hub IoT créé par le simulateur d'appareil. |
    | **Niveau** |  Sélectionnez **PAYG** (*Paiement à l'utilisation*). Il s’agit de la référence SKU du produit Azure Time Series Insights (préversion). |
    | **ID de propriété** | Entrez une valeur qui identifie de façon unique votre instance Time Series. La valeur que vous entrez dans la zone **ID de propriété** ne peut pas être modifiée ensuite. Pour ce tutoriel, utilisez l’identificateur **iothub-connection-device-id**. Pour en savoir plus sur l’ID Time Series, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md). |
    | **Nom du compte de stockage** | Entrez un nom global unique pour le nouveau compte de stockage.|
    |**Activer le magasin chaud**|Sélectionnez **Oui** pour activer le magasin chaud.|
    |**Conservation des données (en jours)**|Choisissez l’option par défaut de 7 jours. |

    Sélectionnez **Suivant : Source de l'événement**.

   [![Volet pour la création d’un environnement Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![Volet pour la création d’un environnement Time Series Insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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

1. Sélectionnez **Create** (Créer).

    [![Page Vérifier + Créer, avec le bouton Créer](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Vous pouvez voir l'état de votre déploiement :

    [![Notification de la fin du déploiement](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Vous avez accès à votre environnement Azure Time Series Insights (préversion) par défaut si vous êtes propriétaire de l’abonnement Azure. Pour vérifier votre accès :

   1. Recherchez votre groupe de ressources et sélectionnez votre environnement Azure Time Series Insights (préversion). 
      [![Environnement sélectionné](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Dans la page Azure Time Series Insights (préversion), sélectionnez **Stratégies d’accès aux données** : [![Stratégies d’accès aux données](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Vérifiez que vos informations d'identification sont répertoriées :

      [![Informations d’identification listées](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Si vos informations d’identification ne sont pas listées, vous devez vous octroyer l’autorisation d’accéder à l’environnement en sélectionnant Ajouter et en recherchant vos informations d'identification. Pour en savoir plus sur la définition des autorisations, consultez [Octroyer l’accès aux données](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Transmettre des données en continu

Maintenant que vous avez déployé votre environnement Time Series Insights, commencez à lui transmettre des données en continu à des fins d'analyse.

1. Revenez à la [page des accélérateurs de solution Azure IoT](https://www.azureiotsolutions.com/Accelerators). Localisez votre solution dans votre tableau de bord d’accélérateur de solution, puis sélectionnez **Lancer** :

    [![Lancer la solution de simulation d'appareil](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Sélectionnez **Accéder à votre accélérateur de solution**.

    [![Lancer la solution de simulation d'appareil](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Vous êtes redirigé vers la page **Simulation d’appareil Microsoft Azure IoT**. Dans le coin supérieur droit de la page, sélectionnez **Nouvelle simulation**.

    [![Page Simulation Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Dans le volet **Configuration de la simulation**, définissez les paramètres suivants :

    | Paramètre | Action |
    | --- | --- |
    | **Nom** | Entrez un nom unique pour le simulateur. |
    | **Description** | Entrez une définition. |
    | **Durée de la simulation** | Définissez ce paramètre sur **Run indefinitely** (Exécuter indéfiniment). |
    | **Modèle de l’appareil** | Cliquez sur + **Ajouter un type d’appareil** <br />**Nom** : Entrez **Ascenseur**. <br />**Quantité** : Entrez **3**. <br /> Conservez les valeurs par défaut restantes |
    | **IoT Hub cible** | Définissez ce paramètre sur **Use pre-provisioned IoT Hub** (Utiliser le hub IoT préprovisionné). |

    [![Paramètres à définir](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Sélectionnez **Démarrer la simulation**.

    Dans le tableau de bord de simulation d’appareil, consultez **Appareils actifs** et **Nombre total de message**.

    [![Tableau de bord Simulation Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analyser des données

Dans cette section, vous procédez à l'analyse de base de vos données de séries chronologiques à l'aide de [l'explorateur Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md).

1. Accédez à l’explorateur Azure Time Series Insights (préversion) en sélectionnant l’URL dans la page des ressources sur le [portail Azure](https://portal.azure.com/).

    [![URL de l’explorateur Time Series Insights (préversion)](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Dans l’Explorateur Time Series Insights, une barre s’affiche en haut de l’écran. Il s’agit de votre sélecteur de disponibilité. Assurez-vous que vous avez sélectionné au moins 2m et, si nécessaire, développez le délai d'exécution en sélectionnant et en faisant glisser les poignées du sélecteur vers la gauche et vers la droite.

1. Vous verrez **Instances de séries chronologiques** à gauche.


    [![Liste des instances non apparentées](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Sélectionnez la première instance de série chronologique. Ensuite, sélectionnez **Show pressure** (Afficher la pression).

    [![Instance de série chronologique sélectionnée avec la commande de menu pour afficher la pression moyenne](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Un graphique des séries chronologiques apparaît. Modifiez l'**Intervalle** sur **30s**.

    [![Graphique de séries chronologiques](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Répétez l’étape 3 avec les deux autres instances de séries chronologiques pour afficher les trois, comme indiqué dans le graphique suivant :

    [![Graphique affichant toutes les séries chronologiques](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Sélectionnez le sélecteur d'intervalle de temps dans le coin supérieur droit. Ici, vous pouvez sélectionner des heures de début et de fin spécifiques jusqu’à la milliseconde, ou choisir parmi les options préconfigurées telles que la dernière heure. Vous avez également la possibilité de modifier le fuseau horaire par défaut.

    [![Définissez la plage horaire à une certaine heure](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Vous trouverez ci-dessous une capture d’écran du volet graphique après exécution de la simulation pendant une heure :

    [![Volet graphique](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Définir et appliquer un modèle

Dans cette section, vous appliquez un modèle pour structurer vos données. Pour finaliser le modèle, vous définissez les types, les hiérarchies et les instances. Pour en savoir plus sur la modélisation des données, consultez [Modèle de série chronologique](./time-series-insights-update-tsm.md).

1. Dans l'explorateur, sélectionnez l'onglet **Modèle** :

   [![Onglet Modèle dans l’Explorateur](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Dans l’onglet **Types**, sélectionnez **Ajouter**.

   [![Bouton Ajouter pour les types](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Entrez les paramètres suivants :

    | Paramètre | Action |
    | --- | ---|
    | **Nom** | Entrez **Ascenseur** |
    | **Description** | Entrez **Définition du type d'ascenseur** |

    [![Bouton Ajouter pour les types](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Sélectionnez ensuite l'onglet **Variables**. [![Sélectionner l'onglet Variables](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Sélectionnez **+ Ajouter une variable** et renseignez les valeurs suivantes pour la première variable du type d'ascenseur. Vous créerez trois variables en tout.

    | Paramètre | Action |
    | --- | --- |
    | **Nom** | Entrez **Avg Temperature** (Température moyenne). |
    | **Type** | Sélectionner **Numérique** |
    | **Valeur** | Sélectionner à partir d'une présélection : Sélectionnez **Température (Double)** . <br /> Remarque : Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements.|
    | **Opération d'agrégation** | Développez **Options avancées**. <br /> Sélectionnez **AVG**. |

    [![Sélections pour définir la température](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Sélectionnez **Appliquer**.

    Sélectionnez à nouveau **+ Ajouter une variable** et définissez les valeurs suivantes :

    | Paramètre | Action |
    | --- | --- |
    | **Nom** | Entrez **Vibration moyenne**. |
    | **Type** | Sélectionner **Numérique** |
    | **Valeur** | Sélectionner à partir d'une présélection : Sélectionnez **vibration (double)** . <br /> Remarque : Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements.|
    | **Opération d'agrégation** | Développez **Options avancées**. <br /> Sélectionnez **AVG**. |

    [![Sélections pour définir la vibration](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Sélectionnez **Appliquer**.

    Sélectionnez à nouveau **+ Ajouter une variable** et définissez les valeurs suivantes pour la troisième et dernière variable :

    | Paramètre | Action |
    | --- | --- |
    | **Nom** | Entrez **Étage**. |
    | **Type** | Sélectionner **Par catégorie** |
    | **Valeur** | Sélectionner à partir d'une présélection : Sélectionnez **Étage (double)** . <br /> Remarque : Le remplissage automatique du champ **Valeur** peut prendre plusieurs minutes après qu’Azure Time Series Insights (préversion) a commencé à recevoir des événements.|
    | **Catégories** | <span style="text-decoration: underline">Valeurs d'</span>  - <span style="text-decoration: underline">étiquette</span> <br /> Inférieur : 1,2,3,4 <br /> Intermédiaire : 5,6,7,8,9 <br /> Supérieur : 10,11,12,13,14,15 |
    | **Catégorie par défaut** | Entrer **Inconnu** |

    [![Sélections pour définir la vibration](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Sélectionnez **Appliquer**. Vous devez voir trois variables créées :

    [![Sélections pour définir la vibration](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Sélectionnez **Enregistrer**. Vous verrez le **Type** créé :

    [![Sélections pour définir la vibration](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Sélectionnez l’onglet **Hiérarchies**. Sélectionnez **+Ajouter**.

    [![Onglet Hiérarchies avec le bouton Ajouter](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Dans le volet **Modifier la hiérarchie**, définissez les paramètres suivants :

   | Paramètre | Action |
   | --- | ---|
   | **Nom** | Entrez **Location Hierarchy** (Hiérarchie d’emplacement). |
   |**Niveaux**| Entrez **Pays** comme nom du premier niveau <br> Sélectionnez **+ Ajouter un niveau** <br> Entrez **Ville** pour le deuxième niveau, puis sélectionnez **+ Ajouter un niveau** <br> Entrez **Immeuble** comme nom du troisième et dernier niveau |

   Sélectionnez **Enregistrer**.

    [![Champs de la hiérarchie avec le bouton Créer](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Voici la hiérarchie que vous avez créée :

    [![Informations sur la hiérarchie](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Accédez à **Instances**. Sous **Actions** à l’extrême droite, sélectionnez l’icône de crayon pour modifier la première instance avec les valeurs suivantes :

    | Paramètre | Action |
    | --- | --- |
    | **Type** | Sélectionnez **Ascenseur**. |
    | **Nom** | Entrez **Ascenseur 1**|
    | **Description** | Entrez **Instance pour ascenseur 1** |

    [![Sélection du bouton Modifier pour une instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Accédez à **Champs d'instance** et entrez ce qui suit :

    | Paramètre | Action |
    | --- | --- |
    | **Hiérarchies** | Sélectionnez **Hiérarchie d'emplacement** |
    | **Pays** | Entrez **USA** |
    | **Ville** | Entrez **Seattle** |
    | **Immeuble** | Entrez **Space Needle** |

    [![Sélection du bouton Modifier pour une instance](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Sélectionnez **Enregistrer**.

1. Répétez l’étape 8 avec les deux autres instances avec les valeurs suivantes :

    <span style="text-decoration: underline;">Pour ascenseur 2</span> :

    | Paramètre | Action |
    | --- | --- |
    | **Type** | Sélectionnez **Ascenseur**. |
    | **Nom** | Entrez **Ascenseur 2**|
    | **Description** | Entrez **Instance for ascenseur 2** |
    | **Hiérarchies** | Sélectionnez **Hiérarchie d'emplacement** |
    | **Pays** | Entrez **USA** |
    | **Ville** | Entrez **Seattle** |
    | **Immeuble** | Entrez **Pacific Science Center** |

    <span style="text-decoration: underline;">Pour ascenseur 3</span> :

    | Paramètre | Action |
    | --- | --- |
    | **Type** | Sélectionnez **Ascenseur**. |
    | **Nom** | Entrez **Ascenseur 3**|
    | **Description** | Entrez **Instance pour ascenseur 3** |
    | **Hiérarchies** | Sélectionnez **Hiérarchie d'emplacement** |
    | **Pays** | Entrez **USA** |
    | **Ville** | Entrez **New York** |
    | **Immeuble** | Entrez **Empire State Building** |

1. Revenez à l’onglet **Analyser** pour afficher le volet graphique. Sous **Location Hierarchy** (Hiérarchie d’emplacement), développez tous les niveaux de hiérarchie pour afficher les instances de séries chronologiques :

   [![Onglet Analyser](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Sous **Pacific Science Center**, sélectionnez l’instance de série chronologique **Ascenseur 2**, puis **Afficher la température moyenne**.

    [![L’instance de série chronologique affiche la température moyenne](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Pour la même instance, **Ascenseur 2**, sélectionnez **Afficher l'étage**.

    [![L'instance de série chronologique affiche l'étage](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Avec votre variable par catégorie, vous pouvez déterminer le temps que passe l'ascenseur aux étages inférieurs, supérieurs et intermédiaires.

## <a name="clean-up-resources"></a>Supprimer des ressources

Maintenant que vous avez terminé le didacticiel, nettoyez les ressources que vous avez créées :

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis votre groupe de ressources Azure Time Series Insights.
1. Vous pouvez supprimer l’intégralité du groupe de ressources (et toutes les ressources qu’il contient) en sélectionnant **Supprimer** ou supprimer chaque ressource individuellement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :  

* Créer et utiliser un accélérateur de simulation d'appareil
* Créer un environnement Azure Time Series Insights (préversion) avec paiement à l'utilisation
* Connecter l'environnement Azure Time Series Insights (préversion) à un hub IoT
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
