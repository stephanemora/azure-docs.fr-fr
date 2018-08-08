---
title: Automatiser les mises à jour correctives du système d’exploitation et du framework avec Azure Container Registry Build (ACR Build)
description: Introduction à ACR Build, suite de fonctionnalités dans Azure Container Registry qui permet la création et la mise à jour corrective d’images de conteneur sécurisées et automatisées dans le cloud.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 63bbd9b5711330207c34ac4aa05aac3a71304653
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413577"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatiser les mises à jour correctives du système d’exploitation et du framework avec ACR Build

Les conteneurs fournissent de nouveaux niveaux de virtualisation en isolant les dépendances d’application et de développement des spécifications opérationnelles et d’infrastructure. Ce qui demeure, toutefois, est la nécessité de gérer les mises à jour correctives de cette virtualisation d’application.

**ACR Build** est une suite de fonctionnalités dans Azure Container Registry. Elle fournit la création d’image conteneur informatique pour Linux, Windows et ARM, et peut automatiser [la mise à jour corrective du framework et du système d’exploitation](#automate-os-and-framework-patching) pour vos conteneurs Docker.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Qu’est-ce qu’ACR Build ?

Azure Container Registry Build est un service de génération d’image de conteneur Azure en mode natif. ACR Build autorise le développement en boucle interne dans le cloud avec génération d’image de conteneur à la demande et génération automatisée en cas de validation du code source et mise à jour de l’image de base.

Vous pouvez déclencher automatiquement la génération d’image de conteneur quand le code est validé dans un dépôt Git ou quand l’image de base d’un conteneur est mise à jour. Avec les déclencheurs de mise à jour d’image de base, vous pouvez automatiser votre flux de travail de mise à jour corrective du système d’exploitation et du framework d’application tout en garantissant la sécurité de l’environnement et en respectant les critères fondamentaux des conteneurs immuables.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Génération rapide : boucle interne étendue au cloud

La gestion du cycle de vie commence avant que les développeurs valident leurs premières lignes de code. La fonctionnalité de [Génération rapide](container-registry-tutorial-quick-build.md) d’ACR Build permet de bénéficier d’une expérience de développement en boucle interne locale et intégrée, déchargeant les builds vers Azure. Avec la génération rapide, vous pouvez vérifier vos définitions de build automatisées avant de valider votre code.

Utilisant le format `docker build` bien connu, la commande [az acr build][az-acr-build] dans Azure CLI prend un **contexte** (un ensemble de fichiers à générer), l’envoie au service ACR Build et, par défaut, exécute un push de l’image intégrée vers son registre lors de son achèvement.

Le tableau suivant présente quelques exemples d’emplacements de contexte pris en charge pour ACR Build :

| Emplacement du contexte | Description | Exemples |
| ---------------- | ----------- | ------- |
| Système de fichiers local | Fichiers dans un répertoire sur le système de fichiers local. | `/home/user/projects/myapp` |
| Branche principale GitHub | Fichiers dans la branche maître (ou autre branche par défaut) d’un référentiel GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Branche GitHub | Branche spécifique d’un référentiel GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | Demande de tirage dans un référentiel GitHub. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Sous-dossier de GitHub | Fichiers dans un sous-dossier d’un référentiel GitHub. L’exemple affiche la combinaison de spécifications de PR et de sous-dossier. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| Tarball distant | Fichiers dans une archive compressée sur un serveur Web à distance. | `http://remoteserver/myapp.tar.gz` |

ACR Build suit vos registres géorépliqués, ce qui permet aux équipes de développement dispersées géographiquement de tirer parti du registre répliqué le plus proche.

ACR Build est conçu comme primitif du cycle de vie de conteneur. Vous pouvez par exemple intégrer ACR Build à votre solution CI/CD. En exécutant [az login][az-login] avec un [principal de service][az-login-service-principal], votre solution CI/CD pourra alors émettre des commandes [az acr build][az-acr-build] pour lancer des générations d’image.

Découvrez comment utiliser la génération rapide dans le premier tutoriel ACR Build, [Générer des images de conteneur dans le cloud avec Azure Container Registry Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Génération automatique lors de la validation du code source

Utilisez ACR Build pour déclencher automatiquement une génération d’image de conteneur quand le code est validé dans un dépôt Git. Les tâches de génération, configurables avec la commande Azure CLI [az acr build-task][az-acr-build-task], vous permettent de spécifier un dépôt Git et éventuellement une branche et un Dockerfile. Quand votre équipe valide du code dans le dépôt, un webhook créé par ACR Build déclenche une génération de l’image de conteneur définie dans le dépôt.

Découvrez comment déclencher une génération lors de la validation du code source dans le deuxième tutoriel ACR Build, [Automatiser la génération d’images de conteneur avec Azure Container Registry Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatiser les mises à jour correctives du système d’exploitation et du framework

L’aptitude d’ACR Build à réellement améliorer votre pipeline de génération de conteneur provient de sa capacité à détecter la mise à jour d’une image de base. Quand l’image de base mise à jour est envoyée vers votre registre, ACR Build peut générer automatiquement toute image d’application basée sur elle.

Les images de conteneur appartiennent en gros à deux catégories : les images de *base* et les images d’*application*. En règle générale, vos images de base incluent les frameworks d’application et de système d’exploitation sur lesquels votre application est générée, ainsi que d’autres personnalisations. Ces images de base sont elles-mêmes généralement basées sur des images publiques en amont, par exemple [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], ou [Node.js][base-node]. Plusieurs de vos images d’application peuvent partager une image de base commune.

Quand une image de framework d’application ou de système d’exploitation est mise à jour par le chargé de maintenance en amont, par exemple avec un correctif de sécurité critique du système d’exploitation, vous devez également mettre à jour vos images de base pour qu’elles contiennent ce correctif critique. Chaque image d’application doit ensuite être également recréée pour inclure ces correctifs en amont maintenant inclus dans votre image de base.

Étant donné qu’ACR Build détecte de manière dynamique les dépendances d’image de base quand il génère une image de conteneur, il est capable de détecter quand l’image de base d’une image d’application est mise à jour. Avec une [tâche de génération](container-registry-tutorial-base-image-update.md#create-build-task) préconfigurée, ACR Build **regénère alors automatiquement chaque image d’application** pour vous. Grâce à cette détection et regénération automatiques, ACR Build vous permet d’économiser le temps et les efforts normalement nécessaires au suivi et à la mise à jour manuels de chaque image d’application faisant référence à votre image de base mise à jour.

Pour en savoir plus sur la mise à jour corrective du système d’exploitation et du framework, consultez le troisième tutoriel ACR Build, [Automatiser la génération d’images lors de la mise à jour de l’image de base avec Azure Container Registry Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Pour la préversion initiale, les mises à jour de l’image de base déclenchent une génération uniquement quand les images de base et d’application se trouvent dans le même registre de conteneur Azure ou les référentiels Docker Hub accessibles publiquement.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous êtes prêt à automatiser les mises à jour correctives du système d’exploitation et du framework en générant vos images de conteneur dans le cloud, consultez la série de tutoriels ACR Build en trois parties.

> [!div class="nextstepaction"]
> [Générer des images de conteneur dans le cloud avec Azure Container Registry Build](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
