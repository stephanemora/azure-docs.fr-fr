---
title: Intégrer tous les abonnements dans un groupe d’administration
description: Vous pouvez déployer une instance Azure Policy pour déléguer tous les abonnements d’un groupe d’administration à un client de gestion Azure Lighthouse.
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525767"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>Intégrer tous les abonnements dans un groupe d’administration

[Azure Lighthouse](../overview.md) autorise la délégation d’abonnements et/ou de groupes de ressources, mais pas de [groupes de gestion](../../governance/management-groups/overview.md). Vous pouvez toutefois déployer une instance [Azure Policy](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) pour déléguer tous les abonnements d’un groupe d’administration à un locataire de gestion Azure Lighthouse.

La stratégie utilise l’[effet deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) pour vérifier si chaque abonnement du groupe d’administration a été délégué au locataire de gestion spécifié. Si un abonnement n’est pas déjà délégué, la stratégie crée l’attribution Azure Lighthouse en fonction des valeurs que vous fournissez dans les paramètres. Vous aurez ensuite accès à tous les abonnements du groupe d’administration, comme s’ils avaient chacun été intégrés manuellement.

Lorsque vous utilisez cette stratégie, gardez à l’esprit :

- Chaque abonnement au sein du groupe d’administration aura le même ensemble d’autorisations. Pour faire varier les utilisateurs et les rôles auxquels l’accès est accordé, vous devez intégrer un abonnement manuellement.
- Bien que chaque abonnement du groupe d’administration sera intégré, vous ne pourrez pas effectuer d’actions sur la ressource du groupe d’administration via Azure Lighthouse. Vous devrez sélectionner les abonnements à utiliser, comme vous le feriez s’ils étaient intégrés individuellement.

Sauf mention contraire ci-dessous, toutes ces étapes doivent être effectuées par un utilisateur dans le locataire du client avec les autorisations appropriées.

> [!TIP]
> Même si nous faisons référence aux fournisseurs de services et aux clients dans cette rubrique, les [entreprises gérant plusieurs locataires](../concepts/enterprise.md) peuvent utiliser les mêmes processus.

## <a name="register-the-resource-provider-across-subscriptions"></a>Inscrire le fournisseur de ressources entre les abonnements

En règle générale, le fournisseur de ressources **Microsoft.ManagedServices** est inscrit pour un abonnement dans le cadre du processus d’intégration. Lorsque vous utilisez la stratégie pour intégrer des abonnements dans un groupe d’administration, le fournisseur de ressources doit être inscrit à l’avance. Cela peut être effectué par un utilisateur contributeur ou propriétaire dans le locataire du client (ou tout utilisateur qui dispose des autorisations nécessaires pour effectuer l' `/register/action` opération pour le fournisseur de ressources). Pour plus d’informations, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

Vous pouvez utiliser une [application logique Azure pour inscrire automatiquement le fournisseur de ressources entre les abonnements](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer). Cette application logique peut être déployée dans le locataire d’un client avec des autorisations limitées qui lui permettent d’inscrire le fournisseur de ressources dans chaque abonnement au sein d’un groupe d’administration.

Nous fournissons également une [application logique Azure qui peut être déployée dans le locataire du fournisseur de services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner). Cette application logique peut attribuer le fournisseur de ressources entre les abonnements dans plusieurs locataires en [accordant au client le consentement](../../active-directory/manage-apps/grant-admin-consent.md) de l’administrateur à l’application logique. Pour accorder le consentement administrateur au niveau locataire, vous devez vous connecter comme utilisateur autorisé à donner un consentement au nom de l’organisation. Notez que même si vous utilisez cette option pour inscrire le fournisseur dans plusieurs locataires, la stratégie doit tout de même être déployée individuellement pour chaque groupe d’administration.

## <a name="create-your-parameters-file"></a>Créer votre fichier de paramètres

Pour attribuer la stratégie, déployez le fichier [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) de notre référentiel d’échantillons, ainsi qu’un fichier de paramètres [deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) que vous modifiez avec vos détails de locataire et d’affectation spécifiques. Ces deux fichiers contiennent les mêmes détails que ceux utilisés pour l'[intégration d’un abonnement individuel](onboard-customer.md).

L’exemple ci-dessous montre un fichier de paramètres qui délègue les abonnements au locataire Relecloud Managed Services, avec un accès accordé à deux principalIDs : un pour la prise en charge de niveau 1, et un compte Automation qui peut [attribuer le delegateRoleDefinitionIds à des identités gérées dans le locataire client](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>Affecter la stratégie à un groupe d’administration  

Une fois que vous avez modifié la stratégie pour créer vos affectations, vous pouvez l’affecter au niveau du groupe d’administration. Pour plus d’informations sur l’attribution d’une stratégie et l’affichage des résultats de l’état de conformité, consultez  [Démarrage rapide : Créer une affectation de stratégie](../../governance/policy/assign-policy-portal.md).

Le script PowerShell ci-dessous montre comment ajouter la définition de stratégie dans le groupe d’administration spécifié, à l’aide du modèle et du fichier de paramètres que vous avez créés. Vous devez créer la tâche d’attribution et de correction des abonnements existants.

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>Vérifier la réussite de l’intégration

Vous pouvez vérifier que les abonnements ont été correctement intégrés de plusieurs façons. Pour plus d’informations, consultez [Vérifier que l’intégration a réussi](onboard-customer.md#confirm-successful-onboarding).

Si vous conservez l’application logique et la stratégie actives pour votre groupe d’administration, tous les nouveaux abonnements ajoutés au groupe d’administration sont également intégrés.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[intégration des clients à Azure Lighthouse](onboard-customer.md).
- En savoir plus sur [Azure Policy](../../governance/policy/index.yml).
- En savoir plus sur [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).
