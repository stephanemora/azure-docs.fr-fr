---
title: Lier un ID de partenaire à vos comptes Power Apps avec vos informations d’identification Azure
description: Cet article explique aux partenaires Microsoft comment utiliser leurs informations d’identification Azure pour aider les clients à utiliser Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727705"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Lier un ID de partenaire à vos comptes Power Apps

Cet article explique aux partenaires Microsoft fournisseurs de services Power Apps comment associer leur service aux clients sur Microsoft Power Apps. Quand vous (partenaire Microsoft) assurez la gestion, la configuration et le support de services Power Apps pour votre client, vous avez accès à son environnement. Vous pouvez utiliser vos informations d’identification Azure et un lien d’administrateur partenaire (PAL, Partner Admin Link) pour associer votre ID réseau partenaire aux informations d’identification de compte utilisées pour la fourniture de service.

Ce lien PAL permet à Microsoft d’identifier et de reconnaître les partenaires qui ont des clients Power Apps. Microsoft attribut l’utilisation à l’organisation d’un partenaire en fonction des autorisations du compte (rôle Power Apps) et de l’étendue (locataire, groupe de ressources, ressource).

## <a name="get-access-from-your-customer"></a>Accéder à votre client

Avant de lier votre ID partenaire, vous devez obtenir de votre client l’accès à ses ressources Power Apps suivant l’une des options ci-après :

- **Utilisateur invité** : Votre client peut vous ajouter comme utilisateur invité et attribuer n’importe quel rôle Power Apps. Pour plus d’informations, consultez [Ajouter des utilisateurs invités à partir d’un autre répertoire](../../active-directory/external-identities/what-is-b2b.md).
- **Compte de répertoire** : Votre client peut créer un compte d’utilisateur pour vous dans son propre répertoire et attribuer n’importe quel rôle Power Apps.
- **Principal de service** : Votre client peut ajouter une application ou un script de votre organisation dans son répertoire et attribuer n’importe quel rôle Power Apps. L’identité de l’application ou du script correspond à un principal du service.
- **Administrateur délégué** : Votre client peut déléguer un groupe de ressources pour permettre à vos utilisateurs de l’utiliser à partir de votre locataire. Pour plus d’informations, consultez [Pour les partenaires : l’administrateur délégué](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Lier un client à un ID partenaire

Quand vous avez accès aux ressources de votre client, utilisez le portail Azure, PowerShell ou Azure CLI pour lier votre ID Microsoft Partner Network (ID MPN) à votre ID d’utilisateur ou principal de service. Liez l’ID partenaire à chaque locataire client.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utiliser le portail Azure pour définir un lien vers un nouvel ID partenaire

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à [Lien vers un ID partenaire](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) dans le portail Azure.
1. Entrez l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation. Veillez à utiliser l’**ID MPN associé** qui est indiqué dans votre profil partenaire.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Capture d’écran montrant la fenêtre Lien vers un ID partenaire." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Pour lier votre ID partenaire à un autre client, changez de répertoire. Sous **Changer de répertoire**, sélectionnez le répertoire approprié.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Capture d’écran montrant la fenêtre Répertoire + abonnement vous permettant de changer de répertoire." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utiliser PowerShell pour lier vers un nouvel ID partenaire

Installez le module Azure PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

Connectez-vous au locataire du client avec le compte d’utilisateur ou le principal du service. Pour plus d’informations, consultez [Se connecter avec PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Définissez le lien vers le nouvel ID partenaire. L’ID partenaire est l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation. Veillez à utiliser l’**ID MPN associé** qui est indiqué dans votre profil partenaire.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Obtenir l’ID partenaire lié

```azurepowershell-interactive
get-AzManagementPartner
```

Mettre à jour l’ID partenaire lié

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Supprimer l’ID partenaire lié

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Utiliser Azure CLI pour définir un lien vers un nouvel ID partenaire

Installez tout d’abord l’extension Azure CLI.

```azurecli-interactive
az extension add --name managementpartner
```

Connectez-vous au locataire du client avec le compte d’utilisateur ou le principal du service. Pour plus d’informations, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Définissez le lien vers le nouvel ID partenaire. L’ID partenaire est l’ID [Microsoft Partner Network](https://partner.microsoft.com/) de votre organisation.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Obtenir l’ID partenaire lié

```azurecli-interactive
az managementpartner show
```

Mettre à jour l’ID partenaire lié

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Supprimer l’ID partenaire lié

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>Étapes suivantes

- Consultez les [questions fréquentes (FAQ) sur Cost Management + Billing](../cost-management-billing-faq.yml) pour voir les questions et réponses sur la liaison d’un ID partenaire à des comptes Power Apps.
- Participez à la discussion dans la [Communauté de partenaires Microsoft](https://aka.ms/PALdiscussion) pour recevoir des mises à jour ou envoyer des commentaires.
- Consultez les questions fréquentes (FAQ) du document [Low Code Application Development advanced specialization](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) (Spécialisation avancée sur le développement d’applications avec peu de code) pour en savoir plus sur l’association de Power Apps basée sur un lien PAL pour la spécialisation avancée sur le développement d’applications avec peu de code.
