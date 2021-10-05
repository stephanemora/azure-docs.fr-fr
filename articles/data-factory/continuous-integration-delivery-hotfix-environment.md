---
title: Utilisation d’un environnement de production de correctif logiciel
description: Découvrez comment utiliser un environnement de production hotfix avec l'intégration et la livraison continues dans les pipelines Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788b576d351984c4f6a3dff7fd43056aa95aba3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219586"
---
# <a name="using-a-hotfix-production-environment"></a>Utilisation d’un environnement de production de correctif logiciel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si vous déployez une fabrique en production et détectez un bogue qui doit être corrigé immédiatement, mais que vous ne pouvez pas déployer la branche de collaboration actuelle, vous devrez peut-être déployer un correctif logiciel. Cette approche est également connue sous le nom de QFE (Quick-Fix Engineering).

## <a name="steps-to-deploy-a-hotfix"></a>Étapes de déploiement d’un correctif logiciel

Suivez les étapes suivantes pour déployer un correctif dans vos environnements de production et de test.

1.    Dans Azure DevOps, accédez à la version qui a été déployée en production. Recherchez la dernière validation qui a été déployée.

1.    À partir du message de validation, obtenez l’ID de validation de la branche de collaboration.

1.    Créez une nouvelle branche de correctifs logiciels à partir de cette validation.

1.    Allez dans le Studio Azure Data Factory et passez à la branche hotfix.

1.    En utilisant l'Azure Data Factory Studio, corrigez le bogue. Tester vos modifications.

1.    Une fois le correctif vérifié, sélectionnez **Exporter le modèle ARM** pour obtenir le modèle Resource Manager du correctif logiciel.

1.    Archivez manuellement cette build dans la branche adf_publish.

1.    Si vous avez configuré votre pipeline de mise en production pour qu’il se déclenche automatiquement en fonction des archivages adf_publish, une nouvelle version démarre automatiquement. Sinon, créez manuellement une file d'attente de versions.

1.    Déployez la version avec correctif logiciel dans les fabriques de test et de production. Cette version contient la charge utile de production précédente ainsi que la correction apportée à l’étape 5.

1.   Ajoutez les modifications issues du correctif logiciel dans la branche de développement pour que les versions ultérieures n’incluent pas le même bogue.

## <a name="video-tutorial"></a>Didacticiel vidéo
Regardez la vidéo ci-dessous, un didacticiel vidéo détaillé sur la façon de corriger vos environnements à chaud. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’intégration et de la livraison continues](continuous-integration-delivery.md)
- [Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promouvoir manuellement un modèle Resource Manager pour chaque environnement](continuous-integration-delivery-manual-promotion.md)
- [Utiliser des paramètres personnalisés avec un modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Modèles Resource Manager liés](continuous-integration-delivery-linked-templates.md)
- [Exemple de script de pré-déploiement et de post-déploiement](continuous-integration-delivery-sample-script.md)