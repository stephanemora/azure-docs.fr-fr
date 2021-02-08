---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493464"
---
+ Un [groupe de ressources](../articles/azure-resource-manager/management/overview.md), qui est un conteneur logique pour les ressources associées.
+ Un [compte Stockage Azure](../articles/storage/common/storage-account-create.md) standard, qui conserve l’état et d’autres informations spécifiques à vos projets.
+ Un plan de consommation, qui définit l’hôte sous-jacent pour votre application de fonction serverless. 
+ Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction vous permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources au sein du même plan d’hébergement.
+ Une instance Application Insights connectée à l’application de fonction, qui effectue le suivi de l’utilisation de votre fonction serverless.