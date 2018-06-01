---
title: Créer et gérer des règles de télémétrie dans votre application Azure IoT Central | Microsoft Docs
description: Les règles de télémétrie d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 47497fbda90ecb6ebe5b5a8675069a7fb262a3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201721"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Créer une règle de télémétrie et configurer des notifications dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour surveiller à distance vos appareils connectés. Les règles d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche. En quelques clics seulement, vous pouvez définir la condition pour surveiller les données de votre appareil et configurer l’action à appeler. Cet article explique les règles de télémétrie en détail.

Azure IoT Central utilise des [mesures de télémétrie](howto-set-up-template.md) pour capturer les données des appareils. Chaque type de mesure a des attributs clés qui définissent la mesure. Vous pouvez créer des règles pour surveiller chaque type de mesure d’appareil et générer des alertes quand la règle se déclenche. Une règle de télémétrie se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié.

## <a name="create-a-telemetry-rule"></a>Créer une règle de télémétrie

Cette section vous montre comment créer une règle de télémétrie. Cet exemple utilise un appareil de climatisation connecté qui envoie les données de télémétrie de la température et de l’humidité. La règle surveille la température signalée par l’appareil et envoie un e-mail quand elle dépasse 80 degrés.

1. Accédez à la page des détails de l’appareil auquel vous ajoutez la règle.

1. Si vous n’avez pas encore créé de règles, vous voyez l’écran suivant :

    ![Pas encore de règles](media\howto-create-telemetry-rules\image1.png)

1. Sous l’onglet **Règles**, choisissez **+ Nouvelle règle** pour voir les types de règles que vous pouvez créer.

    ![Types de règles](media\howto-create-telemetry-rules\image2.png)

1. Choisissez la vignette **Télémétrie** pour ouvrir le formulaire permettant de créer la règle.

    ![Règle de télémétrie](media\howto-create-telemetry-rules\image3.png)

1. Choisissez un nom qui vous aide à identifier la règle dans ce modèle d’appareil.

1. Pour activer immédiatement la règle sur tous les appareils créés à partir de ce modèle, choisissez **Activer la règle**.

### <a name="configure-the-rule-condition"></a>Configurer la condition de règle

Cette section vous montre comment ajouter une condition pour surveiller la télémétrie de la température.

1. Choisissez le signe Plus (**+**) en regard de **Condition**.

1. Choisissez le type de télémétrie **Température** dans la liste déroulante. Choisissez ensuite l’opérateur et spécifiez une valeur de seuil. Vous pouvez ajouter plusieurs conditions de télémétrie. Quand plusieurs conditions sont spécifiées, toutes les conditions doivent être remplies pour que la règle se déclenche.

    ![Ajouter une condition de télémétrie](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Sélectionnez au moins une mesure de télémétrie quand vous définissez une condition de règle de télémétrie.

### <a name="configure-the-action"></a>Configurer l’action

Cette section vous montre comment spécifier ce que fait la règle quand la condition est remplie en ajoutant une action.

1. Choisissez le signe Plus (**+**) en regard de **Actions**. Vous voyez ici la liste des actions disponibles. Dans la préversion publique, **Envoyer un e-mail** est la seule action prise en charge.

    ![Ajouter une action](media\howto-create-telemetry-rules\image5.png)

1. Choisissez l’action **Envoyer un e-mail**, entrez une adresse e-mail valide dans le champ **À** et spécifiez une note qui doit apparaître dans le corps de l’e-mail quand la règle se déclenche.

    > [!NOTE]
    > Les e-mails sont envoyés seulement aux utilisateurs qui ont été ajoutés à l’application et qui se sont connectés au moins une fois. Découvrez plus d’informations sur la [gestion des utilisateurs](howto-administer.md) dans Azure IoT Central.

   ![Configurer une action](media\howto-create-telemetry-rules\image6.png)

1. Pour enregistrer la règle, choisissez **Enregistrer**. La règle est active au bout de quelques minutes et commence à surveiller la télémétrie envoyée à votre application. Quand la condition spécifiée dans la règle est remplie, la règle déclenche l’action d’envoi d’e-mail configurée.

## <a name="parameterize-the-rule"></a>Paramétrer la règle

Les règles peuvent dériver certaines valeurs des **Propriétés de l’appareil** en tant que paramètres. L’utilisation de paramètres est pratique dans les scénarios où les seuils de télémétrie varient sur les différents appareils. Quand vous créez la règle, choisissez une propriété d’appareil qui spécifie le seuil, comme **Seuil maximal idéal**, au lieu de fournir une valeur absolue, par exemple 80 degrés. Quand la règle s’exécute, elle correspond à la télémétrie de l’appareil avec la valeur fournie dans la propriété de l’appareil.

L’utilisation de paramètres est un moyen efficace pour réduire le nombre de règles à gérer par modèle d’appareil.

Vous pouvez également configurer des actions en utilisant **Propriété de l’appareil** comme paramètre. Si une adresse e-mail est stockée en tant que propriété d’un appareil, vous pouvez l’utiliser quand vous définissez l’adresse pour **À**.

## <a name="delete-a-rule"></a>Supprimer une règle

Si vous n’avez plus besoin d’une règle, supprimez-la en ouvrant la règle et en choisissant **Supprimer**. La suppression de la règle la supprime du modèle d’appareil et de tous les appareils associés.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Activer ou désactiver une règle pour un modèle d’appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour tous les appareils de ce modèle** dans la règle, vous l’activez ou la désactivez pour tous les appareils associés au modèle d’appareil.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Activer ou désactiver une règle pour un appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour cet appareil**, vous activez ou désactivez la règle pour cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à modifier des règles dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour gérer vos appareils](howto-manage-devices.md).