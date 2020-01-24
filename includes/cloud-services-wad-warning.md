---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279337"
---
> [!WARNING]
> Quand vous activez les diagnostics pour un rôle existant, toutes les extensions que vous avez déjà définies sont désactivées lors du déploiement du package. notamment :
>
> * Extension Microsoft Monitoring Agent Diagnostics
> * Surveillance de la sécurité de Microsoft Azure
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Agent du profileur de service Microsoft      
> * Extension de domaine Microsoft Azure        
> * Extension Diagnostics Azure pour Windows   
> * Extension Bureau à distance de Microsoft Azure
> * Collecteur de journaux Microsoft Azure
>
> Vous pouvez réinitialiser vos extensions via le portail Azure ou PowerShell une fois que le rôle mis à jour est déployé.
>
