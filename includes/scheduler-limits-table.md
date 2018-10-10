---
title: Fichier Include
description: Fichier Include
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006474"
---
Le tableau suivant décrit chaque quota, limite, valeur par défaut et limitation principaux dans Azure Scheduler.

| Ressource | Description de la limite |
| -------- | ----------------- |
| **Taille du travail** | La taille maximale du travail est de 16 Ko. Si une opération PUT ou PATCH génère un travail qui dépasse ces limites de taille, le code d’état 400 Requête incorrecte est retourné. | 
| **Collections de travaux** | Le nombre maximal de collections de travaux par abonnement Azure est de 200 000. | 
| **Travaux par collection** | Par défaut, le nombre maximal de travaux est de cinq pour les collections gratuites, et de cinquante pour les collections standard. <p>Vous pouvez modifier le nombre maximal de travaux que peut contenir une collection. Le nombre de travaux d’une collection est limité à la valeur définie dans cette collection. Si vous tentez de créer plus de travaux que le quota maximal, la requête échoue avec le code d’état 409 Conflit. | 
| **Délai jusqu'à l'heure de début** | Le « délai jusqu’à l’heure de début » maximal est de 18 mois. |
| **Période de récurrence** | La période de récurrence maximale est 18 mois. | 
| **Fréquence** | Par défaut, le quota de fréquence maximale est d’une heure dans une collection de travaux gratuite, et d’une minute dans une collection de travaux standard. <p>Vous pouvez abaisser la valeur de fréquence maximale d’une collection de travaux. Le nombre de travaux d’une collection est limité à la valeur définie dans cette collection. Si vous tentez de créer un travail avec une fréquence supérieure à la fréquence maximale définie dans la collection de travaux, la requête échoue avec le code d’état 409 Conflit. | 
| **Taille du corps** | Le corps d’une requête peut comprendre un maximum de 8 192 caractères. |
| **Taille d'URL de la requête** | L’URL d’une requête peut comprendre un maximum de 2 048 caractères. |
| **Nombre d'en-têtes** | Le nombre maximal d’en-têtes est de 50. | 
| **Taille de l'en-tête d'agrégat** | Un en-tête d’agrégat peut comprendre un maximum de 4 096 caractères. |
| **Délai d'expiration** | Le délai d’expiration des requêtes est statique (non modifiable). Il est de 60 secondes pour les actions HTTP. Pour les opérations longues, suivez les protocoles HTTP asynchrones. Par exemple, retournez un code 202 immédiatement, mais continuez à travailler en arrière-plan. | 
| **Historique des travaux** | La taille maximale du corps de réponse stocké dans l’historique des travaux est de 2 048 octets. |
| **Conservation de l'historique des travaux** | L’historique des travaux est conservé pendant deux mois maximum ou jusqu’à ce qu’il atteigne 1 000 exécutions. | 
| **Conservation des travaux terminés et ayant généré une erreur** | Les travaux qui se sont terminés correctement ou avec une erreur sont conservés pendant 60 jours. |
||| 

