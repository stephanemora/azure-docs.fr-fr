---
title: Fichier Include
description: Fichier Include
services: iot-accelerators
author: v-krghan
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/25/2019
ms.author: v-krghan
ms.custom: include file
ms.openlocfilehash: 5909533c2153fe7c6697f516ce7568787b82bdab
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55105789"
---
## <a name="download-the-source-code"></a>Télécharger le code source

Les dépôts de code source de supervision à distance incluent le code source et les fichiers config Docker nécessaires à l’exécution des images Docker des microservices.

Pour cloner le référentiel et en créer une version locale, utilisez votre environnement de ligne de commande afin d'accéder à un dossier approprié sur votre ordinateur local. Exécutez ensuite l’un des ensembles de commandes suivants pour cloner le dépôt Java :

Pour télécharger la dernière version des implémentations de microservices java, exécutez :


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Ces commandes téléchargent le code source de tous les microservices en plus des scripts que vous utilisez pour exécuter les microservices localement. Même si vous n’avez pas besoin du code source pour exécuter les microservices dans Docker, vous pouvez l’utiliser si vous envisagez de modifier ultérieurement l’accélérateur de solution et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ceux-ci dépendent de l’exécution des services Azure exécutés dans le cloud. Utilisez le script suivant pour déployer les services Azure. Les exemples de scripts suivants supposent que vous utilisez le dépôt java sur un ordinateur Windows. Si vous travaillez dans un autre environnement, adaptez les chemins, les extensions de fichier et les séparateurs de chemin en conséquence.

### <a name="create-new-azure-resources"></a>Créer des ressources Azure

Si vous n’avez pas encore créé les ressources Azure nécessaires, procédez comme suit :

1. Dans votre environnement de ligne de commande, accédez au dossier **\services\scripts\local\launch** de votre copie clonée du référentiel.

1. Exécutez les commandes suivantes pour installer l'outil CLI **pcs** et vous connecter à votre compte Azure :

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Exécutez le script **start.cmd**. Le script vous invite à entrer les informations suivantes :
    * Le nom de la solution.
    * Sélectionnez l’abonnement Azure à utiliser.
    * L’emplacement du centre de données Azure à utiliser.

    Le script crée un groupe de ressources dans Azure avec le nom de votre solution. Ce groupe de ressources contient les ressources Azure qu’utilise l’accélérateur de solution. Vous pouvez supprimer ce groupe de ressources dès que vous n’avez plus besoin des ressources correspondantes.

    Le script ajoute également un ensemble de variables d'environnement dotées du préfixe **PCS** sur votre ordinateur local. Quand vous lancez les conteneurs Docker ou les projets de microservices localement, ils lisent leurs valeurs de configuration à partir de ces variables d’environnement.

    > [!TIP]
    > Quand le script s’exécute, il enregistre également les variables d’environnement dans un fichier appelé **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env**. Vous pouvez les utiliser pour de futurs déploiements d’accélérateurs de solutions. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

1. Quittez votre environnement de ligne de commande.

### <a name="use-existing-azure-resources"></a>Utiliser les ressources Azure existantes

Si vous avez déjà créé les ressources Azure requises, créez les variables d'environnement correspondantes sur votre ordinateur local. Vous pouvez les enregistrer dans le fichier **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env** du déploiement. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.