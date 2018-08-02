---
title: Créer et gérer des règles d’événement dans votre application Azure IoT Central | Microsoft Docs
description: Les règles d’événement d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c5697f6d4ca2c9d9948b7cdd005a6a75bdabb246
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222563"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Créer une règle d’événement et configurer une action dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour surveiller à distance vos appareils connectés. Les règles d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail ou le déclenchement d’un flux de travail dans Microsoft Flow, quand les conditions de la règle sont remplies. En quelques clics seulement, vous pouvez définir la condition pour surveiller les données de votre appareil et configurer l’action à appeler. Cet article explique en détail la règle de surveillance des événements.

Azure IoT Central utilise une [mesure des événements](howto-set-up-template.md) pour capturer les données des appareils. Chaque type de mesure a des attributs clés qui définissent la mesure. Vous pouvez créer des règles pour surveiller chaque type de mesure d’appareil et générer des alertes quand la règle se déclenche. Une règle d’événement se déclenche quand l’événement d’appareil sélectionné est signalé par l’appareil.

## <a name="create-an-event-rule"></a>Créer une règle d’événements

Cette section vous montre comment créer une règle d’événement. Cet exemple utilise un appareil distributeur réfrigéré qui signale un événement d’erreur de moteur du ventilateur. La règle surveille l’événement signalé par l’appareil et envoie un e-mail chaque fois que l’événement est signalé.

1. Accédez à la page des détails pour l’appareil auquel vous ajoutez la règle.

1. Si vous n’avez pas encore créé de règles, vous voyez l’écran suivant :

    ![Pas encore de règles](media/howto-create-event-rules/image1.png)

1. Sous l’onglet **Règles**, choisissez **+ Nouvelle règle** pour voir les types de règles que vous pouvez créer.

    ![Types de règles](media/howto-create-event-rules/image2.png)

1. Cliquez sur **Événement** pour ouvrir le formulaire permettant de créer la règle.

    ![Règle d’événement](media/howto-create-event-rules/image3.png)

1. Choisissez un nom qui vous aide à identifier la règle dans ce modèle d’appareil.

1. Pour activer immédiatement la règle sur tous les appareils créés à partir de ce modèle, choisissez **Activer la règle**.

### <a name="configure-the-rule-condition"></a>Configurer la condition de règle

Cette section vous montre comment ajouter une condition pour surveiller la mesure des événements d’erreur du moteur du ventilateur.

1. Choisissez le signe Plus (**+**) en regard de **Condition**.

1. Choisissez la mesure d’événement que vous voulez surveiller dans la liste déroulante. Dans cet exemple **Fan Motor Error** (Erreur du moteur du ventilateur) a été sélectionné.

1. Vous pouvez aussi fournir une valeur dans le cas où vous voulez surveiller une valeur spécifique de l’événement signalé par l’appareil. Par exemple, si l’appareil signale le même événement avec différents codes d’erreur, spécifiez le code d’erreur comme valeur dans la condition de la règle pour que cette règle se déclenche seulement quand l’appareil envoie cette valeur spécifique comme charge utile de l’événement. Le fait de laisser vide cette valeur signifie que la règle se déclenche chaque fois que l’appareil envoie l’événement, quelle que soit la valeur de l’événement.

    ![Ajouter une condition d’événement](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > Vous devez sélectionner au moins une mesure d’événement lors de la définition d’une condition de règle d’événement.

1. Cliquez sur **Enregistrer** pour enregistrer votre règle. La règle est active au bout de quelques minutes et commence à surveiller les événements envoyés à votre application.

### <a name="add-an-action"></a>Ajouter une action

Cet exemple montre comment ajouter une action à une règle. Il montre comment ajouter l’action d’e-mail, mais vous pouvez également ajouter d’autres actions :
-  [Action Microsoft Flow](howto-add-microsoft-flow.md) pour lancer un workflow dans Microsoft Flow quand une règle est déclenchée
- [Action de Webhook](howto-create-webhooks.md) pour informer d’autres services quand une règle est déclenchée

> [!NOTE]
> Actuellement, vous ne pouvez associer qu’une seule action à une même règle.

1. Choisissez le signe Plus (**+**) en regard de **Actions**. Vous voyez ici la liste des actions disponibles.

    ![Ajouter une action](media/howto-create-event-rules/image5.png)

1. Choisissez l’action **Envoyer un e-mail**, entrez une adresse e-mail valide dans le champ **À** et spécifiez une note qui doit apparaître dans le corps de l’e-mail quand la règle se déclenche.

    > [!NOTE]
    > Les e-mails sont envoyés seulement aux utilisateurs qui ont été ajoutés à l’application et qui se sont connectés au moins une fois. Découvrez plus en détail la [gestion des utilisateurs](howto-administer.md) dans Azure IoT Central.

   ![Configurer une action](media/howto-create-event-rules/image6.png)

1. Cliquez sur **Enregistrer**. La règle est active au bout de quelques minutes et commence à surveiller les événements envoyés à votre application. Quand la condition spécifiée dans la règle est remplie, la règle déclenche l’action d’envoi d’e-mail configurée.

## <a name="parameterize-the-rule"></a>Paramétrer la règle

Vous pouvez également configurer des actions en utilisant **Propriété de l’appareil** comme paramètre. Si une adresse e-mail est stockée en tant que propriété d’un appareil, vous pouvez l’utiliser quand vous définissez l’adresse pour **À**.

## <a name="delete-a-rule"></a>Supprimer une règle

Si vous n’avez plus besoin d’une règle, supprimez-la en ouvrant la règle et en choisissant **Supprimer**. La suppression de la règle la supprime du modèle d’appareil et de tous les appareils associés.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Activer ou désactiver une règle pour un modèle d’appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour tous les appareils de ce modèle** dans la règle, vous l’activez ou la désactivez pour tous les appareils associés au modèle d’appareil.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Activer ou désactiver une règle pour un appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour cet appareil**, vous activez ou désactivez la règle pour cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des règles dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour ajouter une action Microsoft Flow à une règle](howto-add-microsoft-flow.md)
> [Guide pratique pour gérer vos appareils](howto-manage-devices.md).
