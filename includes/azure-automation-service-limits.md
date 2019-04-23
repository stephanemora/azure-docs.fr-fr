---
title: Fichier Include
description: Fichier Include
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: b701183fa270b1aad9a622a21c5ce0795fc8057f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012497"
---
#### <a name="process-automation"></a>Automatisation de processus

| Ressource | Limite maximale |Notes|
| --- | --- |---|
| Nombre maximal de nouvelles tâches qui peuvent être envoyées toutes les 30 secondes par compte Azure Automation (travaux non planifiés) |100 |Lorsque cette limite est atteinte, les demandes suivantes pour créer une tâche échouent. Le client reçoit une réponse d’erreur.|
| Nombre maximal de travaux simultanés pendant la même instance de temps par compte Automation (travaux non planifiés) |200 |Lorsque cette limite est atteinte, les demandes suivantes pour créer une tâche échouent. Le client reçoit une réponse d’erreur.|
| Taille maximale de stockage de métadonnées du travail pour une période de 30 jours propagée | 10 Go (environ 4 millions travaux)|Lorsque cette limite est atteinte, les demandes suivantes pour créer une tâche échouent. |
| Limite de flux de travail maximale|1 Mo|Un seul flux ne peut pas être supérieur à 1 Mo.|
| Nombre maximal de modules qui peuvent être importés toutes les 30 secondes par compte Automation |5. ||
| Taille maximale d’un module |100 Mo ||
| Travail de l’exécution, niveau gratuit |500 minutes par abonnement et par mois ||
| Quantité maximale d’espace disque autorisée par le bac à sable<sup>1</sup> |1 Go |S’applique aux bacs à sable Azure uniquement.|
| Quantité maximale de mémoire affectée à un bac à sable<sup>1</sup> |400 Mo |S’applique aux bacs à sable Azure uniquement.|
| Nombre maximal de sockets réseau affectés par le bac à sable<sup>1</sup> |1 000 |S’applique aux bacs à sable Azure uniquement.|
| Durée d’exécution maximale autorisée par runbook<sup>1</sup> |3 heures |S’applique aux bacs à sable Azure uniquement.|
| Nombre maximal de comptes Automation dans un abonnement |Aucune limite ||
|Nombre maximal de travaux simultanés qui peuvent être exécutés sur un seul Runbook Worker hybride|50 ||
| Taille de paramètre de tâche de runbook maximale   | 512 Ko||
| Paramètres de runbook maximale   | 50|Si vous atteignez la limite de 50-paramètre, vous pouvez passer une chaîne JSON ou XML à un paramètre et l’analyse avec le runbook.|
| Taille de charge utile de webhook maximale |  512 Ko|
| Nombre maximal de jours de conservation des données de travail|30 jours|
| Taille d’état de flux de travail PowerShell maximale |5 Mo| S’applique aux runbooks de workflow PowerShell lors de flux de travail de points de contrôle.|

<sup>1</sup>un bac à sable est un environnement partagé qui peut être utilisé par plusieurs travaux. Les tâches qui utilisent le même bac à sable sont liés par les limitations de ressources du sandbox.

#### <a name="change-tracking-and-inventory"></a>Suivi des modifications et inventaire

Le tableau suivant indique les limites de l’élément suivies par ordinateur pour le suivi des modifications.

| **Ressource** | **Limite**| **Remarques** |
|---|---|---|
|Fichier|500||
|Registre|250||
|Logiciels Windows|250|N’inclut pas les mises à jour logicielles.|
|Packages Linux|1,250||
|Services|250||
|Daemon|250||
