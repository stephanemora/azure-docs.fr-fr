---
title: Fichier Include
description: Fichier Include
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852039"
---
| Ressource | Limite maximale |Notes|
| --- | --- |---|
| Nombre maximum de nouveaux travaux pouvant être envoyés toutes les 30 secondes par compte Automation (travaux non planifiés) |100 |Lorsque cette limite est atteinte, les demandes suivantes de création d’un travail échouent. Le client reçoit une réponse d’erreur.|
| Nombre maximum de travaux simultanés pendant la même instance de temps par compte Automation (travaux non planifiés) |200 |Lorsque cette limite est atteinte, les demandes suivantes de création d’un travail échouent. Le client reçoit une réponse d’erreur.|
| Nombre maximum de modules pouvant être importés toutes les 30 secondes par compte Automation |5. ||
| Taille maximum d’un module |100 Mo ||
| Durée d’exécution de la tâche - Niveau Gratuit |500 minutes par abonnement et par mois ||
| Quantité maximale d’espace disque autorisée par bac à sable**<sup>1</sup>** |1 Go |S’applique aux bacs à sable Azure uniquement|
| Quantité maximale de mémoire affectée à un bac à sable **<sup>1</sup>** |400 Mo |S’applique aux bacs à sable Azure uniquement|
| Nombre maximal de sockets réseau autorisé par bac à sable **<sup>1</sup>** |1 000 |S’applique aux bacs à sable Azure uniquement|
| Nombre maximal de comptes Automation dans un abonnement |Aucune limite ||
|Nombre maximal de travaux qui peuvent être exécutés simultanément sur un Runbook Worker hybride|50 ||

**<sup>1</sup>**  Un bac à sable est un environnement partagé qui peut être utilisé par plusieurs travaux. Les travaux qui utilisent le même bac à sable sont liés par les limitations de ressources du bac à sable.
