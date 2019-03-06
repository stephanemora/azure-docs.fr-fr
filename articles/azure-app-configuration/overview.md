---
title: Définition d’Azure App Configuration | Microsoft Docs
description: Vue d’ensemble du service Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885018"
---
# <a name="what-is-azure-app-configuration"></a>Définition d’Azure App Configuration

Azure App Configuration offre un service de gestion centralisée des paramètres d’application. Les programmes modernes, en particulier ceux qui s’exécutent dans un cloud, ont généralement de nombreux composants qui sont par nature distribués. La répartition des paramètres de configuration sur tous ces composants peut rendre les erreurs difficiles à corriger pendant le déploiement d’une application. App Configuration vous permet de stocker tous les paramètres de votre application à un seul endroit et ainsi de sécuriser leurs accès.

## <a name="why-use-app-configuration"></a>Pourquoi utiliser App Configuration

Souvent, les applications cloud s’exécutent sur plusieurs machines virtuelles ou conteneurs situés dans plusieurs régions et elles utilisent plusieurs services externes. La création d’une application distribuée de ce type à la fois robuste et scalable est un véritable défi. Différentes méthodologies de programmation ont vu le jour pour aider les développeurs à gérer la complexité croissante de la génération de ces applications. Par exemple, l’application 12 facteurs décrit de nombreux modèles architecturaux bien testés ainsi que les bonnes pratiques à suivre pour les applications cloud. L’une des principales recommandations de ce guide consiste à séparer la configuration du code. Cela signifie que la configuration d’une application, comme les paramètres, doit être conservée à l’écart de son exécutable et lue à partir de son environnement d’exécution ou d’une source externe.

Même si n’importe quelle application peut utiliser App Configuration, les types d’applications suivants sont les meilleurs candidats :

* Microservices basés sur Azure Container Service, Service Fabric ou d’autres applications conteneurisées déployées dans une ou plusieurs des zones géographiques.
* Applications serverless, notamment Azure Functions ou d’autres applications de calcul sans état basées sur les événements.
* Pipeline de déploiement continu.

App Configuration offre les avantages suivants :

* Service complètement managé configurable en quelques minutes.
* Représentations et mappages de clés flexibles.
* Marquage avec des étiquettes.
* Relecture des paramètres à un point dans le temps.
* Comparaison de deux jeux de configurations sur les dimensions personnalisées.
* Sécurité renforcée par le biais d’identités managées par Azure.
* Chiffrements complets des données au repos ou en transit.
* Intégration native à des frameworks populaires.

## <a name="how-to-use-app-configuration"></a>Comment utiliser App Configuration

Le moyen le plus simple d’ajouter un magasin de configuration d’application à votre application fait intervenir une bibliothèque cliente fournie par Microsoft. Selon le langage de programmation et le framework, voici les meilleures méthodes à votre disposition :

| Langage de programmation et framework | Comment se connecter |
|---|---|
| **.NET Core** et **ASP.NET Core** | Fournisseur de configuration App Configuration pour .NET Core. |
| **.NET** et **ASP.NET** | Générateur de configuration App Configuration pour .NET. |
| **Java Spring** | Client de configuration App Configuration pour Spring cloud. |
| Autres | API REST App Configuration. |

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une application web ASP.NET](quickstart-aspnet-core-app.md)  
