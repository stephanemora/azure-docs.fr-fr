---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning – Consommer des points de terminaison de pipeline'
description: Intégrez des points de terminaison de pipeline aux applications clientes dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fc7e222d400c2ded602e3d26d504896302fff014
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311785"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Consommer des points de terminaison de pipeline à partir d’applications clientes

Dans cet article, vous allez apprendre à intégrer des applications clientes à des points de terminaison Azure Machine Learning. Pour plus d’informations sur l’écriture de code d’application, consultez [Consommer un point de terminaison Azure Machine Learning](../how-to-consume-web-service.md).

Cet article fait partie de la série sur la migration de Studio (classique) vers Azure Machine Learning. Pour plus d’informations sur la migration vers Azure Machine Learning, consultez [l’article de présentation de la migration](migrate-overview.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un espace de travail Azure Machine Learning. [Créez un espace de travail Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- [Un point de terminaison en temps réel ou un point de terminaison de pipeline Azure Machine Learning](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Consommer un point de terminaison en temps réel 

Si vous avez déployé votre modèle en tant que **point de terminaison en temps réel**, vous pouvez trouver son point de terminaison REST et le code de consommation prégénéré en C#, Python et R :

1. Accédez à Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com)).
1. Allez dans l’onglet **Points de terminaison**.
1. Sélectionnez votre point de terminaison en temps réel.
1. Sélectionnez **Consommer**.

> [!NOTE]
> Vous pouvez également trouver la spécification de Swagger pour votre point de terminaison sous l’onglet **Détails**. Utilisez la définition de Swagger pour comprendre le schéma de votre point de terminaison. Pour plus d’informations sur la définition de Swagger, consultez [la documentation officielle de Swagger](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Utiliser un point de terminaison de pipeline

Il existe deux façons de consommer un point de terminaison de pipeline :

- Appels de l’API REST
- Intégration à Azure Data Factory

### <a name="use-rest-api-calls"></a>Utilisation d’appels d’API REST

Appelez le point de terminaison REST à partir de votre application cliente. Vous pouvez utiliser la spécification Swagger de votre point de terminaison afin d’en comprendre le schéma :

1. Accédez à Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com)).
1. Allez dans l’onglet **Points de terminaison**.
1. Sélectionnez **Points de terminaison de pipeline**.
1. Sélectionnez votre point de terminaison de pipeline.
1. Dans le volet **Présentation du point de terminaison de pipeline**, sélectionnez le lien sous **Documentation du point de terminaison REST**.

### <a name="use-azure-data-factory"></a>Utilisation d’Azure Data Factory

Vous pouvez appeler votre pipeline Azure Machine Learning comme étape dans un pipeline Azure Data Factory. Pour plus d’informations, consultez [Exécuter des pipelines Azure Machine Learning dans Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à trouver le schéma et des exemples de code pour vos points de terminaison de pipeline. Pour plus d’informations sur la consommation de points de terminaison à partir de l’application cliente, consultez [Consommer un point de terminaison Azure Machine Learning](../how-to-consume-web-service.md).

Consultez les autres articles de la série relative à la migration vers Azure Machine Learning : 
1. [Vue d’ensemble de la migration](migrate-overview.md).
1. [Migrer un jeu de données](migrate-register-dataset.md).
1. [Reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).
1. [Reconstruire un service web Studio (classique)](migrate-rebuild-web-service.md).
1. **Intégrer un service web Azure Machine Learning à des applications clientes**.
1. [Migrer Exécuter un script R](migrate-execute-r-script.md).