---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752834"
---
| Ressource | Limite | Commentaire |
| --- | --- | ---|
| Magasins de configurations pour niveau Gratuit | 1 magasin par abonnement |
| Magasins de configurations pour niveau Standard | Magasins illimités par abonnement | 
| Requêtes de magasins de configurations pour niveau Gratuit | 1 000 requêtes par jour  | Une fois le quota dépassé, le code d'état HTTP 429 est retourné pour toutes les requêtes jusqu'à la fin de la journée |
| Requêtes de magasins de configurations pour niveau Standard | 30 000 par heure  |Une fois le quota épuisé, les demandes peuvent renvoyer le code d’état HTTP 429 indiquant un Trop grand nombre de requêtes, jusqu’à la fin de l’heure|  
| Stockage pour niveau Gratuit | 10 Mo |
| Stockage pour niveau Standard | 1 Go |
| Clés et valeurs | 10 Ko  | Pour un seul élément clé-valeur, y compris toutes les métadonnées
