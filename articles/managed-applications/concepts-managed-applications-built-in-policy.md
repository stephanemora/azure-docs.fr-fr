---
title: Déployer des associations pour une application managée Azure avec une stratégie
description: Découvrez comment déployer des associations pour une application managée à l’aide du service Azure Policy.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: e3dda343c1d5664e188b434c54e4c288b515d420
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932198"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Déployer des associations pour une application managée Azure avec Azure Policy

Les stratégies Azure peuvent être utilisées pour déployer des associations dans le but d’associer des ressources à une application managée. Cet article aborde une stratégie intégrée permettant de déployer des associations, et explique comment utiliser cette stratégie.

## <a name="built-in-policy-to-deploy-associations"></a>Stratégie intégrée pour déployer des associations

Le déploiement d’associations pour une application managée est une stratégie intégrée qui peut être utilisée pour déployer une association dans le but d’associer une ressource à une application managée. La stratégie accepte trois paramètres :

- ID d’application managée - Il s’agit de l’ID de ressource de l’application managée à laquelle des ressources doivent être associées.
- Types de ressources à associer - Ces types de ressources constituent la liste des types de ressources qui doivent être associés à l’application managée. Vous pouvez associer plusieurs types de ressources à une application managée à l’aide d’une même stratégie.
- Préfixe du nom d’association - Cette chaîne correspond au préfixe qui doit être ajouté au nom de la ressource d’association en cours de création. La valeur par défaut est « DeployedByPolicy ».

La stratégie utilise l’évaluation DeployIfNotExists. Elle est exécutée après qu’un fournisseur de ressources a traité une requête de création ou de mise à jour de ressource du ou des types sélectionnés, et après que le fournisseur a retourné un code d’état indiquant la réussite de l’opération. Après cela, la ressource d’association est déployée à l’aide d’un déploiement de modèle.
Pour plus d’informations sur les associations, consultez [Intégration des ressources des fournisseurs personnalisés Azure](./concepts-custom-providers-resourceonboarding.md).

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Utiliser la stratégie intégrée de déploiement des associations 

### <a name="prerequisites"></a>Prérequis
Si l’application managée a besoin d’autorisations d’accès à l’abonnement pour effectuer une action, le déploiement de stratégie de la ressource d’association ne fonctionnera pas si vous n’accordez pas les autorisations nécessaires.

### <a name="policy-assignment"></a>Affectation de rôle
Pour utiliser la stratégie intégrée, créez une attribution de stratégie, puis attribuez la stratégie Déployer des associations pour une application managée. Une fois la stratégie correctement attribuée, celle-ci identifie les ressources non conformes et déploie une association pour ces ressources.

![Attribuer une stratégie intégrée](media/builtin-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Obtenir de l’aide

Si vous avez des questions sur le développement de fournisseurs de ressources personnalisés Azure, postez-les sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Avant de la publier, vérifiez si votre réponse a déjà été posée et a déjà reçu une réponse. Ajoutez le mot clé ```azure-custom-providers``` pour obtenir une réponse rapide !

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu comment utiliser une stratégie intégrée pour déployer des associations. Pour en savoir plus, consultez les articles suivants :

- [Concepts : Intégration des ressources des fournisseurs personnalisés Azure](./concepts-custom-providers-resourceonboarding.md)
- [Tutoriel : Intégration des ressources avec des fournisseurs personnalisés](./tutorial-custom-providers-resource-onboarding.md)
- [Tutoriel : Créer des actions et des ressources personnalisées dans Azure](./tutorial-custom-providers-101.md)
- [Démarrage rapide : Créer un fournisseur de ressources personnalisées et déployer des ressources personnalisées](./create-custom-provider.md)
- [Guide pratique pour ajouter des actions personnalisées à l’API REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Guide pratique pour ajouter des ressources personnalisées à l’API REST Azure](./custom-providers-resources-endpoint-how-to.md)
