---
title: Créer un disque dur virtuel compatible avec Azure pour la Place de marché Microsoft Azure
description: Explique comment créer un disque dur virtuel d’une offre de machine virtuelle dans la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 2014a775edd4e24f5d302d863d0b69d83009b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277988"
---
# <a name="create-an-azure-compatible-vhd"></a>Créer un disque dur virtuel compatible avec Azure

Cet article détaille les étapes nécessaires pour créer un disque dur virtuel (VHD) pour une offre de machine virtuelle dans la Place de marché Microsoft Azure.  Il inclut également les meilleures pratiques pour différents aspects, comme l’utilisation du protocole RDP (Remote Desktop Protocol), la sélection d’une taille pour la machine virtuelle, l’installation des dernières mises à jour de Windows et la généralisation de l’image de disque dur virtuel.  Les sections suivantes sont principalement axées sur les disques durs virtuels basés sur Windows. Pour plus d’informations sur la création de disques durs virtuels basés sur Linux, consultez [Linux sur les distributions approuvées par Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Nous vous recommandons vivement de suivre les instructions de cette rubrique pour utiliser Azure afin de créer une machine virtuelle contenant un système d’exploitation pris en charge préconfiguré.  Si ce n’est pas possible avec votre solution, il est alors possible de créer et de configurer une machine virtuelle locale à l’aide d’un système d’exploitation approuvé.  Vous pouvez ensuite la configurer et la préparer pour le chargement comme décrit dans [Préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Sélectionner une base approuvée
Le disque dur virtuel de système d’exploitation pour votre image de machine virtuelle doit être basé sur une image de base approuvée par Azure contenant Windows Server ou SQL Server.
Pour commencer, créez une machine virtuelle à partir d’une des images suivantes, situées dans le portail Microsoft Azure :

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Si vous utilisez le portail Azure actuel ou PowerShell, les images Windows Server publiées à compter du 8 septembre 2014 sont approuvées.

Azure offre également toute une gamme de distributions de Linux approuvées.  Pour obtenir la liste actuelle, consultez [Linux sur les distributions approuvées par Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure 

Dans le [portail Microsoft Azure](https://ms.portal.azure.com/), créez l’image de base en procédant comme suit.

1. Connectez-vous au portail en utilisant le compte Microsoft de l’abonnement Azure souhaité pour publier votre offre de machine virtuelle.
2. Créez un nouveau groupe de ressources et fournissez le **Nom du groupe de ressources**, l’**Abonnement** et l’**Emplacement du groupe de ressources**.  Pour obtenir plus d’aide, consultez [Gérer les groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Cliquez sur **Machines virtuelles** sur la barre de menus de gauche pour afficher la page de détails des machines virtuelles. 
4. Dans cette nouvelle page, cliquez sur **+ Ajouter** pour afficher le panneau **Calcul**.  Si vous ne voyez pas le type de machine virtuelle sur le premier écran, vous pouvez rechercher le nom de votre machine virtuelle de base. Par exemple :

    ![Panneau Calcul de la nouvelle machine virtuelle](./media/publishvm_014.png)

5. Après avoir sélectionné l’image virtuelle appropriée, renseignez les valeurs suivantes :
   * Dans le panneau **De base**, entrez un **nom** pour la machine virtuelle de 1 à 15 caractères alphanumériques. (Cet exemple utilise `DemoVm009`.)
   * Entrez un **Nom d’utilisateur** et un **Mot de passe** fort, qui servent à créer un compte local sur la machine virtuelle.  (Ici, `adminUser` est utilisé.)  Le mot de passe doit compter 8 à 123 caractères et répondre à trois des quatre conditions suivantes : un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial. Pour plus d’informations, consultez les [exigences relatives au nom d’utilisateur et au mot de passe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Sélectionnez le groupe de ressources que vous avez créé (ici, `DemoResourceGroup`).
   * Sélectionnez un **emplacement** de centre de données Azure (ici, `West US`).
   * Cliquez sur **OK** pour enregistrer ces valeurs. 

6. Sélectionnez la taille de la machine virtuelle à déployer à l’aide des suggestions suivantes :
   * Si vous envisagez de développer le disque dur virtuel localement, la taille n’a pas d’importance. Vous pouvez utiliser une des machines virtuelles plus petites.
   * Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser les tailles de machine virtuelle recommandées pour l’image sélectionnée.
   * Pour plus d’informations sur la tarification, consultez le sélecteur de **niveaux tarifaires recommandés** inclus dans le portail. Celui-ci affiche les trois tailles recommandées fournies par l’éditeur. (Ici, l’éditeur est Microsoft.)

   ![Panneau Taille de la nouvelle machine virtuelle](./media/publishvm_015.png)

7. Dans le panneau **Paramètres**, définissez l’option **Use Managed Disk** (Utiliser un disque managé) sur **Non**.  Vous pouvez ainsi gérer manuellement le nouveau disque dur virtuel. (Le panneau **Paramètres** vous permet également d’apporter d’autres modifications aux options de stockage et de réseau, par exemple, en sélectionnant **Premium (SSD)** dans **Type de disque**.)  Pour continuer, cliquez sur **OK** .

    ![Panneau Paramètres de la nouvelle machine virtuelle](./media/publishvm_016.png)

8. Cliquez sur **Résumé** pour passer en revue vos options. Lorsque le message **Validation réussie** apparaît, cliquez sur **OK**.

    ![Panneau Résumé de la nouvelle machine virtuelle](./media/publishvm_017.png)

Azure commence l’approvisionnement de la machine virtuelle que vous avez spécifiée.  Vous pouvez suivre sa progression en cliquant sur l’onglet **Machines virtuelles** à gauche.  Une fois la création terminée, l’état passe à **En cours d’exécution**.  À ce stade, vous pouvez vous [connecter à la machine virtuelle](./cpp-connect-vm.md).


## <a name="next-steps"></a>Étapes suivantes

Si vous avez rencontré des difficultés pour créer votre nouveau disque dur virtuel basé sur Azure, consultez [Common issues during VHD creation](./cpp-common-vhd-creation-issues.md) (Problèmes courants pendant la création de disque dur virtuel).  Sinon, vous devez vous [connecter aux machines virtuelles](./cpp-connect-vm.md) que vous avez créées dans Azure. 
