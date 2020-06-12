---
title: Fonctionnement de Processus local avec Kubernetes
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Décrit les processus d’utilisation de Processus local avec Kubernetes pour connecter votre ordinateur de développement à votre cluster Kubernetes
keywords: Processus local avec Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 443783eb7f5359318cf8efbec8b6466a80fa1e85
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316260"
---
# <a name="how-local-process-with-kubernetes-works"></a>Fonctionnement de Processus local avec Kubernetes

Processus local avec Kubernetes vous permet d’exécuter et de déboguer votre code sur votre ordinateur de développement tout en restant connecté à votre cluster Kubernetes avec le reste de votre application ou de vos services. Par exemple, si vous avez une architecture de microservices importante avec de nombreux services et bases de données interdépendants, la réplication de ces dépendances sur votre ordinateur de développement peut s’avérer difficile. En outre, la création et le déploiement de code sur votre cluster Kubernetes pour chaque changement de code pendant le développement en boucle interne peuvent être lents, fastidieux et difficiles à utiliser avec un débogueur.

Processus local avec Kubernetes vous évite d’avoir à créer et à déployer votre code sur votre cluster en créant à la place une connexion directement entre votre ordinateur de développement et votre cluster. La connexion de votre ordinateur de développement à votre cluster pendant le débogage vous permet de tester et de développer rapidement votre service dans le contexte de l’application complète sans créer une configuration Docker ou Kubernetes.

Processus local avec Kubernetes redirige le trafic entre votre cluster Kubernetes connecté et votre ordinateur de développement. Cette redirection du trafic permet au code sur votre ordinateur de développement et les services en cours d’exécution dans votre cluster Kubernetes de communiquer comme s’ils se trouvaient dans le même cluster Kubernetes. Processus local avec Kubernetes offre également un moyen de répliquer des variables d’environnement et des volumes montés disponibles pour les pods dans votre cluster Kubernetes sur votre ordinateur de développement. L’accès aux variables d’environnement et aux volumes montés sur votre ordinateur de développement vous permet de travailler rapidement sur votre code sans avoir à répliquer ces dépendances manuellement.

## <a name="using-local-process-with-kubernetes"></a>Utilisation de Processus local avec Kubernetes

Pour utiliser Processus local avec Kubernetes, vous avez besoin de [Visual Studio Code][vs-code] avec les extensions [Azure Dev Spaces][azds-vs-code] et [Azure Kubernetes Service][az-aks-vs-code] installées et en cours d’exécution sur macOS ou Windows 10, ainsi que de la [CLI Azure Dev Spaces installée][azds-cli]. Vous pouvez aussi utiliser [Visual Studio 2019][visual-studio] s’exécutant sur Windows 10 avec les charges de travail *développement ASP.NET et web* et *développement Azure* installées et l’indicateur de fonctionnalité en préversion *AzureDevSpacesTools.LocalKubernetesDebugging* activé, ainsi que la [CLI Azure Dev Spaces installée][azds-cli]. Lorsque vous utilisez Processus local avec Kubernetes pour établir une connexion à votre cluster Kubernetes, vous avez la possibilité de rediriger tout le trafic à destination et en provenance d’un pod existant dans le cluster vers votre ordinateur de développement.

> [!NOTE]
> Lorsque vous utilisez Processus local avec Kubernetes, vous êtes invité à indiquer le nom du service pour la redirection vers votre ordinateur de développement. Cette option est un moyen pratique d’identifier un pod pour la redirection. Toute redirection entre votre cluster Kubernetes et votre ordinateur de développement est destinée à un pod.

Lorsque Processus local avec Kubernetes établit une connexion à votre cluster :

* Il vous invite à configurer le service à remplacer sur votre cluster, le port sur votre ordinateur de développement à utiliser pour votre code et la tâche de lancement de votre code en tant qu’action ponctuelle.
* Il remplace le conteneur du pod sur le cluster par un conteneur d’agent distant qui redirige le trafic vers votre ordinateur de développement.
* Elle exécute la commande [kubectl port-forward][kubectl-port-forward] sur votre ordinateur de développement pour transférer le trafic de celui-ci vers l’agent distant s’exécutant dans votre cluster.
* Elle collecte les informations d’environnement de votre cluster à l’aide de l’agent distant. Ces informations d’environnement incluent les variables d’environnement, les services visibles, les montages de volumes et les montages de secrets.
* Il configure l’environnement dans Visual Studio ou Visual Studio Code afin que le service sur votre ordinateur de développement puisse accéder aux mêmes variables que s’il s’exécutait sur le cluster.  
* Il met à jour votre fichier hosts pour mapper les services sur votre cluster à des adresses IP locales sur votre ordinateur de développement. Ces entrées de fichier hosts permettent au code s’exécutant sur votre ordinateur de développement d’adresser des demandes à d’autres services s’exécutant dans votre cluster. Pour mettre à jour votre fichier hosts, Processus local avec Kubernetes vous demande un accès administrateur sur votre ordinateur de développement lors de la connexion à votre cluster.
* Il démarre l’exécution et le débogage de votre code sur votre ordinateur de développement. Si nécessaire, Processus local avec Kubernetes libère les ports requis sur votre ordinateur de développement en arrêtant les services ou les processus qui utilisent actuellement ces ports.

Après avoir établi une connexion à votre cluster, vous pouvez exécuter et déboguer du code en mode natif sur votre ordinateur, sans conteneur, et le code peut interagir directement avec le reste de votre cluster. Tout trafic réseau que l’agent distant reçoit est redirigé vers le port local spécifié pendant la connexion, de sorte que votre code s’exécutant en mode natif peut accepter et traiter ce trafic. Les variables d’environnement, volumes et secrets de votre cluster sont mis à la disposition du code s’exécutant sur votre ordinateur de développement. Par ailleurs, en raison des entrées de fichier hosts et du transfert de port que le Processus local avec Kubernetes a ajoutés à votre ordinateur de développement, votre code peut envoyer le trafic réseau à des services s’exécutant sur votre cluster en utilisant les noms de service de votre cluster, et ce trafic est transféré aux services s’exécutant dans votre cluster. Le trafic est routé entre votre ordinateur de développement et votre cluster pendant toute la durée de votre connexion.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

Lorsque vous utilisez Processus local avec Kubernetes pour vous connecter à votre cluster, les journaux de diagnostic de votre cluster sont enregistrés dans le [répertoire temporaire][azds-tmp-dir] de votre ordinateur de développement. À l’aide de Visual Studio Code, vous pouvez également utiliser la commande *Afficher les informations de diagnostic* pour imprimer les variables d’environnement actuelles et les entrées DNS de votre cluster.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Processus local avec Kubernetes pour connecter votre ordinateur de développement local à votre cluster, consultez [Utiliser Processus local avec Kubernetes avec Visual Studio Code][local-process-kubernetes-vs-code] et [Utiliser Processus local avec Kubernetes avec Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download