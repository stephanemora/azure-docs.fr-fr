---
title: Lier un compte Azure à un ID partenaire | Documents Microsoft
description: Suivez les engagements avec les clients Azure en liant les ID partenaires au compte utilisateur que vous utilisez pour gérer les ressources du client.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: cwatson
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a0dad228f23b055d68009eb737e0347ade49e94b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968046"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Lier un ID partenaire à vos comptes Azure

En tant que partenaire, vous pouvez suivre votre impact sur les engagements clients en liant votre ID partenaire aux comptes utilisés pour gérer les ressources du client.

Cette fonctionnalité est disponible dans une préversion publique.

## <a name="get-access-from-your-customer"></a>Accéder à votre client

Avant de lier votre ID partenaire, votre client doit vous donner accès à ses ressources Azure en utilisant l’une des options suivantes :

- **Utilisateur invité :** votre client peut vous ajouter en tant qu’utilisateur invité et attribuer des rôles RBAC. Pour plus d’informations, consultez [Ajouter des utilisateurs invités à partir d’un autre répertoire](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Compte Active Directory :**  votre client peut créer un compte d’utilisateur pour vous dans son propre répertoire et affecter un rôle RBAC.

- **Principal du service :**  votre client peut ajouter une application ou un script provenant de votre organisation dans son répertoire et affecter un rôle RBAC. L’identité de l’application ou du script est celle de « principal de service ».

## <a name="link-partner-id"></a>Lier un ID partenaire

Lorsque vous avez accès aux ressources du client, utilisez le portail Azure, PowerShell ou CLI pour lier votre ID Microsoft Partner Network (ID MPN) à votre ID d’utilisateur ou de principal de service. Vous devez lier l’ID partenaire dans chaque locataire client.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Utiliser le portail Azure pour lier le nouvel ID partenaire

1. Accédez à [Lien vers un ID partenaire](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) dans le portail Azure.

2. Connectez-vous au portail Azure.

3. Entrez l’ID partenaire Microsoft. L’ID partenaire est l’ID [MPN (Microsoft Partner Network)](https://partner.microsoft.com/) de votre organisation.

  ![Capture d’écran qui affiche l’ID de partenaire de lien](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Pour lier un ID partenaire à un autre client, utilisez le sélecteur de répertoire. Sous Changer de répertoire, choisissez votre répertoire.

  ![Capture d’écran qui affiche l’ID de partenaire de lien](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Utiliser PowerShell pour lier un nouvel ID partenaire

1. Installez le module PowerShell [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner).

2. Connectez-vous au locataire du client avec le compte d’utilisateur ou de principal de service. Pour plus d’informations, consultez [Connexion avec Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Liez le nouvel ID partenaire. L’ID partenaire est l’ID [MPN (Microsoft Partner Network)](https://partner.microsoft.com/) de votre entreprise.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Obtenir l’ID partenaire lié
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Mettre à jour l’ID partenaire lié
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Supprimer l’ID partenaire lié
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Utiliser CLI pour lier un nouvel ID partenaire
1.  Installez l’extension CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Connectez-vous au locataire du client avec le compte d’utilisateur ou du principal de service. Pour plus d’informations, consultez [Se connecter avec Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Liez le nouvel ID partenaire. L’ID partenaire est l’ID [MPN (Microsoft Partner Network)](https://partner.microsoft.com/) de votre entreprise.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obtenir l’ID partenaire lié
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Mettre à jour l’ID partenaire lié
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Supprimer l’ID partenaire lié
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Étapes suivantes

Participez à la discussion dans la [Communauté de partenaires Microsoft](https://aka.ms/PALdiscussion) pour recevoir des mises à jour ou envoyer des commentaires.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

**Qui peut lier l’ID partenaire ?**

Tout utilisateur de l’organisation partenaire qui gère les ressources Azure du client peut lier l’ID partenaire au compte.

**Une fois l’ID partenaire lié, peut-il être modifié ?**

Oui, un ID partenaire lié peut être modifié, ajouté ou supprimé.

**Que se passe-t-il lorsqu’un utilisateur dispose d’un compte dans plusieurs locataires clients ?**

Le lien entre l’ID partenaire et le compte est établi pour chaque locataire client.  Vous devez lier l’ID partenaire dans chaque locataire client.

**Un autre partenaire ou client peut-il modifier ou supprimer le lien vers l’ID partenaire ?**

Le lien est associé au niveau du compte d'utilisateur. Vous seul pouvez modifier ou supprimer le lien vers l’ID partenaire. Le client et l’autre partenaire ne peuvent pas modifier le lien vers l’ID partenaire. 
