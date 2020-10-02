---
title: Auditer et gérer la conformité avec Azure Policy
titleSuffix: Azure Machine Learning
description: Apprenez à utiliser Azure Policy afin de tirer partir des stratégies intégrées pour Azure Machine Learning.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992734"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditer et gérer Azure Machine Learning à l'aide d'Azure Policy

[Azure Policy](/azure/governance/policy) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies. Avec Azure Machine Learning, vous pouvez attribuer les stratégies suivantes :

* **Clé gérée par le client** : auditez ou appliquez une valeur indiquant si les espaces de travail doivent utiliser une clé gérée par le client.
* **Liaison privée** : procédez à un audit pour déterminer si les espaces de travail utilisent un point de terminaison privé pour communiquer avec un réseau virtuel.

Les stratégies peuvent être définies sur des étendues différentes, par exemple au niveau de l'abonnement ou du groupe de ressources. Pour plus d'informations, consultez la [documentation relative à Azure Policy](/azure/governance/policy/overview).

## <a name="built-in-policies"></a>Stratégies prédéfinies

Azure Machine Learning fournit un ensemble de stratégies que vous pouvez utiliser dans des scénarios courants. Vous pouvez attribuer ces définitions de stratégie à votre abonnement existant ou les utiliser comme base pour créer vos propres définitions personnalisées. Pour obtenir la liste complète des stratégies intégrées pour Azure Machine Learning, consultez [Stratégies intégrées pour Azure Machine Learning](/azure/governance/policy/samples/built-in-policies#machine-learning).

Pour consulter les définitions de stratégie intégrées en rapport avec Azure Machine Learning, procédez comme suit :

1. Accédez à __Azure Policy__ sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez __Définitions__.
1. Dans le champ __Type__, sélectionnez _Intégré_ et dans le champ __Catégorie__, sélectionnez __Machine Learning__.

De là, vous pouvez sélectionner les définitions de stratégie à consulter. Lorsque vous consultez une définition, vous pouvez utiliser le lien __Attribuer__ pour attribuer la stratégie à une étendue spécifique et configurer les paramètres de cette stratégie. Pour plus d'informations, consultez [Attribuer une stratégie - portail](/azure/governance/policy/assign-policy-portal).

Vous pouvez également attribuer des stratégies à l'aide d'[Azure PowerShell](/azure/governance/policy/assign-policy-powershell), d'[Azure CLI](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli) et de [modèles](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Chiffrement des espaces de travail à l'aide d'une clé gérée par le client

Détermine si les espaces de travail doivent être chiffrés à l'aide d'une clé gérée par le client (CMK), ou si une clé gérée par Microsoft doit être utilisée pour chiffrer les métriques et les métadonnées. Pour plus d'informations sur l'utilisation d'une CMK, consultez la section [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) de l'article consacré à la sécurité d'entreprise.

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__ ou __deny__. Si le paramètre est défini sur __audit__, vous pouvez créer des espaces de travail sans CMK ; un événement d'avertissement est alors créé dans le journal d'activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer d'espace de travail, sauf si une CMK est spécifiée. Toute tentative de création d'un espace de travail sans CMK génère une erreur semblable à `Resource 'clustername' was disallowed by policy`, et crée une erreur dans le journal d'activité. L'identificateur de la stratégie est également renvoyé dans le cadre de cette erreur.

## <a name="workspaces-should-use-private-link"></a>Les espaces de travail doivent utiliser Private Link

Détermine si les espaces de travail doivent utiliser Azure Private Link pour communiquer avec le Réseau virtuel Azure. Pour plus d'informations sur l'utilisation de Private Link, consultez [Configurer Private Link pour un espace de travail](how-to-configure-private-link.md).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__. Si vous créez un espace de travail sans utiliser Private Link, un événement d'avertissement est créé dans le journal d'activité.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation Azure Policy](/azure/governance/policy/overview)
* [Stratégies intégrées pour Azure Machine Learning](policy-reference.md)
* [Utiliser des stratégies de sécurité avec Azure Security Center](/azure/security-center/tutorial-security-policy)