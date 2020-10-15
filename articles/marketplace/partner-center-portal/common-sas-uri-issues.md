---
title: Problèmes courants des URI SAS et correctifs - Place de marché Azure
description: Problèmes couramment rencontrés lors de l’utilisation de signatures d’accès partagé, et suggestions de résolution.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316836"
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
