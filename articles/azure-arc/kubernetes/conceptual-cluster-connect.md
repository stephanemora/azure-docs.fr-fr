---
title: Connexion au cluster - Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité Connexion au cluster de Kubernetes avec Azure Arc.
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450715"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Connexion au cluster sur Kubernetes avec Azure Arc

La fonctionnalité *Connexion au cluster* de Kubernetes avec Azure Arc fournit la connectivité à l’`apiserver` du cluster sans qu’il soit nécessaire d’activer un port entrant sur le pare-feu. Un agent proxy inverse s’exécutant sur le cluster peut démarrer de manière sécurisée une session sortante avec le service Azure Arc. 

Connexion au cluster permet aux développeurs d’accéder à leurs clusters depuis n’importe où, à des fins de développement et de débogage interactifs. Il permet également aux utilisateurs et aux administrateurs du cluster d’accéder à leurs clusters ou de les gérer depuis n’importe où. Vous pouvez même utiliser des exécuteurs/agents hébergés d’Azure Pipelines, GitHub Actions ou tout autre service CI/CD hébergé afin de déployer des applications sur des clusters locaux, sans nécessiter d’agents auto-hébergés.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

[ ![Architecture de Connexion au cluster](./media/conceptual-cluster-connect.png) ](./media/conceptual-cluster-connect.png#lightbox)

Du côté du cluster, un agent proxy inverse appelé `clusterconnect-agent`, déployé dans le cadre du chart Helm de l’agent, effectue des appels sortants vers le service Azure Arc pour établir la session.

Lorsque l’utilisateur appelle `az connectedk8s proxy` :
1. Le binaire de proxy Azure Arc est téléchargé et lancé en tant que processus sur l’ordinateur client. 
1. Le proxy Azure Arc extrait un fichier `kubeconfig` associé au cluster Kubernetes avec Azure Arc sur lequel `az connectedk8s proxy` est appelé.
    * Le proxy Azure Arc utilise le jeton d’accès Azure de l’appelant et le nom de l’ID Azure Resource Manager. 
1. Le fichier `kubeconfig`, enregistré sur l’ordinateur par le proxy Azure Arc, pointe l’URL du serveur vers un point de terminaison sur le processus de proxy Azure Arc.

Lorsqu’un utilisateur envoie une requête à l’aide de ce fichier `kubeconfig` :
1. Le proxy Azure Arc mappe le point de terminaison recevant la requête au service Azure Arc. 
1. Le service Azure Arc transfère ensuite la requête au `clusterconnect-agent` en cours d’exécution sur le cluster. 
1. Le `clusterconnect-agent` transfère la requête au composant `kube-aad-proxy`, qui effectue l’authentification Azure AD sur l’entité appelante. 
1. Après l’authentification Azure AD, `kube-aad-proxy` utilise la fonctionnalité d’[emprunt d’identité utilisateur](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) de Kubernetes pour transférer la requête à l’`apiserver` du cluster.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Accédez à votre cluster](./cluster-connect.md) de manière sécurisée où que vous soyez en utilisant Connexion au cluster.