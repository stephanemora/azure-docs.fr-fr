---
title: Intégrer un client à Azure Lighthouse
description: Apprenez à intégrer un client à Azure Lighthouse pour permettre l'accès à ses ressources et la gestion de celles-ci par les utilisateurs de votre locataire.
ms.date: 09/30/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dbd3eb285a0135ca1b86294c5d3a41ef88a8472
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353302"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Intégrer un client à Azure Lighthouse

Cet article explique comment, en tant que fournisseur de services, vous pouvez intégrer un client à Azure Lighthouse. Les ressources déléguées (abonnements et/ou groupes de ressources) dans le locataire Azure Active Directory (Azure AD) du client peuvent être gérées par les utilisateurs de votre locataire par le biais de la [gestion des ressources déléguées Azure](../concepts/architecture.md).

> [!TIP]
> Même si nous faisons ici référence aux fournisseurs de services et aux clients, les [entreprises gérant plusieurs locataires](../concepts/enterprise.md) peuvent suivre le même processus pour configurer Azure Lighthouse et consolider leur expérience de gestion.

Vous pouvez répéter le processus d’intégration pour plusieurs clients. Lorsqu'un utilisateur disposant des autorisations appropriées se connecte à votre locataire gérant, il peut être autorisé à accéder aux étendues de location du client pour effectuer des opérations de gestion sans avoir à se connecter individuellement à chaque locataire du client.

> [!NOTE]
> Les clients peuvent également être intégrés à Azure Lighthouse lorsqu’ils acquièrent une offre de service managé (publique ou privée) que vous [publiez dans la Place de marché Azure](publish-managed-services-offers.md). Vous pouvez également utiliser le processus d’intégration décrit ici en même temps que des offres publiées dans la Place de marché Azure.

Le processus d’intégration requiert d’exécuter des actions tant dans le locataire du fournisseur de services que dans le locataire du client. Toutes ces étapes sont décrites dans cet article.

## <a name="gather-tenant-and-subscription-details"></a>Collecter les détails du locataire et de l’abonnement

Pour qu’il soit possible d’intégrer le locataire d’un client, celui-ci doit disposer d’un abonnement Azure actif. Vous devez disposer des informations suivantes :

