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
ms.openlocfilehash: 6c0ecf70935d4504472156f6bda726d94f130dbb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148176"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Référence de domaine prédéfinie pour votre application LUIS
Cette référence fournit des informations sur les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md), qui sont des collections prédéfinies d’intentions et d’entités proposées par LUIS.

Les [domaines personnalisés](luis-how-to-start-new-app.md), en revanche, commencent sans intentions ni modèles. Vous pouvez ajouter des intentions et entités de domaine prédéfini à un modèle personnalisé.

# <a name="supported-domains-across-cultures"></a>Domaines pris en charge dans toutes les cultures

La culture prise en charge uniquement est anglais. 

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
|Calendrier|Calendrier est quoi que ce soit sur des réunions de personnelles et les rendez-vous, _pas_ événements publics (tels que les planifications de coupe du monde, calendriers des événements Seattle ou génériques (telles que le jour est-il aujourd'hui, ce qui automne commence, lorsque est Labor Day).|
|Communication|Requêtes pour effectuer des appels, envoyer des SMS ou des messages instantanés, rechercher et ajouter des contacts et divers autres demandes liées à la communication (généralement sortants). Nom du contact seules les requêtes n’appartiennent pas au domaine de la Communication.|
|Email|Adresse de messagerie est un sous-domaine du domaine de Communication. Il contient principalement des demandes pour envoyer et recevoir des messages via des e-mails.|
|HomeAutomation|Le domaine HomeAutomation fournit les intentions et entités liées au contrôle des appareils intelligents. Il prend en charge la commande de contrôle liée à des lumières et climatiseur principalement mais il possède certaines capacités de généralisation pour les autres appareils électriques.|
|Notes|Domaine de Remarque fournit les intentions et entités pour la création de notes de publication et l’écriture des éléments pour les utilisateurs.|
|Lieux|Emplacements incluent les entreprises, les institutions, les restaurants, les espaces publics et adresses. Le domaine prend en charge la recherche et de poser sur les informations d’un lieu public comme emplacement, le fonctionnement des heures et la distance.|
|RestaurantReservation|Domaine de réservation de restaurant prend en charge les intentions pour la gestion des réservations pour les restaurants.|
|ToDo|ToDo domaine fournit des types de listes des tâches pour les utilisateurs d’ajouter, de marquer et de supprimer leurs éléments todo.|
|ToDo_IPA|Selon le domaine de ToDo, ToDo_IPA est une version personnalisée pour développer les entités dans ToDo. La version de fichier IPA fournit les intentions et entités pour prendre en charge de la reconnaissance d’un type de liste todo. Le modèle définit trois types de listes de tâches : liste de courses, liste de courses et liste de tâches qui couvrent d’autres cas.|
|Services|Utilitaires domaine est un général entre LUIS tous les modèles prédéfinis qui contient les intentions et les énoncés dans les scénarios de différence courants.|
|Météo|Domaine de météo se concentre sur la vérification des conditions météorologiques et conseils avec l’emplacement et l’heure ou la vérification de temps par les conditions météorologiques.|
|Web|Le domaine Web fournit les intentions et les entités pour la recherche d’un site Web.|
