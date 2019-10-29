---
title: Intégrer un client dans la gestion des ressources déléguées Azure
description: Découvrez comment intégrer un client à la gestion des ressources déléguées Azure, permettant ainsi que ses ressources soient accessibles et gérables via votre propre locataire.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/17/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 882afb83aa2a9bad9633df43b29e00b43162bf87
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595660"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Intégrer un client dans la gestion des ressources déléguées Azure

Cet article explique comment, en tant que fournisseur de services, vous pouvez intégrer un client à la gestion des ressources déléguées Azure, permettant ainsi que ses ressources déléguées (abonnements et groupes de ressources) soient accessibles et gérables via votre propre locataire Azure Active Directory (Azure AD). Si nous faisons référence ici aux fournisseurs de services et aux clients, des entreprises gérant plusieurs locataires peuvent suivre le même processus pour consolider leur expérience de gestion.

Vous pouvez répéter ce processus si vous gérez des ressources pour plusieurs clients. Ensuite, quand un utilisateur autorisé se connecte à votre locataire, il peut être autorisé à accéder aux étendues de location des clients pour effectuer des opérations de gestion sans avoir à se connecter individuellement à chaque locataire du client.

Vous pouvez associer votre ID Microsoft Partner Network (MPN) avec vos abonnements intégrés pour suivre votre impact sur les engagements client et recevoir une reconnaissance. Pour plus d’informations, voir [Lier un ID partenaire à vos comptes Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Notez que vous devez effectuer cette association dans le locataire de votre fournisseur de services.

> [!NOTE]
> Les clients peuvent également être intégrés quand ils achètent une offre de services managés (publics ou privés) que vous avez publiée sur la Place de marché Azure. Pour plus d’informations, voir [Publier des offres de services managés sur la Place de marché Azure](publish-managed-services-offers.md). Vous pouvez également utiliser le processus d’intégration décrit ici avec une offre publiée sur la Place de marché Azure.

Le processus d’intégration requiert d’exécuter des actions tant dans le locataire du fournisseur de services que dans le locataire du client. Toutes ces étapes sont décrites dans cet article.

> [!IMPORTANT]
> Actuellement, vous ne pouvez pas intégrer un abonnement (ou un groupe de ressources au sein d’un abonnement) pour la gestion des ressources déléguées Azure si l’abonnement utilise Azure Databricks. De même, si un abonnement a été inscrit pour une intégration avec le fournisseur de ressources **Microsoft.ManagedServices**, vous ne pouvez pas actuellement créer d’espace de travail Databricks pour cet abonnement.

## <a name="gather-tenant-and-subscription-details"></a>Collecter les détails du locataire et de l’abonnement

Pour qu’il soit possible d’intégrer le locataire d’un client, celui-ci doit disposer d’un abonnement Azure actif. Vous devez disposer des informations suivantes :

- L’ID de locataire du locataire du fournisseur de services (où vous allez gérer les ressources du client)
- L’ID de locataire du locataire du client (dont les ressources seront gérées par le fournisseur de services)
- Les ID d’abonnement de chaque abonnement dans le locataire du client qui sera géré par le fournisseur de services (ou qui contient les groupes de ressources qui seront gérés par le fournisseur de services)

Si vous ne disposez pas de ces informations, vous pouvez les récupérer de l’une des manières suivantes.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez afficher votre ID de locataire en pointant sur le nom de votre compte dans l’angle supérieur droit du portail Azure, ou en sélectionnant **Changer de répertoire**. Pour sélectionner et copier votre ID de locataire, recherchez « Azure Active Directory » dans le portail, puis sélectionnez **Propriétés** et copiez la valeur affichée dans le champ **ID de répertoire**. Pour trouver l’ID d’un abonnement, recherchez « abonnements », puis sélectionnez l’ID d’abonnement approprié.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Lors de l’intégration d’un abonnement (ou d’un ou de plusieurs groupes de ressources au sein d’un abonnement) à l’aide du processus décrit ici, le fournisseur de ressources **Microsoft.ManagedServices** sera inscrit pour cet abonnement.

## <a name="define-roles-and-permissions"></a>Définir des rôles et des autorisations

En tant que fournisseur de services, vous souhaitez peut-être utiliser plusieurs offres avec un seul client, ce qui requiert un accès différent pour les différentes étendues.

Pour faciliter la gestion, nous vous recommandons d’utiliser des groupes d’utilisateurs Azure AD pour chaque rôle, ce qui vous permet d’ajouter ou de supprimer des utilisateurs dans le groupe au lieu d’attribuer directement des autorisations aux utilisateurs. Vous pouvez également attribuer des rôles à un principal de service. Veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail, ce qui contribue à réduire le risque d’erreurs accidentelles. Pour plus d’informations, voir [Pratiques de sécurité recommandées](../concepts/recommended-security-practices.md).

> [!NOTE]
> Les attributions de rôles doivent utiliser des [rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) de contrôle d’accès en fonction du rôle (RBAC). Tous les rôles intégrés sont actuellement pris en charge avec la gestion des ressources déléguées Azure, à l’exception du propriétaire et des rôles intégrés avec l’autorisation [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions). Le rôle intégré Administrateur de l’accès utilisateur est pris en charge pour une utilisation limitée, comme décrit ci-dessous. Les rôles personnalisés et les [Rôles Administrateur classique de l’abonnement](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) ne sont pas non plus pris en charge.

Pour définir des autorisations, vous devez connaître les valeurs d’ID pour chaque utilisateur, groupe d’utilisateurs ou principal de service auquel vous souhaitez accorder l’accès. Vous aurez également besoin de l’ID de définition de rôle pour chaque rôle intégré que vous souhaitez attribuer. Si vous ne disposez pas des ces informations, vous pouvez les récupérer de l’une des manières suivantes.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager

Pour intégrer votre client, vous devez créer un modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) pour votre offre et y inclure les éléments suivants : Le client peut voir les valeurs **mspOfferName** et **mspOfferDescription** quand il consulte les détails de l’offre dans la page [Fournisseurs de services](view-manage-service-providers.md) du portail Azure.

