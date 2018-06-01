---
title: Gérer les appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, apprenez à gérer des appareils dans votre application Azure IoT Central.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 75472d701160e7cfd331d01efcdc1a19ae20fb2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303577"
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


## <a name="bulk-import-devices"></a>Importation d’appareils en masse

Pour connecter un grand nombre d’appareils à votre application, Azure IoT Central offre une importation en masse des appareils via un fichier CSV. 

Exigences du fichier CSV :
1. Le fichier CSV doit contenir une seule colonne contenant les ID d’appareil.

1. Le fichier ne doit contenir aucun en-tête.


Pour l’inscription en masse d’appareils :

1. Choisissez **Explorer** dans le menu de navigation gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez créer des appareils en masse.

1. Choisissez **Nouveau** et sélectionnez **Importation en masse**.

    [![Importation en masse](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Sélectionnez le fichier CSV contenant la liste des ID d’appareil à importer.

1. L’importation de l’appareil démarre une fois le fichier téléchargé. Vous pouvez suivre l’état de l’importation en haut de la grille de l’appareil.

1. Une fois l’importation terminée, un message de réussite s’affiche sur la grille de l’appareil.

    [![Importation en masse réussie](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Si l’importation d’appareils échoue, une erreur s’affichera sur la grille de l’appareil. Un fichier journal capturant toutes les erreurs est généré et peut être téléchargé en cliquant sur le message d’erreur.



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
