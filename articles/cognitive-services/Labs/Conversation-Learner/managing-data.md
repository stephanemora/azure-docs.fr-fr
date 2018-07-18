---
title: Gestion des données utilisateur avec l’Apprenant de conversation – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment gérer les données utilisateur avec l’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369572"
---
# <a name="managing-user-data"></a>Gestion des données utilisateur

Cette page décrit ce que le service cloud Apprenant de conversation enregistre lors de la gestion des dialogues avec les utilisateurs finaux.  Elle décrit également comment associer les journaux Apprenant de conversation à l’ID d’utilisateur, afin que vous puissiez récupérer ou supprimer tous les journaux associés à un utilisateur particulier.

## <a name="overview-of-end-user-data-logging"></a>Vue d’ensemble de la journalisation des données de l’utilisateur final

Par défaut, le service cloud Apprenant de conversation enregistre les interactions entre les utilisateurs finaux et votre robot.  Ces journaux sont importants pour améliorer votre robot, car ils vous permettent d’identifier les cas où votre robot a extrait une entité incorrecte ou sélectionné une action incorrecte.  Ces erreurs peuvent ensuite être corrigées en accédant à la page « Consigner les dialogues » de l’interface utilisateur, en apportant des corrections et en stockant un dialogue corrigé en tant que nouveau dialogue d’apprentissage. Pour plus d’informations, consultez le didacticiel sur « Consigner les dialogues ».

## <a name="how-to-disable-logging"></a>Comment désactiver la journalisation

Vous pouvez décider si les conversations avec les utilisateurs finaux doivent s’afficher sur la page « Paramètres » de votre application Apprenant de conversation.  Il existe une case à cocher pour « Consigner les dialogues ».  En désactivant cette case, les conversations avec les utilisateurs finaux ne seront pas enregistrées.

## <a name="what-is-logged"></a>Quels sont les éléments consignés ? 

Dans les journaux de dialogue, Apprenant de conversation stocke l’entrée utilisateur, les valeurs d’entité, les actions sélectionnées et les horodatages pour chaque tour.  Ces journaux sont stockés pendant une certaine période, puis ignorés (voir la page d’aide sur les valeurs et limites par défaut pour plus d’informations).  

Apprenant de conversation crée un ID unique pour chaque dialogue consigné.  Apprenant de conversation ne stocke *pas* d’identificateur d’utilisateur avec les dialogues consignés.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Association des dialogues consignés à un ID d’utilisateur

Il est souvent important de pouvoir associer les dialogues consignés à l’ID de l’utilisateur--par exemple, pour être en mesure de récupérer ou supprimer les dialogues consignés d’un utilisateur particulier.  Étant donné qu’Apprenant de conversation ne stocke pas d’identificateur d’utilisateur, cette association doit être gérée par le code du développeur.  

Pour créer ce mappage, obtenez l’ID du dialogue consigné dans `EntityDetectionCallback` ; puis, dans le stockage de votre robot, stockez l’association entre l’ID d’utilisateur et ce dialogue consigné.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>En-têtes pour les appels HTTP

Dans chacun des appels HTTP ci-dessous, ajoutez l’en-tête suivant :

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

où `<LUIS_AUTHORING_KEY>` représente la clé de création LUIS utilisée pour accéder à vos applications Apprenant de conversation.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Comment obtenir les données brutes d’un dialogue consigné

Pour obtenir les données brutes d’un dialogue consigné, vous pouvez utiliser cet appel HTTP :

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Où `<appId>` représente le GUID de cette application Apprenant de conversation, et `<logDialgoId>` l’ID du dialogue consigné que vous souhaitez récupérer.  

Notez que les dialogues consignés peuvent être modifiés par le développeur et ensuite stockés en tant que dialogues d’apprentissage.  Une fois l’opération effectuée, Apprenant de conversation stocke l’ID du dialogue « source » consigné avec le dialogue d’apprentissage.  En outre, un dialogue d’apprentissage peut être relié par « branches » dans l’interface utilisateur ; si un dialogue d’apprentissage a un ID de dialogue consigné source associé, les branches de ce dialogue d’apprentissage seront marquées avec le même ID de dialogue consigné.

Pour obtenir tous les dialogues d’apprentissage dérivés d’un dialogue consigné, procédez comme suit.

Récupérez tout d’abord tous les dialogues d’apprentissage :

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Où `<appId>` représente le GUID de cette application Apprenant de conversation.  

Cela renvoie tous les dialogues d’apprentissage.  Dans cette liste, recherchez le `sourceLogDialogId` associé et notez le `trainDialogId` associé. 

Pour récupérer un seul dialogue d’apprentissage par ID :

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Où `<appId>` représente le GUID de cette application Apprenant de conversation, et `<trainDialogId>` l’ID du dialogue consigné que vous souhaitez récupérer.  

## <a name="how-to-delete-a-logged-dialog"></a>Comment supprimer un dialogue consigné

Si vous souhaitez supprimer un dialogue consigné en fonction de son ID, vous pouvez utiliser cet appel HTTP :

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Où `<appId>` représente le GUID de cette application Apprenant de conversation, et `<logDialogId>` l’ID du dialogue consigné que vous souhaitez supprimer. 

Si vous souhaitez supprimer un dialogue d’apprentissage en fonction de son ID, vous pouvez utiliser cet appel HTTP :

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Où `<appId>` représente le GUID de cette application Apprenant de conversation, et `<trainDialogId>` l’ID du dialogue d’apprentissage que vous souhaitez supprimer. 
