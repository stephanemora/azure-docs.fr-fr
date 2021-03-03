---
title: Tutoriel Device Update pour Azure IoT Hub avec l’agent de package Ubuntu Server 18.04 x64 | Microsoft Docs
description: Commencez à utiliser Device Update pour Azure IoT Hub avec l’agent de package Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658849"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Tutoriel Device Update pour Azure IoT Hub avec l’agent de package Ubuntu Server 18.04 x64

Device Update pour IoT Hub prend en charge deux formes de mise à jour : l’une basée sur une image et l’autre sur un package.

Les mises à jour basées sur un package sont des mises à jour ciblées qui modifient uniquement un composant ou une application spécifique sur l’appareil. Cela réduit la consommation de bande passante et permet de réduire le temps de téléchargement et d’installation des mises à jour. Les mises à jour de package permettent généralement de réduire les temps d’arrêt des appareils lors de l’application d’une mise à jour et d’éviter une surcharge liée à la création d’images.

Ce tutoriel vous guide tout au long des étapes de mise à jour de bout en bout basée sur un package via Device Update pour IoT Hub. Nous allons utiliser un exemple d’agent de package pour Ubuntu Server 18.04 x64 pour ce tutoriel. Même si vous prévoyez d’utiliser une autre configuration de plateforme de système d’exploitation, ce tutoriel est quand même utile pour en savoir plus sur les outils et les concepts de Device Update pour IoT Hub. Suivez cette présentation d’un processus de mise à jour de bout en bout, puis choisissez votre forme préférée de mise à jour et votre plateforme de système d’exploitation, avant d’entrer dans les détails. Vous pouvez utiliser Device Update pour IoT Hub pour mettre à jour un appareil Azure IoT ou Azure IoT Edge à l’aide de ce tutoriel. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configurer le référentiel du package Device Update
> * Télécharger et installer l’agent Device Update et ses dépendances
> * Ajouter une étiquette à votre appareil IoT
> * Importer une mise à jour
> * Créer un groupe d’appareils
> * Déployer une mise à jour de package
> * Surveiller le déploiement de la mise à jour

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
* Accès à un hub IoT. Il est recommandé d’utiliser un niveau S1 (standard) ou supérieur.
* Un appareil Azure IoT ou Azure IoT Edge exécutant Ubuntu Server 18.04 x64, connecté à IoT Hub.
   * Si vous utilisez un appareil Azure IoT Edge, assurez-vous qu’il utilise la version v1.2.0 du runtime Edge ou une version ultérieure. 
