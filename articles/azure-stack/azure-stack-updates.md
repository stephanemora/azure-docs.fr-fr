---
title: Gérer les mises à jour dans Azure Stack - Vue d’ensemble | Microsoft Docs
description: Découvrez la gestion des mises à jour pour les systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 6cddb513249b424ef0df2ed2d1b5609de17c0744
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894034"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gérer les mises à jour dans Azure Stack - Vue d’ensemble

*S’applique à : systèmes intégrés Azure Stack*

En général, les packages de mise à jour de Microsoft pour les systèmes intégrés Azure Stack sont mis en production autour du quatrième mardi de chaque mois. Interrogez votre fabricant d’ordinateurs OEM sur son processus de notification pour vous assurer que votre organisation reçoive bien les notifications de mise à jour. Vous pouvez aussi rechercher dans cette bibliothèque de documentation sous **Vue d’ensemble** > **Notes de publication** des informations sur les versions dont le support est actif. 

Chaque version des mises à jour logicielles Microsoft est fournie sous forme de package de mise à jour unique. En tant qu’opérateur Azure Stack, vous pouvez importer, installer et surveiller la progression de l’installation de ces packages de mise à jour à partir du portail administrateur. 

Votre fabricant de matériel OEM publie également des mises à jour, par exemple pour les pilotes et le microprogramme. Même si ces mises à jour sont fournies sous forme de packages distincts par votre fabricant de matériel OEM, elles sont importées, installées et gérées de la même façon que les packages de mise à jour Microsoft.

Pour conserver votre système dans un état de prise en charge, vous devez maintenir Azure Stack à jour à un niveau de version spécifique. Pensez à passer en revue la [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Vous ne pouvez pas appliquer des packages de mise à jour Azure Stack au Kit de développement Azure Stack. Les packages de mise à jour sont conçues pour les systèmes intégrés. Pour plus d’informations, consultez [Redéployer l’ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack inclut un fournisseur de ressources de mise à jour qui orchestre l’application des mises à jour logicielles Microsoft. Ce fournisseur de ressources garantit que les mises à jour sont appliquées sur tous les hôtes physiques, les applications et runtimes Service Fabric, et toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez afficher l’état général à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack (par exemple les hôtes physiques et les machines virtuelles d’infrastructure).

## <a name="plan-for-updates"></a>Planifier les mises à jour

Nous vous recommandons vivement d’informer les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

  ```PowerShell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Utilisation de la vignette Mise à jour pour gérer les mises à jour
Vous gérez les mises à jour à partir du portail administrateur. En tant qu’opérateur Azure Stack, vous pouvez utiliser la vignette Mise à jour dans le tableau de bord pour :

- Afficher des informations importantes telles que la version actuelle
- Installer des mises à jour et surveiller la progression
- Examiner l’historique des mises à jour pour les mises à jour précédemment installées
 
## <a name="determine-the-current-version"></a>Déterminer la version actuelle

La vignette Mise à jour affiche la version actuelle d’Azure Stack. Vous pouvez accéder à la vignette Mise à jour en appliquant l’une des méthodes suivantes dans le portail administrateur :

- Dans le tableau de bord, consultez la version actuelle dans la vignette **Mise à jour**.
 
   ![Vignette Mises à jour sur le tableau de bord par défaut](./media/azure-stack-updates/image1.png)
 
- Dans la vignette **Gestion des régions**, cliquez sur le nom de la région. Consultez la version actuelle dans la vignette **Mise à jour**.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md) 
- [Gestion des régions dans Azure Stack](azure-stack-region-management.md)     


