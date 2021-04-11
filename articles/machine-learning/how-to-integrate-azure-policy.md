---
title: Auditer et gérer la conformité à la stratégie
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Azure Policy pour utiliser des stratégies intégrées pour Azure Machine Learning afin de vous assurer que vos espaces de travail sont conformes à vos besoins.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544364"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditer et gérer Azure Machine Learning à l'aide d'Azure Policy

[Azure Policy](../governance/policy/index.yml) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies. Avec Azure Machine Learning, vous pouvez attribuer les stratégies suivantes :

| Stratégie | Description |
| ----- | ----- |
| **Clé gérée par le client** | auditez ou appliquez une valeur indiquant si les espaces de travail doivent utiliser une clé gérée par le client. |
| **Liaison privée** | Auditez ou appliquez une valeur indiquant si les espaces de travail utilisent un point de terminaison privé pour communiquer avec un réseau virtuel. |
| **Point de terminaison privé** | Configurez le sous-réseau du réseau virtuel Azure sur lequel le point de terminaison privé doit être créé. |
| **Zone DNS privée** | Configurez la zone DNS privée à utiliser pour la liaison privée. |
| **Identité managée affectée par l’utilisateur** | Auditez ou appliquez une valeur indiquant si les espaces de travail utilisent une identité gérée affectée par l’utilisateur. |

Les stratégies peuvent être définies sur des étendues différentes, par exemple au niveau de l'abonnement ou du groupe de ressources. Pour plus d'informations, consultez la [documentation relative à Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Stratégies prédéfinies

Azure Machine Learning fournit un ensemble de stratégies que vous pouvez utiliser dans des scénarios courants. Vous pouvez attribuer ces définitions de stratégie à votre abonnement existant ou les utiliser comme base pour créer vos propres définitions personnalisées. Pour obtenir la liste complète des stratégies intégrées pour Azure Machine Learning, consultez [Stratégies intégrées pour Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Pour consulter les définitions de stratégie intégrées en rapport avec Azure Machine Learning, procédez comme suit :

1. Accédez à __Azure Policy__ sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez __Définitions__.
1. Dans le champ __Type__, sélectionnez _Intégré_ et dans le champ __Catégorie__, sélectionnez __Machine Learning__.

De là, vous pouvez sélectionner les définitions de stratégie à consulter. Lorsque vous consultez une définition, vous pouvez utiliser le lien __Attribuer__ pour attribuer la stratégie à une étendue spécifique et configurer les paramètres de cette stratégie. Pour plus d'informations, consultez [Attribuer une stratégie - portail](../governance/policy/assign-policy-portal.md).

Vous pouvez également attribuer des stratégies à l'aide d'[Azure PowerShell](../governance/policy/assign-policy-powershell.md), d'[Azure CLI](../governance/policy/assign-policy-azurecli.md) et de [modèles](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Chiffrement de l’espace de travail à l’aide d’une clé gérée par le client

Détermine si un espace de travail doit être chiffré à l’aide d’une clé gérée par le client, ou si une clé gérée par Microsoft doit être utilisée pour chiffrer les métriques et les métadonnées. Pour plus d’informations sur l’utilisation d’une clé gérée par le client, consultez la section [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) de l’article consacré au chiffrement des données.

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__ ou __deny__. Si le paramètre est défini sur __audit__, vous pouvez créer un espace de travail sans clé gérée par le client ; un événement d’avertissement est alors créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer d’espace de travail, sauf si une clé gérée par le client est spécifiée. Toute tentative de création d’un espace de travail sans clé gérée par le client génère une erreur semblable à `Resource 'clustername' was disallowed by policy` et crée une erreur dans le journal d’activité. L'identificateur de la stratégie est également renvoyé dans le cadre de cette erreur.

## <a name="workspace-should-use-private-link"></a>L’espace de travail doit utiliser Private Link

Détermine si un espace de travail doit utiliser Azure Private Link pour communiquer avec le Réseau virtuel Azure. Pour plus d'informations sur l'utilisation de Private Link, consultez [Configurer Private Link pour un espace de travail](how-to-configure-private-link.md).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__ ou __deny__. Si le paramètre est défini sur __audit__, vous pouvez créer un espace de travail utiliser Private Link ; un événement d’avertissement est alors créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer d’espace de travail, sauf s’il utilise Private Link. Toute tentative de création d’un espace de travail sans liaison privée génère une erreur. L’erreur est également consignée dans le journal d’activité. L’identificateur de la stratégie est renvoyé dans le cadre de cette erreur.

## <a name="workspace-should-use-private-endpoint"></a>L’espace de travail doit utiliser un point de terminaison privé

Configure un espace de travail pour créer un point de terminaison privé dans le sous-réseau spécifié d’un réseau virtuel Azure.

Pour configurer cette stratégie, définissez le paramètre d’effet sur __DeployIfNotExists__. Définissez le __privateEndpointSubnetID__ sur l’ID Azure Resource Manager du sous-réseau.
## <a name="workspace-should-use-private-dns-zones"></a>L’espace de travail doit utiliser des zones DNS privées

Configure un espace de travail pour utiliser une zone DNS privée, en remplaçant la résolution DNS par défaut pour un point de terminaison privé.

Pour configurer cette stratégie, définissez le paramètre d’effet sur __DeployIfNotExists__. Définissez __privateDnsZoneId__ sur l’ID Azure Resource Manager de la zone DNS privée à utiliser. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>L’espace de travail doit utiliser une identité gérée affectée par l’utilisateur

Contrôle si un espace de travail est créé à l’aide d’une identité gérée affectée par le système (valeur par défaut) ou d’une identité gérée affectée par l’utilisateur. L’identité gérée de l’espace de travail est utilisée pour accéder aux ressources associées telles que le stockage Azure, Azure Container Registry, Azure Key Vault et Azure Application Insights. Pour plus d’informations, consultez [Utiliser les identités managées avec Azure Machine Learning](how-to-use-managed-identities.md).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__, __deny__ ou __disabled__. Si la valeur est définie sur __audit__, vous pouvez créer un espace de travail sans spécifier d’identité gérée affectée à l'utilisateur. Une identité affectée par le système est utilisée et un événement d’avertissement est créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer un espace de travail, sauf si vous fournissez une identité affectée par l’utilisateur pendant le processus de création. Une erreur survient si vous essayez de créer un espace de travail sans fournir d’identité affectée par l’utilisateur. L’erreur est également consignée dans le journal d’activité. L’identificateur de la stratégie est renvoyé dans le cadre de cette erreur.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation Azure Policy](../governance/policy/overview.md)
* [Stratégies intégrées pour Azure Machine Learning](policy-reference.md)
* [Utiliser des stratégies de sécurité avec Azure Security Center](../security-center/tutorial-security-policy.md)