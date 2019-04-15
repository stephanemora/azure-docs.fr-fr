---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330978"
---
> [!WARNING]
> Quand vous activez les diagnostics pour un rôle existant, toutes les extensions que vous avez déjà définies sont désactivées lors du déploiement du package. Il s’agit des actions suivantes :
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
