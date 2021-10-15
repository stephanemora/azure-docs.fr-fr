---
title: Intégration et livraison continues
description: Découvrez comment utiliser l’intégration et la livraison continues pour déplacer des pipelines Azure Data Factory d’un environnement (développement, test, production) à un autre.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21223cc29857b439f87231a5b4649ea9864e2202
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400377"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Intégration et livraison continues dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

L’intégration continue consiste à tester automatiquement et, dès que possible, chaque modification apportée à votre code base.  La livraison continue fait suite au test effectué pendant l’intégration continue, et envoie (push) les modifications à un système de préproduction ou de production.

Dans Azure Data Factory, l’intégration et la livraison continues (CI/CD) impliquent de déplacer des pipelines Data Factory d’un environnement (développement, test, production) vers un autre. Azure Data Factory utilise des [modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md) pour stocker la configuration de vos diverses entités ADF (pipelines, jeux de données, flux de données, etc.). Deux méthodes sont recommandées pour promouvoir une fabrique de données dans un autre environnement :

-    Déploiement automatisé grâce à l’intégration de Data Factory avec [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
-    Chargement manuel d’un modèle Resource Manager en tirant parti de l’intégration de l’expérience utilisateur de Data Factory avec Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Cycle de vie d’intégration et de livraison continues

Vous trouverez ci-dessous une vue d’ensemble du cycle de vie d’intégration et de livraison continues dans une fabrique de données Azure configurée avec Azure Repos Git. Pour plus d’informations sur la configuration d’un dépôt Git, consultez [Contrôle de code source dans Azure Data Factory](source-control.md).

1.  Une fabrique de données de développement est créée et configurée avec Azure Repos Git. Tous les développeurs doivent avoir l’autorisation de créer des ressources Data Factory telles que des pipelines et des jeux de données.

1.  Un développeur [crée une branche de fonctionnalité](source-control.md#creating-feature-branches) pour apporter une modification. Il débogue les exécutions de son pipeline avec ses modifications les plus récentes. Pour plus d’informations sur le débogage d’une exécution de pipeline, consultez [Développement et débogage itératifs avec Azure Data Factory](iterative-development-debugging.md).

1.  Une fois satisfait de ses modifications, le développeur crée une demande de tirage (pull) de sa branche de fonctionnalité vers la branche primaire ou la branche de collaboration pour que ces modifications soient examinées par des pairs.

1.  Une fois la demande de tirage (pull) approuvée et les modifications fusionnées dans la branche primaire, les modifications sont publiées dans la fabrique de développement.

1.  Lorsque l’équipe est prête à déployer les modifications dans une fabrique de test ou de test d’acceptation utilisateur (UAT), elle accède à la mise en production sur Azure Pipelines et déploie la version souhaitée de la fabrique de développement vers l’UAT. Ce déploiement a lieu dans le cadre d’une tâche Azure Pipelines et utilise des paramètres de modèle Resource Manager pour appliquer la configuration appropriée.

1.  Une fois les modifications vérifiées dans la fabrique de test, opérez le déploiement vers la fabrique de production en utilisant la tâche suivante de la mise en production de pipelines.

> [!NOTE]
> Seule la fabrique de développement est associée à un dépôt Git. Les fabriques de test et de production ne doivent pas avoir de dépôt Git associé et ne doivent être mises à jour que via un pipeline Azure DevOps ou un modèle de gestion des ressources.

L’image ci-dessous met en évidence les différentes étapes de ce cycle de vie.

:::image type="content" source="media/continuous-integration-delivery/continuous-integration-image12.png" alt-text="Diagramme de l’intégration continue à Azure Pipelines":::

## <a name="best-practices-for-cicd"></a>Meilleures pratiques pour CI/CD

Si vous utilisez une intégration Git avec votre fabrique de données, et disposez d’un pipeline CI/CD qui déplace vos modifications du développement aux tests, puis en production, nous vous recommandons les bonnes pratiques suivantes :

-   **Intégration Git**. Configurez uniquement votre fabrique de données de développement avec l’intégration Git. Les modifications au niveau des tests et de la production sont déployées via CI/CD et ne nécessitent pas d’intégration Git.

-   **Script de pré-déploiement et de post-déploiement**. Avant l’étape de déploiement Resource Manager dans CI/CD, vous devez effectuer certaines tâches, telles que l’arrêt et le redémarrage des déclencheurs, et le nettoyage. Nous vous recommandons d’utiliser des scripts PowerShell avant et après la tâche de déploiement. Pour plus d’informations, consultez [Mettre à jour des déclencheurs actifs](continuous-integration-delivery-automate-azure-pipelines.md#updating-active-triggers). L’équipe Data Factory a [fourni un script](continuous-integration-delivery-sample-script.md) à utiliser, qui se trouve en bas de cette page.

-   **Runtimes d’intégration et partage**. Les runtimes d’intégration ne changent pas souvent et sont similaires dans toutes les phases de CI/CD. Ainsi, Data Factory s’attend à ce que vous ayez le même nom et le même type de runtime d’intégration dans toutes les phases de CI/CD. Si vous voulez partager les runtimes d’intégration dans toutes les phases, envisagez d’utiliser une fabrique ternaire qui contiendra uniquement les runtimes d’intégration partagés. Vous pouvez utiliser cette fabrique partagée dans tous vos environnements en tant que type de runtime d’intégration lié.

-   **Déploiement du point de terminaison privé managé**. Si un point de terminaison privé existe déjà dans une fabrique et que vous essayez de déployer un modèle ARM qui contient un point de terminaison privé portant le même nom mais dont les propriétés sont modifiées, le déploiement échoue. En d’autres termes, vous pouvez déployer avec succès un point de terminaison privé, à condition qu’il ait les mêmes propriétés que celui qui existe déjà dans la fabrique. Si une propriété est différente d’un environnement à un autre, vous pouvez la remplacer en paramétrant cette propriété et en fournissant la valeur correspondante pendant le déploiement.

-   **Key Vault**. Lorsque vous utilisez des services liés dont les informations de connexion sont stockées dans Azure Key Vault, il est recommandé de conserver des coffres de clés distincts pour les différents environnements. Vous pouvez également configurer des niveaux d’autorisation distincts pour chaque coffre de clés. Par exemple, vous ne souhaitez peut-être pas que les membres de votre équipe disposent d’autorisations sur les secrets de production. Si vous suivez cette approche, nous vous recommandons de conserver les mêmes noms de secrets dans toutes les phases. Si vous conservez les mêmes noms secrets, vous n’avez pas besoin de paramétrer chaque chaîne de connexion dans les environnements d’intégration et de livraison continues, car la seule chose qui change est le nom du coffre de clés, qui est un paramètre distinct.

-  **Nommage des ressources**. En raison de contraintes liées au modèle ARM, des problèmes de déploiement peuvent survenir si le nom de vos ressources contient des espaces. L’équipe Azure Data Factory recommande d’utiliser des caractères « _ » ou « - » au lieu d’espaces dans les noms de ressources. Par exemple, le nom « Pipeline_1 » est préférable à « Pipeline 1 ».

- **Indicateurs de contrôle d’exposition et de fonctionnalité**.  Lorsque vous travaillez en équipe, vous pouvez souhaiter fusionner des modifications, sans les exécuter dans des environnements élevés tels que PROD et QA. Pour gérer un tel scénario, l’équipe ADF recommande [le concept DevOps utilisant les indicateurs de fonctionnalité](/azure/devops/migrate/phase-features-with-feature-flags). Dans ADF, vous pouvez combiner les [paramètres globaux](author-global-parameters.md) et l’[activité IfCondition](control-flow-if-condition-activity.md) pour masquer des ensembles de logique en fonction de ces indicateurs d’environnement.

    Pour savoir comment configurer un indicateur de fonctionnalité, consultez le tutoriel vidéo ci-dessous :

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="unsupported-features"></a>Fonctionnalités non prises en charge

- Par conception, Data Factory n’autorise pas le cherry-picking des validations ni la publication sélective des ressources. Les publications incluent toutes les modifications apportées dans la fabrique de données.

    - Les entités Data Factory dépendent les unes des autres. Par exemple, les déclencheurs dépendent des pipelines et les pipelines dépendent des jeux de données et d’autres pipelines. La publication sélective d’un sous-ensemble de ressources peut engendrer des comportements inattendus et des erreurs.
    - Dans les rares cas où vous avez besoin d’une publication sélective, envisagez d’utiliser un correctif logiciel. Pour plus d’informations, consultez [Environnement de production de correctif logiciel](continuous-integration-delivery-hotfix-environment.md).

- L’équipe Azure Data Factory ne recommande pas d’attribuer des contrôles Azure RBAC à des entités individuelles (pipelines, jeux de données, etc.) dans une fabrique de données. Par exemple, si un développeur a accès à un pipeline ou à un jeu de données, il doit être en mesure d’accéder à tous les pipelines ou jeux de données de la fabrique de données. Si vous estimez que vous devez implémenter de nombreux rôles Azure dans une fabrique de données, envisagez de déployer une deuxième fabrique de données.

-   Vous ne pouvez pas publier à partir de branches privées.

-   Vous ne pouvez actuellement pas héberger de projets sur Bitbucket.

-   Vous ne pouvez pas exporter et importer actuellement des alertes et des matrices en tant que paramètres. 

- Dans le référentiel de code sous la branche *adf_publish*, un dossier nommé « PartialArmTemplates » est actuellement ajouté à côté du dossier « linkedTemplates » et des fichiers « ARMTemplateForFactory.json » et « ARMTemplateParametersForFactory.json » dans le cadre de la publication avec contrôle de code source. 

    :::image type="content" source="media/continuous-integration-delivery/partial-arm-templates-folder.png" alt-text="Diagramme du dossier « PartialArmTemplates ».":::

    Nous ne publierons plus « PartialArmTemplates » dans la branche *adf_publish* à partir du 1er novembre 2021.    

    **Aucune action n’est requise, sauf si vous utilisez « PartialArmTemplates ». Sinon, basculez vers un mécanisme pris en charge par les déploiements à l’aide des fichiers « ARMTemplateForFactory.json » ou « linkedTemplates ».**

## <a name="next-steps"></a>Étapes suivantes

- [Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promouvoir manuellement un modèle Resource Manager pour chaque environnement](continuous-integration-delivery-manual-promotion.md)
- [Utiliser des paramètres personnalisés avec un modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Modèles Resource Manager liés](continuous-integration-delivery-linked-templates.md)
- [Utilisation d’un environnement de production de correctif logiciel](continuous-integration-delivery-hotfix-environment.md)
- [Exemple de script de pré-déploiement et de post-déploiement](continuous-integration-delivery-sample-script.md)
