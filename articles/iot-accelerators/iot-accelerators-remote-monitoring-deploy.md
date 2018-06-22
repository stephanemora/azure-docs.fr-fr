---
title: Déployer la solution de surveillance à distance - Azure | Microsoft Docs
description: Ce tutoriel montre comment provisionner l’accélérateur de solution de surveillance à distance sur le site azureiotsuite.com.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626851"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Déployer l’accélérateur de solution de surveillance à distance

Ce tutoriel montre comment provisionner l’accélérateur de solution de surveillance à distance. Vous déployez la solution à partir du site azureiotsuite.com. Vous pouvez également déployer la solution à l’aide de l’interface CLI. Pour en savoir plus sur cette option, consultez [Déployer un accélérateur de solution à partir de la ligne de commande](iot-accelerators-remote-monitoring-deploy-cli.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Déployer l’accélérateur de solution

Avant de déployer l’accélérateur de solution dans votre abonnement Azure, vous devez choisir des options de configuration :

1. Connectez-vous à [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) à l’aide de vos informations d’identification de compte Azure.

1. Cliquez sur **Essayer maintenant**, dans la vignette **Surveillance à distance**.

    ![Choisir la Surveillance à distance](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Dans la page de **Créer une solution de surveillance à distance**, entrez un **Nom de solution** pour votre accélérateur de solution de surveillance à distance.

1. Sélectionnez un déploiement **De base** ou **Standard**. Si vous déployez la solution pour découvrir son fonctionnement ou effectuer une démonstration, choisissez l’option **De base** afin de réduire les coûts.

1. Choisissez le langage **Java** ou **.NET**. Tous les microservices sont disponibles sous la forme d’implémentations Java ou .NET.

1. Examinez le contenu du panneau **Détails de la Solution** pour plus d’informations sur vos choix de configuration.

1. Sélectionnez l’**Abonnement** et la **Région** à utiliser pour configurer la solution.

1. Cliquez sur **Créer une solution** pour commencer le processus de déploiement. L’exécution de ce processus prend généralement plusieurs minutes :

    ![Détails de la solution de surveillance à distance](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Pour obtenir des informations de dépannage, consultez la rubrique [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (Que faire si le déploiement échoue ?) dans le référentiel GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Établir une connexion à l’accélérateur de solution

Une fois le processus de provisionnement terminé, vous pouvez vous connecter à votre accélérateur de solution de surveillance à distance.

1. Dans la page **Solutions approvisionnées**, choisissez votre nouvelle solution de surveillance à distance :

    ![Choisir la nouvelle solution](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Vous pouvez consulter les informations relatives à votre solution de surveillance à distance dans le panneau qui s’affiche. Choisissez **Tableau de bord des solutions** pour vous connecter à votre solution de surveillance à distance.

    > [!NOTE]
    > Vous pouvez supprimer votre solution de surveillance à distance dans ce panneau quand vous n’en avez plus besoin.

    ![Panneau de solutions](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. Le tableau de bord des solutions Surveillance à distance s’affiche dans votre navigateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

La solution de surveillance à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->