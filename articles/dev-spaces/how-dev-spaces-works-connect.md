---
title: Connexion de votre ordinateur de développement à votre Cluster AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Décrit les processus d’utilisation d’Azure Dev Spaces pour connecter votre ordinateur de développement à votre cluster Azure Kubernetes service
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241339"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Connexion de votre ordinateur de développement à votre Cluster AKS

Avec Azure Dev Spaces, vous pouvez connecter votre ordinateur de développement à votre cluster AKS, ce qui vous permet d’exécuter et de déboguer du code sur votre ordinateur de développement comme s’il s’exécutait sur le cluster. Azure Dev Spaces redirige le trafic de votre cluster AKS connecté en exécutant un pod sur votre cluster, qui joue le rôle d’agent distant pour rediriger le trafic entre votre ordinateur de développement et le cluster. Cette redirection du trafic permet au code sur votre ordinateur de développement et aux services en cours d’exécution dans votre cluster AKS de communiquer comme s’ils se trouvaient dans le même cluster AKS. Cette connexion vous permet également d’exécuter et de déboguer du code avec ou sans conteneur sur votre ordinateur de développement. La connexion de votre ordinateur de développement à votre cluster vous permet de développer rapidement votre application et d’effectuer des tests de bout en bout.

## <a name="connecting-to-your-cluster"></a>Connexion à votre cluster

Vous vous connectez à votre cluster AKS existant en utilisant [Visual Studio Code][vs-code] avec l’extension [Azure Dev Spaces][azds-vs-code] installée et exécutée sur MacOS ou Windows 10. Lorsque vous établissez une connexion, vous avez la possibilité de rediriger tout le trafic à destination et en provenance d’un pod nouveau ou existant dans le cluster vers votre ordinateur de développement.

> [!NOTE]
> Lorsque vous utilisez Visual Studio Code pour vous connecter à votre cluster, l’extension Azure Dev Spaces vous donne la possibilité de rediriger un service vers votre ordinateur de développement. Cette option est un moyen pratique d’identifier un pod pour la redirection. Toute redirection entre votre cluster AKS et votre ordinateur de développement est destinée à un pod.

La connexion à votre cluster ne nécessite pas qu’Azure Dev Spaces soit activé sur votre cluster. Au lieu de cela, lorsque l’extension Azure Dev Spaces établit une connexion à votre cluster, elle effectue les opérations suivantes :

* Elle remplace le conteneur du pod sur le cluster AKS par un conteneur d’agent distant qui redirige le trafic vers votre ordinateur de développement. Lors de la redirection d’un nouveau pod, Azure Dev Spaces crée un pod dans votre cluster AKS avec l’agent distant.
* Elle exécute la commande [kubectl port-forward][kubectl-port-forward] sur votre ordinateur de développement pour transférer le trafic de celui-ci vers l’agent distant s’exécutant dans votre cluster.
* Elle collecte les informations d’environnement de votre cluster à l’aide de l’agent distant. Ces informations d’environnement incluent les variables d’environnement, les services visibles, les montages de volumes et les montages de secrets.
* Elle configure l’environnement dans le terminal Visual Studio Code afin que le service sur votre ordinateur de développement puisse accéder aux mêmes variables que s’il s’exécutait sur le cluster.  
* Elle met à jour votre fichier hosts pour mapper les services sur votre cluster AKS à des adresses IP locales sur votre ordinateur de développement. Ces entrées de fichier hosts permettent au code s’exécutant sur votre ordinateur de développement d’adresser des demandes à d’autres services s’exécutant dans votre cluster. Pour mettre à jour votre fichier hosts, Azure Dev Spaces vous demande un accès administrateur sur votre ordinateur de développement lors de la connexion à votre cluster.

Si Azure Dev Spaces n’est pas activé sur votre cluster, vous avez également la possibilité d’utiliser la [redirection du trafic offerte par Azure Dev Spaces][how-it-works-routing]. La redirection du trafic offerte par Azure Dev Spaces vous permet de vous connecter à une copie de votre service s’exécutant dans un espace de développement enfant. L’utilisation d’un espace de développement enfant vous permet d’éviter de perturber d’autres activités dans l’espace de développement parent, car vous redirigez uniquement le trafic ciblant l’instance de l’espace enfant de votre service, laissant l’instance de l’espace parent du service inchangée.

Une fois que vous êtes connecté à votre cluster, le trafic est acheminé vers votre ordinateur de développement, que votre service s’exécute ou non sur votre ordinateur de développement.

## <a name="running-code-on-your-development-computer"></a>Exécution de code sur votre ordinateur de développement

Une fois que vous avez établi une connexion à votre cluster AKS, vous pouvez exécuter n’importe quel code en mode natif sur votre ordinateur, sans mise en conteneur. Tout trafic réseau que l’agent distant reçoit est redirigé vers le port local spécifié pendant la connexion, de sorte que votre code s’exécutant en mode natif peut accepter et traiter ce trafic. Les variables d’environnement, volumes et secrets de votre cluster sont mis à la disposition du code s’exécutant sur votre ordinateur de développement. Par ailleurs, en raison des entrées de fichier hosts et du transfert de port qu’Azure Dev Spaces a ajoutés à votre ordinateur de développement, votre code peut envoyer le trafic réseau à des services s’exécutant sur votre cluster en utilisant les noms de service de votre cluster, et ce trafic est transféré aux services s’exécutant dans votre cluster.

Étant donné que votre code s’exécute sur votre ordinateur de développement, vous avez la possibilité d’utiliser n’importe quel outil dont vous vous servez normalement à des fins de développement pour exécuter votre code et le déboguer. Le trafic est routé entre votre ordinateur de développement et votre cluster pendant toute la durée de votre connexion. Cette connexion permanente vous permet de démarrer, d’arrêter et de redémarrer votre code autant que nécessaire sans avoir à rétablir une connexion.

De plus, Azure Dev Spaces offre un moyen de répliquer des variables d’environnement et des fichiers montés disponibles pour les pods dans votre cluster AKS sur votre ordinateur de développement via le fichier *azds-local.env*. Vous pouvez également utiliser ce fichier pour créer des variables d’environnement et des montages de volumes.

## <a name="additional-configuration-with-azds-localenv"></a>Configuration supplémentaire avec azds-local.env

Le fichier *azds-local.env* vous permet de répliquer des variables d’environnement et des fichiers montés disponibles pour vos pod dans votre cluster AKS. Vous pouvez spécifier les actions suivantes dans un fichier *azds-local.env* :

* Téléchargez un volume et définissez le chemin d’accès sur ce volume en tant que variable d’environnement.
* Téléchargez un fichier ou un ensemble de fichiers à partir d’un volume, et montez-le sur votre ordinateur de développement.
* Rendez un service disponible indépendamment du cluster auquel vous êtes connecté.

Voici un exemple de fichier *azds-local.env* :

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Un fichier *azds-local. env* par défaut n’étant pas créé automatiquement, vous devez le créer manuellement à la racine de votre projet.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

Lorsque vous êtes connecté à votre cluster AKS, les journaux de diagnostic de votre cluster sont enregistrés dans le [répertoire temporaire][azds-tmp-dir] de votre ordinateur de développement. À l’aide de Visual Studio Code, vous pouvez également utiliser la commande *Afficher les informations de diagnostic* pour imprimer les variables d’environnement actuelles et les entrées DNS de votre cluster AKS.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à connecter votre ordinateur de développement local à votre cluster AKS, consultez [Connecter votre ordinateur de développement à un cluster AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
