---
title: Obtenir des données de capteur auprès des partenaires
description: Décrit comment obtenir des données de capteur auprès de partenaires
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b985dfc1f16372c3fad1b0a5c0894931b4c15dcc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406483"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obtenir des données de capteur auprès de partenaires de capteur

Azure FarmBeats vous permet de récupérer, dans le hub de données, les données de streaming de vos appareils et capteurs IoT. Actuellement, les partenaires de capteur suivants sont pris en charge :

  ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

L’intégration de données d’appareils à Azure FarmBeats vous permet de récupérer, dans le hub de données, des données relatives au sol fournies par les capteurs IoT déployés dans votre ferme. Une fois les données disponibles, vous pouvez les visualiser dans FarmBeats Accelerator et les utiliser pour la fusion de données et la création de modèles IA/ML à l’aide de FarmBeats.

Pour démarrer le streaming des données de capteur, vérifiez les points suivants :

-  Vous avez installé FarmBeats à partir de la Place de Marché Azure.
-  Vous avez déterminé quels capteurs et appareils vous allez installer dans votre ferme.
-  Si vous envisagez d’utiliser des capteurs d’humidité du sol, vous pouvez utiliser la carte d’emplacement des capteurs d’humidité du sol de FarmBeats pour obtenir une recommandation quant au nombre de capteurs et à leur emplacement exact. Pour plus d’informations, consultez [Générer des cartes](generate-maps.md).

- Achetez des appareils/capteurs auprès de votre partenaire d’appareil et déployez-les dans votre ferme. Assurez-vous que vous pouvez accéder aux données des capteurs avec la solution de votre partenaire d’appareil.

### <a name="enable-device-integration-with-farmbeats"></a>Activer l’intégration des appareils à FarmBeats   

Après avoir démarré le streaming des données de capteur, vous pouvez commencer le processus de récupération des données dans votre système FarmBeats. Vous devez fournir les informations suivantes au fournisseur d’appareils pour activer l’intégration à FarmBeats :  

 - Point de terminaison d’API  
 - ID client  
 - ID client  
 - Clé secrète client  
 - Chaîne de connexion du hub d’événements

Les informations ci-dessus sont fournies par votre intégrateur système. Pour tout problème lors de l’activation de l’intégration des appareils, contactez votre intégrateur système.

Vous pouvez également générer les informations d’identification en exécutant ce script à partir d’Azure Cloud Shell. Effectuez les étapes suivantes :

