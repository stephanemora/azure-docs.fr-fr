---
title: Problèmes courants des URI SAS et correctifs - Place de marché Azure
description: Problèmes couramment rencontrés lors de l’utilisation de signatures d’accès partagé, et suggestions de résolution.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: anbene
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 95ce37d92adc3d09c5a09944b094df7971831198
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110723"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problèmes courants des URI SAS et correctifs

Voici certains des problèmes couramment rencontrés dans le cadre de l’utilisation des signatures d’accès partagé (qui permettent d’identifier et de partager les disques durs virtuels chargés pour votre solution), ainsi que des suggestions de résolution.

| **Problème** | **Message d’échec** | **Correctif** |
| --------- | ------------------- | ------- |
| *Échec lors de la copie d’images* |  |  |
| « ? » est introuvable dans l’URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URI SAS à l’aide des outils recommandés. |
| Paramètres « st » et « se » absents de l’URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URI SAS en y incluant les valeurs **Date de début** et **Date de fin** appropriées. |
| « sp=rl » absent de l’URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URI SAS en définissant les autorisations sur `Read` et `List`. |
| L’URI SAS comporte des espaces blancs dans le nom de VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URI SAS en supprimant les espaces blancs. |
| Erreur d’autorisation d’URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Examinez et corrigez le format d’URI SAP. Régénérez-le si nécessaire. |
| Les paramètres « st » et « se » de l’URI SAS ne présentent pas une spécification de date-heure complète | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Les paramètres **Date de début** et **Date de fin** (sous-chaînes `st` et `se`) de l’URI SAS doivent présenter un format DateHeure complet, tel que `11-02-2017T00:00:00Z`. Les versions raccourcies ne sont pas valides (certaines commandes de l’interface de ligne de commande Azure peuvent générer des valeurs raccourcies par défaut). |
|  |  |  |

Pour plus d’informations, consultez [Utilisation des signatures d’accès partagé (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
