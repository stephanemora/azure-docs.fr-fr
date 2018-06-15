---
title: Obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats | Microsoft Docs
description: Explique comment utiliser l’API Génération de rapports Azure AD avec les informations d’identification de certificat pour obtenir des données à partir de répertoires sans intervention de l’utilisateur.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 5ceb3d1ef99189e5b0cfcc48b38906ed28c07730
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588955"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats

Les [API de création de rapports Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournissent un accès par programmation aux données par le biais d’un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Pour accéder à l’API de création de rapports Azure AD sans intervention de l’utilisateur, vous pouvez configurer votre accès de sorte à utiliser des certificats.

Cet article :

- Fournit les étapes nécessaires pour accéder à l’API de création de rapports Azure AD à l’aide de certificats.
- Suppose que les [prérequis à l’accès à l’API de création de rapports Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md) sont satisfaits. 


Pour accéder à l’API de création de rapports avec des certificats, vous devez :

1. Installer les composants requis
2. Définition du certificat dans votre application 
3. Accorder des autorisations
4. Obtention d’un jeton d’accès




Pour plus d’informations sur le code source, consultez [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Utiliser le module d’API Génération de rapports). 

## <a name="install-prerequisites"></a>Installation des composants requis

Vous devez avoir installé Azure AD PowerShell V2 et le module AzureADUtils.

1. Téléchargez et installez Azure AD Powershell V2 en suivant les instructions dans [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Téléchargez le module Azure AD Utils à partir de [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Ce module fournit plusieurs applets de commande d’utilitaire, notamment :
    - La dernière version d’ADAL à l’aide de Nuget
    - Les jetons d’accès d’utilisateur, de clés d’application et de certificats à l’aide d’ADAL
    - L’API Graph gérant les résultats paginés

**Pour installer le module Azure AD Utils :**

1. Créez un répertoire pour enregistrer le module d’utilitaires (par exemple, c:\azureAD) et téléchargez le module à partir de GitHub.
2. Ouvrez une session PowerShell et accédez au répertoire que vous venez de créer. 
3. Importez le module et installez-le dans le chemin d’accès du module PowerShell à l’aide de l’applet de commande Install-AzureADUtilsModule. 

La session doit ressembler à ce qui suit :

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Définition du certificat dans votre application

**Pour définir le certificat dans votre application :**

1. [Obtenez l’ID d’objet](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) de votre application à partir du portail Azure. 

  ![Portail Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Ouvrez une session PowerShell et connectez-vous à Azure AD à l’aide de l’applet de commande Connect-AzureAD.

  ![Portail Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Utilisez l’applet de commande New-AzureADApplicationCertificateCredential d’AzureADUtils pour y ajouter des informations d’identification de certificat. 

>[!Note]
>Vous devez fournir l’ID d’objet de l’application que vous avez récupéré précédemment, ainsi que l’objet de certificat (récupérez-le à l’aide du lecteur Cert :).
>


  ![Portail Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Pour obtenir un jeton d’accès, utilisez l’applet de commande **Get-AzureADGraphAPIAccessTokenFromCert** depuis AzureADUtils. 

>[!NOTE]
>Vous devez utiliser l’ID d’application plutôt que l’ID d’objet que vous avez utilisé dans la dernière section.
>

 ![Portail Azure](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Utilisation du jeton d’accès pour appeler l’API Graph

Vous pouvez maintenant créer le script. Voici un exemple d’utilisation de l’applet de commande **Invoke-AzureADGraphAPIQuery** à partir du module AzureADUtils. Cette applet de commande gère les résultats composés de plusieurs pages et les envoie ensuite dans le pipeline PowerShell. 

 ![Portail Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Vous êtes maintenant prêt à exporter vers un fichier CSV et enregistrer dans un système SIEM. Vous pouvez également encapsuler votre script dans une tâche planifiée pour obtenir régulièrement des données Azure AD à partir de votre client sans avoir à stocker des clés d’application dans le code source. 

## <a name="next-steps"></a>Étapes suivantes

- [Première impression des API de création de rapports](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Créer votre propre solution](active-directory-reporting-api-getting-started-azure-portal.md#customize)




