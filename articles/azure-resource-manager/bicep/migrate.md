---
title: Migrer des ressources Azure et des modèles ARM JSON pour utiliser Bicep
description: Décrit le flux de travail recommandé lors de la migration de ressources Azure et de modèles ARM JSON pour utiliser Bicep.
author: joshuawaddell
ms.author: jowaddel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: e9ec55ac41cb30a902b337184cc75a4f976700bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700952"
---
# <a name="migrate-to-bicep"></a>Migrer vers Bicep

Il existe un certain nombre d’avantages à définir vos ressources Azure dans Bicep, notamment une simplification de la syntaxe, la modularisation, la gestion automatique des dépendances, la validation de type et IntelliSense, ainsi qu’une expérience de création améliorée.

Quand vous avez des modèles Azure Resource Manager (modèles ARM) JSON existants et/ou des ressources déployées, et que vous souhaitez les migrer de manière sécurisée vers Bicep, nous vous suggérons de suivre un flux de travail recommandé constitué de cinq phases :

:::image type="content" source="./media/migrate/five-phases.png" alt-text="Diagramme des cinq phases de migration des ressources Azure vers Bicep : convertir, migrer, refactoriser, tester et déployer." border="false":::

La première étape du processus consiste à capturer vos ressources Azure sous la forme d’un fichier JSON, s’il n’en existe pas encore. Vous décompilez ensuite le fichier JSON dans un fichier Bicep initial, que vous améliorez par la refactorisation. Si vous disposez d’un fichier de travail, vous le testez et le déployez à l’aide d’un processus qui réduit le risque de changement cassant à votre environnement Azure.

:::image type="content" source="./media/migrate/migrate-bicep.png" alt-text="Diagramme du workflow recommandé pour la migration de ressources Azure vers Bicep." border="false":::

Cet article présente une synthèse de ce flux de travail recommandé. Pour obtenir des instructions détaillées, consultez [Migrer des ressources Azure et des modèles ARM JSON pour utiliser Bicep](/learn/modules/migrate-azure-resources-bicep/) sur Microsoft Learn.

## <a name="phase-1-convert"></a>Phase 1 : Conversion

Dans la phase de _conversion_ de la migration de vos ressources vers Bicep, l’objectif est de capturer une représentation initiale de vos ressources Azure. Le fichier Bicep que vous créez dans cette phase n’est pas complet et il n’est pas prêt pour une utilisation. Le fichier vous donne cependant un point de départ pour votre migration.

La phase de conversion se compose de deux étapes, que vous effectuez dans l’ordre :

1. **Capturer une représentation JSON de vos ressources Azure.** Si vous avez un modèle JSON existant que vous convertissez en Bicep, la première étape est simple, car vous disposez déjà de votre modèle source. Si vous convertissez des ressources Azure déployées par le biais du portail ou un autre outil, vous devez *exporter* les définitions de ressources, puis les convertir vers Bicep. Vous pouvez utiliser le portail Azure, Azure CLI et les applets de commande Azure PowerShell pour exporter des ressources individuelles, plusieurs ressources et des groupes de ressources entiers.

1. **Convertir la représentation JSON en Bicep en utilisant la commande _decompile_.** [Les outils Bicep incluent la commande `decompile` pour convertir des modèles.](decompile.md) Vous pouvez appeler la commande `decompile` à partir d’Azure CLI ou de l’interface CLI de Bicep. Le processus de décompilation est un processus qui ne produit pas un résultat parfait et ne garantit pas un mappage complet de JSON à Bicep. Il peut être nécessaire de réviser le fichier Bicep généré pour répondre aux bonnes pratiques dans votre modèle avant d’utiliser le fichier pour déployer des ressources.

## <a name="phase-2-migrate"></a>Phase 2 : Migrer

Dans la phase de _migration_ consistant à migrer vos ressources vers Bicep, l’objectif est de créer le premier brouillon de votre fichier Bicep déployable et de vérifier qu’il définit toutes les ressources Azure qui sont dans l’étendue de la migration.

La phase de migration se compose de trois étapes, que vous effectuez dans l’ordre :

1. **Créer un fichier Bicep vide.** C’est une bonne pratique que de créer un fichier Bicep entièrement nouveau. Le fichier que vous avez créé dans la phase de _conversion_ est un point de référence que vous pouvez examiner, mais vous ne devez pas le considérer comme final ou le déployer en l’état.

