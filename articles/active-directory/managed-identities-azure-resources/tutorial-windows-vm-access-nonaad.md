---
title: Tutoriel `:` Utiliser une identité managée pour accéder à Azure Key Vault - Windows - Azure AD
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité managée attribuée par le système de machine virtuelle Windows pour accéder à Azure Key Vault.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2890eb2211ac0a105363742a0e900e52a577ed27
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255823"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutoriel : Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité managée attribuée par le système pour une machine virtuelle Windows afin d’accéder à Azure Key Vault. En agissant comme un amorçage, le coffre de clés permet à votre application cliente d’utiliser le secret pour accéder aux ressources non sécurisées par Azure Active Directory (AD). Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. 

Vous allez apprendre à effectuer les actions suivantes :


> [!div class="checklist"]
> * Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault 
> * Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer un secret de Key Vault 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Activer

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Accorder l'accès  
 
Cette section montre comment accorder à votre machine virtuelle l’accès à un secret stocké dans un coffre de clés. À l’aide d’identités managées pour ressources Azure, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD.  Toutefois, tous les services Azure ne prennent pas en charge l’authentification Azure AD. Pour utiliser des identités managées pour les ressources Azure avec ces services, stockez les informations d’identification des services dans Azure Key Vault, puis utilisez des identités managées de machine virtuelle afin d’accéder à Key Vault pour récupérer les informations d’identification. 

Tout d’abord, nous devons créer un Key Vault et accorder son accès à l’identité managée attribuée par le système de machine virtuelle.   

1. En haut de la barre de navigation de gauche, sélectionnez **Créer une ressource** > **Sécurité + Identité** > **Key Vault**.  
2. Entrez un **Nom** pour le nouveau Key Vault. 
3. Recherchez le Key Vault dans le même abonnement et le même groupe de ressources que la machine virtuelle créée précédemment. 
4. Sélectionnez des **Stratégies d’accès** et cliquez sur le bouton **Ajouter un nouveau**. 
5. Dans Configurer à partir du modèle, sélectionnez **Gestion de secret**. 
6. Choisissez **Sélectionner le principal**, et dans la zone de recherche entrez le nom de la machine virtuelle créée précédemment.  Sélectionnez la machine virtuelle dans la liste des résultats, cliquez sur **Sélectionner** . 
7. Cliquez sur **OK** pour terminer l’ajout de la nouvelle stratégie d’accès, puis sur **OK** pour terminer la sélection de stratégie d’accès. 
8. Cliquez sur **Créer** pour terminer la création de Key Vault. 

    ![Texte de remplacement d’image](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ensuite, ajoutez un secret à Key Vault, afin de pouvoir le retrouver à l’aide du code en cours d’exécution dans votre machine virtuelle : 

1. Sélectionnez **Toutes les ressources**, puis sélectionnez le coffre de clés créé précédemment. 
2. Sélectionnez **Secret**, puis cliquez sur **Ajouter**. 
3. Dans les **Options de chargement**, sélectionnez **Manuel**. 
4. Entrez un nom et une valeur pour le secret.  Vous pouvez choisir la valeur de votre choix. 
5. Laissez les champs pour la date d’activation et la date d’expiration vides, puis pour **Activé**, laissez la valeur **Oui**. 
6. Cliquez sur **Créer** pour créer le secret. 
 
## <a name="access-data"></a>Accéder aux données  

Cette section montre comment obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et comment l’utiliser pour récupérer un secret dans le coffre de clés. Si PowerShell 4.3.1 ou version ultérieure n’est pas installé, vous devez [télécharger et installer la dernière version](/powershell/azure/).

Tout d’abord, nous utilisons l’identité managée attribuée par le système de machine virtuelle pour obtenir un jeton d’accès permettant de s’authentifier auprès de Key Vault :
 
1. Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows. Puis, dans **Vue d’ensemble**, cliquez sur **Connecter**.
2. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la **machine virtuelle Windows**.  
3. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez PowerShell dans la session à distance.  
4. Dans PowerShell, appelez la requête web sur le client pour obtenir le jeton de l’hôte local dans le port spécifique pour la machine virtuelle.  

    La requête PowerShell :
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Ensuite, extrayez la réponse complète qui est stockée sous forme de chaîne au format JavaScript Objet Notation (JSON) dans l’objet $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Ensuite, extrayez le jeton d’accès de la réponse.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Enfin, utilisez la commande Invoke-WebRequest de PowerShell pour récupérer le secret que vous avez créé précédemment dans Key Vault et transmettre le jeton d’accès vers l’en-tête d’autorisation.  Vous avez besoin de l’URL de votre Key Vault qui se trouve dans la section **Bases** de la page **Vue d’ensemble** de Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    La réponse aura l’aspect suivant : 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Après avoir récupéré le secret à partir de Key Vault, vous pouvez l’utiliser pour vous authentifier sur un service qui requiert un nom et un mot de passe. 


## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Key Vault.  Pour en savoir plus sur Azure Key Vault, consultez :

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)