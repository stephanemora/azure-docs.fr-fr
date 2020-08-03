---
title: Démarrage rapide – Déployer un conteneur Docker dans une instance de conteneur – Docker CLI
description: Dans ce démarrage rapide, vous utilisez Docker CLI pour déployer rapidement une application web conteneurisée qui s’exécute dans une instance de conteneur Azure isolée.
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: ''
ms.openlocfilehash: 684bb3537d26b60afb0fc0796a94e1a134aa50c8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100946"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Démarrage rapide : Déployer une instance de conteneur dans Azure à l’aide de Docker CLI

Utilisez Azure Container Instances pour exécuter, facilement et rapidement, des conteneurs Docker serverless dans Azure. Déployez sur une instance de conteneur à la demande lorsque vous développez des applications cloud natives et que vous souhaitez passer de façon fluide d’un développement local à un déploiement cloud.

Dans ce démarrage rapide, vous utilisez des commandes Docker CLI natives pour déployer un conteneur Docker et rendre son application disponible dans Azure Container Instances. Cette fonctionnalité est activée par l’[intégration entre Docker et Azure](https://docs.docker.com/engine/context/aci-integration/) (bêta). Quelques secondes après l’exécution d’une commande `docker run`, vous pouvez accéder à l’application en cours d’exécution dans le conteneur :

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur":::

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-account] avant de commencer.

Pour ce démarrage rapide, vous devez installer la version 2.3.2.0 ou ultérieure de Desktop Edge, disponible pour [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) ou [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Vous pouvez également installer [Docker ACI Integration CLI pour Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (bêta). 

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et nécessite des fonctionnalités bêta (en préversion) dans Docker. En savoir plus sur les versions [Stable et Edge de Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions) Toutes les fonctionnalités d’Azure Container Instances ne sont pas prises en charge. Fournissez des commentaires sur l’intégration Docker-Azure en créant un problème dans le dépôt GitHub [aci-integration-beta](https://github.com/docker/aci-integration-beta).

## <a name="create-azure-context"></a>Créer un contexte Azure

Pour utiliser les commandes Docker pour exécuter des conteneurs dans Azure Container Instances, connectez-vous d’abord à Azure :

```bash
docker login azure
```

À l’invite, entrez ou sélectionnez vos informations d’identification Azure.


Exécutez `docker context create aci` pour créer un contexte ACI. Ce contexte associe Docker à votre abonnement Azure, ce qui vous permet de créer des instances de conteneur. Par exemple, pour créer un contexte appelé *myacicontext* :

```
docker context create aci myacicontext
```

Lorsque vous y êtes invité, sélectionnez votre ID d’abonnement Azure, puis sélectionnez un groupe de ressources existant ou **créez-en-un**. Si vous choisissez un nouveau groupe de ressources, celui-ci est créé avec un nom généré par le système. Les instances de conteneur Azure, comme toutes les ressources Azure, doivent être déployées dans un groupe de ressources. Les groupes de ressources vous permettent d’organiser et de gérer les ressources Azure connexes.


Exécutez `docker context ls` pour confirmer que vous avez ajouté le contexte ACI à vos contextes Docker :

```
docker context ls
```

## <a name="create-a-container"></a>Créez un conteneur.

Après avoir créé un contexte Docker, vous pouvez créer un conteneur dans Azure. Dans ce démarrage rapide, vous utilisez l’image `mcr.microsoft.com/azuredocs/aci-helloworld` publique. Cette image contient une petite application web écrite en Node.js qui sert une page HTML statique.

Tout d’abord, accédez au contexte ACI. Toutes les commandes Docker ultérieures s’exécutent dans ce contexte.

```
docker context use myacicontext
```

Exécutez la commande `docker run` suivante pour créer l’instance de conteneur Azure avec le port 80 exposé sur Internet :

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Exemple de sortie pour un déploiement réussi :

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Exécutez `docker ps` pour obtenir des détails sur le conteneur en cours d’exécution, notamment l’adresse IP publique :

```
docker ps
```


L’exemple de sortie montre une adresse IP publique, dans le cas présent *52.230.225.232* :

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 À présent, accédez à l’adresse IP dans votre navigateur. Si vous voyez une page web similaire à l’image suivante, félicitations ! Cela signifie que vous avez réussi à déployer une application s’exécutant dans un conteneur Docker sur Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur":::

## <a name="pull-the-container-logs"></a>Extraire les journaux d’activité de conteneur

Lorsque vous avez besoin de résoudre les problèmes relatifs à un conteneur ou à l’application qu’il exécute (ou simplement afficher son résultat), commencez par consulter les journaux d’activité de l’instance de conteneur.

Par exemple, exécutez la commande `docker logs` pour afficher les journaux du conteneur *hungry-kirch* dans le contexte ACI :

```azurecli-interactive
docker logs hungry-kirch
```

La sortie affiche les journaux d’activité du conteneur et, normalement, les requêtes HTTP GET générées lorsque vous avez affiché l’application dans votre navigateur.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé d’utiliser le conteneur, exécutez `docker rm` pour le supprimer. Cette commande arrête et supprime l’instance de conteneur Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image publique en utilisant l’intégration entre Docker et Azure. Pour plus d’informations sur les scénarios d’intégration, consultez la [documentation Docker](https://docs.docker.com/engine/context/aci-integration/). 

Vous pouvez également utiliser l’[extension Docker](https://aka.ms/VSCodeDocker) pour Visual Studio Code afin de bénéficier d’une expérience intégrée de développement, d’exécution et de gestion des conteneurs, des images et des contextes.

Pour utiliser les outils Azure afin de créer et de gérer des instances de conteneur, consultez les autres démarrages rapides utilisant [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), le [portail Azure](container-instances-quickstart-portal.md) et le [modèle Azure Resource Manager](container-instances-quickstart-template.md).

Si vous voulez créer une image conteneur et la déployer à partir d’un registre de conteneurs Azure privé, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

