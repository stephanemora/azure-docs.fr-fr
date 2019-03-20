---
title: Problèmes courants et correctifs relatifs aux URL de signature d’accès partagé (SAP) pour Place de marché Microsoft Azure | Microsoft Docs
description: Liste des problèmes courants liés à l’utilisation des URI de signature d’accès partagé et solutions possibles correspondantes.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: cdee17185b7051220f66ede3b9da50a333409e6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119263"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problèmes courants et correctifs relatifs aux URL de signature d’accès partagé (SAP)

Le tableau ci-après répertorie certains des problèmes courants rencontrés dans le cadre de l’utilisation des signatures d’accès partagé (qui permettent d’identifier et de partager les disques durs virtuels (VHD) chargés pour votre solution), ainsi que les mesures de résolution recommandées.

| **Problème** | **Message d’échec** | **Correctif** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Échec lors de la copie d’images* |  |  |
| « ? » est introuvable dans l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP à l’aide des outils recommandés. |
| Les paramètres « st » et « se » ne sont pas présents dans l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP en y incluant les valeurs **Date de début** et **Date de fin** appropriées. | 
| « sp=rl » n’est pas présent dans l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Mettez à jour l’URL SAP en définissant les autorisations sur `Read` et `List`. | 
| L’URL SAP comporte des espaces blancs dans le nom de VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP en supprimant les espaces blancs. |
| Erreur d’autorisation d’URL SAP | `Failure: Copying Images. Not able to download blob due to authorization error` | Examinez et corrigez le format d’URI SAP. Régénérez-le si nécessaire. |
| Les paramètres « st » et « se » de l’URL SAP ne présentent pas une spécification de date-heure complète | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Les paramètres **Date de début** et **Date de fin** (sous-chaînes `st` et ` se`) de l’URL SAP doivent présenter un format DateHeure complet, tel que `11-02-2017T00:00:00Z`. Les versions raccourcies ne sont pas valides. (Certaines commandes de l’interface de ligne de commande Azure peuvent générer des valeurs raccourcies par défaut.) | 
|  |  |  |

Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
