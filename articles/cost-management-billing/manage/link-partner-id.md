---
title: Lier un compte Azure à un ID partenaire
description: Suivez les engagements avec les clients Azure en liant les ID partenaires au compte utilisateur que vous utilisez pour gérer les ressources du client.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 07/24/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: dc4d319e0e6b55af8af460fa8a56b9ef24a53341
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487349"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Lier un ID partenaire à vos comptes Azure

Les partenaires de Microsoft fournissent des services qui aident les clients à atteindre leurs objectifs commerciaux et stratégiques, à l’aide de produits Microsoft. Lorsqu’il agit au nom du client pour la gestion, la configuration et le support des services Azure, le partenaire doit accéder à l’environnement du client. En utilisant le lien d’administrateur partenaire (PAL), les partenaires peuvent associer leur ID Microsoft Partner Network aux informations d’identification utilisées pour la fourniture de service.

PAL permet à Microsoft d’identifier et de gratifier les partenaires qui favorisent la réussite des clients Azure. Microsoft peut attribuer une influence et un revenu consommé Azure à votre organisation en fonction des autorisations du compte (rôle Azure) et de l’étendue (abonnement, groupe de ressources, ressource).

## <a name="get-access-from-your-customer"></a>Accéder à votre client

Avant de lier votre ID partenaire, votre client doit vous donner accès à ses ressources Azure en utilisant l’une des options suivantes :

- **Utilisateur invité** : votre client peut vous ajouter en tant qu’utilisateur invité et attribuer des rôles Azure. Pour plus d’informations, consultez [Ajouter des utilisateurs invités à partir d’un autre répertoire](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Compte Active Directory** : votre client peut créer un compte d’utilisateur pour vous dans son propre répertoire et affecter un rôle Azure.

- **Principal du service** : votre client peut ajouter une application ou un script provenant de votre organisation dans son répertoire et affecter un rôle Azure. L’identité de l’application ou du script correspond à un principal du service.

- **Azure Lighthouse** : Votre client peut déléguer un abonnement (ou un groupe de ressources) pour permettre à vos utilisateurs de l’utiliser à partir de votre locataire. Pour plus d’informations, consultez [Gestion des ressources déléguées Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).

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

En revanche, si vous gérez les ressources d’un client via Azure Lighthouse, vous devez créer le lien dans votre locataire de fournisseur de services, en utilisant un compte qui a accès aux ressources du client. Pour plus d’informations, consultez [Lier votre ID de partenaire pour activer le crédit Partenaires sur des ressources déléguées](../../lighthouse/how-to/partner-earned-credit.md).

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

**Comment puis-je lier mon ID partenaire si ma société utilise [Azure Lighthouse](../../lighthouse/overview.md) pour accéder aux ressources client ?**

Si vous intégrez des clients à la gestion des ressources déléguées Azure en [publiant une offre de services managés sur la Place de marché Azure](../../lighthouse/how-to/publish-managed-services-offers.md), votre ID MPN est automatiquement associé.

Si vous [intégrez des clients en déployant des modèles Azure Resource Manager](../../lighthouse/how-to/onboard-customer.md), vous devez associer votre ID MPN à au moins un compte d’utilisateur qui a accès à chacun de vos abonnements intégrés. Notez que vous devez faire cela dans votre locataire de fournisseur de services et non dans le locataire de chaque client. Pour simplifier, nous vous recommandons de créer un compte de principal de service dans votre locataire, de l’associer à votre ID MPN, puis d’accorder à chaque client que vous intégrez un accès à ce compte avec un [rôle intégré Azure éligible au crédit Partenaires](/partner-center/azure-roles-perms-pec). Pour plus d’informations, consultez [Lier votre ID de partenaire pour activer le crédit Partenaires sur des ressources déléguées](../../lighthouse/how-to/partner-earned-credit.md).

**Comment expliquer Partner Admin Link (PAL) à mon client ?**

Partner Admin Link (PAL) permet à Microsoft d’identifier et de gratifier les partenaires qui aident les clients à atteindre leurs objectifs métier et à rapporter des bénéfices dans le cloud. Les clients doivent d’abord fournir un accès partenaire à leur ressource Azure. Une fois l’accès accordé, l’ID MPN (Microsoft Partner Network) du partenaire est associé. Cette association permet à Microsoft de comprendre l’écosystème des fournisseurs de services informatiques et d’affiner les outils et les programmes nécessaires à la prise en charge optimale de nos clients courants.

**Quelles sont les données collectées par PAL ?**

L’association PAL aux informations d’identification existantes ne fournit pas de nouvelles données client à Microsoft. Elle fournit simplement la télémétrie à Microsoft où un partenaire est activement impliqué dans l’environnement Azure d’un client. Microsoft peut attribuer une influence et un revenu consommé Azure depuis l’environnement client vers l’organisation partenaire en fonction des autorisations du compte (rôle Azure) et de l’étendue (groupe d’administration, abonnement, groupe de ressources, ressource) fournies au partenaire par le client. 

**Cela a-t-il un impact sur la sécurité de l’environnement Azure d’un client ?**

L’association PAL ajoute uniquement l’ID MPN du partenaire aux informations d’identification déjà provisionnées et ne modifie aucune autorisation (rôle Azure) ni ne fournit de données de service Azure supplémentaires au partenaire ou à Microsoft.
