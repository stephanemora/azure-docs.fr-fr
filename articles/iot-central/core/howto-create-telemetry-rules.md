---
title: Utiliser des règles de télémétrie dans votre application Azure IoT Central | Microsoft Docs
description: Les règles de télémétrie d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0b24c064424b00fa9acb96b03c0a3c5ca69f67f2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264356"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Créer une règle de télémétrie et configurer des notifications dans votre application Azure IoT Central

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Vous pouvez utiliser Azure IoT Central pour surveiller à distance vos appareils connectés. Les règles d’Azure IoT Central vous permettent de surveiller vos appareils quasiment en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail ou le déclenchement de Microsoft Flow. En quelques clics seulement, vous pouvez définir la condition pour surveiller les données de vos appareils et configurer l’action correspondante. Cet article explique comment créer des règles pour surveiller les données de télémétrie envoyées par l’appareil.

Les appareils peuvent utiliser des mesures de télémétrie pour envoyer des données numériques. Une règle de télémétrie se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié.

## <a name="create-a-telemetry-rule"></a>Créer une règle de télémétrie

Pour créer une règle de télémétrie, le modèle d’appareil doit avoir au moins une mesure de télémétrie définie. Cet exemple utilise un distributeur automatique réfrigéré qui envoie les données de télémétrie de la température et de l’humidité. La règle supervise la température signalée par l’appareil et envoie un e-mail quand elle dépasse 70&deg; F.

1. À l’aide de la page **Modèles d’appareil**, accédez au modèle d’appareil pour lequel vous ajoutez la règle.

