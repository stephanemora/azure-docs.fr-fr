---
title: Créer des autorisations éligibles
description: Lorsque vous intégrez des clients à Azure Lighthouse, vous pouvez permettre aux utilisateurs de votre locataire de gestion d’élever leur rôle en flux tendu, selon leurs besoins.
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: f220574a2fb84fcf4e7a6e4933bcfbf61d882091
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385983"
---
# <a name="create-eligible-authorizations"></a>Créer des autorisations éligibles

Lorsque vous intégrez des clients à Azure Lighthouse, vous pouvez créer des autorisations pour octroyer des rôles intégrés spécifiques à Azure aux utilisateurs de votre locataire de gestion. Vous pouvez également créer des autorisations éligibles qui utilisent [Azure Active Directory Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) pour permettre aux utilisateurs de votre locataire de gestion d’élever temporairement leur rôle. Cela vous permet d’octroyer des autorisations supplémentaires en flux tendu. Ainsi, les utilisateurs ayant besoin d’autorisations spécifiques pour effectuer une tâche peuvent bénéficier de ces autorisations le temps d’effectuer leur tâche.

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

Pour plus d’informations sur les licences utilisateur, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements).

## <a name="how-eligible-authorizations-work"></a>Fonctionnement des autorisations éligibles

Une autorisation éligible définit une attribution de rôle qui oblige l’utilisateur à activer le rôle lorsqu’il doit effectuer des tâches privilégiées. Lorsque l’utilisateur active le rôle éligible, il dispose de l’accès complet accordé par ce rôle durant la période spécifiée.

Les utilisateurs du locataire client peuvent passer en revue toutes les attributions de rôle, notamment celles des autorisations éligibles, avant le processus d’intégration.

Lorsqu’un utilisateur active correctement un rôle éligible, il dispose de ce rôle élevé sur l’étendue déléguée durant une période préconfigurée, en plus de son ou ses attributions de rôle permanentes pour cette étendue.

Les administrateurs du locataire de gestion peuvent passer en revue toutes les activités de Privileged Identity Management en consultant le journal d’audit dans le locataire de gestion. Les clients peuvent afficher ces actions dans le journal d’activité Azure pour l’abonnement délégué.

Lorsque vous créez une autorisation éligible, vous définissez trois éléments : l’utilisateur, le rôle et la stratégie d’accès.

- L’**utilisateur** peut être un utilisateur individuel dans le locataire de gestion ou un groupe Azure AD, toujours dans le locataire de gestion. Si un groupe est défini, tous les membres de ce groupe seront en mesure d’élever leur accès individuel au rôle par la stratégie d’accès. Vous ne pouvez pas utiliser les autorisations éligibles avec les principaux de service.
- Le **rôle** peut être n’importe quel rôle Azure intégré pris en charge pour la gestion des ressources déléguées Azure, à l’exception du rôle Administrateur de l’accès utilisateur.
- La **stratégie d’accès** définit les exigences en matière d’authentification multifacteur (ou « MFA » pour « Multi-Factor Authorization ») et la durée pendant laquelle un utilisateur pourra endosser le rôle avant que ce dernier n’expire. La durée maximale que vous pouvez spécifier pour n’importe quel rôle est de 8 heures.

Pour plus d’informations sur ces éléments et sur la façon de les définir, voir ci-dessous.

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Créer des autorisations éligibles à l’aide de modèles Azure Resource Manager

Pour intégrer votre client à Azure Lighthouse, vous utilisez un [modèle Azure Resource Manager, ainsi qu’un fichier de paramètres correspondant](onboard-customer.md#create-an-azure-resource-manager-template) que vous modifiez. Le modèle que vous choisissez dépendra de votre choix d’intégrer un abonnement entier, un groupe de ressources ou plusieurs groupes de ressources au sein d’un abonnement.

> [!NOTE]
> Bien que vous puissiez également intégrer des clients à l’aide d’offres Managed Service dans la Place de marché Azure, vous ne pouvez pas inclure actuellement des autorisations éligibles dans ces offres.

Pour inclure des autorisations éligibles lors de l’intégration d’un client, utilisez l’un des modèles de la [section delegated-resource-management-eligible-authorizations de notre référentiel d’exemples](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) (contenu en anglais).


|Pour intégrer ceci (avec des autorisations éligibles)  |Utiliser ce modèle Azure Resource Manager  |Et modifier ce fichier de paramètres |
|---------|---------|---------|
|Abonnement   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)    |
|Groupe de ressources   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |

