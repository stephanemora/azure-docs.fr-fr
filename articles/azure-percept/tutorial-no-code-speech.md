---
title: Créer un Assistant vocal avec le DK Azure Percept et Azure Percept Audio
description: Découvrez comment créer et déployer une solution vocale sans code sur votre DK Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023161"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Créer un Assistant vocal avec le DK Azure Percept et Azure Percept Audio

Dans ce tutoriel, vous allez créer un Assistant vocal à partir d’un modèle en vue de l’utiliser avec votre DK Azure Percept et Azure Percept Audio. La démonstration de l’Assistant vocal s’exécute dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) et contient une sélection d’objets virtuels contrôlés par la voix. Pour contrôler un objet, prononcez votre mot clé (qui est le mot ou la phrase courte qui sort de veille votre appareil), suivi d’une commande. Chaque modèle répond à un ensemble de commandes spécifiques.

Ce guide explique comment configurer vos appareils, créer un Assistant vocal et les ressources [Speech Services](../cognitive-services/speech-service/overview.md) nécessaires, tester votre Assistant vocal, configurer votre mot clé et créer des mots clés personnalisés.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- Azure Percept Audio
- Haut-parleur ou casque qui peut être branché à une prise audio de 3,5 mm (facultatif)
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT
- [Installation d’Azure Percept Audio](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Créer un Assistant vocal à l’aide d’un modèle

1. Accédez à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Ouvrez l’onglet **Tutoriels et démonstrations**.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Capture d’écran de la page d’accueil du portail Azure.":::

1. Cliquez sur **Tester les modèles de l’Assistant vocal** sous **Tutoriels et démonstrations - Reconnaissance vocale**. Une fenêtre s’ouvre alors sur le côté droit de l’écran.

1. Effectuez les étapes suivantes dans la fenêtre :

    1. Dans le menu déroulant **IoT Hub**, sélectionnez le hub IoT auquel votre devkit est connecté.

    1. Dans le menu déroulant **Appareil**, sélectionnez votre devkit.

    1. Sélectionnez l’un des modèles disponibles de l’Assistant vocal.

    1. Cochez la case **J’accepte les conditions générales pour ce projet**.

    1. Cliquez sur **Créer**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Capture d’écran de la création d’un modèle de l’Assistant vocal.":::

1. Une fois que vous avez cliqué sur **Créer**, le portail ouvre une autre fenêtre pour créer votre ressource de reconnaissance vocale. Effectuez les étapes suivantes dans la fenêtre :

    1. Sélectionnez votre abonnement Azure dans la zone **Abonnement**.

    1. Sélectionnez le groupe de ressources de votre choix dans le menu déroulant **Groupe de ressources**. Si vous souhaitez créer un groupe de ressources à utiliser avec votre Assistant vocal, cliquez sur **Créer** sous le menu déroulant, puis suivez les invites.

    1. Pour **Préfixe de l’application**, entrez un nom. Il s’agit du préfixe de votre projet et du nom de votre commande personnalisée.

    1. Sous **Région**, sélectionnez la région dans laquelle déployer les ressources.

    1. Sous **Niveau tarifaire de prédiction LUIS**, sélectionnez **Standard** (le niveau gratuit ne prend pas en charge les demandes vocales).

    1. Cliquez sur le bouton **Créer**. Les ressources de l’application Assistant vocal seront déployées dans votre abonnement.

        > [!WARNING]
        > **Ne fermez pas** la fenêtre tant que le portail n’a pas terminé le déploiement de la ressource. La fermeture prématurée de la fenêtre peut entraîner un comportement inattendu de l’Assistant vocal. Une fois que votre ressource a été déployée, la démonstration s’affiche.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Capture d’écran de la fenêtre de sélection de l’abonnement et du groupe de ressources.":::

## <a name="test-out-your-voice-assistant"></a>Essayer l’Assistant vocal

Pour interagir avec votre Assistant vocal, prononcez le mot clé suivi d’une commande. Lorsque le SoM d’écoute reconnaît votre mot clé, l’appareil émet une sonnerie (que vous pouvez entendre si un haut-parleur ou un casque est connecté) et les voyants clignotent en bleu. Les voyants passent au bleu vif pendant le traitement de votre commande. La réponse de l’Assistant vocal à votre commande s’affiche sous forme de texte dans la fenêtre de démonstration, et elle est émise de façon audible par le biais de votre haut-parleur ou de votre casque. Le mot clé par défaut (listé en regard de **Mot clé personnalisé**) est défini sur « Ordinateur ». En outre, chaque modèle est associé à un ensemble de commandes compatibles qui vous permettent d’interagir avec des objets virtuels dans la fenêtre de démonstration. Par exemple, si vous utilisez la démonstration Hôtellerie ou Santé, dites « Ordinateur, allume la télévision » pour allumer la télévision virtuelle.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Capture d’écran de la fenêtre de la démonstration Hôtellerie.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Commandes des démonstrations Hôtellerie et Santé

Les démonstrations Hôtellerie et Santé incluent des téléviseurs virtuels, des lumières, des volets et des thermostats avec lesquels vous pouvez interagir. Les commandes suivantes (et autres variantes) sont prises en charge :

* « Allume/Éteins la lumière »
* « Allume/Éteins la télévision »
* « Allume/Éteins la climatisation »
* « Ouvre/Ferme les volets ».
* « Règle la température sur X degrés » (X correspondant à la température souhaitée, par exemple 24.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Capture d’écran de la fenêtre de la démonstration Santé.":::

### <a name="automotive-demo-commands"></a>Commandes de la démonstration Automobile

La démonstration Automobile inclut un chauffage de siège virtuel, un dégivreur et un thermostat avec lequel vous pouvez interagir. Les commandes suivantes (et autres variantes) sont prises en charge :

* « Active/Désactive le dégivreur »
* « Active/Désactive le chauffage de siège »
* « Règle la température sur X degrés » (X correspondant à la température souhaitée, par exemple 24.)
* « Augmente/Diminue la température de Y degrés »


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Capture d’écran de la fenêtre de la démonstration Automobile.":::

### <a name="inventory-demo-commands"></a>Commandes de la démonstration Inventaire

La démonstration Inventaire propose une sélection de boîtes virtuelles bleues, jaunes et vertes permettant d’interagir avec une application d’inventaire virtuel. Les commandes suivantes (et autres variantes) sont prises en charge :

* « Ajoute/Supprime les boîtes X » (X correspondant au nombre de boîtes, par exemple 4)
* « Commande/Expédie X boîtes »
* « Combien de boîtes sont en stock ? »
* « Compte les boîtes Y » (Y correspondant à la couleur des boîtes, par exemple jaune.)
* « Expédie tout le stock »


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Capture d’écran de la fenêtre de la démonstration Inventaire.":::

## <a name="configure-your-keyword"></a>Configurer votre mot clé

Vous pouvez personnaliser le mot clé pour votre application Assistant vocal.

1. Cliquez sur **Modifier** à côté de **Mot clé personnalisé** dans la fenêtre de démonstration.

1. Sélectionnez l’un des mots clés disponibles. Vous pouvez choisir parmi une sélection d’exemples de mots clés, ainsi que parmi tous les mots clés personnalisés que vous avez créés.

1. Cliquez sur **Enregistrer**.

### <a name="create-a-custom-keyword"></a>Créer un mot clé personnalisé

Vous pouvez créer votre propre mot clé pour votre application vocale. L’entraînement de votre mot clé personnalisé peut ne prendre que quelques minutes.

1. Cliquez sur **+ Créer un mot clé personnalisé** en haut de la fenêtre de démonstration. 

1. Entrez le mot clé de votre choix. Il peut s’agir d’un mot unique ou d’une expression courte.

1. Sélectionnez votre **ressource Speech** (dont la liste figure à côté de **Commande personnalisée** dans la fenêtre de démonstration et qui comprend le préfixe de votre application).

1. Cliquez sur **Enregistrer**. 

## <a name="create-a-custom-command"></a>Créer une commande personnalisée

Le portail fournit également des fonctionnalités permettant de créer des commandes personnalisées avec des ressources vocales existantes. Le terme « commande personnalisée » fait référence à l’application Assistant vocal, et non à une commande spécifique de l’application existante. En créant une commande personnalisée, vous créez un projet de reconnaissance vocale, que vous devez développer dans [Speech Studio](https://speech.microsoft.com/).

Pour créer une commande personnalisée à partir de la fenêtre de démonstration, cliquez sur **+ Créer une commande personnalisée** en haut de la page, puis effectuez les étapes suivantes :

1. Entrez un nom pour votre commande personnalisée.

1. Entrez la description de votre projet (facultatif).

1. Sélectionnez votre langue par défaut.

1. Sélectionnez votre ressource Speech.

1. Sélectionnez votre ressource LUIS.

1. Sélectionnez votre ressource de création LUIS ou créez-en une.

1. Cliquez sur **Créer**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Capture d’écran de la fenêtre de création de commandes personnalisées.":::

Une fois que vous avez créé votre commande personnalisée, vous devez accéder à [Speech Studio](https://speech.microsoft.com/) pour poursuivre le développement. Si vous ouvrez Speech Studio et ne voyez pas votre commande personnalisée dans la liste, effectuez les étapes suivantes :

1. Dans le volet de gauche d’Azure Percept Studio, cliquez sur **Speech** dans **Projets d’IA**.

1. Sélectionnez l’onglet **Commandes**.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Capture d’écran de la liste des commandes personnalisées pouvant être modifiées.":::

1. Sélectionnez la commande personnalisée que vous souhaitez développer. Cette sélection ouvre le projet dans Speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Capture d’écran de l’écran d’accueil de Speech Studio.":::

Pour plus d’informations sur le développement de commandes personnalisées, consultez la [documentation Speech Service](../cognitive-services/speech-service/custom-commands.md).

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>L’Assistant vocal a été créé mais ne répond pas aux commandes

Vérifiez les voyants LED sur la carte d’interposeur :

* Trois voyants bleus fixes indiquent que l’Assistant vocal est prêt et qu’il attend le mot clé.
* Si le voyant central (L02) est blanc, cela signifie que l’initialisation du devkit est terminée et que celui-ci doit être configuré avec un mot clé.
* Si la LED centrale (L02) clignote en blanc, cela signifie que le SoM Audio n’a pas encore terminé l’initialisation. L’initialisation peut prendre quelques minutes.

Pour plus d’informations sur les voyants LED, consultez l’article [LED](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>L’Assistant vocal ne répond pas à un mot clé personnalisé créé dans Speech Studio

Cela peut se produire si le module de reconnaissance vocale est obsolète. Effectuez les étapes suivantes pour mettre à jour le module Speech vers la dernière version :

1. Dans le menu de gauche de la page d’accueil Azure Percept Studio, cliquez sur **Appareils**.

1. Recherchez puis sélectionnez votre appareil.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Capture d’écran de la liste des appareils dans Azure Percept Studio.":::

1. Dans la fenêtre Appareils, sélectionnez l’onglet **Speech**.

1. Vérifiez la version du module Speech. Si une mise à jour est disponible, le bouton **Mettre à jour** s’affiche à côté du numéro de version.

1. Cliquez sur **Mettre à jour** pour déployer la mise à jour du module Speech. Le processus de mise à jour prend généralement de 2 à 3 minutes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez fini d’utiliser l’application Assistant vocal, effectuez les étapes suivantes pour nettoyer les ressources Speech que vous avez déployées dans ce tutoriel :

1. Sur le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources** dans le menu de gauche ou tapez « groupes de ressources » dans la barre de recherche.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Capture d’écran de la page d’accueil du portail Azure montrant le menu de gauche et les groupes de ressources.":::

1. Sélectionnez votre groupe de ressources.

1. Sélectionnez les six ressources dont le nom comprend le préfixe de votre application, puis cliquez sur l’icône **Supprimer** dans le menu du haut.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Capture d’écran des ressources Speech sélectionnées pour la suppression.":::

1. Pour confirmer la suppression, tapez **oui** dans la zone de confirmation, vérifiez que vous avez sélectionné les bonnes ressources, puis cliquez sur **Supprimer**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Capture d’écran de la fenêtre de confirmation de la suppression.":::

> [!WARNING]
> Cette opération supprimera tous les mots clés personnalisés qui ont été créés avec les ressources Speech que vous supprimez, et la démonstration de l’Assistant vocal ne fonctionnera plus.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une solution de reconnaissance vocale sans code, essayez de créer une [solution de vision sans code](./tutorial-nocode-vision.md) pour votre DK Azure Percept.