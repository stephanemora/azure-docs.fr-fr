---
title: Installer une application sur un cluster autonome
description: Dans ce tutoriel, vous allez apprendre à installer une application dans votre cluster Service Fabric autonome.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613956"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Didacticiel : déployer une application sur votre cluster autonome Service Fabric

Les clusters autonomes Service Fabric vous permettent de choisir votre propre environnement et de créer un cluster dans le cadre de l’approche « n’importe quel SE, n’importe quel cloud » suivie par Service Fabric. Dans cette série de tutoriels, vous créez un cluster autonome hébergé sur AWS et vous déployez une application dans celui-ci.

Ce tutoriel est le troisième de la série.  Les clusters autonomes Service Fabric vous permettent de choisir votre propre environnement et de créer un cluster dans le cadre de l’approche « n’importe quel SE, n’importe quel cloud » avec Service Fabric. Ce didacticiel vous montre comment créer l’infrastructure AWS nécessaire pour héberger ce cluster autonome.

Dans ce troisième volet, vous apprenez à :

> [!div class="checklist"]
> * Télécharger l’exemple d’application
> * Déployer sur le cluster

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce tutoriel :

* [Installez Visual Studio 2019](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
* [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote

Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Déployer l’application sur le cluster Service Fabric

À présent que l’application est téléchargée, vous pouvez la déployer sur un cluster directement à partir de Visual Studio.

1. Ouvrez Visual Studio.

2. Sélectionnez **Fichier** > **Ouvrir**.

3. Accédez au dossier dans lequel vous avez cloné le référentiel git et sélectionnez Voting.sln.

4. Cliquez avec le bouton droit sur le projet de l’application `Voting` dans l’Explorateur de solutions et choisissez **Publier**.

5. Sélectionnez la flèche déroulante pour le **point de terminaison de connexion** et entrez le nom DNS public de l’un des nœuds de votre cluster.  Par exemple : `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Notez que dans Azure, un nom de domaine complet (FQDN) n’est pas accordé automatiquement, mais peut facilement [être défini sur la page Vue d’ensemble de la machine virtuelle.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Ouvrez votre navigateur préféré et tapez l’adresse du cluster (le point de terminaison de connexion, cette application se déploie sur le port 8080 - par exemple, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Réponse de l’API à partir du cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Étapes suivantes

Dans le troisième volet de la série, vous avez appris à déployer une application sur votre cluster :

> [!div class="checklist"]
> * Télécharger l’exemple d’application
> * Déployer sur le cluster

Passez au quatrième volet de la série pour nettoyer votre cluster.

> [!div class="nextstepaction"]
> [Nettoyez vos ressources](service-fabric-tutorial-standalone-clean-up.md)