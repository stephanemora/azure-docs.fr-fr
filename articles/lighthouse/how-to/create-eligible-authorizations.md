---
title: Créer des autorisations éligibles
description: Lorsque vous intégrez des clients à Azure Lighthouse, vous pouvez permettre aux utilisateurs de votre locataire de gestion d’élever leur rôle en flux tendu, selon leurs besoins.
ms.date: 09/08/2021
ms.topic: how-to
ms.openlocfilehash: b36c3a91c780c2d374ceafe14a671aaa135a2d3c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777827"
---
# <a name="create-eligible-authorizations"></a>Créer des autorisations éligibles

Lorsque vous intégrez des clients à Azure Lighthouse, vous pouvez créer des autorisations pour octroyer des rôles intégrés spécifiques à Azure aux utilisateurs de votre locataire de gestion. Vous pouvez également créer des autorisations éligibles qui utilisent [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) pour permettre aux utilisateurs de votre locataire de gestion d’élever temporairement leur rôle. Cela vous permet d’accorder des autorisations supplémentaires en juste-à-temps. Ainsi, les utilisateurs peuvent en bénéficier pendant une durée définie.

La création d’autorisations éligibles vous permet de réduire le nombre d’attributions permanentes de rôles privilégiés à des utilisateurs, ce qui contribue à réduire les risques de sécurité liés aux accès privilégiés attribués aux utilisateurs de votre locataire.

Cette rubrique explique le fonctionnement des autorisations éligibles et comment les créer lors de l’[intégration d’un client à Azure Lighthouse](onboard-customer.md).

> [!IMPORTANT]
> Les autorisations éligibles sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="license-requirements"></a>Conditions de licence :

