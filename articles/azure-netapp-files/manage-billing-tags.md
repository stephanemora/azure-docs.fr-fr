---
title: Gérer la facturation d’Azure NetApp Files à l’aide d’étiquettes | Microsoft Docs
description: Décrit comment gérer la facturation d’Azure NetApp Files à l’aide d’étiquettes.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: b0efd8c580b0d4f1ae94d4dfc655bb351e084a67
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109480868"
---
# <a name="manage-billing-by-using-capacity-pool-billing-tags"></a>Gérer la facturation à l’aide d’étiquettes de facturation de pool de capacité

Les étiquettes sont des paires nom-valeur qui vous permettent de catégoriser les ressources et de visualiser la facturation consolidée. Vous pouvez appliquer la même étiquette à plusieurs ressources et groupes de ressources.  Pour en savoir plus sur les étiquettes, consultez [Utiliser des étiquettes pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md).  

L’utilisation d’étiquettes vous aide à gérer la facturation et les dépenses liées à Azure NetApp Files. Par exemple, votre entreprise peut avoir un seul abonnement Azure, mais plusieurs services qui utilisent des ressources Azure et engagent des dépenses. Vous pouvez étiqueter les ressources avec les noms des services au niveau du pool de capacité. Les étiquettes correspondantes seront affichées sur la facture pour vous aider à voir les dépenses engagées par chaque service.   

Les étiquettes de facturation sont attribuées au niveau du pool de capacité, et non au niveau du volume.

## <a name="steps"></a>Étapes

1. Pour ajouter ou modifier une étiquette sur un pool de capacité, accédez au **pool de capacité** et sélectionnez **Étiquettes**.   

2. Renseignez la paire **Nom** et **Valeur**.  Cliquez sur **Appliquer**.

    > [!IMPORTANT] 
    > Les données des étiquettes sont répliquées au niveau mondial. Par conséquent, n’utilisez pas de noms ni de valeurs d’étiquettes qui pourraient compromettre la sécurité de vos ressources. Par exemple, n’utilisez pas de noms d’étiquettes qui contiennent des informations personnelles ou sensibles. 

      ![Capture d’écran montrant la fenêtre Étiquettes d’un pool de capacité.](../media/azure-netapp-files/billing-tags-capacity-pool.png)

3. Vous pouvez afficher et télécharger des informations sur les ressources étiquetées à l’aide du portail [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) : 
    1. Cliquez sur **Analyse des coûts** et sélectionnez l’affichage **Coût par ressource**.    
      [![Capture d’écran montrant Analyse des coûts d’Azure Cost Management](../media/azure-netapp-files/cost-analysis.png)](../media/azure-netapp-files/cost-analysis.png#lightbox)  

    2. Pour télécharger une facture, sélectionnez **factures**, puis cliquez sur le bouton **Télécharger**.   
      [![Capture d’écran montrant Factures d’Azure Cost Management](../media/azure-netapp-files/azure-cost-invoices.png)](../media/azure-netapp-files/azure-cost-invoices.png#lightbox)  

    1. Dans la fenêtre Téléchargement qui s’affiche, téléchargez les détails d’utilisation. Le fichier `csv` téléchargé inclut les étiquettes de facturation de pool de capacité pour les ressources correspondantes.   
       ![Capture d’écran montrant la fenêtre Téléchargement d’Azure Cost Management.](../media/azure-netapp-files/invoice-download.png)   

       [![Capture d’écran montrant la feuille de calcul téléchargée.](../media/azure-netapp-files/spreadsheet-download.png)](../media/azure-netapp-files/spreadsheet-download.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

[Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md) 