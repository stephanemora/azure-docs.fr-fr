---
title: Ajouter un cluster Kubernetes sur la Place de marché Azure Stack | Microsoft Docs
description: Découvrez comment ajouter un cluster Kubernetes sur la Place de marché Azure Stack.
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: f74d378359ec66b76fd53b95ab898f1cec9b07f7
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34009569"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Ajouter un cluster Kubernetes sur la Place de marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!note]  
> Le service ACS (Azure Container Service) Kubernetes est disponible en préversion privée dans Azure Stack. Pour obtenir l’accès à l’élément Kubernetes sur la Place de marché afin de pouvoir suivre les instructions données dans cet article, [envoyez une demande d’accès](https://aka.ms/azsk8).

Vous pouvez créer une offre de cluster Kubernetes et la mettre à disposition des utilisateurs en tant qu’élément de la Place de marché. Les utilisateurs peuvent alors déployer Kubernetes en une seule opération coordonnée.

L’article suivant décrit comment utiliser un modèle Azure Resource Manager pour déployer et provisionner les ressources d’un cluster Kubernetes autonome. Avant de commencer, vérifiez les paramètres Azure Stack et les paramètres du locataire Azure global. Collectez les informations sur Azure Stack requises. Ajoutez les ressources nécessaires à votre locataire et à la Place de marché Azure Stack. Le cluster dépend d’un serveur Ubuntu, du script personnalisé et des éléments du cluster Kubernetes à ajouter à la Place de marché.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Créer un plan, une offre et un abonnement

Créez un plan, une offre et un abonnement pour l’élément cluster Kubernetes de la Place de marché. Vous pouvez également utiliser un plan et une offre existants.

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

2. Créez un plan de base. Pour obtenir des instructions, consultez [Créer un plan dans Azure Stack](azure-stack-create-plan.md).

3. Créez une offre. Pour obtenir des instructions, consultez [Créer une offre dans Azure Stack](azure-stack-create-offer.md).

4. Sélectionnez **Offres**et recherchez l’offre que vous venez de créer.

5. Sélectionnez **Vue d’ensemble** dans le panneau Offre.

6. Sélectionnez **Changer l’état**. Sélectionnez **Public**.

7. Sélectionnez **+ Nouveau** > **Offres et plans** > **Abonnement** pour créer un abonnement.

    a. Entrez un **nom d’affichage**.

    b. Entrez un **utilisateur**. Utilisez le compte Azure AD associé au locataire.

    c. Entrez une **description du fournisseur**

    d. Définissez le **locataire d’annuaire** sur le locataire Azure AD dans votre environnement Azure Stack. 

    e. Sélectionnez **Offre**. Sélectionnez le nom de l’offre que vous avez créée. Prenez note de l’ID d’abonnement.

## <a name="add-an-ubuntu-server-image"></a>Ajouter une image de serveur Ubuntu

Ajoutez l’image de serveur Ubuntu suivante sur la Place de marché :

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

2. Sélectionnez **Autres services** > **Gestion de la Place de marché**.

3. Sélectionnez **+ Ajouter à partir d’Azure**.

4. Entrez `UbuntuServer`.

5. Sélectionnez le serveur avec le profil suivant :
    - **Publisher** (Serveur de publication) : Canonical
    - **Offer** (Offre) : UbuntuServer
    - **SKU** : 16.04-LTS
    - **Version** : 16.04.201802220

    > [!Note]  
    > Plusieurs versions d’Ubuntu Server 16.04 LTS peuvent être répertoriées. Vous devez ajouter la version appropriée. Le cluster Kubernetes requiert la version appropriée de l’élément.

6. Sélectionnez **Télécharger**.

## <a name="add-a-custom-script-for-linux"></a>Ajouter un script personnalisé pour Linux

Ajoutez le cluster Kubernetes à partir de la Place de marché :

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

2. Sélectionnez **Autres services** > **Gestion de la Place de marché**.

3. Sélectionnez **+ Ajouter à partir d’Azure**.

4. Entrez `Custom Script for Linux`.

5. Sélectionnez le script avec le profil suivant :
    - **Offer** (Offre) : script personnalisé pour Linux 2.0
    - **Version** : 2.0.3
    - **Publisher** (Serveur de publication) : Microsoft Corp

    > [!Note]  
    > Plusieurs versions d’un script personnalisé pour Linux peuvent être répertoriées. Vous devez ajouter la version appropriée. Le cluster Kubernetes requiert la version appropriée de l’élément.

6. Sélectionnez **Télécharger**.


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Ajouter le cluster Kubernetes sur la Place de marché

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

2. Sélectionnez **Autres services** > **Gestion de la Place de marché**.

3. Sélectionnez **+ Ajouter à partir d’Azure**.

4. Entrez `Kubernetes Cluster`.

5. Sélectionnez `Kubernetes Cluster`.

6. Sélectionnez **Télécharger**.

    > [!note]  
    > L’affichage de l’élément sur la Place de marché peut prendre jusqu’à cinq minutes.

    ![Cluster Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Mettre à jour ou supprimer le cluster Kubernetes 

Quand vous mettez à jour l’élément Cluster Kubernetes, vous devez supprimer l’élément existant sur la Place de marché. Vous pouvez ensuite suivre les instructions fournies dans cet article pour ajouter le cluster Kubernetes sur la Place de marché.

Pour supprimer l’élément Cluster Kubernetes :

1. Notez le nom de l’élément actuel (par exemple, `Microsoft.AzureStackKubernetesCluster.0.1.0`)

2. Connectez-vous à Azure Stack à l’aide de PowerShell.

3. Pour supprimer l’élément, utilisez l’applet de commande PowerShell suivante :

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Étapes suivantes

[Déployer un cluster Kubernetes dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md)