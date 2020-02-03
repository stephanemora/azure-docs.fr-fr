---
title: 'Débogage interactif : Instances de calcul VS Code et ML'
titleSuffix: Azure Machine Learning
description: Configurez VS Code Remote pour déboguer de manière interactive votre code avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 27243f47df7da22ab4adda088bdf631c1030dd6c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845188"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Déboguer de manière interactive sur une instance de calcul Azure Machine Learning avec VS Code Remote

Dans cet article, vous allez apprendre à configurer Visual Studio Code Remote sur une instance de calcul Azure Machine Learning afin de pouvoir **déboguer de manière interactive votre code** à partir de VS Code. 

> [!NOTE]
> Les instances de calcul ne sont disponibles que pour les espaces de travail associés aux régions **USA Centre Nord**, ** USA Est 2**, **Europe Nord** ou **Royaume-Uni Sud**.

+ Une [Instance de calcul Azure Machine Learning](concept-compute-instance.md) est une station de travail Cloud complètement managée pour les scientifiques des données qui fournit des fonctionnalités de gestion et de préparation de l’entreprise pour les administrateurs informatiques. 


+ Le développement [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) vous permet d’utiliser un conteneur, un ordinateur distant ou le sous-système Windows pour Linux (WSL) en tant qu’environnement de développement complet. 

## <a name="prerequisite"></a>Configuration requise  

Sur les plateformes Windows, vous devez [installer un client SSH compatible OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) s’il n’en existe pas déjà un. 

> [!Note]
> PuTTY n’est pas pris en charge sur Windows, car la commande SSH doit se trouver dans le chemin d’accès. 

## <a name="get-ip-and-ssh-port"></a>Obtenir le port IP et SSH 

1. Accédez à Azure Machine Learning Studio à l’adresse https://ml.azure.com/.

2. Sélectionnez votre [espace de travail](concept-workspace.md).
1. Cliquez sur l’onglet **Instances de calcul**.
1. Dans la colonne **URI de l’application**, cliquez sur le lien **SSH** de l’instance de calcul que vous souhaitez utiliser comme calcul distant. 
1. Dans la boîte de dialogue, notez l’adresse IP et le port SSH. 
1. Enregistrez votre clé privée dans le répertoire ~/.ssh/ de votre ordinateur local ; par exemple, ouvrez un éditeur pour un nouveau fichier et collez la clé dans : 

   **Linux** : 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows** : 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   La clé privée ressemble à ce qui suit :
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Modifiez les autorisations sur le fichier pour vous assurer que vous êtes le seul à pouvoir lire le fichier.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Ajouter une instance en tant qu’hôte 

Ouvrez le fichier `~/.ssh/config` (Linux) ou `C:\Users<username>.ssh\config` (Windows) dans un éditeur et ajoutez une nouvelle entrée semblable à celle-ci :

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Voici quelques détails sur les champs : 

|Champ|Description|
|----|---------|
|Host|Utilisez le raccourci de votre choix pour l’instance de calcul |
|HostName|Il s’agit de l’adresse IP de l’instance de calcul |
|Port|Il s’agit du port affiché dans la boîte de dialogue SSH ci-dessus |
|Utilisateur|Il doit être défini sur `azureuser` |
|IdentityFile|Doit pointer vers le fichier où vous avez enregistré la clé privée |

À présent, vous devez être en mesure d’utiliser SSH pour votre instance de calcul, à l’aide de la syntaxe abrégée que vous avez utilisée précédemment, `ssh azmlci1`. 

## <a name="connect-vs-code-to-the-instance"></a>Connecter VS Code à l’instance 

1. [Installez Visual Studio Code](https://code.visualstudio.com/).

1. [Installez l’extension SSH à distance](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Cliquez sur l’icône SSH à distance à gauche pour afficher vos configurations SSH.

1. Cliquez avec le bouton droit sur la configuration d’hôte SSH que vous venez de créer.

1. Sélectionnez **Se connecter à l’hôte dans la fenêtre active**. 

À partir de là, vous travaillez entièrement sur l’instance de calcul et vous pouvez modifier, déboguer, utiliser Git, utiliser des extensions, etc., tout comme vous pouvez le faire avec votre Visual Studio Code local. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré Visual Studio Code Remote, vous pouvez utiliser une instance de calcul comme calcul à distance à partir de Visual Studio Code pour déboguer votre code de manière interactive. 

[Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.