---
title: 'Tutoriel : Créer une application de suivi de la consommation d’eau avec Azure IoT Central'
description: 'Tutoriel : Découvrez comment créer une application de suivi de la consommation d’eau à l’aide de modèles d’application Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1bad4ab6320e757ac766776a95b8dbe6ebaa3259
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016423"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Tutoriel : Créer une application de suivi de la consommation d’eau dans IoT Central



Ce tutoriel vous guide dans la création d’une application de suivi de la consommation d’eau Azure IoT Central à partir du modèle d’application IoT Central Suivi de la consommation d’eau. 

Dans ce didacticiel, vous apprendrez à : 

> [!div class="checklist"]
> * Utiliser le modèle Azure IoT Central **Water consumption monitoring** (Suivi de la consommation d’eau) pour créer votre application de suivi de la consommation d’eau
> * Explorer et personnaliser le tableau de bord de l’opérateur 
> * Explorer le modèle d’appareil
> * Explorer les appareils simulés
> * Explorer et configurer les règles
> * Configuration des travaux
> * Personnaliser le marquage de votre application à l’aide d’une étiquette blanche

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :
-  Un abonnement Azure est recommandé. Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un sur la [page d’inscription à Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Créer une application de suivi de la consommation d’eau dans IoT Central

Dans cette section, vous utilisez le modèle Azure IoT Central **Water consumption monitoring** (Suivi de la consommation d’eau) pour créer votre application de suivi de la consommation d’eau dans IoT Central.

Pour créer une application Azure IoT Central de suivi de la consommation d’eau :  

1. Accédez à la [page d’accueil d’Azure IoT Central](https://aka.ms/iotcentral).

    Si vous disposez d’un abonnement Azure, connectez-vous avec les informations d’identification que vous utilisez pour y accéder. Sinon, connectez-vous à l’aide d’un compte Microsoft :

    ![Entrer votre compte d’entreprise](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Cliquez sur **Build** (Créer) dans le volet gauche et sélectionnez l’onglet **Government** (Service public). La page relative au secteur public affiche plusieurs modèles d’application pour le secteur public.

   ![Créer des modèles d’application pour le secteur public](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Sélectionnez le modèle d’application **Water consumption monitoring** (Suivi de la consommation d’eau). Ce modèle comprend un exemple de modèle d’appareil pour la consommation d’eau, un appareil simulé, un tableau de bord d’opérateur et des règles de supervision préconfigurées.    

2. Cliquez sur **Create app** (Créer une application) pour ouvrir le formulaire de création **New application** (Nouvelle application) avec les champs suivants :
    * **Application name** (Nom de l’application) : par défaut, le nom de l’application est*Water consumption monitoring* suivi d’une chaîne d’ID unique générée par IoT Central. Si vous le souhaitez, choisissez un nom d’application convivial. Vous pouvez également modifier ultérieurement le nom de l’application.
    * **URL** : IoT Central génère automatiquement une URL basée sur le nom de l’application. Vous pouvez choisir de mettre à jour l’URL à votre convenance. Vous pouvez également modifier ultérieurement l’URL. 
    * Si vous disposez d’un abonnement Azure, entrez vos informations *Répertoire, Abonnement Azure et Région*. Si vous n’avez pas d’abonnement, vous pouvez activer une **version d’évaluation gratuite de 7 jours** et remplir les coordonnées requises.  

    Pour plus d’informations sur les répertoires et les abonnements, consultez le guide de démarrage rapide [Créer une application](../core/quick-deploy-iot-central.md).

5. Cliquez sur le bouton **Create** (Créer) en bas de la page. 

    ![Page de création d’une application Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Création d’une application Azure IoT Central, informations de facturation](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

6. Vous avez désormais créé une application de suivi de la consommation d’eau à l’aide du modèle Azure IoT Central **Water consumption monitoring** (Suivi de la consommation d’eau).

Félicitations ! Vous avez terminé la création de votre application de suivi de la consommation d’eau, qui inclut les éléments préconfigurés suivants :
* Exemples de tableaux de bord d’opérateur
* Exemples de modèles de dispositif de vanne et de débit d’eau prédéfinis
* Débitmètre et dispositif de vanne intelligente simulés
* Règles et travaux préconfigurés
* Exemple de marquage à l’aide d’une étiquette blanche 

Cette application est la vôtre et vous pouvez la modifier à tout moment. Nous allons maintenant explorer l’application et effectuer certaines personnalisations.  

## <a name="explore-and-customize-operator-dashboard"></a>Explorer et personnaliser le tableau de bord de l’opérateur 
Après avoir créé l’application, vous accédez à l’exemple de tableau de bord de l’opérateur, appelé **Wide World water consumption dashboard** (Tableau de bord de la consommation d’eau dans le monde).

   ![Tableau de bord de suivi de la consommation d’eau](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

En tant que créateur, vous pouvez créer et personnaliser des vues dans le tableau de bord pour les opérateurs. Avant d’essayer de le personnaliser, commençons par explorer le tableau de bord. 

> [!NOTE]
> Toutes les données affichées dans le tableau de bord sont basées sur des données d’appareils simulés, que nous explorerons dans la section suivante. 
  
Le tableau de bord est constitué de différents types de vignettes :

* **Vignette d’image Wide World Water utility** (Utilitaire Wide World Water) : la première vignette du tableau de bord est une vignette d’image d’un utilitaire fictif « Wide World Water ». Vous pouvez personnaliser cette vignette et fournir votre propre image, ou la supprimer. 

* **Vignette d’indicateur de performance clé de débit moyen d’eau** : la vignette de KPI est configurée pour afficher comme exemple *la moyenne sur les 30 dernières minutes*. Vous pouvez personnaliser les vignettes de KPI et définir un type et une plage de temps différents.

* Le tableau de bord intègre des vignettes de *commande de l’appareil* pour fermer la vanne (**Close valve**), ouvrir la vanne (**Open valve**) ou définir la position de la vanne (**Set valve position**). Cliquez sur les commandes pour accéder à la page des commandes de l’appareil simulé. Dans IoT Central, une *commande* est un type de *capacité d’appareil* que vous pouvez explorer plus loin dans la **section sur le modèle d’appareil** de ce tutoriel.

*  **Water distribution area map** (Carte des zones de distribution d’eau) : la carte utilise Azure Maps, que vous pouvez configurer directement dans Azure IoT Central. La vignette de carte affiche l’emplacement des appareils. Essayez de survoler la carte et essayez les contrôles sur la carte, comme le *zoom avant*, le *zoom arrière* ou *développer*. 

    ![Carte du tableau de bord de suivi de la consommation d’eau](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Graphique en courbes du débit moyen d’eau** et **Graphique en courbes des conditions environnementales** : vous pouvez visualiser une ou plusieurs télémétries d’appareil tracées sous forme de graphique en courbes sur une plage de temps souhaitée.  

* **Carte thermique de la pression moyenne au niveau de la vanne** : vous pouvez choisir le type de visualisation Carte thermique des données de télémétrie de l’appareil que vous souhaitez examiner dans la distribution sur une plage de temps avec un indice de couleur.

* **Vignette de contenu Reset alert threshold** (Réinitialiser le seuil d’alerte) : vous pouvez inclure des vignettes de contenu d’invite à l’action incorporant le lien vers une page d’action. Dans ce cas, la vignette de réinitialisation du seuil d’alerte vous permet d’accéder aux **travaux** de l’application, où vous pouvez exécuter des mises à jour sur les propriétés des appareils, que nous explorerons ultérieurement dans la section **Configurer des travaux** de ce tutoriel.

* **Vignettes de propriétés** : le tableau de bord affiche **Valve operational info** (Informations opérationnelles de la vanne), **Flow alert thresholds** (Seuils d’alerte de débit) et **Maintenance info** (Informations de maintenance). 


### <a name="customize-dashboard"></a>Personnaliser le tableau de bord 

En tant que créateur, vous pouvez personnaliser les vues du tableau de bord pour les opérateurs. Vous pouvez essayer de procéder comme suit :
1. Cliquez sur **Edit** (Modifier) pour personnaliser le **tableau de bord de suivi de la consommation d’eau dans le monde**. Vous pouvez personnaliser ce tableau de bord en cliquant sur le menu **Edit** (Modifier). Une fois que le tableau de bord est en **mode d’édition**, vous pouvez ajouter de nouvelles vignettes et les configurer. 

     ![Modification du tableau de bord](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Cliquez sur **+ New** (Nouveau) pour créer un nouveau tableau de bord et le configurer à partir de zéro. Vous pouvez avoir plusieurs tableaux de bord et naviguer entre eux via le menu Dashboard (Tableau de bord). 

## <a name="explore-device-template"></a>Explorer le modèle d’appareil
Un modèle d’appareil dans Azure IoT Central définit la fonctionnalité d’un appareil, qui peut être la télémétrie, une propriété ou une commande. En tant que créateur, vous pouvez définir un ou plusieurs modèles d’appareil dans IoT Central qui représenteront la fonctionnalité des appareils que vous connecterez. 
 

L’application **Water consumption monitoring** (Suivi de la consommation d’eau) est accompagnée de deux modèles d’appareil de référence représentant un *débitmètre* et un dispositif de *vanne intelligente*. 

Pour afficher ce modèle d’appareil :

1. Cliquez sur **Modèles d’appareil** dans le volet de navigation gauche de votre application dans IoT Central. 
    Dans la liste Modèles d’appareil, vous pouvez voir deux modèles d’appareil : **Flow meter** (Débitmètre) et **Smart Valve** (Vanne intelligente).

   ![Modèle d'appareil](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Cliquez sur le modèle d’appareil **Flow meter** et familiarisez-vous avec les fonctionnalités de l’appareil. 

     ![Modèle d’appareil Flow meter (Débitmètre)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Personnalisation du modèle d’appareil

Essayez de personnaliser les éléments suivants :
1. Accédez à **Customize** (Personnaliser) via le menu du modèle d’appareil.
2. Recherchez le type de télémétrie `Temperature`.
3. Mettez à jour le nom d’affichage (**Display name**) en remplaçant `Temperature` par `Reported temperature`.
4. Mettez à jour l’unité de mesure ou définissez *Min value* (Valeur minimale) et *Max value* (Valeur maximale).
5. Enregistrez (**Save**) les modifications. 

### <a name="add-a-cloud-property"></a>Ajouter une propriété cloud 
1. Accédez à **Cloud properties** (Propriétés cloud) via le menu du modèle d’appareil.
2. Ajoutez une nouvelle propriété cloud en cliquant sur **+ Add Cloud Property** (Ajouter une propriété cloud). 
    Dans IoT Central, vous pouvez ajouter une propriété qui s’applique à l’appareil. Par exemple, une propriété cloud peut être un seuil d’alerte spécifique à la zone d’installation, aux informations sur les ressources ou à d’autres informations de maintenance. 
3. Enregistrez (**Save**) les modifications. 
 
### <a name="views"></a>Les vues 
Le modèle d’appareil de suivi de la consommation d’eau est fourni avec des vues prédéfinies. Vous pouvez explorer ces vues et y apporter des modifications. Les vues définissent la façon dont les opérateurs verront les données de l’appareil et réaliseront également la saisie des propriétés cloud. 

  ![Vues du modèle d’appareil](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publish 
Si vous avez apporté des modifications, veillez à **publier** le modèle d’appareil. 

### <a name="create-a-new-device-template"></a>Créer un modèle d’appareil 
- Sélectionnez **+ New** (Nouveau) pour créer un nouveau modèle d’appareil et suivre le processus de création. Vous pouvez créer un modèle d’appareil personnalisé à partir de zéro ou choisir un modèle d’appareil dans le catalogue d’appareils Azure. 

## <a name="explore-simulated-devices"></a>Explorer les appareils simulés
Dans IoT Central, vous pouvez créer des appareils simulés pour tester votre modèle d’appareil et votre application. L’application **Water consumption monitoring** (Suivi de la consommation d’eau) a deux appareils simulés mappés sur les modèles d’appareil *Flow meter* (Débitmètre) et *Smart Valve* (Vanne intelligente). 

### <a name="to-view-the-devices"></a>Pour afficher ces appareils :
1. Accédez à **Devices** (Appareils) dans le volet de navigation gauche IoT Central. 

   ![Appareils](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Cliquez sur **Smart Valve 1** (Vanne intelligente 1) 

    ![Appareil 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Dans **Device Commands** (Commandes de l’appareil), vous pouvez voir les trois commandes de l’appareil *Open Valve* (Ouvrir la vanne), *Close Valve* (Fermer la vanne) et *Set Valve position* (Définir la position de la vanne), qui sont les fonctionnalités définies dans le modèle d’appareil *Smart Valve* (Vanne intelligente). 
4. Explorez l’onglet **Device Properties** (Propriétés de l’appareil) et l’onglet **Device Dashboard** (Tableau de bord de l’appareil). 

> [!NOTE]
> Notez que tous les onglets ont été configurés à partir des vues du modèle d’appareil.

### <a name="add-new-devices"></a>Ajouter de nouveaux appareils
* Ajoutez de nouveaux appareils en cliquant sur **+ New** (Nouveau) sous l’onglet **Devices** (Appareils). 

## <a name="explore-and-configure-rules"></a>Explorer et configurer les règles

Dans Azure IoT Central, vous pouvez créer des règles pour superviser automatiquement les données de télémétrie des appareils et déclencher des actions quand une ou plusieurs conditions sont remplies. Ces actions peuvent inclure l’envoi d’e-mails de notification, le déclenchement d’une action Microsoft Flow ou une action webhook pour envoyer des données à d’autres services.

L’application de **suivi de la consommation d’eau** que vous avez créée a trois règles préconfigurées.

### <a name="to-view-rules"></a>Pour afficher ces règles :
1. Accédez à **Rules** (Règles) dans le volet de navigation gauche IoT Central. 

   ![Règles](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Sélectionnez et cliquez sur **High pH alert** (Alerte de pH élevé), qui est une des règles préconfigurées dans l’application.

     ![Alerte de débit élevé](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    La règle `High flow alert` est configurée pour vérifier si la condition `Acidity (pH)` est `greater than` à `Max flow threshold`. Le seuil de débit maximal est une propriété cloud définie dans le modèle d’appareil *Smart Valve* (Vanne intelligente). La valeur de `Max flow threshold` est définie pour chaque instance d’appareil. 

À présent, nous allons créer une action de messagerie.

Pour ajouter une action à la règle :

1. Sélectionnez **+ E-mail**. 
1. Entrez *Alerte de conteneur plein* comme nom d’affichage (**Display name**) convivial pour l’action.
    * Entrez l’adresse e-mail associée à votre compte IoT Central dans **To** (À). 
1. Si vous le souhaitez, entrez une note à inclure dans le texte de l’e-mail.
1. Sélectionnez **Terminé** pour terminer l’action.
1. Sélectionnez **Enregistrer** pour enregistrer et activer la nouvelle règle. 

Après quelques minutes, vous devrez recevoir un e-mail quand la **condition** configurée sera remplie.

> [!NOTE]
> L’application enverra un e-mail chaque fois qu’une condition sera remplie. Désactivez (**Disable**) la règle pour cesser de recevoir des e-mails à partir de la règle automatisée. 
  
Pour créer une nouvelle règle : 
- Sélectionnez **+New** (Nouveau) sur les règles (**Rules**) dans le volet de navigation gauche.

## <a name="configure-jobs"></a>Configurer des travaux

Dans IoT Central, les travaux vous permettent de déclencher des mises à jour des propriétés d’appareil ou cloud sur plusieurs appareils. Outre les propriétés, vous pouvez également utiliser des travaux pour déclencher des commandes d’appareil sur plusieurs appareils. IoT Central automatisera le flux de travail pour vous. 

1. Accédez à **Jobs** (Travaux) à partir du volet de navigation gauche. 
2. Cliquez sur **+New** (Nouveau) et configurez un ou plusieurs travaux. 


## <a name="customize-your-application"></a>Personnaliser votre application 
En tant que réalisateur de logiciel, vous pouvez modifier plusieurs paramètres pour personnaliser l’expérience utilisateur dans votre application.

1.  Accédez à **Administration > Personnaliser votre application**.
3. Utilisez le bouton **Modifier** pour choisir une image à télécharger en tant que **Logo de l’application**.
4. Utilisez le bouton **Modifier** pour choisir l’image de l’**icône du navigateur** qui apparaîtra dans les onglets du navigateur.
5. Vous pouvez également remplacer les **couleurs du navigateur** par défaut en ajoutant des codes couleur hexadécimaux HTML.

   ![Personnalisation de votre application dans Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Vous pouvez également modifier les images de l’application en accédant à **Administration > Paramètres d’application** et en cliquant sur le bouton **Sélectionner une image** pour choisir une image à charger en tant qu’image de l’application. 
2. Enfin, vous pouvez également modifier le **thème** en cliquant sur **Paramètres** sur le titre de l’application. 

  
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez votre application en procédant comme suit :

1. Ouvrez l’onglet Administration dans le volet gauche de votre application IoT Central. 
2. Sélectionnez Paramètres de l'application, puis cliquez sur le bouton Supprimer en bas de la page. 


## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail les [concepts du suivi de la consommation d’eau](./concepts-waterconsumptionmonitoring-architecture.md).
