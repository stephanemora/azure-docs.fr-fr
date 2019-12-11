---
title: Bonnes pratiques de déploiement
description: Découvrez les principaux mécanismes de déploiement dans Azure App Service. Trouvez des recommandations propres au langage et autres mises en garde.
keywords: Azure App Service, application web, déployer, déploiement, pipelines, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: d3959b9a86ccc2d42cbf7bd188ce86bf4b7a2e63
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670089"
---
# <a name="deployment-best-practices"></a>Meilleures pratiques de déploiement

Chaque équipe de développement a des exigences uniques qui peuvent compliquer l’implémentation d’un pipeline de déploiement efficace sur un service cloud. Cet article présente les trois principaux composants du déploiement sur App Service : les sources de déploiement, les pipelines de génération et les mécanismes de déploiement. Cet article présente également quelques-unes des meilleures pratiques et des conseils pour des piles de langage spécifiques.

## <a name="deployment-components"></a>Composants de déploiement

### <a name="deployment-source"></a>Source de déploiement

Une source de déploiement correspond à l’emplacement de votre code d’application. Pour les applications de production, la source de déploiement est généralement un référentiel hébergé par un logiciel de gestion de version, comme [GitHub, BitBucket ou Azure Repos](deploy-continuous-deployment.md). Pour les scénarios de développement et de test, la source de déploiement peut être [un projet sur votre ordinateur local](deploy-local-git.md). App Service prend également en charge les [dossiers OneDrive et Dropbox](deploy-content-sync.md) comme sources de déploiement. Bien que les dossiers cloud puissent faciliter la prise en main d’App Service, il n’est généralement pas recommandé d’utiliser cette source pour les applications de production au niveau de l’entreprise. 

### <a name="build-pipeline"></a>Pipeline de build

Une fois que vous avez choisi une source de déploiement, l’étape suivante consiste à choisir un pipeline de build. Un pipeline de build lit votre code source à partir de la source de déploiement et exécute une série d’étapes (telles que la compilation de code, la minimisation du HTML et du JavaScript, l’exécution de tests et l’empaquetage de composants) pour rendre l’application exécutable. Les commandes spécifiques exécutées par le pipeline de build dépendent de votre pile de langage. Ces opérations peuvent être exécutées sur un serveur de builds, comme Azure Pipelines, ou exécutées localement.

### <a name="deployment-mechanism"></a>Mécanisme de déploiement

Le mécanisme de déploiement est l’action utilisée pour placer votre application intégrée dans le répertoire */home/site/wwwroot* de votre application web. Le répertoire */wwwroot* est un emplacement de stockage monté partagé par toutes les instances de votre application web. Lorsque le mécanisme de déploiement place votre application dans ce répertoire, vos instances reçoivent une notification pour synchroniser les nouveaux fichiers. App Service prend en charge les mécanismes de déploiement suivants :

- Points de terminaison Kudu : [Kudu](https://github.com/projectkudu/kudu/wiki) est l’outil de productivité de développement open source qui s’exécute en tant que processus distinct dans App Service Windows, et en tant que deuxième conteneur dans App Service Linux. Kudu gère les déploiements continus et fournit des points de terminaison HTTP pour le déploiement, comme zipdeploy.
- FTP et WebDeploy : À l' aide des [informations d’identification de votre site ou de votre utilisateur](deploy-configure-credentials.md), vous pouvez télécharger des fichiers [via FTP](deploy-ftp.md) ou WebDeploy. Ces mécanismes ne passent pas par Kudu.  

Les outils de déploiement, tels qu’Azure Pipelines, Jenkins et les plug-ins d’éditeur utilisent un de ces mécanismes de déploiement.

## <a name="language-specific-considerations"></a>Considérations spécifiques au langage

### <a name="java"></a>Java

Utilisez l’API [zipdeploy/](deploy-zip.md) de Kudu pour déployer des applications JAR, et [wardeploy/](deploy-zip.md#deploy-war-file) pour des applications WAR. Si vous utilisez Jenkins, vous pouvez utiliser ces API directement lors de votre phase de déploiement. Pour plus d’informations, consultez [cet article](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nœud

Par défaut, Kudu exécute les étapes de génération de votre application Node (`npm install`). Si vous utilisez un service de build tel qu’Azure DevOps, la build Kudu n’est pas nécessaire. Pour désactiver la build Kudu, créez un paramètre d’application `SCM_DO_BUILD_DURING_DEPLOYMENT`, avec une valeur de `false`.

### <a name="net"></a>.NET 

Par défaut, Kudu exécute les étapes de génération de votre application .Net (`dotnet build`). Si vous utilisez un service de build tel qu’Azure DevOps, la build Kudu n’est pas nécessaire. Pour désactiver la build Kudu, créez un paramètre d’application `SCM_DO_BUILD_DURING_DEPLOYMENT`, avec une valeur de `false`.

## <a name="other-deployment-considerations"></a>Autres points à prendre en considération pour le déploiement

### <a name="use-deployment-slots"></a>Utiliser des emplacements de déploiement

Dans la mesure du possible, utilisez des [emplacements de déploiement](deploy-staging-slots.md) lors du déploiement d’un nouveau build de production. Lorsque vous utilisez un plan App Service Standard ou mieux, vous pouvez déployer votre application dans un environnement intermédiaire, valider vos modifications et effectuer des tests de vérification de build. Lorsque vous êtes prêt, vous pouvez échanger vos emplacements intermédiaires et de production. L’opération d’échange préchauffe les instances de worker nécessaires pour correspondre à votre échelle de production, ce qui élimine les temps d’arrêt. 

### <a name="local-cache"></a>cache local

Le contenu Azure App Service est stocké sur Stockage Azure est exposé de manière durable en tant que partage de contenu. Toutefois, certaines applications ont uniquement besoin d’un magasin de contenu en lecture seule très performant à partir duquel elles peuvent s’exécuter avec une haute disponibilité. Ces applications peuvent tirer parti de l’utilisation du [cache local](overview-local-cache.md). Le cache local n’est pas recommandé pour les sites de gestion de contenu tels que WordPress.

Utilisez toujours le cache local conjointement avec les [emplacements de déploiement](deploy-staging-slots.md) pour éviter les temps d’arrêt. Consultez [cette section](overview-local-cache.md#best-practices-for-using-app-service-local-cache) pour plus d’informations sur l’utilisation conjointe de ces fonctionnalités.

### <a name="high-cpu-or-memory"></a>Utilisation de l’UC ou de mémoire élevée

Si votre plan App Service utilise plus de 90 % de l’UC ou de la mémoire disponible, la machine virtuelle sous-jacente risque de rencontrer des problèmes lors du traitement de votre déploiement. Dans ce cas, mettez temporairement à l’échelle le nombre d’instances pour effectuer le déploiement. Une fois le déploiement terminé, vous pouvez remettre le nombre d’instances à sa valeur précédente.
