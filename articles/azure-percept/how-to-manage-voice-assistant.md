---
title: Configurer l’application Assistant vocal dans Azure Percept Studio
description: Configurer l’application Assistant vocal dans Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 593d2ccadf814cb5af27a4bb738f28132f39ab7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660267"
---
# <a name="managing-your-voice-assistant"></a>Gestion de votre Assistant vocal

Cet article explique comment configurer le mot clé et les commandes de votre application Assistant vocal dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Pour obtenir des conseils sur la configuration de votre mot clé dans IoT Hub (pas dans le portail), consultez cet [article de procédure](./how-to-configure-voice-assistant.md).

Si vous n’avez pas encore créé d’application Assistant vocal, consultez [Créer un Assistant vocal sans code avec Azure Percept Studio et Azure Percept Audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Configuration du mot clé

Un mot clé est un mot ou une expression courte utilisé pour activer un Assistant vocal. Par exemple, « Hey Cortana » est le mot clé de l’assistant Cortana. Avec l’activation de la fonction vocale, vos utilisateurs peuvent commencer à interagir avec votre produit en mode mains libres, simplement en prononçant le mot clé. Comme votre produit écoute en permanence le mot clé, toutes les données audio sont traitées localement sur l’appareil jusqu’à ce qu’une détection se produise afin de garantir que les données de l’utilisateur restent aussi privées que possible.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuration dans la fenêtre de démonstration de l’Assistant vocal

1. Cliquez sur **modifier** en regard de **Mot clé personnalisé** dans la page de démonstration.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Image.":::

    Si la page de démonstration n’est pas ouverte, accédez à la page de l’appareil (voir ci-dessous), puis cliquez sur **Tester votre Assistant vocal** sous **Actions** pour accéder à la démonstration.

1. Sélectionnez l’un des mots clés disponibles, puis cliquez sur **Enregistrer** pour appliquer les modifications.

1. Les trois voyants de l’appareil Azure Percept Audio passent au bleu vif (sans clignotement) lorsque la configuration est terminée et que votre Assistant vocal est prêt à être utilisé.

### <a name="configuration-within-the-device-page"></a>Configuration dans la page de l’appareil

1. Dans la page de présentation d’[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819), cliquez sur **Appareils** dans le volet du menu de gauche.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Image.":::

1. Sélectionnez l’appareil sur lequel votre application Assistant vocal a été déployée.

1. Ouvrez l’onglet **Speech**.

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Image.":::

1. Cliquez sur **Modifier** en regard de **Mot clé**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Image.":::

1. Sélectionnez l’un des mots clés disponibles, puis cliquez sur **Enregistrer** pour appliquer les modifications.

1. Les trois voyants de l’appareil Azure Percept Audio passent au bleu vif (sans clignotement) lorsque la configuration est terminée et que votre Assistant vocal est prêt à être utilisé.

## <a name="create-a-custom-keyword"></a>Créer un mot clé personnalisé

Avec [Speech Studio](https://speech.microsoft.com/), vous pouvez créer un mot clé personnalisé pour votre Assistant vocal. L’entraînement d’un modèle de mot clé personnalisé de base peut prendre jusqu’à 30 minutes.

Pour obtenir des conseils sur la création d’un mot clé personnalisé, consultez la [documentation de Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws). Une fois configuré, votre nouveau mot clé est disponible dans le portail Project Santa Cruz pour une utilisation avec votre application Assistant vocal.

## <a name="commands-configuration"></a>Configuration des commandes

Les commandes personnalisées facilitent la création d’applications de commandes vocales complètes, optimisées pour les expériences d’interaction de type « voice-first ». Les commandes personnalisées conviennent bien à la réalisation de tâches ou aux scénarios de commande et de contrôle.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuration dans la fenêtre de démonstration de l’Assistant vocal

1. Cliquez sur **Modifier** en regard de **Commande personnalisée** dans la page de démonstration. Si la page de démonstration n’est pas ouverte, accédez à la page de l’appareil (voir ci-dessous), puis cliquez sur **Tester votre Assistant vocal** sous **Actions** pour accéder à la démonstration.

1. Sélectionnez l’une des commandes personnalisées disponibles, puis cliquez sur **Enregistrer** pour appliquer les modifications.

### <a name="configuration-within-the-device-page"></a>Configuration dans la page de l’appareil

1. Dans la page de présentation d’[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819), cliquez sur **Appareils** dans le volet du menu de gauche.

1. Sélectionnez l’appareil sur lequel votre application Assistant vocal a été déployée.

1. Ouvrez l’onglet **Speech**.

1. Cliquez sur **Modifier** en regard de **Commande**.

1. Sélectionnez l’une des commandes personnalisées disponibles, puis cliquez sur **Enregistrer** pour appliquer les modifications.

## <a name="create-custom-commands"></a>Créer des commandes personnalisées

Avec [Speech Studio](https://speech.microsoft.com/), vous pouvez créer des commandes personnalisées que votre Assistant vocal exécute.

Pour obtenir des conseils sur la création de commandes personnalisées, consultez la [documentation de Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application). Une fois configurés, vos nouvelles commandes sont disponibles dans Azure Percept Studio pour une utilisation avec votre application Assistant vocal.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une application Assistant vocal, essayez de développer une [solution de vision sans code](./tutorial-nocode-vision.md) avec votre DK Azure Percept.