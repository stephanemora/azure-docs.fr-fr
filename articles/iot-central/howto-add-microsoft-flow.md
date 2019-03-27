---
title: Créer des flux de travail avec le connecteur Azure IoT Central dans Microsoft Flow | Microsoft Docs
description: Utiliser le connecteur IoT Central dans Microsoft Flow pour déclencher des workflows et créer, obtenir, mettre à jour, supprimer des appareils et exécuter des commandes dans les workflows.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 2c4ee6a2feb737bcafc64b1c8503c03757a53364
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497735"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Créer des flux de travail avec le connecteur IoT Central dans Microsoft Flow

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Utilisez Microsoft Flow pour automatiser les flux de travail entre les nombreux services et applications sur lesquels s’appuient les utilisateurs métier. Avec le connecteur IoT Central dans Microsoft Flow, vous pouvez déclencher des workflows quand une règle est déclenchée dans IoT Central. Dans un flux de travail déclenché par IoT Central ou toute autre application, vous pouvez utiliser les actions dans le connecteur IoT Central pour :
- Créer un appareil
- Obtenir des informations sur l’appareil
- Mettre à jour les propriétés et les paramètres d’un appareil
- Exécuter une commande sur un appareil
- Suppression d’un appareil

Consultez [ces modèles Microsoft Flow](https://aka.ms/iotcentralflowtemplates) qui connectent IoT Central à d’autres services, comme les notifications mobiles et Microsoft Teams.

## <a name="prerequisites"></a>Conditions préalables

- Application avec paiement à l'utilisation
- Un compte Microsoft personnel, scolaire ou professionnel pour se connecter à Flow ([En savoir plus sur les plans Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Déclencher un flux de travail

Cette section vous montre comment déclencher une notification mobile dans l’application mobile Flow lorsqu’une règle est déclenchée dans IoT Central. Vous pouvez créer ce flux de travail au sein de votre application IoT Central à l’aide du concepteur intégré de Microsoft Flow.

1. Commencez par [créer une règle dans IoT Central](howto-create-telemetry-rules.md). Après avoir enregistré les conditions de règle, sélectionnez le **Microsoft Flow action** comme une nouvelle action. Une fenêtre de boîte de dialogue s’ouvre pour vous permettre de configurer votre flux de travail. Vous êtes connecté dans le compte d’utilisateur IoT Central permet de vous connecter à Microsoft Flow.

    ![Créer une action Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Vous verrez une liste de flux de travail qui vous avez accès à et sont attachés à cette règle IoT Central. Cliquez sur **Explorer des modèles** ou **New > à partir d’un modèle** et vous pouvez choisir parmi tous les modèles disponibles. 

    ![Modèles Microsoft Flow disponibles](media/howto-add-microsoft-flow/flowtemplates.png)

1. Vous devrez vous connecter aux connecteurs dans le modèle que vous avez choisi. Une fois les connecteurs sont connectés, vous accédez dans le concepteur pour créer votre flux de travail. Le flux de travail comporte un déclencheur IoT Central où votre application et votre règle sont déjà renseignées.

1. Vous pouvez personnaliser le flux de travail en personnalisant les informations transmises à l’action et l’ajout de nouvelles actions. Dans cet exemple, l’action est **Notifications - m’envoyer une notification mobile**. Vous pouvez inclure du *contenu dynamique* provenant de votre règle IoT Central, en passant à votre notification des informations importantes, comme le nom de l’appareil et l’horodatage.

    > [!NOTE]
    > Sélectionnez le **plus** texte dans la fenêtre de contenu dynamique pour obtenir les valeurs de mesure et de la propriété qui a déclenché la règle.

    ![Édition d’une action dans Flow avec le volet dynamique ouvert](./media/howto-add-microsoft-flow/flowdynamicpane.png)

1. Lorsque vous avez terminé votre opération d’édition, sélectionnez **enregistrer**. Vous êtes alors dirigé vers la page qui donne une vue d’ensemble de votre flux de travail. Vous pouvez voir ici l’historique des exécutions et le partager avec d’autres collègues.

    > [!NOTE]
    > Si vous voulez que d’autres utilisateurs de votre application IoT Central modifient cette règle, vous devez la partager avec eux dans Microsoft Flow. Ajoutez leurs comptes Microsoft Flow comme propriétaires dans votre flux de travail.

1. Si vous revenez à votre application IoT Central, vous verrez que cette règle a une action de Microsoft Flow dans la zone d’Actions.

Vous pouvez également créer des workflows à l’aide du connecteur de IoT Central directement à partir de Microsoft Flow. Vous pouvez ensuite choisir quelle application IoT Central pour vous connecter à.

## <a name="create-a-device-in-a-workflow"></a>Créer un appareil dans un flux de travail

Cette section vous montre comment créer un appareil dans IoT Central quand l’utilisateur appuie sur un bouton d’un appareil mobile avec l’application mobile Microsoft Flow. Vous pouvez utiliser cette action dans Flow pour l’intégration de systèmes ERP comme Dynamics à IoT Central en créant un appareil quand un appareil est ajouté ailleurs.

1. Commencez par créer un flux de travail vide dans Microsoft Flow.

1. Recherchez **Bouton Flow pour mobile** comme déclencheur.

1. Dans ce déclencheur, ajoutez une entrée de texte nommée **Device name** (Nom de l’appareil). Changez le texte de la description en **Enter the device name of your new device** (Entrez le nom d’appareil de votre nouvel appareil).

1. Ajoutez une nouvelle action. Recherchez l’action **Azure IoT Central - Créer un appareil**.

1. Choisissez votre application, puis choisissez un modèle d’appareil pour créer un appareil dans les listes déroulantes. Vous voyez que l’action se développe pour afficher toutes les propriétés et les paramètres de l’appareil.

1. Sélectionnez le champ Nom de l’appareil. Dans le volet Contenu dynamique, choisissez **Nom de l’appareil**. Cette valeur est transmise à partir de l’entrée de l’utilisateur entre dans l’application mobile et est le nom de votre nouvel appareil dans IoT Central. Dans cet exemple, le seul champ obligatoire est le nom de l’appareil, indiqué par un astérisque rouge. Un autre modèle d’appareil peut avoir plusieurs champs obligatoires qui doivent être renseignés pour pouvoir créer un appareil.

    ![Volet de l’action de création dynamique d’un appareil dans Flow](./media/howto-add-microsoft-flow/flowcreatedevice.png)

1. (Facultatif) Renseignez les autres champs comme vous le souhaitez pour la création des appareils.

1. Pour terminer, enregistrez votre flux de travail.

1. Essayez votre flux de travail dans l’application mobile Microsoft Flow. Accédez à l’onglet **Boutons** dans l’application. Vous devez voir votre flux de travail Bouton -> Créer un appareil. Entrez le nom de votre nouvel appareil et regardez-le apparaître dans IoT Central !

    ![Capture d’écran de la création d’un appareil mobile dans Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Mettre à jour un appareil dans un flux de travail

Cette section vous montre comment mettre à jour les paramètres et les propriétés d’un appareil dans IoT Central quand l’utilisateur appuie sur un bouton d’un appareil mobile avec l’application mobile Microsoft Flow.

1. Commencez par créer un flux de travail vide dans Microsoft Flow.

1. Recherchez **Bouton Flow pour mobile** comme déclencheur.

1. Dans ce déclencheur, ajoutez une entrée de texte comme **Location** (Emplacement) qui correspond à un paramètre ou à une propriété à modifier. Changez le texte de la description.

1. Ajoutez une nouvelle action. Recherchez l’action **Azure IoT Central - Mettre à jour un appareil**.

1. Choisissez votre application dans la liste déroulante. Vous avez maintenant besoin d’un ID de l’appareil existant que vous voulez mettre à jour. Vous pouvez vous procurer l’ID de l’appareil IoT Central à partir de **Device Explorer**.

    ![ID d’appareil dans l’Explorateur d’appareils d’IoT Central](./media/howto-add-microsoft-flow/iotcdeviceid.png)

1. Vous pouvez mettre à jour le nom de l’appareil. Pour mettre à jour des propriétés et des paramètres de l’appareil, vous devez sélectionner le modèle d’appareil de l’appareil que vous voulez mettre à jour dans la liste déroulante **Modèle d’appareil**. La vignette de l’action se développe pour montrer tous les paramètres et propriétés que vous pouvez mettre à jour.

    ![Flux de travail de mise à jour d’appareil dans Flow](./media/howto-add-microsoft-flow/flowupdatedevice.png)

1. Sélectionnez les propriétés et les paramètres que vous voulez mettre à jour. Dans le volet Contenu dynamique, sélectionnez l’entrée correspondante dans le déclencheur. Dans cet exemple, la valeur de Location (Emplacement) est propagée vers le bas pour mettre à jour la propriété Location de l’appareil.

1. Pour terminer, enregistrez votre flux de travail.

1. Essayez votre flux de travail dans l’application mobile Microsoft Flow. Accédez à l’onglet **Boutons** dans l’application. Vous devez voir votre flux de travail Bouton -> Mettre à jour un appareil. Renseignez les entrées : vous voyez alors que l’appareil est mis à jour dans IoT Central !

## <a name="get-device-information-in-a-workflow"></a>Obtenir des informations de périphérique dans un flux de travail

Vous pouvez obtenir des informations sur l’appareil par son ID de périphérique à l’aide de la **Azure IoT Central - obtenir un appareil** action. Vous pouvez obtenir des informations telles que le nom de l’appareil, nom de modèle d’appareil, les valeurs de propriété et les valeurs de paramètres à passer à des actions ultérieures dans votre flux de travail. Voici un exemple de workflow qui transmet la valeur de propriété de nom de client à partir d’un appareil à Microsoft Teams.

   ![Flux de travail de flux get appareil](./media/howto-add-microsoft-flow/flowgetdevice.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Exécuter une commande sur un appareil dans un flux de travail
Vous pouvez exécuter une commande sur un périphérique spécifié par son ID de périphérique à l’aide de la **Azure IoT Central - exécuter une commande** action. Vous pouvez choisir la commande pour exécuter et transmettre les paramètres de la commande via cette action. Voici un exemple de workflow qui s’exécute une commande de redémarrage du périphérique à partir d’un bouton dans l’application mobile Microsoft Flow.

   ![Flux de travail de flux get appareil](./media/howto-add-microsoft-flow/flowrunacommand.png)

## <a name="delete-a-device-in-a-workflow"></a>Supprimer un appareil dans un flux de travail

Vous pouvez supprimer un appareil en utilisant son ID d’appareil avec l’action **Azure IoT Central - Supprimer un appareil**. Voici un exemple de flux de travail qui supprime un appareil quand l’utilisateur appuie sur un bouton dans l’application mobile Microsoft Flow.

   ![Flux de travail de suppression d’appareil dans Flow](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes pour créer une connexion au connecteur Azure IoT Central, voici quelques conseils pour vous aider.

1. Les comptes personnels Microsoft (comme les domaines @hotmail.com, @live.com ou @outlook.com) ne sont pas pris en charge pour l’instant. Vous devez utiliser un Azure Active Directory (AD) ou compte scolaire.

2. Pour utiliser le connecteur IoT Central dans Microsoft Flow, vous devez vous être connecté au moins une fois à l’application IoT Central. Sinon, l’application n’apparaît pas dans les listes déroulantes Application.

3. Si vous recevez une erreur lors de l’utilisation d’un compte Azure AD, essayez d’ouvrir Windows PowerShell et exécutez les applets de commande suivante en tant qu’administrateur.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Microsoft Flow pour générer des flux de travail, l’étape suivante suggérée consiste à [gérer les appareils](howto-manage-devices.md).

