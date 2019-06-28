---
title: Référence de domaine prédéfini
titleSuffix: Azure
description: Référence pour les domaines prédéfinis, qui sont des collections prédéfinies d’intentions et d’entités de LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233897"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Référence de domaine prédéfinie pour votre application LUIS
Cette référence fournit des informations sur les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md), qui sont des collections prédéfinies d’intentions et d’entités proposées par LUIS.

Les [domaines personnalisés](luis-how-to-start-new-app.md), en revanche, commencent sans intentions ni modèles. Vous pouvez ajouter des intentions et entités de domaine prédéfini à un modèle personnalisé.

# <a name="supported-domains-across-cultures"></a>Domaines pris en charge dans toutes les cultures

La seule culture prise en charge est l’anglais. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Type d’entité|description|
|--|--|
|Calendrier|Le calendrier contient les réunions et les rendez-vous personnels, _pas_ les événements publics (tels que les planifications de coupe du monde, les calendriers des événements à Seattle ou les calendriers génériques (qui indiquent quel jour on est, quand l’automne commence, ou quel jour tombe le Labor Day).|
|Communication|Requêtes pour effectuer des appels, envoyer des SMS ou des messages instantanés, rechercher et ajouter des contacts, et autres demandes diverses liées à la communication (généralement sortante). Les requêtes exclusivement limitées au nom du contact ne font pas partie du domaine Communication.|
|Email|L’e-mail est un sous-domaine du domaine de la communication. Il contient principalement des demandes d’envoi et de réception de messages par e-mail.|
|HomeAutomation|Le domaine HomeAutomation fournit les intentions et entités liées au contrôle des appareils domestiques intelligents. Il prend essentiellement en charge la commande de contrôle liée à l’éclairage et à la climatisation, mais il a certaines capacités de généralisation pour les autres appareils électriques.|
|Notes|Le domaine Notes fournit les intentions et entités pour la création de notes et l’écriture d’éléments pour les utilisateurs.|
|Lieux|Lieux inclut des entreprises, des établissements, des restaurants, des espaces publics et des adresses. Le domaine prend en charge la recherche de lieux et les questions relatives à des informations sur un lieu public, par exemple un emplacement, les heures d’ouverture et la distance.|
|RestaurantReservation|Le domaine de réservation de restaurant prend en charge les intentions de gestion des réservations pour les restaurants.|
|ToDo|Le domaine ToDo fournit des types de listes de tâches pour que les utilisateurs ajoutent, marquent et suppriment leurs éléments todo.|
|Services|Le domaine Services est un domaine général parmi tous les modèles LUIS prédéfinis, qui contient des intentions et des énoncés courants dans des scénarios de différence.|
|Météo|Le domaine Météo se concentre sur la vérification des conditions météorologiques et les conseils avec indication de l’emplacement et de l’heure ou de l’heure de vérification des conditions météorologiques.|
|Web|Le domaine Web fournit les intentions et les entités pour la recherche d’un site web.|