|Champ  |Définition  |
|---------|---------|
|**mspOfferName**     |Nom décrivant cette définition. Cette valeur est présentée au client comme titre de l’offre.         |
|**mspOfferDescription**     |Brève description de votre offre (par exemple, « Offre de gestion des machines virtuelles Contoso »)      |
|**managedByTenantId**     |Votre ID d’abonné         |
|**authorizations**     |Les valeurs **principalId** pour les utilisateurs/groupes/noms de principal de service de votre locataire, chacune avec un **principalIdDisplayName** pour aider votre client à comprendre l’objectif de l’autorisation, et mappée à une valeur **roleDefinitionId** intégrée pour spécifier le niveau d’accès         |

Pour intégrer l’abonnement d’un client, utilisez le modèle Azure Resource Manager approprié que nous fournissons dans nos [exemples de référentiels](https://github.com/Azure/Azure-Lighthouse-samples/), ainsi qu’un fichier de paramètres correspondant que vous modifiez pour qu’il corresponde à votre configuration, puis définissez votre autorisations. Des modèles distincts sont fournis selon que vous intégrez un abonnement entier, un groupe de ressources ou plusieurs groupes de ressources au sein d’un abonnement. Nous fournissons également un modèle qui peut être utilisé pour des clients qui ont acheté une offre de service managé que vous avez publiée sur la Place de marché Azure, si vous préférez intégrer leurs abonnements de cette façon.

|**Pour intégrer ceci**  |**Utiliser ce modèle Azure Resource Manager**  |**Et modifier ce fichier de paramètres** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (lors de l’utilisation d’une offre publiée sur la Place de marché Azure)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Le processus décrit ici nécessite un déploiement distinct pour chaque abonnement intégré. Des déploiements distincts sont également requis si vous intégrez plusieurs groupes de ressources dans différents abonnements. Toutefois, l’intégration de plusieurs groupes de ressources au sein d’un même abonnement peut être effectuée dans un seul déploiement.
>
> De plus, vous devez déployer séparément chacune des offres qui sont appliquées à un même abonnement (ou aux groupes de ressources d’un abonnement). Chaque offre appliquée doit utiliser un **mspOfferName** différent.

L’exemple suivant montre un fichier **delegatedResourceManagement.parameters.json** qui sera utilisé pour l’intégration d’un abonnement. Les fichiers de paramètres de groupe de ressources (situés dans le dossier [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) sont similaires, mais incluent en outre un paramètre **rgName** pour identifier les groupes de ressources à intégrer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
La dernière autorisation dans l’exemple ci-dessus ajoute un **principalId** avec le rôle Administrateur de l’accès utilisateur (18d7d88d-d35e-4fb5-A5C3-7773c20a72d9). Lorsque vous attribuez ce rôle, vous devez inclure la propriété **delegatedRoleDefinitionIds** et un ou plusieurs rôles intégrés. L’utilisateur créé dans cette autorisation sera en mesure d’attribuer ces rôles intégrés à des [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Notez qu’aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’appliquera à cet utilisateur.

## <a name="deploy-the-azure-resource-manager-templates"></a>Déployer les modèles Azure Resource Manager

Une fois que vous avez mis à jour votre fichier de paramètres, le client doit déployer le modèle de gestion des ressources dans le locataire du client en tant que déploiement de niveau abonnement. Un déploiement distinct est nécessaire pour chaque abonnement que vous souhaitez intégrer à la gestion des ressources déléguées Azure (ou pour chaque abonnement contenant des groupes de ressources que vous souhaitez intégrer).

> [!IMPORTANT]
> Le déploiement doit être effectué par un compte non invité dans le locataire du client qui a le [rôle intégré Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pour l’abonnement en cours d’intégration (ou qui contient les groupes de ressources en cours d’intégration). Pour voir tous les utilisateurs qui peuvent déléguer l’abonnement, un utilisateur du locataire du client peut sélectionner l’abonnement dans le portail Azure, ouvrir **Contrôle d’accès (IAM)** et [afficher tous les utilisateurs ayant le rôle Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).


```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Vérifier la réussite de l’intégration

Quand un abonnement de client a été correctement intégré à la gestion des ressources déléguées Azure, les utilisateurs dans le locataire du fournisseur de services peuvent voir l’abonnement et ses ressources (si l’accès à ceux-ci leur a été octroyé via le processus ci-dessus, soit individuellement, soit en tant que membres d’un groupe Azure AD disposant des autorisations appropriées). Pour vérifier cela, assurez-vous que l’abonnement s’affiche de l’une des manières suivantes.  

### <a name="azure-portal"></a>Portail Azure

Dans le locataire du fournisseur de services :

1. Accédez à la [page Mes clients](view-manage-customers.md).
2. Sélectionnez **Clients**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

Dans le locataire du client :

1. Accédez à la [page Fournisseurs de services](view-manage-service-providers.md).
2. Sélectionnez **Offres de fournisseur de services**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

> [!NOTE]
> Quelques minutes peuvent s’écouler après votre déploiement, avant que les mises à jour apparaissent dans le portail Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
