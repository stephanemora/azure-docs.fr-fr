---
title: Déboguer vos API à l’aide du suivi des demandes dans la Gestion des API Azure | Microsoft Docs
description: Suivez les étapes de ce didacticiel pour apprendre à inspecter les étapes du traitement des demandes dans la Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: fc5e8c7a7aa0d4693d96c3405ec0e180a6d13f8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75768523"
---
# <a name="debug-your-apis-using-request-tracing"></a>Déboguer vos API à l’aide du suivi des demandes

Ce didacticiel explique comment inspecter le traitement des demandes pour mieux déboguer et résoudre les problèmes liés à votre API. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Suivre un appel

![Inspecteur d’API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Conditions préalables requises

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

## <a name="trace-a-call"></a>Suivre un appel

![Suivi API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Sélectionnez **API**.
2. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
3. Basculez vers l’onglet **Test**.
4. Sélectionnez l’opération **GetSpeakers**.
5. Veillez à inclure un en-tête HTTP nommé **Ocp-Apim-Trace** dont la valeur est définie sur **true**.

   > [!NOTE]
   > * Si Ocp-Apim-Subscription-Key n’est pas rempli automatiquement, vous pouvez le récupérer en accédant au portail des développeurs et en exposant les clés sur la page de profil.
   > * Pour obtenir une trace quand l’en-tête HTTP Ocp-Apim-Trace est utilisé, le paramètre **Autoriser le suivi** de la clé d’abonnement doit être activé. Pour configurer le paramètre **Autoriser le suivi**, sous **Gestion des API** dans le menu de gauche, sélectionnez **Abonnements**.
   >   ![Autoriser le traçage dans le volet des abonnements à Gestion des API](media/api-management-howto-api-inspector/allowtracing.png)

6. Cliquez sur **Envoyer** pour effectuer un appel d’API. 
7. Attendez la fin de l’appel. 
8. Accédez à l’onglet **Trace** dans la **console d’API**. Vous pouvez cliquer sur les liens suivants pour accéder aux informations de suivi détaillées : **Entrant**, **Principal**, **Sortant**.

    La section **Entrant** contient la demande d’origine reçue par le service Gestion des API de la part de l’appelant et toutes les stratégies appliquées à la demande, y compris les stratégies rate-limit et set-header que nous avons ajoutées à l’étape 2.

    La section **Principal** affiche les demandes envoyées par le service Gestion des API au backend d’API et la réponse qu’il a reçue.

    La section **Sortant** présente toutes les stratégies appliquées à la réponse avant son envoi à l’appelant.

    > [!TIP]
    > Chaque étape indique également le temps écoulé depuis la réception de la demande par le service Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Suivre un appel

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Utiliser des révisions](api-management-get-started-revise-api.md)
