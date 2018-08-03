---
title: Comment utiliser le contrôle de version et le marquage avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser le contrôle de version et le marquage avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170543"
---
# <a name="how-to-use-versioning-and-tagging"></a>Comment utiliser le contrôle de version et le marquage

Ce tutoriel montre comment marquer des versions de votre modèle d’Apprenant de conversation et définir la version « active ».  

## <a name="requirements"></a>Configuration requise
Ce tutoriel requiert l’utilisation de l’émulateur bot pour créer des boîtes de dialogue de journal, pas l’interface utilisateur web de la boîte de dialogue de journal.  

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution :

    npm run tutorial-general

## <a name="details"></a>Détails

Lors de modifications, vous modifiez toujours la balise appelée « maître ». Vous pouvez créer des versions à partir du maître (qui effectue, en fait, une capture instantané du maître), mais vous ne pouvez pas modifier de versions avec balise.

## <a name="steps"></a>Étapes

### <a name="install-the-bot-framework-emulator"></a>Installez l’émulateur Bot Framework

- Accédez à [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Téléchargez et installez l’émulateur.

### <a name="create-an-model"></a>Créer un modèle

1. Cliquez sur Nouveau modèle
2. Dans le champ Nom, entrez le Tutorial-16-Versioning
3. Click Create 
4. Cliquez sur Paramètres
5. Copiez l’ID du modèle

### <a name="configure-the-emulator"></a>Configurez l’émulateur

- Dans le dossier racine de l’Apprenant de conversation, ouvrez le fichier .env.
- Collez l’ID du modèle en tant que la valeur de CONVERSATION_LEARNER_MODEL_ID
- Redémarrez le service d’Apprenant de conversation en sortant de l’invite de commande et en réexécutant :
 
    npm run tutorial-general 

### <a name="actions"></a>Actions

Créons une action :

1. Se connecter à l’interface utilisateur web.
1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans réponse, entrez « bonjour (version 1) ».
3. Cliquez sur Enregistrer.


![](../media/tutorial16_action1.PNG)

Créer une balise :

3. Cliquez sur « paramètres » et créez une « balise ».
    - Appelez-la « version 1 »
4. Définissez « version 1 » sur « active ».  
    - L’effet de la définition de la balise active sur « version 1 » est que les canaux utilisant ce robot utiliseront la balise « version 1 ».
    - Les versions avec balise des modèles ne sont pas affectées par les modifications (modification d’actions, d’entités, ajout de boîtes de dialogue d’apprentissage).  
    - Les modifications apportées à un modèle (en modifiant les actions, entités, en ajoutant des boîtes de dialogue train) sont toujours effectuées sur la balise « maître ».  En d’autres termes, « maître » est la seule balise modifiable ; les autres balises sont des captures instantanées fixes.
    - Les boîtes de dialogue de journal dans l’interface utilisateur de l’Apprenant de conversation utilisent toujours le maître (pas la balise active).

![](../media/tutorial16_v1_create.PNG)

La version a été créée dans les paramètres :

![](../media/tutorial16_settings.PNG)

Ajoutons une deuxième action :

1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans Réponse, entrez « au revoir (version 2) ».

Modifiez la première action :

1. Cliquez sur Actions.
2. Sous Actions, cliquez sur « bonjour (version 1) ».
3. Changez la réponse en « bonjour (version 2) ».

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Basculez vers l’émulateur bot

1. Dans l’interface utilisateur du bot, entrez « au revoir ».
2. Le bot répond « bonjour (version 1) ».
    - Cela indique que la version 1 est « active ». 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Connectez-vous à l’interface utilisateur web

1. Cliquez sur les boîtes de dialogue journal (si vous ne voyez pas de boîtes de dialogue, cliquez sur le bouton Actualiser).
2. Cliquez sur « bonjour (version 2) »

> [!NOTE]
> Nous pouvons apporter des corrections en choisissant parmi les actions actuellement disponibles. Ces modifications seront effectuées sur le maître.

Vous savez maintenant comment le contrôle de version fonctionne et comment vous pouvez interagir avec le bot à l’aide de l’émulateur Bot Framework.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démonstration : réinitialisation du mot de passe](./demo-password-reset.md)
