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
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488819"
---
## <a name="sign-in-to-luis-portal"></a>Se connecter au portail LUIS

Un nouvel utilisateur de LUIS doit suivre cette procédure :

1. Connectez-vous au [portail LUIS](https://www.luis.ai), sélectionnez votre pays, puis acceptez les conditions d’utilisation.
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