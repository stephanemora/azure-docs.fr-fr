---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: dcca63cc86889ad9dc0e56f932dbed96153de7ed
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44754342"
---
## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes
À présent, exécutons notre code. Dans la fenêtre de terminal, exécutez la commande ci-après à partir du **dossier de code racine**, webfrontend :

```cmd
azds up
```

En surveillant la sortie de la commande pendant que celle-ci s’exécute, vous remarquerez plusieurs choses :
- Le code source est synchronisé avec l’espace de développement dans Azure.
- Une image conteneur est créée dans Azure, comme spécifié par les ressources Docker dans votre dossier de code.
- La commande crée des objets Kubernetes qui utilisent l’image conteneur, comme spécifié par le graphique Helm dans votre dossier de code.
- Des informations relatives aux points de terminaison du conteneur s’affichent. Dans notre cas, nous attendons une URL HTTP publique.
- En supposant que les étapes ci-dessus se sont correctement déroulées, vous devriez commencer à voir la sortie `stdout` (et `stderr`) lorsque le conteneur démarre.

> [!Note]
> Ces étapes nécessitent davantage de temps lors de la première exécution de la commande `up` ; les exécutions suivantes s’effectuent plus rapidement.

### <a name="test-the-web-app"></a>Tester l’application web
Analysez la sortie de la console pour y rechercher les informations concernant l’URL publique qui a été créée par la commande `up`. Ces informations se présentent sous la forme suivante : 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
```

Ouvrez cette URL dans une fenêtre de navigateur. Vous devriez alors voir l’application web se charger. Lorsque le conteneur s’exécute, les sorties `stdout` et `stderr` sont transmises à la fenêtre de terminal.

> [!Note]
> À la première exécution, vous devrez peut-être patienter plusieurs minutes avant que le serveur DNS public ne soit prêt. Si l’URL publique n’est pas résolue, vous pouvez utiliser en guise d’alternative l’URL http://localhost: <portnumber> affichée dans la sortie de la console. Si vous utilisez l’URL localhost, le conteneur semble s’exécuter en local. En réalité, il s’exécute dans AKS. Pour des raisons pratiques et pour faciliter l’interaction avec le service à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure. Vous pouvez réessayer l’URL publique plus tard quand l’enregistrement DNS est prêt.
