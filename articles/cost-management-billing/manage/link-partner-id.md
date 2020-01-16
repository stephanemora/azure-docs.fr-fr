---
title: Lier un compte Azure à un ID partenaire | Microsoft Docs
description: Suivez les engagements avec les clients Azure en liant les ID partenaires au compte utilisateur que vous utilisez pour gérer les ressources du client.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 10/01/2019
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a67f2985e2db8c48d7e50a91d20c76b88c1c55e6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985633"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Lier un ID partenaire à vos comptes Azure

Les partenaires de Microsoft fournissent des services qui aident les clients à atteindre leurs objectifs commerciaux et stratégiques, à l’aide de produits Microsoft. Lorsqu’il agit au nom du client pour la gestion, la configuration et le support des services Azure, le partenaire doit accéder à l’environnement du client. À l’aide du lien d’administrateur partenaire, les partenaires peuvent associer leur ID Microsoft Partner Network aux informations d’identification utilisées pour la remise du service.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Accéder à votre client

Avant de lier votre ID partenaire, votre client doit vous donner accès à ses ressources Azure en utilisant l’une des options suivantes :

- **Utilisateur invité** : votre client peut vous ajouter en tant qu’utilisateur invité et vous attribuer des rôles de contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations, consultez [Ajouter des utilisateurs invités à partir d’un autre répertoire](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Compte Active Directory** : votre client peut créer un compte d’utilisateur pour vous dans son propre répertoire et affecter un rôle RBAC.

- **Principal du service** : votre client peut ajouter une application ou un script provenant de votre organisation dans son répertoire et affecter un rôle RBAC. L’identité de l’application ou du script correspond à un principal du service.

## <a name="link-to-a-partner-id"></a>Lien vers un ID partenaire

Lorsque vous avez accès aux ressources du client, utilisez le portail Azure, PowerShell ou Azure CLI pour lier votre ID Microsoft Partner Network (ID MPN) à votre ID d’utilisateur ou principal du service. Liez l’ID partenaire dans chaque locataire client.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utiliser le portail Azure pour définir un lien vers un nouvel ID partenaire

1. Accédez à [Lien vers un ID partenaire](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) dans le portail Azure.

2. Connectez-vous au portail Azure.

3. Entrez l’ID partenaire Microsoft. L’ID partenaire est l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation.

   ![Capture d’écran qui montre l’option Lien vers un ID partenaire](./media/link-partner-id/link-partner-id01.png)

4. Pour lier un ID partenaire d’un autre client, changez de répertoire. Sous **Changer de répertoire**, sélectionnez votre répertoire.

   ![Capture d’écran montrant Changer de répertoire](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utiliser PowerShell pour lier vers un nouvel ID partenaire

1. Installez le module PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Connectez-vous au locataire du client avec le compte d’utilisateur ou le principal du service. Pour plus d’informations, consultez [Se connecter avec PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Définissez le lien vers le nouvel ID partenaire. L’ID partenaire est l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obtenir l’ID partenaire lié
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Mettre à jour l’ID partenaire lié
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Supprimer l’ID partenaire lié
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Utiliser Azure CLI pour définir un lien vers un nouvel ID partenaire
1. Installez l’extension Azure CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Connectez-vous au locataire du client avec le compte d’utilisateur ou le principal du service. Pour plus d’informations, consultez [Se connecter avec Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Définissez le lien vers le nouvel ID partenaire. L’ID partenaire est l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation.

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

## <a name="frequently-asked-questions"></a>Forum aux questions

**Qui peut lier l’ID partenaire ?**

Tout utilisateur de l’organisation partenaire qui gère les ressources Azure du client peut lier l’ID partenaire au compte.

**Un ID partenaire peut-il être modifié une fois qu’il est lié ?**

Oui. Un ID partenaire lié peut être modifié, ajouté ou supprimé.

**Que se passe-t-il si un utilisateur dispose d’un compte dans plusieurs locataires clients ?**

Le lien entre l’ID partenaire et le compte est établi pour chaque locataire client. Liez l’ID partenaire dans chaque locataire client.

**D’autres partenaires ou clients peuvent-ils modifier ou supprimer le lien vers l’ID partenaire ?**

Le lien est associé au niveau du compte d'utilisateur. Vous seul pouvez modifier ou supprimer le lien vers l’ID partenaire. Le client et les autres partenaires ne peuvent pas modifier le lien vers l’ID partenaire.


**Quel ID MPN utiliser si ma société en possède plusieurs ?**

Les comptes associés aux emplacements partenaire et les ID MPN associés doivent être utilisés pour la liaison de l’ID du partenaire.  En savoir plus sur les [comptes de partenaires](https://docs.microsoft.com/partner-center/account-structure)

**Où trouver un rapport sur le chiffre d’affaires influencé pour l’ID de partenaire lié ?**

Les partenaires peuvent accéder aux rapports sur les performances des produits cloud en consultant le [tableau de bord Mes Insights](https://partner.microsoft.com/membership/reports/myinsights) de Partner Central. Vous devez sélectionner le lien d’administrateur partenaire en tant que le type d’association partenaire.

**Pourquoi ne puis-je pas voir mon client dans les rapports ?**

Vous ne voyez pas le client dans les rapports pour les raisons suivantes

1. Le compte d’utilisateur lié n’a pas d’[accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview) à un abonnement ou à une ressource Azure du client.

2. L’abonnement Azure auquel l’utilisateur a [accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview) n’est d’aucune utilité.

**L’option Lier un ID partenaire fonctionne-t-elle avec Azure Stack ?**

Oui, vous pouvez lier votre ID partenaire pour Azure Stack.
