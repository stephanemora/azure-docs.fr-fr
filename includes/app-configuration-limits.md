---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 16929503e0710b61a5dd0cd0057059487369f8b5
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122636571"
---
| Ressource | Limite | Commentaire |
| --- | --- | ---|
| Magasins de configurations pour niveau Gratuit | 1 magasin par abonnement |
| Magasins de configurations pour niveau Standard | Magasins illimités par abonnement | 
| Requêtes de magasins de configurations pour niveau Gratuit | 1 000 requêtes par jour  | Une fois le quota dépassé, le code d'état HTTP 429 est retourné pour toutes les requêtes jusqu'à la fin de la journée |
| Requêtes de magasins de configurations pour niveau Standard | 30 000 par heure  |Une fois le quota épuisé, les demandes peuvent renvoyer le code d’état HTTP 429 indiquant un Trop grand nombre de requêtes, jusqu’à la fin de l’heure|  |
| Stockage pour niveau Gratuit | 10 Mo |
| Stockage pour niveau Standard | 1 Go |
| Taille de la demande | 10 Ko  | La taille est par demande, y compris toutes les métadonnées 