La création d’autorisations éligibles nécessite une licence Enterprise Mobility + Security E5 (EMS E5) ou Azure AD Premium P2. Pour trouver la licence appropriée à vos besoins, consultez [Comparaison des fonctionnalités mises à la disposition générale des éditions gratuite, de base et Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Cette licence EMS E5 ou Azure AD Premium P2 doit être détenue par le locataire de gestion, et non le locataire client.

Les coûts supplémentaires associés à un rôle éligible s’appliquent uniquement pendant la période durant laquelle l’utilisateur élève son accès jusqu’à ce rôle.

Pour plus d’informations sur les licences utilisateur, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="how-eligible-authorizations-work"></a>Fonctionnement des autorisations éligibles

Une autorisation éligible définit une attribution de rôle qui oblige l’utilisateur à activer le rôle lorsqu’il doit effectuer des tâches privilégiées. Lorsque l’utilisateur active le rôle éligible, il dispose de l’accès complet accordé par ce rôle durant la période spécifiée.

Les utilisateurs du locataire client peuvent passer en revue toutes les attributions de rôle, notamment celles des autorisations éligibles, avant le processus d’intégration.

Lorsqu’un utilisateur active correctement un rôle éligible, il dispose de ce rôle élevé sur l’étendue déléguée durant une période préconfigurée, en plus de son ou ses attributions de rôle permanentes pour cette étendue.

Les administrateurs du locataire de gestion peuvent passer en revue toutes les activités de Privileged Identity Management en consultant le journal d’audit dans le locataire de gestion. Les clients peuvent afficher ces actions dans le journal d’activité Azure pour l’abonnement délégué.

## <a name="eligible-authorization-elements"></a>Éléments des autorisations éligibles

Vous pouvez créer une autorisation éligible lors de l’intégration de clients aux modèles Azure Resource Manager ou en publiant une offre de services managés sur la Place de marché Azure. Chaque autorisation éligible doit inclure trois éléments : l’utilisateur, le rôle et la stratégie d’accès.

### <a name="user"></a>Utilisateur

Pour chaque autorisation éligible, vous fournissez l’ID du principal pour un utilisateur individuel ou un groupe Azure AD dans le locataire de gestion. Avec l’ID du principal, vous devez fournir un nom d’affichage de votre choix pour chaque autorisation.

Si un groupe est fourni dans une autorisation éligible, tous les membres de ce groupe sont en mesure d’élever leur accès individuel à ce rôle, par la stratégie d’accès.

Vous ne pouvez pas utiliser d’autorisations éligibles avec des principaux de service, car il n’existe aucun moyen pour un compte de principal de service d’élever son accès et d’utiliser un rôle éligible. Vous ne pouvez pas non plus utiliser d’autorisations éligibles avec `delegatedRoleDefinitionIds` qu’un administrateur de l’accès utilisateur peut [attribuer à des identités managées](deploy-policy-remediation.md).

> [!NOTE]
> Pour chaque autorisation éligible, veillez à créer également une autorisation permanente (active) pour le même ID de principal avec un rôle différent, par exemple Lecteur (ou un autre rôle intégré Azure qui comprend un accès lecteur). Si vous n’incluez pas d’autorisation permanente avec un accès Lecteur, l’utilisateur ne peut pas élever son rôle dans le portail Azure.

### <a name="role"></a>Rôle

Chaque autorisation éligible doit inclure un [rôle intégré Azure](../../role-based-access-control/built-in-roles.md) que l’utilisateur peut utiliser en flux tendu.

Le [rôle](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) peut être n’importe quel rôle Azure intégré pris en charge pour la gestion des ressources déléguées Azure, à l’exception du rôle Administrateur de l’accès utilisateur.

> [!IMPORTANT]
> Si vous incluez plusieurs autorisations éligibles qui utilisent le même rôle, chacune des autorisations éligibles doit avoir les mêmes paramètres de stratégie d’accès.

### <a name="access-policy"></a>Stratégie d’accès

La stratégie d’accès définit l’authentification multifacteur requise, la durée pendant laquelle un utilisateur peut endosser le rôle avant que ce dernier n’expire et si des approbateurs sont nécessaires.

#### <a name="multifactor-authentication"></a>Authentification multifacteur

Spécifiez s’il faut ou non exiger l’[authentification multifacteur Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md) pour permettre l’activation d’un rôle éligible.

#### <a name="maximum-duration"></a>Durée maximale

Définissez la durée totale pendant laquelle le rôle éligible est attribué à l’utilisateur. La valeur minimale est 30 minutes et la valeur maximale est 8 heures.

#### <a name="approvers"></a>Approbateurs

L’élément des approbateurs est facultatif. Si vous l’incluez, vous pouvez spécifier jusqu’à dix utilisateurs ou groupes d’utilisateurs dans le locataire de gestion qui peuvent approuver ou refuser des demandes d’un utilisateur pour activer le rôle éligible.

Vous ne pouvez pas utiliser un compte de principal de service comme approbateur. De plus, les approbateurs ne peuvent pas approuver leur propre accès ; si un approbateur est également inclus en tant qu’utilisateur dans une autorisation éligible, un approbateur différent doit accorder l’accès pour lui permettre d’élever son rôle.

Si vous n’incluez pas d’approbateurs, l’utilisateur est en mesure d’activer le rôle éligible chaque fois qu’il le choisit.

## <a name="create-eligible-authorizations-using-managed-services-offers"></a>Créer des autorisations éligibles avec des offres de services managés

Pour intégrer votre client à Azure Lighthouse, vous pouvez publier des offres de services managés sur la Place de marché Azure. Quand [vous créez vos offres dans l’Espace partenaires](publish-managed-services-offers.md), vous pouvez désormais spécifier si le **type d’accès** pour chaque [autorisation](../../marketplace/create-managed-service-offer-plans.md#authorizations) doit être **Actif** ou **Éligible**.

Quand vous sélectionnez **Éligible**, l’utilisateur dans votre autorisation peut activer le rôle en fonction de la stratégie d’accès que vous configurez. Vous devez définir une durée maximale comprise entre 30 minutes et 8 heures et spécifier si vous exigez l’authentification multifacteur Azure. Vous pouvez également ajouter jusqu’à 10 approbateurs si vous choisissez de les utiliser, en fournissant un nom complet et un ID de principal pour chacun d’entre eux.

Veillez à consulter les détails de la section [Éléments des autorisations éligibles](#eligible-authorization-elements) lors de la configuration de vos autorisations éligibles dans l’Espace partenaires.

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Créer des autorisations éligibles à l’aide de modèles Azure Resource Manager

Pour intégrer votre client à Azure Lighthouse, vous utilisez un [modèle Azure Resource Manager, ainsi qu’un fichier de paramètres correspondant](onboard-customer.md#create-an-azure-resource-manager-template) que vous modifiez. Le modèle que vous choisissez dépendra de votre choix d’intégrer un abonnement entier, un groupe de ressources ou plusieurs groupes de ressources au sein d’un abonnement.

Pour inclure des autorisations éligibles lors de l’intégration d’un client, utilisez l’un des modèles de la [section delegated-resource-management-eligible-authorizations de notre référentiel d’exemples](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) (contenu en anglais). Nous fournissons des modèles avec et sans approbateurs inclus, afin que vous puissiez utiliser celui qui convient le mieux à votre scénario.

|Pour intégrer ceci (avec des autorisations éligibles)  |Utiliser ce modèle Azure Resource Manager  |Et modifier ce fichier de paramètres |
|---------|---------|---------|
|Abonnement   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.parameters.json)    |
|Abonnement (avec approbateurs)  |[subscription-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.json)  |[subscription-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json)    |
|Groupe de ressources   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|Groupe de ressources (avec approbateurs)  |[rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.json)  |[rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement (avec approbateurs)  |[multiple-rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.json)  |[multiple-rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.parameters.json)    |

Le modèle **subscription-managing-tenant-approvers.json**, qui peut être utilisé pour intégrer un abonnement avec des autorisations éligibles (y compris des approbateurs), est indiqué ci-dessous.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            }
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            }
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            }
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            }
        },
        "eligibleAuthorizations": {
            "type": "array",
            "metadata": {
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments with support for approvals from the managing tenant"
            }
        }
    },
        "variables": {
            "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
            "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
        },
        "resources": [
            {
                "type": "Microsoft.ManagedServices/registrationDefinitions",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspRegistrationName')]",
                "properties": {
                    "registrationDefinitionName": "[parameters('mspOfferName')]",
                    "description": "[parameters('mspOfferDescription')]",
                    "managedByTenantId": "[parameters('managedByTenantId')]",
                    "authorizations": "[parameters('authorizations')]",
                    "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]"
                }
            },
            {
                "type": "Microsoft.ManagedServices/registrationAssignments",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspAssignmentName')]",
                "dependsOn": [
                    "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                ],
                "properties": {
                    "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                }
            }
        ],
        "outputs": {
            "mspOfferName": {
                "type": "string",
                "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
            },
            "authorizations": {
                "type": "array",
                "value": "[parameters('authorizations')]"
            },
            "eligibleAuthorizations": {
                "type": "array",
                "value": "[parameters('eligibleAuthorizations')]"
            }
        }
    }
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>Définir des autorisations éligibles dans votre fichier de paramètres

L’[exemple de modèle subscription-managing-tenant-approvers.Parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json) peut être utilisé pour définir des autorisations, y compris des autorisations éligibles, lors de l’intégration d’un abonnement.

Chacune de vos autorisations éligibles doit être définie dans le paramètre `eligibleAuthorizations`. Cet exemple inclut une autorisation éligible.

Ce modèle inclut également l’élément `managedbyTenantApprovers`, qui ajoute un `principalId` nécessaire pour approuver toutes les tentatives d’activation des rôles éligibles définis dans l’élément `eligibleAuthorizations`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H",
                            "managedByTenantApprovers": [ 
                                { 
                                    "principalId": "00000000-0000-0000-0000-000000000000", 
                                    "principalIdDisplayName": "PIM-Approvers" 
                                } 
                            ]
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

Chaque entrée dans le paramètre `eligibleAuthorizations` contient [trois éléments](#eligible-authorization-elements) qui définissent une autorisation éligible : `principalId`, `roleDefinitionId` et `justInTimeAccessPolicy`.

`principalId` spécifie l’ID de l’utilisateur ou du groupe Azure AD auquel s’applique cette autorisation éligible.

`roleDefinitionId` contient l’ID de définition de rôle pour un [rôle intégré Azure](../../role-based-access-control/built-in-roles.md) que l’utilisateur peut utiliser en flux tendu. Si vous incluez plusieurs autorisations éligibles qui utilisent le même `roleDefinitionId`, chacune d’elles doit avoir des paramètres identiques pour `justInTimeAccessPolicy`.

`justInTimeAccessPolicy` spécifie trois éléments :

- `multiFactorAuthProvider` peut être défini sur **Azure**, afin de requérir une authentification multifacteur Azure AD, ou sur **Aucun** si aucune authentification multifacteur n’est nécessaire.
- `maximumActivationDuration` définit la durée totale pendant laquelle le rôle éligible sera attribué à l’utilisateur. Cette valeur doit respecter le format de durée ISO 8601. La valeur minimale est PT30M (30 minutes) et la valeur maximale est PT8H (8 heures). Pour rester simple, nous vous recommandons d’utiliser des valeurs par incréments de demi-heure uniquement (par exemple, PT6H pour 6 heures ou PT6H30M pour 6,5 heures).
- `managedByTenantApprovers` est facultatif. Si vous l’incluez, il doit contenir une ou plusieurs combinaisons d’un principalId et d’un principalIdDisplayName qui sont nécessaires pour approuver toute activation du rôle éligible.

Pour plus d’informations sur ces éléments, consultez la section [Éléments des autorisations éligibles](#eligible-authorization-elements) plus haut.

## <a name="elevation-process-for-users"></a>Processus d’élévation pour les utilisateurs

Lorsque vous avez intégré un client à Azure Lighthouse, tous les rôles éligibles que vous avez inclus seront disponibles pour l’utilisateur spécifié (ou pour les utilisateurs des groupes spécifiés).

Chaque utilisateur peut à tout moment élever son accès. Pour cela, il doit accéder à la page **Mes clients** du Portail Azure et sélectionner une délégation, puis **Gérer les rôles éligibles**. Après cela, ils peuvent suivre les [étapes pour activer le rôle](../../active-directory/privileged-identity-management/pim-resource-roles-activate-your-roles.md) dans Azure AD Privileged Identity Management.

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Capture d’écran montrant le bouton Gérer les rôles éligibles sur le Portail Azure.":::

Si des approbateurs ont été spécifiés, l’utilisateur n’a pas accès au rôle tant que l’approbation n’est pas accordée par un approbateur désigné. Tous les approbateurs sont avertis quand l’approbation est demandée et l’utilisateur ne peut pas utiliser le rôle éligible tant que l’approbation n’est pas accordée. Les approbateurs sont également avertis quand cela se produit. Pour plus d’informations sur le processus d’approbation, consultez [Approuver ou rejeter des demandes de rôles de ressources Azure dans Privileged Identity Management](../../active-directory/privileged-identity-management/pim-resource-roles-approval-workflow.md).

Lorsque le rôle éligible a été activé, il sera attribué à l’utilisateur durant toute la période spécifiée dans l’autorisation éligible. Après cette période, l’utilisateur ne sera plus en mesure d’utiliser ce rôle, sauf s’il répète le processus d’élévation et élève de nouveau son accès.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [intégrer des clients à Azure Lighthouse en utilisant des modèles ARM](onboard-customer.md).
- Découvrez comment [intégrer des clients avec des offres de services managés](publish-managed-services-offers.md).
- Obtenez plus d’informations sur [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).
- Obtenez plus d’informations sur [les locataires, les utilisateurs et les rôles dans Azure Lighthouse](../concepts/tenants-users-roles.md).
