---
title: Fichier Include
description: Fichier Include
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 03/29/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 29256b3bcfedb7fe5045ff4c6c3842eb25e00a28
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
Les limites suivantes s’appliquent aux ressources Log Analytics par abonnement :

| Ressource | Limite par défaut | Commentaires
| --- | --- | --- |
| Nombre d’espaces de travail gratuits par abonnement | 10 | Cette limite ne peut pas être augmentée. |
| Nombre d’espaces de travail payants par abonnement | N/A | Vous êtes limité par le nombre de ressources au sein d’un groupe de ressources et le nombre de groupes de ressources par abonnement. | 

>[!NOTE]
>À compter du 2 avril 2018, nouveaux espaces de travail dans un nouvel abonnement utilisera automatiquement le *par Go* plan de tarification.  Pour un abonnement existant créé avant le 2 avril, ou pour un abonnement lié à une inscription EA existante, vous pouvez continuer de choisir entre trois niveaux tarifaires pour les nouveaux espaces de travail. 
>

Les limites suivantes s’appliquent à chaque espace de travail Log Analytics :

|  | Gratuit | standard | Premium | Standalone | OMS | Par Go |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de données collecté par jour |500 MO<sup>1</sup> |Aucun |Aucun | Aucun | Aucun | Aucun
| Période de rétention des données |7 jours |1 mois |12 mois | 1 mois<sup>2</sup> | 1 mois <sup>2</sup>| 1 mois <sup>2</sup>|

<sup>1</sup> Lorsque les clients atteignent leur limite quotidienne de transfert de données de 500 Mo, l’analyse des données s’interrompt et reprend au début de la journée suivante. Les journées sont basées sur l’heure UTC.

<sup>2</sup> La période de conservation des données pour les plans de tarification autonomes, OMS et par Go peut être augmentée à 730 jours.

| Catégorie | limites | Commentaires
| --- | --- | --- |
| API du collecteur de données | La taille maximale d’une publication est de 30 Mo<br>La taille maximale des valeurs de champ est de 32 Ko | Fractionner les volumes plus importants en plusieurs publications<br>Les champs de plus de 32 Ko de champs sont tronqués. |
| API de recherche | 5 000 enregistrements renvoyés pour des données non agrégées<br>500 000 enregistrements pour des données agrégées | La recherche de données agrégées inclut la commande `measure`
 