1. Téléchargez le [fichier ZIP](https://aka.ms/farmbeatspartnerscript) et extrayez-le sur votre lecteur local. Vous trouverez deux fichiers dans le fichier ZIP.
2. Connectez-vous au portail Azure (https://portal.azure.com/ ) et ouvrez Cloud Shell (cette option est disponible dans la barre supérieure droite du portail).  

    ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Vérifiez que l’environnement est défini sur **PowerShell** (par défaut, il est défini sur Bash).

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Chargez les deux fichiers (obtenus à l’étape 1 ci-dessus) dans Cloud Shell.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Accédez au répertoire dans lequel les fichiers ont été chargés (par défaut, le répertoire de base > nom d’utilisateur).
6. Exécutez le script qui suit :

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Suivez les instructions à l’écran pour obtenir les valeurs (point de terminaison d’API, ID de locataire, ID client, secret client et chaîne de connexion du hub d’événements). La chaîne de connexion EventHub sera disponible dans le cadre de la réponse de l’API dans le Swagger.

**Intégrer des données d’appareil à l’aide des informations d’identification générées**

Accédez au portail du partenaire d’appareil pour lier FarmBeats à l’aide des informations d’identification générées à la section précédente.

 - Point de terminaison d’API  
 - Chaîne de connexion du hub d’événements  
 - ID client  
 - Clé secrète client  
 - ID client  

 Le fournisseur d’appareil confirme la réussite de l’intégration. Une fois cette confirmation effectuée, vous pouvez voir tous les appareils et capteurs dans Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Voir les appareils et les capteurs

La section suivante explique comment voir les appareils et capteurs de votre ferme.

### <a name="view-devices"></a>Afficher les appareils

Actuellement, FarmBeats prend en charge les appareils suivants :

- **Nœud** : appareil auquel un ou plusieurs capteurs sont liés.
- **Passerelle** : appareil auquel un ou plusieurs nœuds sont liés.

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Devices** (Appareils) dans le menu.
  La page Devices indique le type d’appareil, le modèle d’appareil, son état, la ferme dans laquelle il est installé et la date de dernière mise à jour des métadonnées. Par défaut, la colonne FARM est définie sur Null. Vous pouvez choisir d’attribuer un appareil à une ferme. Pour plus d’informations, consultez [Attribuer des appareils](#assign-devices).
2. Sélectionnez l’appareil pour voir ses propriétés, les données de télémétrie correspondantes et les appareils enfants qui lui sont connectés.  

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Voir les capteurs

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Sensors** (Capteurs) dans le menu.
  La page Sensors présente des informations sur le type de capteur, la ferme à laquelle il est connecté, l’appareil parent, le nom du port, le type de port et la date de dernière mise à jour.
2. Sélectionnez le capteur pour voir ses propriétés, les alertes actives et les données de télémétrie correspondantes.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Attribuer des appareils  

Une fois commencée la transmission des données d’un capteur, vous pouvez attribuer ce dernier à la ferme dans laquelle vous avez déployé les capteurs.

1. Dans la page d’accueil, sélectionnez **Farms** dans le menu. La page de liste **Farms** s’affiche.  
2. Sélectionnez la ferme à laquelle vous souhaitez attribuer l’appareil, puis sélectionnez **Add Devices** (Ajouter des appareils).  
3. La fenêtre **Add Devices** s’affiche. Sélectionnez l’appareil que vous souhaitez attribuer à la ferme.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Sélectionnez **Add Devices**. Vous pouvez également accéder au menu **Devices**, sélectionner les appareils que vous souhaitez attribuer à une ferme, puis sélectionner **Associate Devices**.  
5. Dans la fenêtre **Associate Devices**, sélectionnez la ferme dans la liste déroulante et sélectionnez **Apply to All** pour associer la ferme à tous les appareils sélectionnés.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Pour associer chaque appareil à une ferme différente, sélectionnez la liste déroulante dans la colonne **Assign to Farm** et sélectionnez une ferme pour chaque ligne d’appareil.  
7. Sélectionnez **Assign** pour terminer l’attribution de l’appareil.

### <a name="visualize-sensor-data"></a>Visualiser les données de capteur

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Farms** dans le menu pour afficher la page **Farms**.  
2. Sous **Farm**, sélectionnez la ferme dont vous souhaitez voir les données de capteur.  
3. Vous pouvez voir les données de télémétrie dans le tableau de bord **Farm**. Vous pouvez voir les données de télémétrie en temps réel ou utiliser l’option **Custom Range** pour définir une plage de dates spécifique.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Supprimer un capteur

Procédez comme suit :

1. Sur la page d’accueil, sélectionnez **Sensors** dans le menu pour afficher la page **Sensors**.  
2. Sélectionnez l’appareil que vous souhaitez supprimer, puis sélectionnez **Delete** dans la fenêtre de confirmation.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Un message de confirmation indique que le capteur a été correctement supprimé.  

## <a name="delete-devices"></a>Supprimer des appareils

Procédez comme suit :

1. Sur la page d’accueil, sélectionnez **Devices** dans le menu pour afficher la page des appareils.  
2. Sélectionnez l’appareil que vous souhaitez supprimer, puis sélectionnez **Delete** dans la fenêtre de confirmation.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Étapes suivantes

Les données de capteur sont à présent transmises à votre instance Azure FarmBeats. À présent, découvrez comment [générer des cartes](generate-maps.md#generate-maps) pour vos fermes.
