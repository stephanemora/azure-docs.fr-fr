---
title: Créer des actions et des ressources personnalisées dans Azure
description: Ce tutoriel passe en revue la création d’actions et de ressources personnalisées dans Azure Resource Manager, ainsi que leur intégration dans des workflows personnalisés pour Modèles Azure Resource Manager, Azure CLI, Azure Policy et Journal d’activité.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799188"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Créer des actions et des ressources personnalisées dans Azure

Les fournisseurs personnalisés vous permettent de personnaliser des workflows dans Azure. Un fournisseur personnalisé implique qu’un contrat soit passé entre Azure et un point de terminaison (`endpoint`). Il permet d’ajouter des API personnalisées à Azure Resource Manager pour bénéficier de nouvelles fonctionnalités de gestion et de déploiement. Ce tutoriel fait appel à un exemple simple pour vous montrer comment ajouter de nouvelles actions et ressources à Azure et comment les intégrer.

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

- Configurer des fonctions Azure pour des fournisseurs personnalisés Azure
- Créer un point de terminaison RESTful pour des fournisseurs personnalisés
- Créer et utiliser le fournisseur personnalisé

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurer des fonctions Azure pour des fournisseurs personnalisés Azure

Cette partie du tutoriel explique en détail comment configurer une fonction Azure pour fonctionner avec des fournisseurs personnalisés. Les fournisseurs personnalisés peuvent utiliser n’importe quelle URL publique.

- [Configurer des fonctions Azure pour des fournisseurs personnalisés Azure](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Créer un point de terminaison RESTful pour des fournisseurs personnalisés

Cette partie du tutoriel explique en détail la création d’un point de terminaison RESTful pour des fournisseurs personnalisés.

- [Créer un point de terminaison RESTful pour des fournisseurs personnalisés](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Créer et utiliser le fournisseur personnalisé

Cette partie du tutoriel explique en détail comment créer un fournisseur personnalisé et utiliser ses actions et ressources personnalisées.

- [Créer et utiliser un fournisseur personnalisé Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris ce qu’est un fournisseur personnalisé et comment en créer un. Pour passer à l’étape suivante du tutoriel :

- [Tutoriel : Configurer des fonctions Azure pour des fournisseurs personnalisés Azure](./tutorial-custom-providers-function-setup.md)

Si vous cherchez des références ou un guide de démarrage rapide, voici quelques liens utiles :

- [Démarrage rapide : Créer un fournisseur de ressources personnalisé Azure et déployer des ressources personnalisées](./create-custom-provider.md)
- [Guide pratique pour ajouter des actions personnalisées à l’API REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Guide pratique pour ajouter des ressources personnalisées à l’API REST Azure](./custom-providers-resources-endpoint-how-to.md)