Le modèle **subscription.json**, qui peut être utilisé pour intégrer un abonnement avec des autorisations éligibles, est indiqué ci-dessous. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            },
            "defaultValue": "<to be filled out by MSP> Specify a title for your offer"
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            },
            "defaultValue": "<to be filled out by MSP> Provide a brief description of your offer"
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            },
            "defaultValue": "<to be filled out by MSP> Provide your tenant id"
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            },
            "defaultValue": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM_Group" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46",
                    "principalIdDisplayName": "PIM_Group" 
                }   
            ]
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "metadata": { 
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments" 
            },
           "defaultValue": [ 
                { 
                        "justInTimeAccessPolicy": { 
                            "multiFactorAuthProvider": "Azure", 
                            "maximumActivationDuration": "PT8H" 
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "PIM_Group",
                        "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608" 
                        
                }                    
            ]    

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

L’[exemple de modèle subscription.Parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json) peut être utilisé pour définir des autorisations, y compris des autorisations éligibles, lors de l’intégration d’un abonnement.

Chacune de vos autorisations éligibles doit être définie dans le paramètre `eligibleAuthorizations`. Cet exemple inclut une autorisation éligible.

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
                            "maximumActivationDuration": "PT8H"
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

Dans le paramètre `eligibleAuthorizations`, le `principalId` spécifie l’ID de l’utilisateur ou du groupe Azure AD auquel s’applique cette autorisation éligible. N’utilisez pas l’ID d’un compte de principal de service, car il n’existe actuellement aucun moyen pour un compte de principal de service d’élever son accès et d’utiliser un rôle éligible.

> [!IMPORTANT]
> Veillez à inclure le même `principalId` dans la section `authorizations` de votre modèle avec un rôle différent de l’autorisation éligible, tel que le rôle Lecteur (ou tout autre rôle intégré Azure qui inclut l’accès Lecteur). Dans le cas contraire, l’utilisateur ne pourra pas élever son rôle dans le portail Azure.

Le `roleDefinitionId` contient l’ID de définition de rôle pour un [rôle intégré Azure](/azure/role-based-access-control/built-in-roles) que l’utilisateur peut utiliser en flux tendu.

Le `justInTimeAccessPolicy` spécifie deux éléments :

- `multiFactorAuthProvider` peut être défini sur **Azure**, afin de requérir une authentification multifacteur Azure ou sur **Aucun** si aucune authentification multifacteur n’est nécessaire.
- `maximumActivationDuration` définit la durée totale pendant laquelle le rôle éligible sera attribué à l’utilisateur. Cette valeur doit respecter le format de durée ISO 8601. La valeur minimale est PT30M (30 minutes) et la valeur maximale est PT8H (8 heures).

> [!NOTE]
> Remarque : l’accès en flux tendu ne s’applique pas au `delegatedRoleDefinitionIds` qu’un administrateur de l’accès utilisateur peut [attribuer à des identités gérées](deploy-policy-remediation.md). Ces attributions de rôle ne peuvent pas être créées en tant qu’autorisations éligibles. De même, vous ne pouvez pas créer d’autorisation éligible pour le rôle Administrateur de l’accès utilisateur lui-même.

## <a name="elevation-process-for-users"></a>Processus d’élévation pour les utilisateurs

Lorsque vous avez intégré un client à Azure Lighthouse, tous les rôles éligibles que vous avez inclus seront disponibles pour l’utilisateur spécifié (ou pour les utilisateurs des groupes spécifiés).

Chaque utilisateur peut élever son accès à tout moment en visitant la page **Mes clients** du portail Azure, en sélectionnant une délégation, puis en sélectionnant le bouton **Gérer les rôles éligibles**. Après cela, ils peuvent suivre les [étapes pour activer le rôle](/azure/active-directory/privileged-identity-management/pim-how-to-activate-role) dans Azure AD Privileged Identity Management.

Lorsque le rôle éligible a été activé, il sera attribué à l’utilisateur durant toute la période spécifiée dans l’autorisation éligible. Après cette période, l’utilisateur ne sera plus en mesure d’utiliser ce rôle, sauf s’il répète le processus d’élévation et élève de nouveau son accès.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [intégrer des clients à Azure Lighthouse en utilisant des modèles ARM](onboard-customer.md).
- Obtenez plus d’informations sur [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure).
- Obtenez plus d’informations sur [les locataires, les utilisateurs et les rôles dans Azure Lighthouse](../concepts/tenants-users-roles.md).
