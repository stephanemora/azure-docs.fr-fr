---
title: Problèmes courants des URI SAS et correctifs - Place de marché Azure
description: Problèmes couramment rencontrés lors de l’utilisation de signatures d’accès partagé, et suggestions de résolution.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265093"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problèmes courants des URI SAS et correctifs

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres de machines virtuelles Azure du portail Cloud Partner vers l’Espace partenaires. Tant que vous n’avez pas effectué la migration de vos offres, suivez les instructions fournies dans [Problèmes courants et correctifs relatifs aux URI SAS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) sur le portail Cloud Partner pour gérer vos offres.

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