1. Si vous n’avez pas encore créé de règles, vous voyez l’écran suivant :

    ![Pas encore de règles](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Sous l’onglet **Règles**, sélectionnez **+ Nouvelle règle** pour voir les types de règles que vous pouvez créer.

1. Sélectionnez **Télémétrie** pour créer une règle afin de surveiller la télémétrie de l’appareil.

    ![Types de règles](media/howto-create-telemetry-rules/rule_types1.png)

1. Entrez un nom facilitant l’identification de la règle dans ce modèle d’appareil.

1. Pour activer immédiatement la règle sur tous les appareils créés pour ce modèle, choisissez **Activer la règle pour tous les appareils de ce modèle**.

   ![Détail de la règle](media/howto-create-telemetry-rules/rule_detail1.png)

    La règle s’applique automatiquement à tous les appareils de ce modèle.

### <a name="configure-the-rule-conditions"></a>Configurer les conditions de la règle

La condition définit les critères qui sont surveillés par la règle.

1. Sélectionnez **+** en regard de **Conditions** pour ajouter une nouvelle condition.

1. Sélectionnez les données de télémétrie que vous souhaitez analyser dans la liste déroulante **Mesure**.

1. Choisissez **Agrégation**, **Opérateur** et spécifiez une valeur **Seuil**.
   - L’agrégation est facultative. Sans agrégation, la règle se déclenche pour chaque point de données de télémétrie qui répond à la condition. Par exemple, si la règle est configurée pour se déclencher quand la température dépasse 70&deg; F, elle se déclenche presque instantanément quand l’appareil affiche une température supérieure à 70.
   - Si une fonction d’agrégation comme Average, Min, Max, Count est sélectionnée, l’utilisateur doit spécifier la **fenêtre de temps d’agrégation** pendant laquelle la condition sera évaluée. Par exemple, si vous définissez la fenêtre sur « 5 minutes » et que votre règle recherche une température moyenne supérieure à 70, la règle se déclenche quand la température moyenne est au-dessus de 70&deg; F pendant au moins cinq minutes. La fréquence d’évaluation de la règle est la même que la **fenêtre de temps d’agrégation**, ce qui signifie que, dans cet exemple, la règle est évaluée toutes les 5 minutes.

     ![Condition](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Vous pouvez ajouter plusieurs mesures de télémétrie sous **Condition**. Quand plusieurs conditions sont spécifiées, toutes les conditions doivent être remplies pour que la règle se déclenche. Chaque condition est implicitement jointe par une clause « AND ». Lorsque vous utilisez un agrégat, chaque mesure doit être agrégée.

### <a name="configure-actions"></a>Configurer les actions

Cette section vous indique comment configurer les actions à effectuer lorsque la règle est déclenchée. Les actions sont appelées lorsque toutes les conditions spécifiées dans la règle présentent la valeur true.

1. Choisissez le signe Plus ( **+** ) en regard de **Actions**. Vous voyez ici la liste des actions disponibles.  

    ![Ajouter une action](media/howto-create-telemetry-rules/add_action1.png)

1. Choisissez l’action **Envoyer un e-mail**, entrez une adresse e-mail valide dans le champ **À** et spécifiez une note qui doit apparaître dans le corps de l’e-mail quand la règle se déclenche.

    > [!NOTE]
    > Les e-mails sont envoyés seulement aux utilisateurs qui ont été ajoutés à l’application et qui se sont connectés au moins une fois. Découvrez plus en détail la [gestion des utilisateurs](howto-administer.md) dans Azure IoT Central.

   ![Configurer une action](media/howto-create-telemetry-rules/configure_action1.png)

1. Pour enregistrer la règle, choisissez **Enregistrer**. La règle est active au bout de quelques minutes et commence à surveiller la télémétrie envoyée à votre application. Quand la condition spécifiée dans la règle est remplie, cette dernière déclenche l’action d’envoi d’e-mail configurée.

Vous pouvez ajouter d’autres actions à la règle, par exemple Microsoft Flow et des Webhooks. Vous pouvez ajouter jusqu’à 5 actions par règle.

- [Action Microsoft Flow](howto-add-microsoft-flow.md) pour lancer un workflow dans Microsoft Flow quand une règle est déclenchée 
- [Action de Webhook](howto-create-webhooks.md) pour informer d’autres services quand une règle est déclenchée

## <a name="parameterize-the-rule"></a>Paramétrer la règle

Les règles peuvent dériver certaines valeurs des **Propriétés de l’appareil** en tant que paramètres. L’utilisation de paramètres est pratique dans les scénarios où les seuils de télémétrie varient sur les différents appareils. Quand vous créez la règle, choisissez une propriété d’appareil qui spécifie le seuil, comme **Seuil maximal idéal**, au lieu de fournir une valeur absolue, par exemple 70&deg; F. Quand la règle s’exécute, elle établit une correspondance entre la télémétrie de l’appareil et la valeur définie dans la propriété de l’appareil.

L’utilisation de paramètres est un moyen efficace pour réduire le nombre de règles à gérer par modèle d’appareil.

Vous pouvez également configurer des actions en utilisant **Propriété de l’appareil** comme paramètre. Si une adresse e-mail est stockée en tant que propriété, vous pouvez l’utiliser quand vous définissez l’adresse pour **À**.

## <a name="delete-a-rule"></a>Supprimer une règle

Si vous n’avez plus besoin d’une règle, supprimez-la en ouvrant la règle et en choisissant **Supprimer**. La suppression de la règle la supprime du modèle d’appareil et de tous les appareils associés.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Activer ou désactiver une règle pour un modèle d’appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. Cliquez sur le bouton bascule **Activer la règle pour tous les appareils de ce modèle** dans la règle afin de l’activer ou de la désactiver pour tous les appareils associés au modèle d’appareil.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Activer ou désactiver une règle pour un appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour cet appareil**, vous activez ou désactivez la règle pour cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des règles dans votre application Azure IoT Central, voici les étapes suivantes :

- [Ajouter une action Microsoft Flow dans les règles](howto-add-microsoft-flow.md)
- [Ajouter une action Webhook dans les règles](howto-create-webhooks.md)
- [Regrouper plusieurs actions à exécuter à partir d’une ou plusieurs règles](howto-use-action-groups.md)
- [Gérer vos appareils](howto-manage-devices.md)
