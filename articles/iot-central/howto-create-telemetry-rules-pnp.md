---
title: Créer et gérer des règles de télémétrie dans votre application Azure IoT Central | Microsoft Docs
description: Les règles de télémétrie d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879041"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Créer une règle de télémétrie et configurer des notifications dans votre application Azure IoT Central (fonctionnalité en préversion)

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Vous pouvez utiliser Azure IoT Central pour surveiller à distance vos appareils connectés. Les règles d’Azure IoT Central vous permettent de surveiller vos appareils quasiment en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail ou le déclenchement de Microsoft Flow. En quelques clics seulement, vous pouvez définir la condition pour surveiller les données de votre appareil et configurer l’action correspondante. Cet article explique comment créer des règles pour surveiller les données de télémétrie envoyées par l’appareil.

Les appareils peuvent utiliser des mesures de télémétrie pour envoyer des données numériques. Une règle de télémétrie se déclenche quand la télémétrie de l’appareil sélectionné dépasse un seuil spécifié.

## <a name="create-a-telemetry-rule"></a>Créer une règle de télémétrie

Pour créer une règle de télémétrie, la définition de l’appareil doit avoir au moins une mesure de télémétrie définie. Cet exemple utilise un distributeur automatique réfrigéré qui envoie les données de télémétrie de la température et de l’humidité. La règle surveille la température signalée par l’appareil et envoie un e-mail quand elle dépasse 80 degrés.

1. Accédez à la page **Règles**.

1. Si vous n’avez pas encore créé de règles, vous voyez l’écran suivant :

    ![Pas encore de règles](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Sélectionnez **+ Nouvelle règle** pour voir les types de règles que vous pouvez créer.

1. Sélectionnez **Télémétrie** pour créer une règle afin de surveiller la télémétrie de l’appareil.

    ![Types de règles](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Entrez un nom facilitant l’identification de la règle, puis tapez sur Entrée.

1. Sélectionnez la définition d’appareil à laquelle vous souhaitez voir s’appliquer cette règle dans la section Étendues. Cet écran vous permet également de filtrer les appareils auxquels la règle s'applique en utilisant l’option **+ Filtre**. La règle s’applique automatiquement à tous les appareils de ce modèle. Pour désactiver la règle, sélectionnez le bouton **Désactiver** dans l'en-tête.

### <a name="configure-the-rule-conditions"></a>Configurer les conditions de la règle

La condition définit les critères qui sont surveillés par la règle.

1. Indiquez si vous voulez activer ou désactiver l’option **Set aggregation** (Définir l’agrégation).

      - L’agrégation est facultative. Sans agrégation, la règle se déclenche pour chaque point de données de télémétrie qui répond à la condition. Par exemple, si la règle est configurée pour se déclencher quand la température dépasse 80, la règle se déclenche presque instantanément lorsque l’appareil affiche température > 80.
      - Si une fonction d’agrégation comme average, min, max, count est sélectionnée, l’utilisateur doit spécifier une **fenêtre de temps** pendant laquelle la condition sera évaluée. Par exemple, si vous définissez la fenêtre sur « 5 minutes » et que votre règle recherche une température moyenne supérieure à 80, la règle se déclenche lorsque la température moyenne est au-dessus de 80 pendant au moins 5 minutes. La fréquence d’évaluation de la règle est identique à la **fenêtre de temps**, ce qui signifie que, dans cet exemple, la règle est évaluée toutes les 5 minutes.

1. Sélectionnez les données de télémétrie que vous souhaitez analyser dans la liste déroulante **Mesure**.

1. Ensuite, choisissez un **opérateur** et indiquez une **valeur**.

     ![Condition](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>Vous pouvez ajouter plusieurs mesures de télémétrie en sélectionnez **+ Condition**. Quand plusieurs conditions sont spécifiées, toutes les conditions doivent être remplies pour que la règle se déclenche. Chaque condition est implicitement jointe par une clause « AND ». Lorsque vous utilisez un agrégat, chaque mesure doit être agrégée.

### <a name="configure-actions"></a>Configurer les actions

Cette section vous indique comment configurer les actions à effectuer lorsque la règle est déclenchée. Les actions sont appelées lorsque toutes les conditions spécifiées dans la règle présentent la valeur true.

1. Cliquez sur **+ Action** dans les sections **Action**. Vous voyez ici la liste des actions disponibles.  

    ![Ajouter une action](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Choisissez l’action **Envoyer un e-mail**, entrez un nom d'affichage pour l’action, une adresse e-mail valide dans le champ **À** et spécifiez une note qui doit apparaître dans le corps de l’e-mail quand la règle se déclenche.

    > [!NOTE]
    > Les e-mails sont envoyés seulement aux utilisateurs qui ont été ajoutés à l’application et qui se sont connectés au moins une fois. Découvrez plus en détail la [gestion des utilisateurs](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dans Azure IoT Central.

   ![Configurer une action](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Pour enregistrer l'action, choisissez **Terminé**.

1. Pour enregistrer la règle, choisissez **Enregistrer**. La règle est active au bout de quelques minutes et commence à surveiller la télémétrie envoyée à votre application. Quand la condition spécifiée dans la règle est remplie, cette dernière déclenche l’action d’envoi d’e-mail configurée.

## <a name="parameterize-the-rule"></a>Paramétrer la règle

Les règles peuvent dériver certaines valeurs des **Propriétés de l’appareil** en tant que paramètres. L’utilisation de paramètres est pratique dans les scénarios où les seuils de télémétrie varient sur les différents appareils. Pendant la création de la règle, choisissez une propriété d’appareil qui spécifie le seuil, comme **Seuil maximal idéal** au lieu de fournir une valeur absolue, par exemple 80 degrés. Quand la règle s’exécute, elle correspond à la télémétrie de l’appareil avec la valeur définie dans la propriété de l’appareil.

L’utilisation de paramètres est un moyen efficace pour réduire le nombre de règles à gérer.

Vous pouvez également configurer des actions en utilisant **Propriété de l’appareil** comme paramètre. Si une adresse e-mail est stockée en tant que propriété, vous pouvez l’utiliser quand vous définissez l’adresse pour **À**.

## <a name="delete-a-rule"></a>Supprimer une règle

Si vous n’avez plus besoin d’une règle, supprimez-la en ouvrant la règle et en choisissant **Supprimer**. La suppression de la règle la supprime du modèle d’appareil et de tous les appareils associés.

## <a name="enable-or-disable-a-rule"></a>Activer ou désactiver une règle

Choisissez la règle que vous voulez activer ou désactiver. **Activez** ou **désactivez** le bouton de la règle pour activer ou désactiver la règle pour tous les appareils figurant dans la portée de la règle.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Activer ou désactiver une règle pour un appareil

Choisissez la règle que vous voulez activer ou désactiver. Ajoutez un filtre dans la section **Étendues** pour inclure ou exclure un certain appareil dans le modèle d’appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des règles dans votre application Azure IoT Central, l’étape suivante suggérée consiste à [apprendre à gérer vos appareils](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
