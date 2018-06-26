---
title: Déployer un cluster Kubernetes sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer un cluster Kubernetes sur Azure Stack.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823620"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Déployer un cluster Kubernetes sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!Note]  
> Le Kubernetes ACS (Azure Container Service) sur Azure Stack est disponible en préversion privée. Votre opérateur Azure Stack doit demander l’accès à l’élément Kubernetes sur la Place de marché, nécessaire pour suivre les instructions données dans cet article.

L’article suivant décrit l’utilisation d’un modèle de solution Azure Resource Manager pour déployer et approvisionner les ressources pour Kubernetes en une seule opération coordonnée. Vous allez devoir collecter les informations requises sur votre installation d’Azure Stack, générer le modèle, puis le déployer sur votre cloud.

## <a name="kubernetes-and-containers"></a>Kubernetes et conteneurs

Vous pouvez installer Kubernetes à l’aide de modèles Azure Resource Manager générés par le moteur ACS (Azure Container Services) sur Azure Stack. Un [Kubernetes](https://kubernetes.io) est un système open source permettant d’automatiser le déploiement, la mise à l’échelle et la gestion d’applications dans des conteneurs. A [conteneur](https://www.docker.com/what-container) est contenu dans une image, tout comme une machine virtuelle. À la différence d’une machine virtuelle, l’image de conteneur inclut les ressources nécessaires pour exécuter une application, telles que le code, un runtime pour exécuter le code, des bibliothèques spécifiques et des paramètres.

Vous pouvez utiliser Kubernetes pour ce qui suit :

- Développer des applications hautement évolutives et extensibles, qui peuvent être déployées en quelques secondes. 
- Simplifier la conception de vos applications et améliorer leur fiabilité à l’aide de différentes applications Helm. [Helm](https://github.com/kubernetes/helm) est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes.
- Surveiller et diagnostiquer facilement l’intégrité de vos applications grâce à des fonctionnalités de mise à l’échelle et de mise à niveau.

## <a name="prerequisites"></a>Prérequis 

Pour commencer, assurez-vous que vous disposez des autorisations appropriées et que votre Azure Stack est prêt.

1. Vérifiez que vous pouvez créer des applications dans votre client Azure Active Directory (Azure AD). Ces autorisations sont nécessaires pour le déploiement de Kubernetes.

    Pour obtenir des instructions sur la vérification de vos autorisations, voir [Vérifier les autorisations Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Générez une paire de clés publique et privée SSH pour vous connecter à la machine virtuelle Linux sur Azure Stack. Vous aurez besoin de la clé publique lors de la création du cluster.

    Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Vérifiez que vous avez un abonnement valide dans votre portail de client Azure Stack et que vous disposez d’un nombre suffisant d’adresses IP publiques pour ajouter de nouvelles applications.

    Vous ne pouvez pas déployer le cluster sur un abonnement **Administrateur** Azure Stack. Vous devez utiliser un abonnement Utilisateur**. 

## <a name="create-a-service-principal-in-azure-ad"></a>Créer un principal de service dans Azure AD

1. Connectez-vous au [portail Azure](http://portal.azure.com) global.
2. Vérifiez que vous vous êtes connecté à l’aide du locataire Azure AD associé à l’instance Azure Stack.
3. Créez une application Azure AD.

    a. Sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

    b. Entrez le **nom** de l’application.

    c. Sélectionnez **Application web/API**.

    d. Entrez l’**URL de connexion** `http://localhost`.

    c. Cliquez sur **Créer**.

4. Notez l’**ID d’application**. Vous aurez besoin de l’ID lors de la création du cluster. L’ID est référencé en tant qu’**ID client du principal de service**.

5. Sélectionnez **Paramètres** > **Clés**.

    a. Entrez la **Description**.

    b. Pour **Expire**, sélectionnez **N’expire jamais**.

    c. Sélectionnez **Enregistrer**. Notez la chaîne de clé. Vous aurez besoin de la chaîne de clé lors de la création du cluster. La clé est référencée comme **Secret client du principal de service**.



## <a name="give-the-service-principal-access"></a>Accorder l’accès au principal de service

Accordez l’accès à votre abonnement au principal de service afin qu’il puisse créer des ressources.

1.  Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

2. Sélectionnez **Autres services** > **Abonnements utilisateur** > **+ Ajouter**.

3. Sélectionnez l’abonnement que vous avez créé.

4. Sélectionnez **Contrôle d’accès (IAM)** > **+ Ajouter**.

5. Sélectionnez le rôle **Propriétaire**.

6. Sélectionnez le nom de l’application créée pour votre principal de service. Vous devrez peut-être taper le nom dans la zone de recherche.

7. Cliquez sur **Enregistrer**.

## <a name="deploy-a-kubernetes-cluster"></a>Déployer un cluster Kubernetes

1. Ouvrez le [portail Azure Stack](https://portal.local.azurestack.external).

2. Sélectionnez **+ Nouveau** > **Compute** > **cluster Kubernetes**. Cliquez sur **Créer**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Sélectionnez **Bases** dans le volet Créer un cluster Kubernetes.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Entrez le **nom de l’utilisateur administrateur de la machine virtuelle Linux**. Nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.

3. Entrez la **Clé publique SSH** utilisée pour l’autorisation sur toutes les machines Linux, créée en même temps que le cluster Kubernetes et DVM.

4. Entrez le **Préfixe DNS du profil principal** qui est unique pour la région. Il doit s’agir d’un nom unique pour la région, tel que `k8s-12345`. Nous vous recommandons d’essayer de choisir le même préfixe que celui du nom du groupe de ressources.

    > [!Note]  
    > Pour chaque cluster, utilisez un préfixe DNS de profil principal nouveau et unique.

5. Entrez le **nombre de profils du pool d’agents**. Il s’agit du nombre d’agents dans le cluster. Il peut être compris entre 1 et 4.

6. Entrez **l’ID client du principal de service**. Celui-ci est utilisé par le fournisseur cloud d’Azure Kubernetes.

7. Entrez le **Secret client du principal de service** que vous avez créé au moment de la création de l’application du principal de service.

8. Entrez la **version du fournisseur cloud d’Azure Kubernetes**. Il s’agit de la version du fournisseur d’Azure Kubernetes. Azure Stack publie une build Kubernetes personnalisée pour chaque version d’Azure Stack.

9. Sélectionnez votre ID **d’abonnement**.

10. Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules.

11. Sélectionnez **l’emplacement** du groupe de ressources. Il s’agit de la région que vous avez choisie pour votre installation Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Spécifier les paramètres Azure Stack

1. Sélectionnez les **Paramètres de tampon Azure Stack**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Entrez le **point de terminaison ARM du locataire**. Il s’agit du point de terminaison Azure Resource Manager auquel se connecter pour créer le groupe de ressources pour le cluster Kubernetes. Vous devez obtenir le point de terminaison de votre opérateur Azure Stack pour un système intégré. Pour le Kit de développement Azure Stack (ASDK), vous pouvez utiliser `https://management.local.azurestack.external`.

3. Entrez **l’ID du locataire**. Si vous avez besoin d’aide pour trouver cette valeur, voir [Obtenir l’ID de client](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Se connecter au cluster

Vous êtes maintenant prêt à vous connecter à votre cluster. Le maître se trouve dans votre groupe de ressources de cluster et est nommé `k8s-master-<sequence-of-numbers>`. Utilisez un client SSH pour vous connecter au maître. Sur le maître, vous pouvez utiliser **kubectl**, client de ligne de commande Kubernetes, pour gérer le cluster. Pour obtenir des instructions, consultez [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Le gestionnaire de package **Helm** peut également s’avérer utile pour installer et déployer des applications sur votre cluster. Pour obtenir des instructions sur l’installation et l’utilisation de Helm avec votre cluster, consultez [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un cluster Kubernetes à la Place de marché (pour l’opérateur Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes sur Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
