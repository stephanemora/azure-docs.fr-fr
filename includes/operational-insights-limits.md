---
title: Fichier Include
description: Fichier Include
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238142"
---
Les limites suivantes s’appliquent aux ressources d’Analytique de journal Azure par abonnement.

| Ressource | Limite par défaut | Commentaires
| --- | --- | --- |
| Nombre d’espaces de travail gratuits par abonnement | 10 | Impossible d’augmenter cette limite. |
| Nombre d’espaces de travail payants par abonnement | N/A | Vous êtes limité par le nombre de ressources au sein d’un groupe de ressources et le nombre de groupes de ressources par abonnement. | 

>[!NOTE]
>À compter du 2 avril 2018, nouveaux espaces de travail dans un nouvel abonnement utilisent automatiquement le *par Go* plan de tarification. Pour les abonnements existants créés avant le 2 avril, ou un abonnement qui a été lié à une inscription de contrat entreprise existante, vous pouvez continuer à choisir parmi les trois niveaux de tarification pour les nouveaux espaces de travail. 
>

Les limites suivantes s’appliquent à chaque espace de travail Analytique de journal.

|  | Gratuit | standard | Premium | Standalone | OMS | Par Go |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de données collecté par jour |500 MO<sup>1</sup> |Aucun |Aucun | Aucun | Aucun | Aucun
| Période de rétention des données |7 jours |1 mois |12 mois | 1 mois<sup>2</sup> | 1 mois<sup>2</sup>| 1 mois<sup>2</sup>|

<sup>1</sup>lorsque les clients atteignent leur limite quotidienne de transfert des données 500 Mo, l’analyse des données s’interrompt et reprend au début de la journée suivante. Les journées sont basées sur l’heure UTC.

<sup>2</sup>la période de rétention de données pour autonome, OMS, les plans de tarification par Go peut être augmentée à 730 jours.

| Catégorie | limites | Commentaires
| --- | --- | --- |
| API du collecteur de données | Taille maximale d’une publication est de 30 Mo.<br>Taille maximale des valeurs de champ est de 32 Ko. | Fractionner des volumes plus importants en plusieurs publications.<br>Les champs de plus de 32 Ko de champs sont tronqués. |
| API de recherche | 5 000 enregistrements renvoyés pour les données non agrégées.<br>500 000 enregistrements pour les données agrégées. | Données agrégées sont une recherche qui inclut le `summarize` commande.
 
