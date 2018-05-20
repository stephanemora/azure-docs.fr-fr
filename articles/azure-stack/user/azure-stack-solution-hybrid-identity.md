---
title: Configurer l’identité de cloud hybride avec des applications Azure et Azure Stack | Microsoft Docs
description: Découvrez comment configurer l’identité de cloud hybride avec des applications Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Tutoriel : Configurer l’identité de cloud hybride avec des applications Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Il y a deux moyens d’accorder l’accès à des applications à la fois dans Azure global et dans Azure Stack. Si Azure Stack dispose d’une connexion permanente à Internet, il est possible d’utiliser Azure Active Directory (Azure AD). Dans le cas contraire, vous pouvez opter pour les services de fédération Active Directory (AD FS). Les principaux du service vous permettent d’accorder l’accès à vos applications dans Azure Stack à des fins de déploiement ou de configuration Azure Resource Manager dans Azure Stack. 

Dans ce tutoriel, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> * établir une identité hybride dans Azure global et Azure Stack ;
> * récupérer un jeton pour accéder à l’API Azure Stack.

Ces étapes exigent des autorisations d’opérateur Azure Stack.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Créer un principal du service pour Azure AD sur le portail

Si vous avez déployé Azure Stack avec Azure AD comme magasin d’identités, vous pouvez créer des principaux de service de la même façon que pour Azure. [Ce document](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) explique comment effectuer ces actions sur le portail. Vérifiez que vous disposez des [autorisations Azure AD requises](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) avant de commencer.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Créer un principal du service pour AD FS avec PowerShell

Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour créer un principal de service, attribuer un rôle pour l’accès et vous connecter à partir de PowerShell avec cette identité. [Ce document](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) explique comment effectuer ces actions avec PowerShell.

## <a name="using-the-azure-stack-api"></a>Utiliser l’API Azure Stack

[Ce tutoriel](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) décrit le processus de récupération d’un jeton permettant d’accéder à l’API Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Se connecter à Azure Stack avec PowerShell

Voici un exemple de script appliquant les concepts abordés dans ce document pour se connecter à Azure Stack.

### <a name="prerequisites"></a>Prérequis


Une installation Azure Stack connectée à Azure Active Directory avec un abonnement accessible. Si Azure Stack n’est pas installé, vous pouvez suivre ces instructions pour configurer un [Kit de développement Azure Stack](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Ce tutoriel](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) décrit la procédure à suivre pour installer Azure PowerShell et se connecter à une installation Azure Stack.

#### <a name="connect-to-azure-stack-using-code"></a>Se connecter à Azure Stack avec du code

Pour vous connecter à Azure Stack avec du code, utilisez l’API des points de terminaison Azure Resource Manager pour obtenir les points de terminaison d’authentification et de graphique de votre installation Azure Stack, puis authentifiez-vous à l’aide de demandes REST. Vous trouverez [ici](https://github.com/shriramnat/HybridARMApplication) un exemple d’application.

> [!Note]  
Si le Kit SDK Azure du langage de votre choix ne prend pas en charge les profils d’API Azure, il risque de ne pas fonctionner avec Azure Stack. Pour plus d’informations sur les profils d’API Azure, rendez-vous [ici](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur la gestion des identités dans Azure Stack, voir [Architecture d’identité d’Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Pour plus d’informations sur les modèles cloud Azure, voir [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
