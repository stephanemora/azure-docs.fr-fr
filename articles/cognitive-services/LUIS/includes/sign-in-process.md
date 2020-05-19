---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5e2a74f69f575f4b9b332b98d7e19089418b29ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585199"
---
## <a name="sign-in-to-luis-portal"></a>Se connecter au portail LUIS

Un nouvel utilisateur de LUIS doit suivre cette procédure :

1. Connectez-vous au [portail LUIS](https://www.luis.ai), sélectionnez votre pays, puis acceptez les conditions d’utilisation. Si vous voyez à la place **Mes applications**, c’est qu’une ressource LUIS existe déjà et vous devez passer directement à la création d’une application.

1. Sélectionnez **Create Azure resource** (Créer une ressource Azure), puis **Create an authoring resource to migrate your apps to** (Créer une ressource de création vers laquelle migrer vos applications).

    ![Choisir un type de ressource de création Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Renseignez les détails de la ressource.

    ![Créer une ressource de création](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Quand vous **créez une ressource de création**, fournissez les informations suivantes :

    * **Resource name** (Nom de la ressource) : nom personnalisé que vous choisissez, utilisé comme élément de l’URL pour vos requêtes de point de terminaison de création et de prédiction.
    * **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé.
    * **Subscription name** (Nom de l’abonnement) : abonnement auquel la ressource sera facturée.
    * **Resource group** (Groupe de ressources) : nom de groupe de ressources personnalisé que vous choisissez ou que vous créez. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.
    * **Location** (Emplacement) : le choix de l’emplacement est basé sur la sélection du groupe de ressources (**Resource group)** .
    * **Pricing tier** (Niveau tarifaire) : le niveau tarifaire détermine la transaction maximale par seconde et par mois.

1. Un récapitulatif de la ressource à créer s’affiche. Sélectionnez **Suivant**.

    ![Créer une ressource de création](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirmez en sélectionnant **Continuer**.

    ![Créer une ressource de création](../media/sign-in/sign-in-confirm-continue.png)