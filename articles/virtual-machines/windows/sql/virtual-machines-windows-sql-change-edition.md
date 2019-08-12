---
title: Effectuer une mise à niveau sur place de l’édition de SQL Server sur une machine virtuelle Azure | Microsoft Docs
description: Découvrez comment modifier l’édition de votre machine virtuelle SQL Server dans Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607353"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Effectuer une mise à niveau sur place de l’édition de SQL Server sur une machine virtuelle Azure

Cet article explique comment modifier l’édition de SQL Server pour une instance SQL Server présente sur une machine virtuelle Windows dans Azure. 

L’édition de SQL Server est déterminée par la clé de produit et spécifiée avec le processus d’installation. L’édition régit les [fonctionnalités](/sql/sql-server/editions-and-components-of-sql-server-2017) disponibles au sein du produit SQL Server. Vous pouvez modifier l’édition de SQL Server avec le support d’installation, et passer à une version antérieure pour réduire les coûts ou à une version ultérieure pour activer plus de fonctionnalités.

Si vous avez mis à jour l’édition de SQL Server à l’aide du support d’installation après son inscription auprès du fournisseur de ressources de machine virtuelle SQL, pour mettre à jour la facturation Azure en conséquence, vous devez définir la propriété Édition SQL Server de la ressource de machine virtuelle SQL comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre ressource de machine virtuelle SQL Server. 
1. Sous **Paramètres**, sélectionnez **Configurer** , puis l'édition de SQL Server souhaitée dans la liste déroulante sous **Édition**. 

   ![Modifier les métadonnées de l'édition](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Lisez l’avertissement vous informant que vous devez d'abord modifier l'édition de SQL Server, et que cette propriété de l’édition doit correspondre à l’édition de SQL Server. 
1. Sélectionnez **Appliquer** pour appliquer les modifications des métadonnées de l'édition. 


## <a name="prerequisites"></a>Prérequis

Pour apporter une modification sur place à l’édition de SQL Server, vous devez disposer de ce qui suit : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) Windows inscrite auprès du [fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Le support d’installation correspondant à l'édition de SQL Server souhaitée. Les clients disposant de la [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) peuvent obtenir leur support d’installation à partir du [Centre de gestion des licences en volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Les clients ne disposant pas de la Software Assurance peuvent utiliser le support d’installation à partir d’une image de machine virtuelle SQL Server de place de marché proposant l'édition souhaitée.


## <a name="upgrade-edition"></a>Mettre à niveau l'édition

  > [!WARNING]
  > - **La mise à niveau de l’édition de SQL Server redémarre le service pour SQL Server, ainsi que tous les services associés, comme Analysis Services et R Services.** 

Pour mettre à niveau l’édition de SQL Server, procurez-vous le support d’installation de SQL Server correspondant à l'édition souhaitée, puis procédez comme suit :

1. Lancez Setup.exe à partir du support d’installation de SQL Server. 
1. Accédez à **Maintenance** et sélectionnez l'option **Mise à niveau d’édition**. 

   ![Mettre à niveau l'édition de SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Sélectionnez **Suivant** jusqu'à accéder à la page **Prêt pour la mise à niveau de l'édition**, puis sélectionnez **Mettre à niveau**. La fenêtre d’installation peut se figer quelques minutes, le temps que la modification prenne effet. Vous accédez ensuite à une page **Terminer** qui confirme que la mise à niveau de l'édition est terminée. 

Une fois l’édition de SQL Server mise à niveau, modifiez la propriété « Edition » de la machine virtuelle SQL sur le portail Azure comme indiqué ci-dessus. Les métadonnées et la facturation associées à cette machine virtuelle sont ainsi mises à jour.

## <a name="downgrade-edition"></a>Passer à une édition antérieure

  > [!WARNING]
  > - **Pour passer à une édition antérieure de SQL Server, vous devez désinstaller complètement SQL Server, ce qui peut entraîner des temps d’arrêt supplémentaires**. 


Pour passer à une édition antérieure de SQL Server, vous devez complètement désinstaller SQL Server et le réinstaller avec le support d’installation de l'édition souhaitée. 

Pour passer à une édition antérieure de SQL Server, procédez comme suit :

1. Sauvegardez toutes les bases de données, y compris les bases de données système. 
1. Déplacez des bases de données système (maître, modèle et msdb) vers un nouvel emplacement. 
1. Désinstallez complètement SQL Server et tous les services associés. 
1. Redémarrez la machine virtuelle. 
1. Installez SQL Server à l’aide du support correspondant à l'édition de SQL Server souhaitée.
1. Installez les derniers Service Packs et mises à jour cumulatives.  
1. Remplacez les nouvelles bases de données système créées lors de l’installation par les bases de données système que vous précédemment déplacées vers un autre emplacement. 

Une fois SQL Server repassée à l'édition antérieure, modifiez la propriété « Edition » de la machine virtuelle SQL sur le portail Azure comme indiqué ci-dessus. Les métadonnées et la facturation associées à cette machine virtuelle sont ainsi mises à jour.

## <a name="remarks"></a>Remarques

 - La propriété d'édition de la machine virtuelle SQL Server doit correspondre à l’édition de SQL Server installée sur la machine virtuelle pour toutes les machines virtuelles SQL, y compris les types de licences PAYG et BYOL.
 - Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre d'édition codé en dur de l’image.
  - La possibilité de changer d'édition est une fonctionnalité du fournisseur de ressources de machine virtuelle SQL. Le déploiement d’une image de place de marché via le portail Azure inscrit automatiquement une machine virtuelle SQL Server auprès du fournisseur de ressources. Toutefois, les clients qui installent automatiquement SQL Server doivent manuellement [inscrire leur machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- L'ajout d'une machine virtuelle SQL Server à un groupe à haute disponibilité implique la recréation de la machine virtuelle. Dès lors, toute machine virtuelle ajoutée à un groupe à haute disponibilité reviendra à l'édition par défaut et celle-ci devra être à nouveau modifiée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