1. **Copier chaque ressource depuis votre modèle décompilé.** Copiez chaque ressource individuellement depuis le fichier Bicep converti vers le nouveau fichier Bicep. Ce processus vous aide à résoudre les éventuels problèmes pour chaque ressource et à éviter toute confusion quand la taille de votre modèle s’accroît.

1. **Identifier et recréer les ressources manquantes.** Tous les types de ressources Azure ne peuvent pas être exportés via le portail Azure, Azure CLI ou Azure PowerShell. Par exemple, les extensions de machine virtuelle, comme DependencyAgentWindows et MMAExtension (Microsoft Monitoring Agent), ne sont pas des types de ressources pris en charge pour l’exportation. Pour les ressources qui n’ont pas été exportées, comme les extensions de machine virtuelle, vous devez recréer ces ressources dans votre nouveau fichier Bicep. Il existe plusieurs outils et approches que vous pouvez utiliser pour recréer des ressources, notamment [Azure Resource Explorer](/azure/azure-resource-manager/templates/view-resources?azure-portal=true#use-resource-explorer), la [documentation de référence sur les modèles Bicep et ARM](/azure/templates/?azure-portal=true) et le site [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates?azure-portal=true).

## <a name="phase-3-refactor"></a>Phase 3 : Refactorisation

Dans la phase de _refactorisation_ de la migration de vos ressources vers Bicep, l’objectif est d’améliorer la qualité de votre code Bicep. Ces améliorations peuvent inclure des modifications, comme l’ajout de commentaires du code, qui mettent le modèle en conformité avec vos standards pour les modèles.

La phase de déploiement comprend huit étapes, que vous effectuez dans n’importe quel ordre :

1. **Passer en revue les versions d’API des ressources.** Lors de l’exportation de ressources Azure, le modèle exporté peut ne pas avoir la dernière version de l’API pour un type de ressource. Si vous avez besoin de propriétés spécifiques pour des déploiements futurs, mettez à jour l’API vers la version appropriée. C’est une bonne pratique que de passer en revue les versions d’API pour chaque ressource exportée.

1. **Passer en revue les suggestions du linter dans votre nouveau fichier Bicep.** Lors de la création de fichiers Bicep à l’aide de l’[extension Bicep pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep&azure-portal=true), le [linter Bicep](linter.md) s’exécute automatiquement et met en surbrillance les suggestions et les erreurs dans votre code. La plupart des suggestions et des erreurs incluent une option permettant d’appliquer un correctif rapide au problème. Passez en revue ces recommandations et modifiez votre fichier Bicep.

1. **Réviser les paramètres, les variables et les noms symboliques.** Il est possible que les noms des paramètres, les noms des variables et les noms symboliques générés par le décompilateur ne correspondent pas à votre convention de nommage standard. Examinez les noms générés et apportez les modifications nécessaires.

1. **Simplifier les expressions.** Le processus de décompilation peut ne pas toujours tirer parti de certaines fonctionnalités de Bicep. Passez en revue les expressions générées dans la conversion et simplifiez-les. Par exemple, le modèle décompilé peut inclure une fonction `concat()` ou `format()` qui peut être simplifiée en utilisant une [interpolation de chaîne](bicep-functions-string.md#concat). Passez en revue les suggestions du linter et apportez les modifications nécessaires.

1. **Passer en revue les ressources enfants et d’extension.** Avec Bicep, il existe plusieurs façons de déclarer des [ressources enfants](child-resource-name-type.md) et des [ressources d’extension](scope-extension-resources.md), notamment concaténer les noms de vos ressources, utiliser le mot clé `parent` et utiliser des ressources imbriquées. Envisagez de passer en revue ces ressources après la décompilation et vérifiez que la structure correspond à vos standards. Par exemple, veillez à ne pas utiliser la concaténation de chaînes pour créer des noms de ressources enfants : vous devez utiliser la propriété `parent` ou une ressource imbriquée. De même, les sous-réseaux peuvent être référencés en tant que propriétés d’un réseau virtuel ou en tant que ressource distincte.

1. **Modulariser.** Si vous convertissez un modèle qui contient un grand nombre de ressources, pour des raisons de simplicité, envisagez de diviser les différents types de ressources en [modules](modules.md). Les modules Bicep permettent de réduire la complexité de vos déploiements et d’augmenter la réutilisation de votre code Bicep.

    > [!NOTE]
    > Il est possible d’utiliser vos modèles JSON comme modules dans un déploiement Bicep. Bicep a la capacité de reconnaître les modules JSON et de les référencer de la même façon que vous utilisez les modules Bicep.

1. **Ajouter des commentaires et des descriptions.** Tout bon code Bicep est _auto-documenté_. Bicep vous permet d’ajouter des commentaires et des attributs `@description()` à votre code pour vous aider à documenter votre infrastructure. Bicep prend en charge les commentaires sur une seule ligne avec une séquence de caractères `//`, et les commentaires multilignes qui commencent par `/*` et se terminent par `*/`. Vous pouvez ajouter des commentaires à des lignes spécifiques de votre code et à des sections du code.

1. **Suivre les bonnes pratiques de Bicep.** Vérifiez que votre fichier Bicep suit les recommandations standard. Consultez le document de référence [Bonnes pratiques de Bicep](best-practices.md) pour voir ce que vous auriez pu oublier.

## <a name="phase-4-test"></a>Phase 4 : Test

Dans la phase de _test_ de la migration de vos ressources vers Bicep, l’objectif est de vérifier l’intégrité de vos modèles migrés et d’effectuer un déploiement de test.

La phase de test se compose de deux étapes, que vous effectuez dans l’ordre :

1. **Exécuter l’opération de simulation de déploiement du modèle ARM.** Pour vous aider à vérifier vos modèles convertis avant un déploiement, vous pouvez utiliser l’[opération de simulation de déploiement de modèle Azure Resource Manager](../templates/deploy-what-if.md). Elle compare l’état actuel de votre environnement à l’état souhaité défini dans le modèle. L’outil génère la liste des modifications qui vont se produire *sans* appliquer les modifications à votre environnement. Vous pouvez utiliser la simulation avec des déploiements en mode incrémentiel et complet. Même si vous prévoyez de déployer votre modèle en utilisant le mode incrémentiel, il est judicieux d’exécuter votre opération de simulation en mode complet.

1. **Effectuer un déploiement de test.** Avant d’introduire votre modèle Bicep converti en production, pensez à exécuter plusieurs déploiements de test. Si vous avez plusieurs environnements (par exemple développement, test et production), vous pouvez d’abord essayer de déployer votre modèle sur l’un de vos environnements hors production. Après le déploiement, comparez les ressources d’origine avec les nouveaux déploiements de ressources pour en vérifier la cohérence.

## <a name="phase-5-deploy"></a>Phase 5 : Déploiement

Dans la phase de _déploiement_ de la migration de vos ressources vers Bicep, l’objectif est de déployer votre fichier Bicep final en production.

La phase de déploiement se compose de quatre étapes, que vous effectuez dans l’ordre :

1. **Préparer un plan de restauration.** La possibilité de récupérer à partir d’un déploiement qui a échoué est cruciale. Développez un plan de restauration pour le cas où des changements cassants ont été introduits dans vos environnements. Effectuez l’inventaire des types de ressources qui sont déployées, comme les machines virtuelles, les applications web et les bases de données. Le plan de données de chaque ressource doit également être pris en compte. Avez-vous un moyen de récupérer une machine virtuelle et ses données ? Avez-vous un moyen de récupérer une base de données après suppression ? Un plan de restauration correctement développé permet de réduire au minimum le temps d’indisponibilité en cas de problème lors d’un déploiement.

1. **Exécuter l’opération de simulation sur l’environnement de production.** Avant de déployer votre fichier Bicep final en production, exécutez l’opération de simulation sur votre environnement de production, en veillant à utiliser les valeurs des paramètres de production et à documenter les résultats.

1. **Déployer manuellement.** Si vous prévoyez d’utiliser le modèle converti dans un pipeline, comme [Azure DevOps](add-template-to-azure-pipelines.md) ou [GitHub Actions](deploy-github-actions.md), exécutez d’abord le déploiement à partir de votre machine locale. Il est préférable de vérifier le fonctionnement du modèle avant de l’ajouter à votre pipeline de production. De cette façon, vous pouvez répondre rapidement en cas de problème.

1. **Effectuer des tests de détection de fumée.** Une fois votre déploiement terminé, il est judicieux d’exécuter une série de *tests de détection de fumée*, c’est-à-dire des vérifications simples qui confirment le bon fonctionnement de votre application ou de votre charge de travail. Par exemple, testez pour voir si votre application web est accessible via des canaux d’accès normaux, comme l’Internet public ou sur un VPN d’entreprise. Pour les bases de données, essayez d’établir une connexion de base de données et d’exécuter une série de requêtes. Pour les machines virtuelles, connectez-vous à celles-ci et vérifiez que tous les services sont opérationnels.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le décompilateur Bicep, consultez [Décompilation du JSON d’un modèle ARM dans un fichier Bicep](decompile.md).
