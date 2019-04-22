---
title: Gérer les appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, apprenez à gérer des appareils dans votre application Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a723ca6d69d45613636c7659e6566cec19b31d65
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679030"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gérer les appareils dans votre application Azure IoT Central

Cet article explique comment gérer, en tant qu’opérateur, des appareils dans une application Azure IoT Central. En tant qu’opérateur, vous pouvez :

- Utilisez la page **Device Explorer** pour afficher, ajouter et supprimer des appareils connectés à votre application Azure IoT Central.
- Tenir à jour un inventaire de vos appareils.
- Maintenir à jour les métadonnées de votre appareil en modifiant les valeurs stockées dans les propriétés de l’appareil.
- Contrôler le comportement de vos appareils en mettant à jour un paramètre sur un appareil spécifique à partir de la page **Paramètres**.

## <a name="view-your-devices"></a>Voir vos appareils

Pour voir un appareil particulier :

1. Choisissez **Device Explorer** dans le menu de navigation gauche. Vous trouverez une liste de vos [modèles d’appareils](howto-set-up-template.md).

1. Choisissez un modèle d’appareils dans la liste **Modèle**.

1. Le volet droit de la page **Device Explorer** présente la liste des appareils créés à partir de ce modèle d’appareils. Choisissez un des appareils pour afficher la page de détails correspondante :

    ![Page Détails de l’appareil](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Ajout d’un appareil

Pour ajouter un appareil à votre application Azure IoT Central :

1. Choisissez **Device Explorer** dans le menu de navigation gauche.

1. Choisissez le modèle d’appareils à partir duquel vous souhaitez créer un appareil.

1. Choisissez + **Nouveau**.

1. Choisissez **Réel** ou **Simulé**. Un appareil réel correspond à un appareil physique que vous connectez à votre application Azure IoT Central. Un appareil simulé a des exemples de données générés par Azure IoT Central.

## <a name="import-devices"></a>Importer des appareils

Pour connecter un grand nombre d’appareils à votre application, vous pouvez importer en bloc des appareils à partir d’un fichier CSV. Le fichier CSV doit disposer des colonnes et en-têtes suivants :

* **IOTC_DeviceID** : l’ID de l’appareil doit être tout en minuscules.
* **IOTC_DeviceName** : cette colonne est facultative.

Pour l’inscription en masse d’appareils :

1. Choisissez **Device Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez créer des appareils en masse.

    > [!NOTE]
    > Si vous n’avez pas encore de modèle d’appareil, vous pouvez alors importer les appareils sous **Appareils non associés** et les enregistrer sans modèle. Une fois les appareils importés, vous pouvez ensuite les associer à un modèle.

1. Sélectionnez **Importer**.

    ![Action d’importation](./media/howto-manage-devices/BulkImport1.png)

1. Sélectionnez le fichier CSV contenant la liste des ID d’appareil à importer.

1. L’importation de l’appareil démarre une fois le fichier téléchargé. Vous pouvez suivre l’état de l’importation en haut de la grille de l’appareil.

1. Une fois l’importation terminée, un message de réussite s’affiche sur la grille de l’appareil.

    ![Importation réussie](./media/howto-manage-devices/BulkImport3.png)

Si l’importation d’appareils échoue, un message d’erreur s’affiche sur la grille de l’appareil. Un fichier journal capturant toutes les erreurs est généré et vous pouvez le télécharger.

**Association d’appareils à un modèle**

Si vous enregistrez des appareils en démarrant l’importation sous **Appareils non associés**, les appareils sont créés sans association à un modèle d’appareil. Les appareils doivent être associés à un modèle pour explorer les données et les autres informations concernant l’appareil. Procédez comme suit pour associer les appareils à un modèle :

1. Choisissez **Device Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez **Appareils non associés** :

    ![Appareils non associés](./media/howto-manage-devices/UnassociatedDevices1.png)

1. Sélectionnez les appareils que vous souhaitez associer à un modèle :

1. Sélectionnez **associer**:

    ![Associer des appareils](./media/howto-manage-devices/UnassociatedDevices2.png)

1. Cliquez sur le modèle à partir de la liste des modèles disponibles et sélectionnez **associer**.

1. Les appareils sélectionnés sont associés au modèle d’appareil que vous avez choisi.

> [!NOTE]
> Une fois qu’un appareil a été associé à un modèle, il ne peut pas être dissocié ou associé à un autre modèle.

## <a name="export-devices"></a>Exporter des appareils

Pour connecter un appareil réel à IoT Central, vous avez besoin de sa chaîne de connexion. Vous pouvez exporter les détails de l’appareil en bloc pour obtenir les informations que vous avez besoin créer des chaînes de connexion de périphérique. Le processus d’exportation crée un fichier CSV avec l’identité d’appareil, nom de l’appareil et les clés pour tous les périphériques sélectionnés.

Pour exporter en bloc des appareils à partir de votre application :

1. Choisissez **Device Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil à partir duquel vous souhaitez exporter les appareils.

1. Sélectionnez les appareils que vous souhaitez exporter, puis sélectionnez le **exporter** action.

    ![Exportation](./media/howto-manage-devices/Export1.png)

1. Le processus d’exportation démarre. Vous pouvez suivre l’état en haut de la grille.

1. Une fois l’exportation terminée, un message de réussite s’affiche avec un lien pour télécharger le fichier généré.

1. Sélectionnez le **message de réussite** pour télécharger le fichier vers un dossier local sur le disque.

    ![Exportation réussie](./media/howto-manage-devices/Export2.png)

1. Le fichier CSV exporté contient les colonnes suivantes : ID de l’appareil, nom de l’appareil, clés d’appareil et empreintes numériques de certificat X509 :

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Consultez [connectivité des appareils dans Azure IoT Central](concepts-connectivity.md), pour plus d’informations sur les chaînes de connexion et des appareils réels qui se connectés à votre application IoT Central.

## <a name="delete-a-device"></a>Suppression d’un appareil

Pour supprimer un appareil réel ou simulé depuis votre application Azure IoT Central :

1. Choisissez **Device Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil que vous souhaitez supprimer.

1. Cochez la case à côté de l’appareil pour le supprimer.

1. Choisissez **Supprimer**.

## <a name="change-a-device-setting"></a>Modification d’un paramètre d’appareil

Les paramètres contrôlent le comportement d’un appareil. En d’autres termes, ils vous permettent de fournir des entrées pour votre appareil. Vous pouvez afficher et mettre à jour des paramètres d’appareil sur la page **Détails de l’appareil**.

1. Choisissez **Device Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil dont les paramètres doivent être modifiés.

1. Choisissez l’onglet **Paramètres** . Vous trouverez ici tous les paramètres de votre appareil et leurs valeurs actuelles. Pour chaque paramètre, vous pouvez voir si l’appareil est toujours synchronisé.

1. Modifiez les paramètres selon les valeurs que vous souhaitez. Vous pouvez modifier plusieurs paramètres à la fois et les mettre à jour en même temps.

1. Choisissez **Mettre à jour**. Les valeurs sont envoyées à votre appareil. Lorsque l’appareil confirme la modification des paramètres, le paramètre revient à l’état **Synchronisé**.

## <a name="change-a-property"></a>Modifier une propriété

Les propriétés sont les métadonnées associées à l’appareil, comme la ville et le numéro de série. Vous pouvez afficher et mettre à jour des propriétés sur la page **Détails de l’appareil**.

1. Choisissez **Device Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil dont les propriétés doivent être modifiées.

1. Choisissez l’onglet **Propriétés**, où vous pouvez voir toutes les propriétés.

1. Remplacez les propriétés de l’application par les valeurs souhaitées. Vous pouvez modifier plusieurs propriétés à la fois et les mettre à jour en même temps. Choisissez **Mettre à jour**.

> [!NOTE]
> Vous ne pouvez pas modifier la valeur des _Propriétés de l’appareil_. Les propriétés de l’appareil sont définies par l’appareil et sont en lecture seule dans l’application Azure IoT Central.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Comment utiliser des ensembles d’appareils](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
