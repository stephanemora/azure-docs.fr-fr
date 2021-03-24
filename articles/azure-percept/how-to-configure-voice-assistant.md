---
title: Configurer l’application Assistant vocal avec Azure IoT Hub
description: Configurer l’application Assistant vocal avec Azure IoT Hub
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: b22ef4ee0a8b5978bb2ec1c02fadf368815f3014
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095780"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Configurer l’application Assistant vocal avec Azure IoT Hub

Cet article explique comment configurer votre application Assistant vocal avec IoT Hub. Pour obtenir un tutoriel pas à pas qui vous guide tout au long du processus de création d’un Assistant vocal à l’aide d’un modèle de démonstration, consultez [Créer un Assistant vocal sans code avec Azure Percept Studio et Azure Percept Audio](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Mettre à jour la configuration de votre Assistant vocal

1. Ouvrez le [portail Azure](https://portal.azure.com) et tapez **IoT Hub** dans la barre de recherche. Cliquez sur l’icône pour ouvrir la page IoT Hub.

1. Dans la page IoT Hub, sélectionnez le hub IoT sur lequel votre appareil a été provisionné.

1. Sélectionnez **IoT Edge** sous **Gestion automatique des appareils** dans le menu de navigation de gauche pour afficher tous les appareils connectés à votre hub IoT.

1. Sélectionnez l’appareil sur lequel votre application Assistant vocal a été déployée.

1. Cliquez sur **Définir des modules**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Capture d’écran de la page de l’appareil avec Définir les modules encadré.":::

1. Vérifiez que l’entrée suivante est présente sous la section **Informations d’identification du registre de conteneurs**. Ajoutez des informations d’identification si nécessaire.

    |Nom|Adresse|Nom d’utilisateur|Mot de passe|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. Dans la section **Modules IoT Edge**, sélectionnez **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Capture d’écran montrant la liste de tous les modules IoT Edge de l’appareil.":::

1. Cliquez sur l’onglet **Paramètres du module**. Vérifiez la configuration suivante :

    URI d’image|Stratégie de redémarrage|État souhaité
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule:preload-devkit|toujours|exécution en cours

    Si vos paramètres ne correspondent pas, modifiez-les et cliquez sur **Mettre à jour**.

1. Cliquez sur l’onglet **Variables d’environnement**. Vérifiez qu’aucune variable d’environnement n’est définie.

1. Cliquez sur l’onglet **Paramètres du jumeau de module**. Mettez à jour la section **speechConfigs** comme ceci :

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Le mot clé utilisé ci-dessus est un mot clé par défaut disponible publiquement. Si vous souhaitez utiliser le vôtre, vous pouvez ajouter votre propre mot clé personnalisé en chargeant un fichier de table créé sur le stockage d’objets blob. Le stockage d’objets blob doit être configuré avec un accès anonyme aux conteneurs ou aux objets blob.

## <a name="how-to-find-out-appid-key-and-region"></a>Comment trouver l’ID d’application, la clé et la région

Pour localiser votre **ID d’application**, votre **clé** et votre **région**, accédez à [Speech Studio](https://speech.microsoft.com/) :

1. Connectez-vous et sélectionnez la ressource vocale appropriée.
1. Dans la page d’accueil de **Speech Studio** , cliquez sur **Commandes personnalisées** sous **Assistants vocaux**.
1. Sélectionnez votre projet cible.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Capture d’écran de la page de projet dans Speech Studio.":::

1. Cliquez sur **Paramètres** dans le panneau du menu de gauche.
1. L’**ID d’application** et la **clé** se trouvent sous l’onglet **Général** des paramètres.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Capture d’écran des paramètres généraux du projet vocal.":::

1. Pour trouver votre **région**, ouvrez l’onglet **Ressources LUIS** dans les paramètres. La sélection **Ressource de création** contient des informations sur la région.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Capture d’écran des ressources LUIS du projet vocal.":::

1. Après avoir entré vos informations **speechConfigs**, cliquez sur **Mettre à jour**.

1. Cliquez sur l’onglet **Routes** situé en haut de la page **Définir des modules**. Vérifiez que vous disposez d’une route avec la valeur suivante :

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Ajoutez la route si elle n’existe pas.

1. Cliquez sur **Revoir + créer**.

1. Cliquez sur **Créer**.


## <a name="next-steps"></a>Étapes suivantes

Après la mise à jour de votre configuration d’Assistant vocal, revenez à la démonstration dans Azure Percept Studio pour interagir avec l’application.

