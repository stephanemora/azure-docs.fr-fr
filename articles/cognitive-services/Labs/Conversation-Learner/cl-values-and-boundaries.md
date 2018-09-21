---
title: Configuration par défaut de l’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: En savoir plus sur la configuration par défaut de l’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b048509f38356b19d52507f4dfb5bfa61122f9f6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576423"
---
# <a name="default-values-and-boundaries"></a>Limites et valeurs par défaut

Ce document décrit la configuration par défaut de l’Apprenant de conversation et les limites du service de clé.

## <a name="default-configuration"></a>Configuration par défaut

Paramètre | Valeur par défaut
--- | --- 
Délai d’expiration de la session par défaut | 30 minutes

## <a name="boundaries"></a>Limites

Paramètre | Limite
--- | --- 
Création d’API, maximum d’appels HTTP par mois | 5 Mo
Création d’API, maximum d’appels HTTP par seconde | 25
API de session, maximum d’appels HTTP par mois | 500K
API de session, maximum d’appels HTTP par seconde | 10
Nombre maximal d’entités personnalisées (autrement que par programmation) par modèle | Consultez [doc de limites LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries) ; dans la pratique, le nombre réel peut être légèrement inférieur
Nombre maximal d’entités prédéfinies par modèle | Consultez [doc de limites LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Nombre maximal d’entités (au total) par modèle | 100
Nombre maximal d’actions par modèle | 32
Nombre maximal de dialogues d’apprentissage par modèle | 1 000
Nombre maximal de tours d’utilisateurs par dialogue d’apprentissage | 100
Nombre maximal de dialogues de journal par modèle | Aucune limite prédéfinie, mais les boîtes de dialogue de journal ne sont conservées que pendant une période fixe avant d’être supprimées.  En outre, l’interface utilisateur de l’Apprenant de conversation affiche 100 boîtes de dialogue de journal à la fois. 
Nombre maximal de modèles par utilisateur | Aucune limite prédéfinie
Nombre maximal d’actions séquentielles sans attente | 5 (*)

(*) Après 5 actions séquentielles sans attente, toutes les actions sans attente sont masquées et l’Apprenant de conversation fait son choix dans la liste des actions d’attente disponibles.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main de l’Apprenant de conversation](./quickstart.md)
