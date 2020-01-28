---
title: Obtenir des données de capteur auprès des partenaires
description: Cet article décrit comment obtenir des données de capteur auprès de partenaires.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 745e3f0b6c7bd4e6d984ce2df29b9965d4b8ca21
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513731"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obtenir des données de capteur auprès de partenaires de capteur

Azure FarmBeats vous permet de récupérer, dans Datahub, les données de streaming de vos appareils et capteurs IoT. À l’heure actuelle, nous travaillons en partenariat avec les fournisseurs de capteurs suivants.

  ![Partenaires de FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

L’intégration de données d’appareils à Azure FarmBeats vous permet de récupérer, dans le hub de données, des données relatives au sol fournies par les capteurs IoT déployés dans votre ferme. Une fois les données disponibles, vous pouvez les visualiser par le biais de l’accélérateur FarmBeats. Vous pouvez utiliser les données pour la fusion de données et pour la création de modèles ML/IA (machine learning/intelligence artificielle) à l’aide de FarmBeats.

Pour démarrer le streaming des données de capteur, vérifiez les points suivants :

-  Vous avez installé FarmBeats (disponible dans la Place de marché Azure).
-  Vous avez déterminé quels capteurs et appareils vous souhaitez installer dans votre ferme.
-  Si vous prévoyez d’utiliser des capteurs d’humidité du sol, utilisez la carte d’emplacement des capteurs d’humidité du sol de FarmBeats pour obtenir une recommandation quant au nombre de capteurs et à leur emplacement exact. Pour plus d’informations, consultez [Générer des cartes](generate-maps-in-azure-farmbeats.md).
- Achetez des appareils ou des capteurs auprès de votre fournisseur d’appareils partenaire et déployez-les dans votre ferme. Vérifiez que vous pouvez accéder aux données des capteurs par le biais de la solution de votre partenaire.

## <a name="enable-device-integration-with-farmbeats"></a>Activer l’intégration des appareils à FarmBeats

Après avoir démarré le streaming des données de capteur, vous pouvez commencer le processus de récupération des données dans votre système FarmBeats. Indiquez les informations suivantes au fournisseur d’appareils pour activer l’intégration à FarmBeats :

 - Point de terminaison d’API
 - ID client
 - ID client
 - Clé secrète client
 - Chaîne de connexion du hub d’événements

Les informations précédentes sont fournies par votre intégrateur système. Pour tout problème susceptible de se présenter lors de l’activation de l’intégration des appareils, contactez votre intégrateur système.

Vous pouvez également générer les informations d’identification en exécutant ce script à partir d’Azure Cloud Shell. Effectuez les opérations suivantes.

1. Téléchargez le [fichier zip](https://aka.ms/farmbeatspartnerscriptv2) et extrayez-le sur votre lecteur local. Il y aura un fichier dans le fichier zip.
2. Connectez-vous à https://portal.azure.com/ et accédez à Azure Active Directory -> Inscriptions d’applications.

3. Cliquez sur l’inscription de l’application qui a été créée dans le cadre de votre déploiement FarmBeats. Elle aura le même nom que votre hub de données FarmBeats.

4. Cliquez sur « Exposer une API » -> cliquez sur « Ajouter une application cliente », puis entrez **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** et cochez la case « Autoriser l’étendue ». Cela permet d’accéder à l’interface de ligne de commande Azure (Cloud Shell) pour effectuer les étapes ci-dessous.

5. Ouvrez Cloud Shell. Cette option est disponible dans la barre d’outils située en haut à droite du portail Azure.

    ![Barre d’outils du portail Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Veillez à ce que **PowerShell** soit défini comme environnement. Bash est sélectionné par défaut.

    ![Paramètre de la barre d’outils PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Chargez le fichier obtenu à l’étape 1 dans votre instance Cloud Shell.

    ![Bouton de chargement dans la barre d’outils](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Accédez au répertoire où le fichier a été chargé. Par défaut, les fichiers sont chargés dans le répertoire de base sous le nom d’utilisateur.

9. Exécutez le script suivant. Le script invite à fournir l’ID de locataire, qui peut être obtenu à partir de la page Azure Active Directory > Vue d’ensemble.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Suivez les instructions à l’écran pour capturer les valeurs suivantes : **API Endpoint** (Point de terminaison d’API), **Tenant ID** (ID de locataire), **Client ID** (ID client), **Client Secret** (Secret client) et **EventHub Connection String** (Chaîne de connexion du hub d’événements).

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Intégrer des données d’appareil avec des informations d’identification générées

Accédez au portail du partenaire d’appareil pour lier FarmBeats en utilisant des informations d’identification générées à la section précédente :

 - Point de terminaison d’API
 - Chaîne de connexion du hub d’événements
 - ID client
 - Clé secrète client
 - ID client

 Le fournisseur d’appareil confirme la réussite de l’intégration. Une fois cette confirmation effectuée, vous pouvez voir tous les appareils et capteurs dans Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Voir les appareils et les capteurs

La section suivante explique comment voir les appareils et capteurs de votre ferme.

### <a name="view-devices"></a>Afficher les appareils

À l’heure actuelle, FarmBeats prend en charge les appareils suivants :

- **Nœud** : appareil auquel un ou plusieurs capteurs sont attachés.
- **Passerelle** : appareil auquel un ou plusieurs nœuds sont attachés.

Effectuez les opérations suivantes.

1. Dans la page d’accueil, sélectionnez **Devices** (Appareils) dans le menu.
  La page **Devices** indique le type d’appareil, le modèle d’appareil, son état, la ferme dans laquelle il est placé et la date de dernière mise à jour des métadonnées. Par défaut, la colonne FARM a la valeur *NULL*. Vous pouvez choisir d’attribuer un appareil à une ferme. Pour plus d’informations, consultez [Attribuer des appareils](#assign-devices).
2. Sélectionnez l’appareil pour voir ses propriétés, les données de télémétrie correspondantes et les appareils enfants qui lui sont connectés.

    ![Page Appareils](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Voir les capteurs

Effectuez les opérations suivantes.

1. Dans la page d’accueil, sélectionnez **Sensors** (Capteurs) dans le menu.
  La page **Sensors** présente des informations sur le type de capteur, la ferme à laquelle il est connecté, l’appareil parent, le nom du port, le type de port et la date de dernière mise à jour.
2. Sélectionnez le capteur pour voir ses propriétés, les alertes actives et les données de télémétrie correspondantes.

    ![Page Sensors (Capteurs)](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Attribuer des appareils  

Quand vous commencez à recevoir les données de capteurs, vous pouvez les attribuer à la ferme dans laquelle vous avez déployé les capteurs.

1. Dans la page d’accueil, sélectionnez **Farms** dans le menu. La liste **Farms** s’affiche.
2. Sélectionnez la ferme à laquelle vous souhaitez attribuer l’appareil, puis sélectionnez **Add Devices** (Ajouter des appareils).
3. La fenêtre **Add Devices** s’affiche. Sélectionnez l’appareil que vous souhaitez attribuer à la ferme.

    ![Fenêtre Add Devices (Ajouter des appareils)](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Sélectionnez **Add Devices**. Vous pouvez également accéder au menu **Devices**, sélectionner les appareils que vous souhaitez attribuer à une ferme, puis sélectionner **Associate Devices** (Associer des appareils).
5. Dans la fenêtre **Associate Devices**, sélectionnez la ferme dans la liste déroulante, puis sélectionnez **Apply to All** (Appliquer à tout) pour associer la ferme à tous les appareils sélectionnés.

    ![Fenêtre Associate Devices (Associer des appareils)](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Pour associer chaque appareil à une ferme différente, sélectionnez la flèche déroulante vers le bas dans la colonne **Assign to Farm** (Attribuer à une ferme), puis sélectionnez une ferme pour chaque ligne d’appareil.
7. Sélectionnez **Assign** (Attribuer) pour terminer l’attribution de l’appareil.

### <a name="visualize-sensor-data"></a>Visualiser les données de capteur

Effectuez les opérations suivantes.

1. Dans la page d’accueil, sélectionnez **Farms** dans le menu pour afficher la page **Farms**.
2. Sous **Farm**, sélectionnez la ferme dont vous souhaitez voir les données de capteur.
3. Vous pouvez voir les données de télémétrie dans le tableau de bord **Farm**. Vous pouvez voir les données de télémétrie en temps réel ou utiliser l’option **Custom Range** (Plage personnalisée) pour voir une plage de dates spécifique.

    ![Tableau de bord de la ferme](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Supprimer un capteur

Effectuez les opérations suivantes.

1. Sur la page d’accueil, sélectionnez **Sensors** dans le menu pour afficher la page **Sensors**.
2. Sélectionnez l’appareil que vous souhaitez supprimer, puis sélectionnez **Delete** (Supprimer) dans la fenêtre de confirmation.

    ![Bouton Supprimer](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Un message de confirmation indique que le capteur a été correctement supprimé.

## <a name="delete-devices"></a>Supprimer des appareils

Effectuez les opérations suivantes.

1. Dans la page d’accueil, sélectionnez **Devices** dans le menu pour afficher la page **Devices**.
2. Sélectionnez l’appareil que vous souhaitez supprimer, puis sélectionnez **Delete** (Supprimer) dans la fenêtre de confirmation.

    ![Bouton Supprimer](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Étapes suivantes

Les données de capteur sont à présent transmises à votre instance Azure FarmBeats. À présent, découvrez comment [générer des cartes](generate-maps-in-azure-farmbeats.md#generate-maps) pour vos fermes.
