---
title: Obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats | Microsoft Docs
description: Explique comment utiliser l’API Génération de rapports Azure AD avec les informations d’identification de certificat pour obtenir des données à partir de répertoires sans intervention de l’utilisateur.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7e2dd4c50a1d6995302c5a2a6f9b4877253d0a41
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382278"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats

Les [API de création de rapports Azure Active Directory (Azure AD)](concept-reporting-api.md) vous fournissent un accès par programmation aux données par le biais d’un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation. Pour accéder à l’API de création de rapports Azure AD sans intervention de l’utilisateur, vous pouvez configurer votre accès afin d’utiliser des certificats.

Cela implique les étapes suivantes :

1. [Installer les composants requis](#install-prerequisites)
2. [Inscrire le certificat dans votre application](#register-the-certificate-in-your-app)
3. [Obtenir un jeton accès pour l’API MS Graph](#get-an-access-token-for-ms-graph-api)
4. [Interroger les points de terminaison de l’API MS Graph](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Installation des composants requis

1. Commencez par vous assurer que les [prérequis à l’accès à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) sont satisfaits. 

2. Téléchargez et installez Azure AD Powershell V2 en suivant les instructions indiquées dans [Azure Active Directory PowerShell(https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)

3. Installez MSCloudIDUtils à partir de [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ce module fournit plusieurs applets de commande d’utilitaire, notamment :
    - Les bibliothèques d’authentification Active Directory (ADAL) nécessaires pour l’authentification
    - Les jetons d’accès d’utilisateur, de clés d’application et de certificats à l’aide d’ADAL
    - L’API Graph gérant les résultats paginés

4. S’il s’agit de la première utilisation du module, exécutez **Install-MSCloudIdUtilsModule** pour terminer l’installation. Dans le cas contraire, vous pouvez l’importer simplement à l’aide de la commande Powershell **Import-Module**.

Votre session doit ressembler à ce qui suit :

  ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Inscrire le certificat dans votre application

1. Tout d’abord, accédez à la page d’inscription de votre application. Pour ce faire, accédez au [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**, puis sur **Inscriptions des applications** et choisissez votre application dans la liste. 

2. Ensuite, suivez les étapes pour [enregistrer votre certificat auprès d’Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) pour l’application. 

3. Notez l’ID de l’application et l’empreinte du certificat que vous venez d’enregistrer avec votre application. Pour rechercher l’empreinte, à partir de la page de votre application dans le portail, accédez à **Paramètres** et cliquez sur **Clés**. L’empreinte se trouve sous la liste **Clés publiques**.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Obtenir un jeton accès pour l’API MS Graph

Pour obtenir un jeton d’accès pour l’API MS Graph, utilisez la cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** à partir du module PowerShell MSCloudIdUtils. 

>[!NOTE]
>Vous devez utiliser l’ID de l’application (également appelé ID de client) et l’empreinte du certificat avec la clé privée installée dans le magasin de certificats de votre ordinateur (magasin de certificats CurrentUser ou LocalMachine).
>

 ![Portail Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Utilisation du jeton d’accès pour appeler l’API Graph

Maintenant, vous pouvez utiliser le jeton d’accès dans votre script PowerShell pour interroger l’API Graph. Vous trouverez ci-dessous un exemple utilisant la cmdlet **Invoke-MSCloudIdMSGraphQuery** à partir de MSCloudIDUtils pour énumérer le point de terminaison SignIns ou DirectoryAudits. Cette applet de commande gère les résultats composés de plusieurs pages et les envoie ensuite dans le pipeline PowerShell.

### <a name="query-the-directoryaudits-endpoint"></a>Interroger le point de terminaison DirectoryAudits
 ![Portail Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Interroger le point de terminaison SignIns
 ![Portail Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

Vous pouvez maintenant choisir d’exporter ces données dans un fichier CSV et de les enregistrer dans un système SIEM. Vous pouvez également encapsuler votre script dans une tâche planifiée pour obtenir régulièrement des données Azure AD à partir de votre client sans avoir à stocker des clés d’application dans le code source. 


## <a name="next-steps"></a>Étapes suivantes

* [Première impression des API de création de rapports](concept-reporting-api.md)
* [Informations de référence sur l’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informations de référence sur l’API de création de rapports relatifs à l’activité de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



