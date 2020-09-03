---
title: Tutoriel `:` Utiliser une identité managée pour accéder à Azure Key Vault - Linux - Azure AD
description: Ce didacticiel vous guide tout au long du processus consistant à utiliser une identité managée attribuée par le système de machine virtuelle Linux pour accéder à Azure Resource Manager.
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
ms.date: 11/20/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6756d66f176314ad5abd0c94f7bdf96c42a460a4
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255307"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutoriel : Utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité managée assignée par le système pour une machine virtuelle Linux afin d’accéder à Azure Key Vault. En agissant comme un amorçage, le coffre de clés permet à votre application cliente d’utiliser le secret pour accéder aux ressources non sécurisées par Azure Active Directory (AD). Les identités managées pour les ressources Azure sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault 
> * Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer la clé secrète de Key Vault 
 
## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault  

À l’aide d’identités de service managées pour les ressources Azure, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure Active Directory. Toutefois, tous les services Azure ne prennent pas en charge l’authentification Azure AD. Pour utiliser des identités managées pour les ressources Azure avec ces services, stockez les informations d’identification des services dans Azure Key Vault, puis utilisez des identités managées pour des ressources Azure afin d’accéder à Key Vault pour récupérer les informations d’identification. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer la clé secrète de Key Vault  

Pour effectuer cette procédure, vous avez besoin d’un client SSH.  Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](/windows/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Dans le portail, accédez à votre machine virtuelle Linux et dans **Vue d’ensemble**, cliquez sur **Connexion**. 
2. **Connectez-vous** à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre de terminal, à l’aide de CURL, envoyez une requête aux identités managées locales pour le point de terminaison des ressources Azure en vue d’obtenir un jeton d’accès pour Azure Key Vault.  
 
    Vous trouverez la requête CURL pour le jeton d’accès ci-dessous.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    La réponse inclut le jeton d’accès dont vous avez besoin pour accéder au Gestionnaire des ressources. 
    
    Réponse :  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Vous pouvez utiliser ce jeton d’accès pour vous authentifier sur Azure Key Vault.  La requête CURL suivante montre comment lire un secret de Key Vault à l’aide de CURL et de l’API REST de Key Vault.  Vous avez besoin de l’URL de votre Key Vault qui se trouve dans la section **Bases** de la page **Vue d’ensemble** de Key Vault.  Vous avez également besoin du jeton d’accès que vous avez obtenu lors de l’appel précédent. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La réponse aura l’aspect suivant : 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Après avoir récupéré le secret à partir de Key Vault, vous pouvez l’utiliser pour vous authentifier sur un service qui requiert un nom et un mot de passe.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Key Vault.  Pour en savoir plus sur Azure Key Vault, consultez :

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)