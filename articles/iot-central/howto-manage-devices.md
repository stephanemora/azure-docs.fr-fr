---
title: Gérer les appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, apprenez à gérer des appareils dans votre application Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cf803c03d266f2a400e47fc551dea62936456177
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937616"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gérer les appareils dans votre application Azure IoT Central

Cet article décrit comment, en tant qu’opérateur, gérer des appareils dans votre application Microsoft Azure IoT Central. En tant qu’opérateur, vous pouvez :

- Utiliser la page **Explorer** pour afficher, ajouter et supprimer des appareils connectés à votre application Azure IoT Central.
- Tenir à jour un inventaire de vos appareils.
- Maintenir à jour les métadonnées de votre appareil en modifiant les valeurs stockées dans les propriétés de l’appareil.
- Contrôler le comportement de vos appareils en mettant à jour un paramètre sur un appareil spécifique à partir de la page **Paramètres**.

## <a name="view-your-devices"></a>Voir vos appareils

Pour voir un appareil particulier :

1. Choisissez **Explorer** dans le menu de navigation gauche. Vous trouverez une liste de vos [modèles d’appareils](howto-set-up-template.md).

1. Choisissez un **modèle d’appareils** dans le volet gauche.

1. Dans le volet droit, vous consultez une liste des appareils créés à partir de ce modèle d’appareils. Choisissez un appareil individuel pour afficher la page **Détails de l’appareil** pour cet appareil :

    [![Page Détails de l’appareil](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Ajout d’un appareil

Pour ajouter un appareil à votre application Azure IoT Central :

1. Choisissez **Explorer** dans le menu de navigation gauche.

1. Choisissez le modèle d’appareils à partir duquel vous souhaitez créer un appareil.

1. Choisissez + **Nouveau**.

1. Choisissez **Réel** ou **Simulé**. Un appareil réel correspond à un appareil physique que vous connectez à votre application Azure IoT Central. Un appareil simulé a des exemples de données générés par Azure IoT Central. Cet exemple utilise un appareil réel. Choisissez **Réel** pour accéder à la page **Détails de l’appareil** pour votre nouvel appareil.


## <a name="import-devices"></a>Importer des appareils

Pour connecter un grand nombre d’appareils à votre application, Azure IoT Central offre une importation en masse des appareils via un fichier CSV. 

Exigences du fichier CSV :
1. Le fichier CSV doit contenir une seule colonne contenant les ID d’appareil.

1. Le fichier ne doit contenir aucun en-tête.


Pour l’inscription en masse d’appareils :

1. Choisissez **Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez créer des appareils en masse.

 >   [!NOTE] 
    Si vous n’avez pas encore de modèle d’appareil, vous pouvez ensuite importer les appareils sous **Appareils non associés** et les enregistrer sans modèle. Une fois les appareils importés, vous pouvez ensuite les associer à un modèle.

1. Cliquez sur **Importer**.

    [![Action d’importation](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Sélectionnez le fichier CSV contenant la liste des ID d’appareil à importer.

1. L’importation de l’appareil démarre une fois le fichier téléchargé. Vous pouvez suivre l’état de l’importation en haut de la grille de l’appareil.

1. Une fois l’importation terminée, un message de réussite s’affiche sur la grille de l’appareil.

    [![Importation réussie](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Si l’importation d’appareils échoue, un message d’erreur s’affichera sur la grille de l’appareil. Un fichier journal capturant toutes les erreurs est généré et peut être téléchargé en cliquant sur le message d’erreur.


**Association d’appareils à un modèle**

Si vous enregistrez des appareils en démarrant l’importation sous **Appareils non associés**, les appareils sont créés sans association à un modèle d’appareil. Les appareils doivent être associés à un modèle pour explorer les données et les autres informations concernant l’appareil. Procédez comme suit pour associer les appareils à un modèle :
1. Choisissez **Explorer** dans le menu de navigation gauche.
1. Dans le volet gauche, choisissez **Appareils non associés**.
    [![Appareils non associées](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Sélectionnez les appareils que vous souhaitez associer à un modèle.
1. Cliquez sur l’option **Associer**.
    [![Associer des appareils](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Choisissez le modèle à partir de la liste des modèles disponibles, puis cliquez sur le bouton **Associer**.
1. Les appareils sélectionnés seront déplacés dans le modèle d’appareil correspondant.

 >   [!NOTE] 
    Une fois qu’un appareil a été associé à un modèle, il ne peut pas être dissocié ou associé à un autre modèle.

## <a name="export-devices"></a>Exporter des appareils

Pour provisionner des appareils pour la connexion à IoT Central, vous aurez besoin de la chaîne de connexion de l’appareil qui est générée par IoT Central. Vous pouvez utiliser la fonctionnalité d’exportation pour obtenir les chaînes de connexion et d’autres propriétés des appareils en bloc à partir de votre application. L’exportation crée un fichier CSV avec l’identité de l’appareil, le nom de l’appareil et la chaîne de connexion principale pour tous les appareils sélectionnés.

Pour exporter en bloc des appareils à partir de votre application :
1. Choisissez **Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez exporter les appareils.

1. Sélectionnez les appareils que vous souhaitez exporter, puis cliquez sur l’action **Exporter**.

    [![Exporter](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Le processus d’exportation démarre et vous pouvez en suivre l’état en haut de la grille. 

1. Une fois l’exportation terminée, un message de réussite s’affiche avec un lien pour télécharger le fichier généré.

1. Cliquez sur le **message de réussite** pour télécharger le fichier vers un dossier local sur le disque.

    [![Exportation réussie](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Le fichier CSV exporté contient les informations suivantes :
    1. NOM
    1. ID de périphérique
    1. Chaîne de connexion principale


## <a name="delete-a-device"></a>Suppression d’un appareil

Pour supprimer un appareil réel ou simulé depuis votre application Azure IoT Central :

1. Choisissez **Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil que vous souhaitez supprimer.

1. Cochez la case à côté de l’appareil pour le supprimer.

1. Choisissez **Supprimer**.

## <a name="change-a-device-setting"></a>Modification d’un paramètre d’appareil

Les paramètres contrôlent le comportement d’un appareil. En d’autres termes, ils vous permettent de fournir des entrées pour votre appareil. Vous pouvez afficher et mettre à jour des paramètres d’appareil sur la page **Détails de l’appareil**.

1. Choisissez **Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil dont les paramètres doivent être modifiés.

1. Choisissez l’onglet **Paramètres** . Vous trouverez ici tous les paramètres de votre appareil et leurs valeurs actuelles. Pour chaque paramètre, vous pouvez voir si l’appareil est toujours synchronisé.

1. Modifiez les paramètres avec les valeurs souhaitées. Vous pouvez modifier plusieurs paramètres à la fois et les mettre à jour en même temps.

1. Choisissez **Mettre à jour**. Les valeurs sont envoyées à votre appareil. Lorsque l’appareil accuse réception d’une modification de paramètre, le paramètre revient à l’état **Synchronisé**.

## <a name="change-a-property"></a>Modifier une propriété

Les propriétés sont les métadonnées associées à l’appareil, comme la ville et le numéro de série. Vous pouvez afficher et mettre à jour des propriétés sur la page **Détails de l’appareil**.

1. Choisissez **Explorer** dans le menu de navigation.

1. Choisissez le modèle d’appareil de l’appareil dont les propriétés doivent être modifiées.

1. Choisissez l’onglet **Propriétés**, où vous pouvez voir toutes les propriétés.

1. Modifiez les propriétés avec les valeurs souhaitées. Vous pouvez modifier plusieurs propriétés à la fois et les mettre à jour en même temps.

1. Choisissez **Mettre à jour**.

> [!NOTE]
> Vous ne pouvez pas modifier la valeur des _Propriétés de l’appareil_. Les propriétés de l’appareil sont définies par l’appareil et sont en lecture seule dans l’application Azure IoT Central.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Comment utiliser des ensembles d’appareils](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
