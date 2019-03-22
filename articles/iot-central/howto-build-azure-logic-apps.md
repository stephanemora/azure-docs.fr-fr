---
title: Créer des flux de travail avec le connecteur IoT Central dans Azure Logic Apps | Microsoft Docs
description: Utilisez le connecteur IoT Central dans Azure Logic Apps pour déclencher des flux de travail, et créer, mettre à jour et supprimer des appareils dans les flux de travail.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759880"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Créer des flux de travail avec le connecteur IoT Central dans Azure Logic Apps

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Utilisez Azure Logic Apps pour créer des flux de travail évolutifs et automatisés intégrant les applications et les données sur des services cloud et des systèmes locaux. Azure Logic Apps dispose de nombreux connecteurs dans de nombreux services Azure, services Microsoft et autres produits SaaS (Software as a Service). Avec le connecteur IoT Central dans Azure Logic Apps, vous pouvez déclencher des workflows quand une règle est déclenchée dans IoT Central. Vous pouvez également utiliser les actions dans le connecteur IoT Central pour créer un appareil, mettre à jour les propriétés et les paramètres d’un appareil, ou supprimer un appareil.

Vous pouvez utiliser le connecteur IoT Central dans Microsoft Flow. Azure Logic Apps et Microsoft Flow sont des services d’intégration orientés concepteur, mais ciblent des publics légèrement différents. Flow permet à n’importe quel employé de bureau de créer les flux de travail métier dont il a besoin. Logic Apps permet aux professionnels de l’informatique de créer les intégrations dont ils ont besoin pour connecter des données. Comparez Flow et Logic Apps [ici](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Découvrez comment créer des flux de travail avec le connecteur IoT Central dans Microsoft Flow [ici](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Conditions préalables

- Application avec paiement à l'utilisation
- Compte et abonnement Azure pour créer et gérer des applications logiques

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Déclencher un workflow quand une règle est déclenchée

Cette section vous explique comment publier un message dans Microsoft Teams lorsqu'une règle est déclenchée. Vous pouvez configurer votre flux de travail pour utiliser d’autres connecteurs afin d'effectuer différentes opérations, notamment : envoyer un événement à votre Event Hub, créer un élément de travail Azure DevOps ou insérer une nouvelle ligne dans SQL Server.

1. Commencez par [créer une règle dans IoT Central](howto-create-telemetry-rules.md). Après avoir enregistré les conditions de règle, sélectionnez le **Azure Logic Apps** vignette comme une nouvelle action. Sélectionnez **créer dans le portail Azure**. Vous accédez au portail Azure pour créer une application logique. Vous serez peut-être amené à vous connecter à votre compte Azure.

1. Entrez les informations requises pour créer une nouvelle application logique. Vous pouvez sélectionner un abonnement Azure dans lequel configurer votre nouvelle application logique. Vous n'êtes pas tenu d'utiliser le même abonnement que celui dans lequel votre application IoT Central a été créée. Sélectionnez **Créer**.

    ![Créer une application logique dans le portail Azure](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Une fois que votre application logique a été créée avec succès, vous sont parcourues automatiquement pour le concepteur Logic Apps. Sélectionnez **logique application vide**. 

    ![Créer une application logique vide](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. Dans le concepteur, recherchez « iot central », puis sélectionnez le déclencheur **Lorsqu'une règle est déclenchée**. Connectez-vous au connecteur en utilisant le compte avec lequel vous vous connectez à votre application IoT Central.

    ![Se connecter au connecteur IoT Central](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Une fois la connexion établie, plusieurs champs doivent s'afficher. Sélectionnez **Application** et **Règle** dans les menus déroulants.

    ![Sélectionner une application et une règle](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Ajoutez une nouvelle action. Recherchez les **équipes pour lesquelles publier un message** et sélectionnez **Publier un message** à partir du connecteur Microsoft Teams. Connectez-vous au connecteur à l'aide du compte que vous utilisez dans Microsoft Teams.

1. Dans l’action, sélectionnez **Équipe** et **Canal**. Renseignez le champ **Message** à votre convenance. Vous pouvez inclure du *contenu dynamique* provenant de votre règle IoT Central, en passant à votre notification des informations importantes, comme le nom de l’appareil et l’horodatage.
    > [!NOTE]
    > Sélectionnez le **plus** texte dans la fenêtre de contenu dynamique pour obtenir les valeurs de mesure et de la propriété qui a déclenché la règle.

    ![Action d'édition d'une application logique avec le volet dynamique ouvert](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Lorsque vous avez terminé votre opération d’édition, sélectionnez **enregistrer**.

1. Si vous revenez à votre application IoT Central, vous voyez que cette règle a une action Azure Logic Apps sous la zone Actions.

Vous pouvez toujours commencer à créer un flux de travail avec le connecteur IoT Central dans Azure Logic Apps, dans le portail Azure. Vous pouvez ensuite choisir quelle application IoT Central et la règle à se connecter à, et il fonctionne toujours la même façon. Vous n'êtes pas tenu de démarrer à chaque fois depuis la page de règles IoT Central.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Créer, mettre à jour et supprimer un appareil dans un flux de travail

Lorsque vous créez un flux de travail dans votre application logique, vous pouvez ajouter une action à l’aide du connecteur IoT Central. Les actions disponibles sont les **créer un appareil**, **mettre à jour un appareil**, et **supprimer un appareil**.

> [!NOTE]
> Pour **mettre à jour un appareil** et **supprimer un appareil**, vous devrez utiliser l'ID de l’appareil que vous souhaitez mettre à jour ou supprimer. Vous pouvez vous procurer l'ID de l'appareil IoT Central à partir de **Device Explorer**

![ID d’appareil dans l’Explorateur d’appareils d’IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Microsoft Flow pour générer des flux de travail, l’étape suivante suggérée consiste à [gérer les appareils](howto-manage-devices.md).