- L’ID de locataire du locataire du fournisseur de services (où vous allez gérer les ressources du client). Si vous [créez votre modèle dans le Portail Azure](#create-your-template-in-the-azure-portal), cette valeur est renseignée automatiquement.
- L’ID de locataire du locataire du client (dont les ressources seront gérées par le fournisseur de services).
- Les ID d’abonnement de chaque abonnement dans le locataire du client qui sera géré par le fournisseur de services (ou qui contient les groupes de ressources qui seront gérés par le fournisseur de services).

Si vous ne connaissez pas l’ID d’un locataire, vous pouvez [le récupérer à l’aide du portail Azure, d’Azure PowerShell ou d’Azure CLI](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="define-roles-and-permissions"></a>Définir des rôles et des autorisations

En tant que fournisseur de services, vous souhaitez peut-être effectuer plusieurs tâches pour un seul client, ce qui requiert un accès différent pour les différentes étendues. Vous pouvez définir autant d’autorisations que vous le souhaitez pour attribuer les [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md) nécessaires. Chaque autorisation comprend une **principalId** qui fait référence à un utilisateur, à un groupe ou à un principal de service Azure AD dans le locataire gérant.

> [!NOTE]
> Sauf spécification explicite, les références à un « utilisateur » dans la documentation Azure Lighthouse peuvent s’appliquer à un utilisateur, à un groupe ou à un principal de service Azure AD dans une autorisation.

Pour définir des autorisations, vous devez connaître les valeurs d’ID pour chaque utilisateur, groupe d’utilisateurs ou principal de service dans le locataire gérant auquel vous souhaitez accorder l’accès. Vous pouvez [récupérer ces ID à l’aide du portail Azure, d’Azure PowerShell ou d’Azure CLI](../../role-based-access-control/role-assignments-template.md#get-object-ids) à partir du locataire gérant. Vous aurez également besoin de l’ID de définition de rôle pour chaque [rôle intégré](../../role-based-access-control/built-in-roles.md) que vous souhaitez attribuer.

> [!TIP]
> Nous vous recommandons d’attribuer le [rôle de suppression de l’affectation d’inscription des services managés](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) lors de l’intégration d’un client, afin que les utilisateurs de votre locataire puissent [supprimer l’accès à la délégation](remove-delegation.md) ultérieurement si nécessaire. Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.

Nous vous recommandons d’utiliser des groupes d’utilisateurs Azure AD pour chaque rôle dans la mesure du possible, plutôt que des utilisateurs individuels. Cela vous donne la possibilité d’ajouter ou de supprimer des utilisateurs individuels dans le groupe qui a accès, afin de ne pas avoir à répéter le processus d’intégration pour apporter des modifications à l’utilisateur. Il est également possible d’attribuer des rôles à un principal de service, ce qui peut être utile dans les scénarios d’automatisation.

> [!IMPORTANT]
> Pour que vous puissiez ajouter des autorisations pour un groupe Azure AD, le **Type de groupe** doit être défini sur **Sécurité**. Cette option est sélectionnée lors de la création du groupe. Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Lorsque vous définissez vos autorisations, veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail. Pour plus d’informations sur les rôles pris en charge et les meilleures pratiques, consultez [Locataires, utilisateurs et rôles dans les scénarios Azure Lighthouse](../concepts/tenants-users-roles.md).

Pour suivre votre impact sur les engagements client et recevoir une reconnaissance, associez votre ID Microsoft Partner Network (MPN) à au moins un compte d’utilisateur qui a accès à tous vos abonnements intégrés. Vous devez effectuer cette association dans le locataire de votre fournisseur de services. Nous vous recommandons de créer un compte de principal de service dans votre locataire associé à votre ID MPN, puis d’inclure ce principal de service chaque fois que vous intégrez un client. Pour plus d’informations, consultez [Lier votre ID de partenaire pour activer le crédit Partenaires sur des ressources déléguées](partner-earned-credit.md).

> [!TIP]
> Vous pouvez également créer des *autorisations éligibles* qui permettent aux utilisateurs de votre locataire de gestion d’élever temporairement leur rôle. Cette fonctionnalité est actuellement en préversion publique et a des exigences spécifiques en matière de licences. Pour plus d’informations, consultez [Créer des autorisations éligibles](create-eligible-authorizations.md).

## <a name="create-an-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager

Pour intégrer votre client, vous devez créer un modèle [Azure Resource Manager](../../azure-resource-manager/index.yml) pour votre offre et y inclure les éléments suivants : Le client pourra voir les valeurs **mspOfferName** et **mspOfferDescription** dans la [page Fournisseurs de services](view-manage-service-providers.md) du Portail Azure une fois que le modèle sera déployé dans le locataire du client.

|Champ  |Définition  |
|---------|---------|
|**mspOfferName**     |Nom décrivant cette définition. Cette valeur est présentée au client comme titre de l'offre. Il doit s'agir d'une valeur unique.        |
|**mspOfferDescription**     |Brève description de votre offre (par exemple, « Offre de gestion des machines virtuelles Contoso »). Ce champ est facultatif, mais recommandé pour que les clients comprennent bien votre offre.   |
|**managedByTenantId**     |Votre ID de client.          |
|**authorizations**     |Valeurs **principalId** pour les utilisateurs/groupes/noms de principal du service de votre locataire, chacune avec un **principalIdDisplayName** pour aider votre client à comprendre l’objectif de l’autorisation, et mappées à une valeur **roleDefinitionId** intégrée pour spécifier le niveau d’accès.      |

Vous pouvez créer ce modèle dans le Portail Azure ou en modifiant manuellement les modèles fournis dans nos [exemples de référentiels](https://github.com/Azure/Azure-Lighthouse-samples/). 

> [!IMPORTANT]
> Le processus décrit ici nécessite un déploiement distinct pour chaque abonnement en cours d'intégration, même si vous intégrez des abonnements dans le même locataire du client. Des déploiements distincts sont également requis si vous intégrez plusieurs groupes de ressources dans différents abonnements dans le même locataire du client. Toutefois, l’intégration de plusieurs groupes de ressources au sein d’un même abonnement peut être effectuée dans un seul déploiement.
>
> De plus, vous devez déployer séparément chacune des offres qui sont appliquées à un même abonnement (ou aux groupes de ressources d’un abonnement). Chaque offre appliquée doit utiliser un **mspOfferName** différent.

### <a name="create-your-template-in-the-azure-portal"></a>Créer votre modèle dans le Portail Azure

Pour créer votre modèle dans le Portail Azure, accédez à **Mes clients**, puis sélectionnez **Créer un modèle ARM** à partir de la page de présentation.

Sur la page **Créer une offre de modèle ARM**, indiquez votre **Nom** et éventuellement une **Description**. Ces valeurs seront utilisées pour les champs **mspOfferName** et **mspOfferDescription** de votre modèle. La valeur **managedByTenantId** sera renseignée automatiquement, sur la base du locataire Azure AD auquel vous êtes connecté.

Ensuite, sélectionnez soit **Abonnement** soit **Groupe de ressources**, en fonction de l’étendue client que vous souhaitez intégrer. Si vous sélectionnez **Groupe de ressources**, vous devez renseigner le nom du groupe de ressources à intégrer. Vous pouvez sélectionner l'icône **+** pour ajouter des groupes de ressources supplémentaires en fonction des besoins. (Tous les groupes de ressources doivent se trouver dans le même abonnement client.)

Enfin, créez vos autorisations en sélectionnant **+ Ajouter une autorisation**. Pour chacune de vos autorisations, fournissez les informations suivantes :

1. Sélectionnez le **Type de principal** en fonction du type de compte que vous souhaitez inclure dans l’autorisation. Les options possibles sont **Utilisateur**, **Groupe** ou **Principal de service**. Dans cet exemple, nous allons choisir **Utilisateur**.
1. Sélectionnez le lien **+ Sélectionner un utilisateur** pour ouvrir le volet de sélection. Vous pouvez utiliser le champ de recherche pour trouver l’utilisateur que vous souhaitez ajouter. Une fois que vous l’avez trouvé, cliquez sur **Sélectionner**. L’**ID de principal** de l’utilisateur sera automatiquement renseigné.
1. Vérifiez le champ **Nom d’affichage**  (renseigné en fonction de l’utilisateur que vous avez sélectionné) et apportez les modifications souhaitées.
1. Sélectionnez le **Rôle** à affecter à cet utilisateur.
1. Pour le type d’**Accès**, sélectionnez **Permanent** ou **Éligible**. Si vous choisissez **Éligible**, vous devez spécifier des options pour la durée maximale, l’authentification multifacteur et la nécessité éventuelle d’une approbation. Pour plus d’informations concernant ces options, consultez [Créer des autorisations éligibles](create-eligible-authorizations.md). La fonctionnalité d’autorisations éligibles est actuellement en préversion publique et ne peut pas être utilisée avec les principaux de service.
1. Sélectionnez **Ajouter** pour créer votre autorisation.

:::image type="content" source="../media/add-authorization.png" alt-text="Capture d’écran de la section Ajouter une autorisation dans le Portail Azure.":::

Après avoir sélectionné **Ajouter**, vous retournerez à l’écran **Créer une offre de modèle ARM**. Vous pouvez sélectionner de nouveau **+ Ajouter une autorisation** pour ajouter autant d’autorisations que nécessaire.

Une fois que vous avez ajouté toutes vos autorisations, sélectionnez **Afficher le modèle**. Sur cet écran, vous verrez un fichier .json qui correspond aux valeurs que vous avez entrées. Sélectionnez **Télécharger** pour enregistrer une copie de ce fichier .json. Ce modèle peut ensuite être [déployé dans le locataire du client](#deploy-the-azure-resource-manager-template). Vous pouvez également le modifier manuellement si vous devez apporter des modifications. Notez que le fichier n’est pas stocké dans le Portail Azure.

### <a name="create-your-template-manually"></a>Créer votre modèle manuellement

Vous pouvez créer votre modèle en utilisant un modèle Azure Resource Manager (fourni dans nos [exemples de référentiels](https://github.com/Azure/Azure-Lighthouse-samples/)) et un fichier de paramètres correspondant que vous modifiez pour correspondre à votre configuration et pour définir vos autorisations. Si vous préférez, vous pouvez inclure toutes les informations directement dans le modèle, plutôt que d’utiliser un fichier de paramètres distinct.

Les modèles que vous choisissez dépendront de votre choix d’intégrer un abonnement entier, un groupe de ressources ou plusieurs groupes de ressources au sein d’un abonnement. Nous fournissons également un modèle qui peut être utilisé pour des clients qui ont acheté une offre de service managé que vous avez publiée sur la Place de marché Azure, si vous préférez intégrer leurs abonnements de cette façon.

|Pour intégrer ceci  |Utiliser ce modèle Azure Resource Manager  |Et modifier ce fichier de paramètres |
|---------|---------|---------|
|Abonnement   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.parameters.json)    |
|Groupe de ressources   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement   |[multi-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multi-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multiple-rg.parameters.json)    |
|Abonnement (lors de l’utilisation d’une offre publiée sur la Place de marché Azure)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

Si vous voulez inclure des [autorisations éligibles](create-eligible-authorizations.md#create-eligible-authorizations-using-azure-resource-manager-templates) (actuellement en préversion publique), sélectionnez le modèle correspondant dans la [section delegated-resource-management-eligible-authorizations de nos exemples de référentiels](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations).

> [!TIP]
> Bien que vous ne puissiez pas intégrer un groupe d’administration entier dans un déploiement, vous pouvez déployer une stratégie pour [intégrer chaque abonnement dans un groupe d’administration](onboard-management-group.md). Vous aurez alors accès à tous les abonnements du groupe d’administration, mais vous devrez les utiliser en tant qu’abonnements individuels (plutôt que d’effectuer des actions sur la ressource du groupe d’administration directement).

L’exemple suivant montre un fichier **subscription.parameters.json** modifié qui peut être utilisé pour l’intégration d’un abonnement. Les fichiers de paramètres de groupe de ressources (situés dans le dossier [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg)) sont similaires, mais incluent en outre un paramètre **rgName** pour identifier les groupes de ressources à intégrer.

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
            "value": "00000000-0000-0000-0000-000000000000"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
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

La dernière autorisation dans l’exemple ci-dessus ajoute un **principalId** avec le rôle Administrateur de l’accès utilisateur (18d7d88d-d35e-4fb5-A5C3-7773c20a72d9). Lorsque vous attribuez ce rôle, vous devez inclure la propriété **delegatedRoleDefinitionIds** et un ou plusieurs rôles intégrés Azure pris en charge. L’utilisateur créé dans cette autorisation pourra attribuer ces rôles aux [identités managées](../../active-directory/managed-identities-azure-resources/overview.md) dans le même locataire du client, ce qui est nécessaire au [déploiement de stratégies pouvant être corrigées](deploy-policy-remediation.md).  L’utilisateur peut également créer des incidents de support. Aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’applique à ce **principalId**.

## <a name="deploy-the-azure-resource-manager-template"></a>Déployer le modèle Azure Resource Manager

Une fois que vous avez créé votre modèle, un utilisateur du locataire du client doit le déployer dans son locataire. Un déploiement distinct est nécessaire pour chaque abonnement que vous souhaitez intégrer (ou pour chaque abonnement contenant des groupes de ressources que vous souhaitez intégrer).

Lors de l’intégration d’un abonnement (ou d’un ou de plusieurs groupes de ressources au sein d’un abonnement) à l’aide du processus décrit ici, le fournisseur de ressources **Microsoft.ManagedServices** sera inscrit pour cet abonnement.

> [!IMPORTANT]
> Ce déploiement doit être effectué par un compte non invité dans le locataire client disposant de l’autorisation `Microsoft.Authorization/roleAssignments/write`, telle que [Propriétaire](../../role-based-access-control/built-in-roles.md#owner), pour l’abonnement en cours d’intégration (ou qui contient les groupes de ressources en cours d’intégration). Pour rechercher les utilisateurs qui peuvent déléguer l’abonnement, un utilisateur du locataire du client peut sélectionner l’abonnement dans le portail Azure, ouvrir **Contrôle d’accès (IAM)** et [afficher tous les utilisateurs ayant le rôle Propriétaire](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Si l’abonnement a été créé par le biais du [programme Fournisseur de solutions Cloud (CSP)](../concepts/cloud-solution-provider.md), tout utilisateur disposant du rôle [Agent d’administration](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) dans le locataire de votre fournisseur de services peut effectuer le déploiement.

Le déploiement peut être effectué à l'aide de PowerShell, d'Azure CLI ou dans le Portail Azure, comme indiqué ci-dessous.

### <a name="powershell"></a>PowerShell

Pour déployer un seul modèle :

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -Verbose
```

Pour déployer un modèle avec un fichier de paramètres distinct :

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

Pour déployer un seul modèle :

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --verbose
```

Pour déployer un modèle avec un fichier de paramètres distinct :

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

### <a name="azure-portal"></a>Portail Azure

Avec cette option, vous pouvez modifier votre modèle directement dans le Portail Azure puis le déployer. Cette opération doit être effectuée par un utilisateur dans le locataire client.

1. Dans notre [référentiel GitHub](https://github.com/Azure/Azure-Lighthouse-samples/), sélectionnez le bouton **Déployer sur Azure** disponible en regard du modèle que vous souhaitez utiliser. Le modèle s’ouvre dans le portail Azure.
1. Entrez vos valeurs dans les champs **Nom de l'offre MSP**, **Description de l'offre MSP**, **ID géré par le locataire** et **Autorisations**. Si vous préférez, vous pouvez sélectionner **Modifier les paramètres** pour entrer les valeurs de `mspOfferName`, `mspOfferDescription`, `managedbyTenantId` et `authorizations` directement dans le fichier de paramètres. Veillez à mettre à jour ces valeurs plutôt que d'utiliser les valeurs par défaut du modèle.
1. Sélectionnez **Vérifier et créer**, puis **Créer**.

Au bout de quelques minutes, une notification indique que le déploiement est terminé.

## <a name="confirm-successful-onboarding"></a>Vérifier la réussite de l’intégration

Lorsqu'un abonnement de client a été correctement intégré à Azure Lighthouse, les utilisateurs du locataire du fournisseur de services peuvent voir l'abonnement et ses ressources (si l'accès à ceux-ci leur a été octroyé via le processus ci-dessus, soit individuellement, soit en tant que membres d'un groupe Azure AD disposant des autorisations appropriées). Pour vérifier cela, assurez-vous que l’abonnement s’affiche de l’une des manières suivantes.  

### <a name="azure-portal"></a>Portail Azure

Dans le locataire du fournisseur de services :

1. Accédez à la [page Mes clients](view-manage-customers.md).
2. Sélectionnez **Clients**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

> [!IMPORTANT]
> Pour afficher l'abonnement délégué dans [Mes clients](view-manage-customers.md), les utilisateurs du locataire du fournisseur de services doivent avoir reçu le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) (ou un autre rôle intégré qui comprend un accès Lecteur) lorsque l'abonnement a été intégré.

Dans le locataire du client :

1. Accédez à la [page Fournisseurs de services](view-manage-service-providers.md).
2. Sélectionnez **Offres de fournisseur de services**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

> [!NOTE]
> Il peut s’écouler 15 minutes entre la fin du déploiement et le moment où les mises à jour apparaissent sur le Portail Azure. Il se peut que vous voyiez les mises à jour plus tôt si vous mettez à jour votre jeton Azure Resource Manager en actualisant le navigateur, en vous connectant puis vous déconnectant ou en demandant un nouveau jeton.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Si vous devez apporter des modifications après l’intégration du client, vous pouvez [mettre à jour la délégation](update-delegation.md). Vous pouvez également [supprimer complètement l’accès à la délégation](remove-delegation.md).

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne parvenez pas à intégrer votre client ou si vos utilisateurs ont des difficultés à accéder aux ressources déléguées, vérifiez les conseils et les conditions suivantes, puis réessayez.

- Les utilisateurs qui ont besoin d’afficher les ressources du client dans le portail Azure doivent avoir le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) (ou un autre rôle intégré qui inclut l’accès Lecteur) durant le processus d’intégration.
- La valeur `managedbyTenantId` ne doit pas être identique à l’ID de locataire pour l’abonnement en cours d’intégration.
- Vous ne pouvez pas avoir plusieurs affectations dans la même étendue avec le même `mspOfferName`.
- Le fournisseur de ressources **Microsoft.ManagedServices** doit être inscrit pour l’abonnement délégué. Cela doit se produire automatiquement pendant le déploiement, mais si ce n’est pas le cas, vous pouvez [l’inscrire manuellement](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Les autorisations ne doivent pas inclure d’utilisateurs dotés du rôle intégré [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) ni d’aucun rôle intégré avec [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- Les groupes doivent être créés avec un [**type de groupe**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) défini sur **Sécurité** et non **Microsoft 365**.
- Si l’accès a été accordé à un groupe, vérifiez que l’utilisateur est membre de ce groupe. Si ce n’est pas le cas, vous pouvez [l’ajouter au groupe à l’aide d’Azure AD](../../active-directory/fundamentals/active-directory-groups-members-azure-portal.md), sans avoir à effectuer un autre déploiement. Notez que les [propriétaires de groupes](../../active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners.md) ne sont pas nécessairement membres des groupes qu’ils gèrent, et qu’il peut être nécessaire de les ajouter pour qu’ils y aient accès.
- Il peut y avoir un délai supplémentaire avant l’activation de l’accès pour les [groupes imbriqués](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).
- Les [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md) que vous incluez dans les autorisations ne doivent pas inclure de rôles déconseillés. Si un rôle intégré Azure est déconseillé, tous les utilisateurs qui étaient intégrés à ce rôle perdront l’accès et vous ne pourrez pas intégrer de délégations supplémentaires. Pour résoudre ce problème, mettez à jour votre modèle pour utiliser uniquement les rôles intégrés pris en charge, puis effectuez un nouveau déploiement.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
- Apprenez à [mettre à jour](update-delegation.md) ou à [supprimer](remove-delegation.md) une délégation.
