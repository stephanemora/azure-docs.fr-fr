---
title: Qu’est-ce que Personalizer ?
titleSuffix: Azure Cognitive Services
description: Personalizer est un service d’API cloud qui vous permet de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement en temps réel.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e5781af44732782936e1e1a87bf70bd4a9d4804d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722308"
---
# <a name="what-is-personalizer"></a>Qu’est-ce que Personalizer ?

Azure Personalizer est un service d’API cloud qui vous permet de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement en temps réel.

* Fournissez des informations sur vos utilisateurs et sur le contenu, et recevez l’action classée en premier à montrer à vos utilisateurs. 
* Il n’est pas nécessaire de nettoyer et d’étiqueter les données avant d’utiliser Personalizer.
* Fournissez un feedback à Personalizer au moment où cela vous convient. 
* Consultez une analytique en temps réel. 
* Utilisez Personalizer dans le cadre d’une démarche de science des données plus vaste pour valider des expériences existantes.

## <a name="how-does-personalizer-work"></a>Fonctionnement de Personalizer

Personalizer utilise des modèles Machine Learning pour découvrir quelle action classer en premier dans un contexte donné. Votre application cliente fournit une liste d’actions possibles, avec des informations sur celles-ci, et des informations sur le contexte, qui peuvent inclure des informations sur l’utilisateur, l’appareil, etc. Personalizer détermine l’action à effectuer. Une fois que votre application cliente utilise l’action choisie, elle fournit un feedback à Personalizer sous la forme d’un score de récompense. Une fois la boucle de rétroaction terminée, Personalizer met automatiquement à jour son propre modèle utilisé pour les classements futurs.

## <a name="how-do-i-use-the-personalizer"></a>Comment utiliser Personalizer ?

![Utilisation de Personalizer pour choisir quelle vidéo montrer à un utilisateur](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Choisissez une expérience dans votre application à personnaliser.
1. Créer et configurer le service de personnalisation dans le portail Azure
1. Utilisez le SDK pour appeler Personalizer avec des informations (_caractéristiques_) sur vos utilisateurs et le contenu (_actions_). Vous n’avez pas besoin de fournir des données nettoyées et étiquetées avant d’utiliser Personalizer. 
1. Dans l’application cliente, montrez à l’utilisateur l’action sélectionnée par Personalizer.
1. Utilisez le SDK pour fournir un feedback à Personalizer indiquant si l’utilisateur a sélectionné l’action de Personalizer. Il s’agit d’un _score de récompense_, généralement compris entre -1 et 1.
1. Visualisez l’analytique dans le portail Azure pour évaluer comment le système fonctionne et comment vos données aident à la personnalisation.

## <a name="where-can-i-use-personalizer"></a>Où puis-je utiliser Personalizer ?

Par exemple, votre application cliente peut ajouter Personalizer pour :

* Personnaliser le choix de l’article qui est mis en évidence sur un site web d’info.    
* Optimiser le positionnement des publicités sur un site web.
* Afficher un « élément recommandé » personnalisé sur un site web de vente en ligne.
* Suggérer des éléments d’interface utilisateur, comme des filtres à appliquer à une photo spécifique.
* Choisir la réponse d’un chatbot pour clarifier l’intention de l’utilisateur ou suggérer une action.
* Hiérarchiser les suggestions de ce qu’un utilisateur devrait faire à l’étape suivante d’un processus métier.

## <a name="personalization-for-developers"></a>Personnalisation pour les développeurs

Le service Personalizer a deux API :

* Envoyer des informations (_caractéristiques_) sur vos utilisateurs et le contenu (_actions_) à personnaliser. Personalizer répond avec l’action classée en premier.
* Envoyez un feedback à Personalizer indiquant dans quelle mesure le classement a fonctionné, sous la forme d’un nombre généralement compris entre 0 et 1 (la section précédente indiquait entre -1 et 1). 

![Séquence de base des événements pour la personnalisation](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une boucle de rétroaction dans C#](csharp-quickstart-commandline-feedback-loop.md)
* [Utiliser la démonstration interactive](https://personalizationdemo.azurewebsites.net/)