* Si vous n’utilisez pas un appareil Azure IoT Edge, [installez le package le plus récent `aziot-identity-service` (préversion) sur votre appareil IoT](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Instance et compte Device Update liés au même hub IoT que ci-dessus.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Configurer le référentiel du package Device Update

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil. Pour ce tutoriel, il s’agit d’Ubuntu Server 18.04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Copiez la liste générée dans le répertoire sources.list.d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Installez la clé publique Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Installer les packages de l’agent .deb de Device Update

1. Mettre à jour les listes de packages sur votre appareil

   ```shell
      sudo apt-get update
   ```

2. Installer le package deviceupdate-agent et ses dépendances

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Les packages logiciels de Device Update pour Azure IoT Hub sont soumis aux termes de contrat de licence suivants :
   * [Licence Device Update pour IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence cliente d’optimisation de la distribution](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Configurer l’agent Device Update à l’aide du service d’identité Azure IoT (préversion)

Une fois les packages nécessaires installés, vous devez provisionner l’appareil avec ses informations d’identité et d’authentification cloud.

1. Ouvrez le fichier de configuration.

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Recherchez la section de configuration du provisionnement dans le fichier. Supprimez les marques de commentaire de la section « Provisionnement manuel avec chaîne de connexion ». Mettez à jour la valeur de connection_string avec la chaîne de connexion de votre appareil IoT (Edge). Veillez à ajouter des marques de commentaire à toutes les autres sections de provisionnement.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Enregistrez le fichier et fermez-le en utilisant Ctrl+X, Y.

4. Appliquez la configuration. 

   Si vous utilisez un appareil IoT Edge, exécutez la commande suivante. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Si vous utilisez un appareil IoT, avec le package `aziot-identity-service` installé, utilisez la commande suivante. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Si vous le souhaitez, vous pouvez vérifier que les services sont en cours d’exécution

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    La sortie doit être :

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Ajouter une étiquette à votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez au hub IoT.

2. À partir de « Appareils IoT » ou « IoT Edge » dans le volet de navigation gauche, recherchez votre appareil IoT et accédez au jumeau d’appareil.

3. Dans le jumeau d’appareil, supprimez toute valeur d’étiquette Device Update existante en lui affectant la valeur null.

4. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importer la mise à jour

1. Téléchargez le [fichier manifeste apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) et le [fichier manifeste d’importation](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json) suivants. Ce manifeste apt installera la dernière version disponible de `libcurl4-doc package` sur votre appareil IoT. 

   Vous pouvez également télécharger ce [fichier manifeste apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) et le [fichier manifeste d’importation](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Cela permet d’installer la version spécifique v7.58.0 de `libcurl4-doc package` sur votre appareil IoT. 

2. Dans le portail Azure, sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche de votre hub IoT.

3. Sélectionnez l’onglet Mises à jour.

4. Sélectionnez « + Importer une nouvelle mise à jour ».

5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un fichier manifeste d’importation ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le manifeste d’importation que vous avez téléchargé. Ensuite, sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un ou plusieurs fichiers de mise à jour ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le fichier de mise à jour de manifeste apt que vous avez téléchargé.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Capture d’écran illustrant la sélection du fichier de mise à jour." lightbox="media/import-update/select-update-files.png":::

6. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un conteneur de stockage ». Sélectionnez ensuite le compte de stockage approprié.

7. Si vous avez déjà créé un conteneur, vous pouvez le réutiliser. (Dans le cas contraire, sélectionnez « + Conteneur » pour créer un conteneur de stockage pour les mises à jour.)  Sélectionnez le conteneur à utiliser et cliquez sur « Sélectionner ».

   :::image type="content" source="media/import-update/container.png" alt-text="Capture d’écran illustrant la sélection du conteneur." lightbox="media/import-update/container.png":::

8. Sélectionnez « Soumettre » pour démarrer le processus d’importation.

9. Le processus d’importation commence et l’écran passe à la section « Historique d’importation ». Sélectionnez « Actualiser » pour voir la progression jusqu’à la fin du processus d’importation. Selon la taille de la mise à jour, cette opération peut prendre quelques minutes ou durer plus longtemps.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Capture d’écran illustrant la séquence d’importation de mise à jour." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quand la colonne État indique que l’importation a réussi, sélectionnez l’en-tête « Prêt pour le déploiement ». Vous devez maintenant voir votre mise à jour importée dans la liste.

[Apprenez-en davantage](import-update.md) sur l’importation de mises à jour.

## <a name="create-update-group"></a>Créer un groupe de mises à jour

1. Accédez au hub IoT que vous avez précédemment connecté à votre instance Device Update.

2. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.

3. Sélectionnez l’onglet Groupes en haut de la page. 

4. Sélectionnez le bouton Ajouter pour créer un nouveau groupe.

5. Sélectionnez l’étiquette IoT Hub que vous avez créée à l’étape précédente à partir de la liste. Sélectionnez Créer un groupe de mises à jour.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Capture d’écran illustrant la sélection d’une étiquette" lightbox="media/create-update-group/select-tag.PNG":::

[En savoir plus](create-update-group.md) sur l’ajout d’étiquettes et la création de groupes de mises à jour


## <a name="deploy-update"></a>Déployer la mise à jour

1. Une fois le groupe créé, vous devriez voir une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers cette mise à jour sous Mises à jour en attente. Vous devrez peut-être actualiser une fois. 

2. Cliquez sur la mise à jour disponible.

3. Vérifiez que le groupe approprié est sélectionné en tant que groupe cible. Planifiez votre déploiement, puis sélectionnez Déployer la mise à jour.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

4. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

5. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

2. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

3. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.

Vous avez réussi une mise à jour de package de bout en bout avec Device Update pour IoT Hub sur un appareil Ubuntu Server 18.04 x64. 

## <a name="bonus-steps"></a>Étapes bonus

1. Téléchargez le [fichier manifeste apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) et le [fichier manifeste d’importation](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json) suivants. Ce manifeste apt supprimera le package `libcurl4-doc package` installé sur votre appareil IoT. 

2. Répétez les sections « Importer la mise à jour » et « Déployer la mise à jour ».

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, nettoyez vos compte, instance, hub IoT et appareil IoT Device Update. Pour ce faire, accédez à chaque ressource individuelle et sélectionnez « Supprimer ». Notez que vous devez nettoyer une instance Device Update avant de nettoyer le compte Device Update. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel de mise à jour d’image sur Raspberry Pi 3 B+](device-update-raspberry-pi.md)

