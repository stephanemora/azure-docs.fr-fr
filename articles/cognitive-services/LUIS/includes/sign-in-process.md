---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 5e7feac35ddd607d36cb0ddd3bae10cf1b9bc7a7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278601"
---
## <a name="sign-in-to-luis-portal"></a>Se connecter au portail LUIS

Un nouvel utilisateur de LUIS doit suivre cette procédure :

1. Connectez-vous au [portail LUIS (préversion)](https://preview.luis.ai), sélectionnez votre pays, puis acceptez les conditions d’utilisation. Si vous voyez à la place **Mes applications**, c’est qu’une ressource LUIS existe déjà et vous devez passer directement à la création d’une application.

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