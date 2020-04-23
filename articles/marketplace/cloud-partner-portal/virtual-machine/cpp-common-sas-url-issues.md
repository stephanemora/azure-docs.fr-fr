---
title: Problèmes courants et correctifs relatifs aux URL de signature d’accès partagé (SAP) pour Place de marché Microsoft Azure
description: Liste des problèmes courants liés à l’utilisation des URI de signature d’accès partagé et solutions possibles correspondantes.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273458"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problèmes courants et correctifs relatifs aux URL de signature d’accès partagé (SAP)

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Problèmes courants et correctifs relatifs aux URL de signature d’accès partagé](https://aka.ms/AzureSAS_URL_FAQ) pour gérer vos offres migrées.

Le tableau ci-après répertorie certains des problèmes courants rencontrés dans le cadre de l’utilisation des signatures d’accès partagé (qui permettent d’identifier et de partager les disques durs virtuels (VHD) chargés pour votre solution), ainsi que les mesures de résolution recommandées.

| **Problème** | **Message d’échec** | **Correctif** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Échec lors de la copie d’images* |  |  |
| « ? » est introuvable dans l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP à l’aide des outils recommandés. |
| Paramètres « st » et « se » absents de l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP en y incluant les valeurs **Date de début** et **Date de fin** appropriées. | 
| « sp=rl » absent de l’URL SAP | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Mettez à jour l’URL SAP en définissant les autorisations sur `Read` et `List`. | 
| L’URL SAP comporte des espaces blancs dans le nom de VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Mettez à jour l’URL SAP en supprimant les espaces blancs. |
| Erreur d’autorisation d’URL SAP | `Failure: Copying Images. Not able to download blob due to authorization error` | Examinez et corrigez le format d’URI SAP. Régénérez-le si nécessaire. |
| Les paramètres « st » et « se » de l’URL SAP ne présentent pas une spécification de date-heure complète | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Les paramètres **Date de début** et **Date de fin** (sous-chaînes `st` et `se`) de l’URL SAP doivent présenter un format DateHeure complet, tel que `11-02-2017T00:00:00Z`. Les versions raccourcies ne sont pas valides. (Certaines commandes de l’interface de ligne de commande Azure peuvent générer des valeurs raccourcies par défaut.) | 
|  |  |  |

Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
