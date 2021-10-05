---
title: Gérer les appareils individuellement dans votre application Azure IoT Central | Microsoft Docs
description: Découvrez comment gérer des appareils individuellement dans votre application Azure IoT Central. Créer, supprimer et mettre à jour des appareils.
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0ca2190bf5994edec56dce638d7361d90d2da2de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810632"
---
# <a name="manage-individual-devices-in-your-azure-iot-central-application"></a>Gérer les appareils individuels dans votre application Azure IoT Central

Cet article explique la procédure de gestion des appareils dans votre application Azure IoT Central. Vous pouvez :

- Utiliser la page **Appareils** pour visualiser, ajouter et supprimer des appareils connectés à votre application Azure IoT Central
- Maintenir à jour les métadonnées de votre appareil en modifiant les valeurs stockées dans les propriétés de l’appareil à partir des vues.
- Contrôler le comportement de vos appareils en mettant à jour un paramètre sur un appareil spécifique à partir des vues

Pour savoir comment gérer des groupes personnalisés d’appareils, consultez [Tutoriel : Utiliser des groupes d’appareils pour analyser les données de télémétrie des appareils](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Voir vos appareils

Pour voir un appareil particulier :

1. Choisissez **Appareils** dans le volet gauche. Vous voyez ici une liste de vos modèles d’appareil et une liste de tous les appareils auxquels votre organisation a accès.

1. Choisissez un modèle d’appareil.

1. Le volet droit de la page **Appareils** présente la liste des appareils créés à partir de ce modèle d’appareils auxquels votre organisation a accès. Choisissez un des appareils pour afficher la page de détails correspondante :

    :::image type="content" source="media/howto-manage-devices-individually/device-list.png" alt-text="Capture d’écran montrant la liste des appareils.":::

    > [!TIP]
    > Vous pouvez utiliser l’outil de filtre de cette page pour afficher les appareils d’une organisation spécifique.

## <a name="add-a-device"></a>Ajout d’un appareil

Pour ajouter un appareil à votre application Azure IoT Central :

1. Choisissez **Appareils** dans le volet gauche.

1. Choisissez le modèle d’appareils à partir duquel vous souhaitez créer un appareil.

1. Choisissez + **Nouveau**.

1. Entrez un nom et un ID de l’appareil ou acceptez la valeur par défaut. La longueur maximale de ce nom de l’appareil est de 148 caractères. La longueur maximale d’un ID l’appareil est de 128 caractères.

1. Définissez la bascule **Simulé** sur **Activé** ou **Désactivé**. Un appareil réel correspond à un appareil physique que vous connectez à votre application Azure IoT Central. Un appareil simulé a des exemples de données générés par Azure IoT Central.

1. Si votre application utilise des organisations, choisissez celle à laquelle l’appareil appartient.

    > [!TIP]
    > Vous pouvez définir une [organisation par défaut](howto-create-organizations.md#default-organization) à afficher dans la liste déroulante des organisations.

1. Sélectionnez **Create** (Créer).

1. Cet appareil apparaît maintenant dans la liste des appareils pour ce modèle. Sélectionnez l’appareil pour voir la page de détails correspondante, qui contient toutes les vues relatives à l’appareil.

## <a name="change-organization"></a>Modifier l’organisation

Pour déplacer un appareil vers une autre organisation, vous devez avoir accès aux organisations source et de destination. Pour déplacer un appareil :

1. Choisissez **Appareils** dans le volet gauche.

1. Sélectionnez l’appareil à déplacer dans la liste.

1. Sélectionnez **Organisation**.

1. Sélectionnez la nouvelle organisation pour l’appareil :

    :::image type="content" source="media/howto-manage-devices-individually/change-device-organization.png" alt-text="Capture d’écran montrant comment déplacer un appareil vers une nouvelle organisation.":::

1. Sélectionnez **Enregistrer**.

## <a name="migrate-devices-to-a-template"></a>Migrer des appareils vers un modèle

Si vous inscrivez des appareils en démarrant l’importation sous **Tous les appareils**, les appareils sont créés sans association à un modèle d’appareil. Les appareils doivent être associés à un modèle pour explorer les données et les autres informations concernant l’appareil. Procédez comme suit pour associer les appareils à un modèle :

1. Choisissez **Appareils** dans le volet gauche.

1. Dans le volet gauche, choisissez **Tous les appareils** :

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-1.png" alt-text="Capture d’écran montrant les appareils qui ne sont pas associés.":::

1. Utilisez le filtre sur la grille pour savoir si la colonne **Modèle d’appareil** présente la valeur **Non attribué(e)** pour l’un de vos appareils.

1. Sélectionnez les appareils que vous souhaitez associer à un modèle :

1. Sélectionnez **Migrer** :

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-2.png" alt-text="Capture d’écran montrant comment associer un appareil.":::

1. Choisissez le modèle dans la liste des modèles disponibles, puis sélectionnez **Migrer**.

1. Les appareils sélectionnés sont associés au modèle d’appareil que vous avez choisi.

## <a name="delete-a-device"></a>Supprimer une unité

Pour supprimer un appareil réel ou simulé depuis votre application Azure IoT Central :

1. Choisissez **Appareils** dans le volet gauche.

1. Choisissez le modèle d’appareil de l’appareil que vous souhaitez supprimer.

1. Utilisez les outils de filtre pour filtrer et rechercher vos appareils. Cochez les cases en regard des appareils à supprimer.

1. Choisissez **Supprimer**. Vous pouvez suivre l’état de la suppression dans le volet Opérations sur les appareils.

## <a name="change-a-property"></a>Modifier une propriété

Les propriétés de cloud sont les métadonnées associées à l’appareil comme la ville et le numéro de série. Les propriétés de cloud existent uniquement dans l’application IoT Central et ne sont pas synchronisées avec vos appareils. Les propriétés accessibles en écriture contrôlent le comportement d’un appareil et vous permettent de définir l’état d’un appareil à distance, par exemple, en définissant la température cible d’un thermostat.  Les propriétés de l’appareil sont définies par l’appareil et sont en lecture seule dans IoT Central. Vous pouvez afficher et mettre à jour des propriétés dans les vues **Détails de l’appareil** relatives à votre appareil.

1. Choisissez **Appareils** dans le volet gauche.

1. Choisissez le modèle de l’appareil dont les propriétés doivent être modifiées, puis sélectionnez l’appareil cible.

1. Choisissez la vue qui contient les propriétés de votre appareil. Vous pouvez y entrer des valeurs, puis sélectionner **Enregistrer** en haut de la page. Vous voyez les propriétés de votre appareil et leurs valeurs actuelles. Les propriétés de cloud et les propriétés accessibles en écriture comportent des champs modifiables, tandis que les propriétés de l’appareil sont en lecture seule. Vous pouvez voir l’état de synchronisation des propriétés accessibles en écriture en bas du champ.

1. Modifiez les propriétés selon les valeurs souhaitées. Vous pouvez modifier plusieurs propriétés à la fois et les mettre à jour en même temps.

1. Choisissez **Enregistrer**. Si vous avez enregistré des propriétés accessibles en écriture, les valeurs sont envoyées à votre appareil. Quand l’appareil confirme la modification de la propriété accessible en écriture, l’état revient à **synchronisé**. Si vous avez enregistré une propriété de cloud, la valeur est mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des appareils individuellement, une étape suivante suggérée consiste à apprendre à [gérer des appareils en bloc dans votre application Azure IoT Central](howto-manage-devices-in-bulk.md)).
