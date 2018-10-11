---
title: Ajouter Kubernetes sur la Place de marché Azure Stack | Microsoft Docs
description: Découvrez comment ajouter Kubernetes sur la Place de marché Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 9250e38eadb9d9b1e8bf0c09fa1a9a165e5e69f1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886260"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Ajouter Kubernetes sur la Place de marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!note]  
> Kubernetes sur Azure Stack est en préversion.

Vous pouvez créer une offre Kubernetes et la mettre à disposition des utilisateurs en tant qu’élément de la Place de marché. Les utilisateurs peuvent alors déployer Kubernetes en une seule opération coordonnée.

L’article suivant décrit comment utiliser un modèle Azure Resource Manager pour déployer et provisionner les ressources d’un cluster Kubernetes autonome. L’élément de la Place de marché Cluster Kubernetes version 0.3.0 nécessite Azure Stack 1808. Avant de commencer, vérifiez les paramètres Azure Stack et les paramètres du locataire Azure global. Collectez les informations sur Azure Stack requises. Ajoutez les ressources nécessaires à votre locataire et à la Place de marché Azure Stack. Le cluster dépend d’un serveur Ubuntu, du script personnalisé et des éléments Kubernetes à ajouter à la Place de marché.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Créer un plan, une offre et un abonnement

Créez un plan, une offre et un abonnement pour l’élément Kubernetes de la Place de marché. Vous pouvez également utiliser un plan et une offre existants.

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

1. Créez un plan de base. Pour obtenir des instructions, consultez [Créer un plan dans Azure Stack](azure-stack-create-plan.md).

1. Créez une offre. Pour obtenir des instructions, consultez [Créer une offre dans Azure Stack](azure-stack-create-offer.md).

1. Sélectionnez **Offres**et recherchez l’offre que vous venez de créer.

1. Sélectionnez **Vue d’ensemble** dans le panneau Offre.

1. Sélectionnez **Changer l’état**. Sélectionnez **Public**.

1. Sélectionnez **+ Créer une ressource** > **Offres et plans** > **Abonnement** pour créer un abonnement.

    a. Entrez un **nom d’affichage**.

    b. Entrez un **utilisateur**. Utilisez le compte Azure AD associé au locataire.

    c. Entrez une **description du fournisseur**

    d. Définissez le **locataire d’annuaire** sur le locataire Azure AD dans votre environnement Azure Stack. 

    e. Sélectionnez **Offre**. Sélectionnez le nom de l’offre que vous avez créée. Prenez note de l’ID d’abonnement.

## <a name="add-an-ubuntu-server-image"></a>Ajouter une image de serveur Ubuntu

Ajoutez l’image de serveur Ubuntu suivante sur la Place de marché :

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `UbuntuServer`.

1. Sélectionnez la dernière version du serveur avec le profil suivant :
    - **Publisher** (Serveur de publication) : Canonical
    - **Offer** (Offre) : UbuntuServer
    - **SKU** : 16.04-LTS

1. Sélectionnez **Télécharger**.

## <a name="add-a-custom-script-for-linux"></a>Ajouter un script personnalisé pour Linux

Ajoutez Kubernetes à partir de la Place de marché :

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Custom Script for Linux`.

1. Sélectionnez le script avec le profil suivant :
    - **Offer** (Offre) : script personnalisé pour Linux 2.0
    - **Version** : 2.0.6
    - **Publisher** (Serveur de publication) : Microsoft Corp

    > [!Note]  
    > Plusieurs versions d’un script personnalisé pour Linux peuvent être répertoriées. Vous devez ajouter la version appropriée. Kubernetes requiert la version appropriée de l’élément.

1. Sélectionnez **Télécharger**.


## <a name="add-kubernetes-to-the-marketplace"></a>Ajouter Kubernetes sur la Place de marché

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services** et, sous la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Kubernetes`.

1. Sélectionnez `Kubernetes Cluster`.

1. Sélectionnez **Télécharger**.

    > [!note]  
    > L’affichage de l’élément sur la Place de marché peut prendre jusqu’à cinq minutes.

    ![kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Mettre à jour ou supprimer Kubernetes 

Quand vous mettez à jour l’élément Kubernetes, vous devez supprimer l’élément existant sur la Place de marché. Vous pouvez ensuite suivre les instructions fournies dans cet article pour ajouter Kubernetes sur la Place de marché.

Pour supprimer l’élément Kubernetes :

1. Connectez-vous en tant qu’opérateur à Azure Stack à l’aide de PowerShell. Pour plus d’informations, consultez [Se connecter en tant qu’opérateur à Azure Stack à l’aide de PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Recherchez l’élément Cluster Kubernetes actuel dans la galerie.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notez le nom de l’élément actuel (par exemple, `Microsoft.AzureStackKubernetesCluster.0.2.0`)

4. Pour supprimer l’élément, utilisez l’applet de commande PowerShell suivante :

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Étapes suivantes

[Déployer Kubernetes sur Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md)