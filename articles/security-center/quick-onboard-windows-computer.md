---
title: Intégrer des ordinateurs Windows à Azure Security Center
description: Ce guide de démarrage rapide explique comment provisionner l’agent Log Analytics sur un ordinateur Windows.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: ecbdb6dc73082fc0fab04c8802c43219cabb6a79
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519760"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Démarrage rapide : Intégrer des ordinateurs Windows à Azure Security Center
Après avoir intégré vos abonnements Azure, vous pouvez activer Security Center pour les ressources s’exécutant en dehors d’Azure, par exemple, localement ou dans d’autres clouds, en provisionnant l’agent Log Analytics.

Ce guide de démarrage rapide explique comment installer l’agent Log Analytics sur un ordinateur Windows.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Vous devez utiliser le niveau de prix standard de Security Center pour commencer ce guide de démarrage rapide. Vous trouverez des instructions sur la mise à niveau sur la page [Intégrer un abonnement Azure à Security Center Standard](security-center-get-started.md). Vous pouvez essayer le niveau Standard de Security Center gratuitement pendant 60 jours. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Ajouter un nouvel ordinateur Windows

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

   ![Vue d’ensemble de Security Center][2]

3. Dans le menu principal de Security Center, sélectionnez **Prise en main**.
4. Sélectionnez l’onglet **Prise en main**.

   ![Bien démarrer][3]

5. Cliquez sur **Configurer** sous **Ajouter de nouveaux ordinateurs non Azure**. Une liste de vos espaces de travail Log Analytics apparaît. Elle comprend, le cas échéant, l’espace de travail par défaut créé pour vous par Security Center à l’activation de l’approvisionnement automatique. Sélectionnez cet espace de travail ou un autre espace de travail à utiliser.

    ![Ajouter un ordinateur autre qu’Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Le panneau **Agent direct** s’ouvre et affiche un lien permettant de télécharger un agent Windows et des clés pour votre ID d’espace de travail, qui serviront à la configuration de l’agent.

6. Cliquez sur le lien **Télécharger l’Agent Windows** correspondant au type de processeur de votre ordinateur pour télécharger le fichier d’installation.

7. À droite du champ **ID de l’espace de travail**, sélectionnez l’icône de copie et collez l’ID dans le Bloc-notes.

8. À droite du champ **Clé primaire**, sélectionnez l’icône de copie et collez la clé dans le Bloc-notes.

## <a name="install-the-agent"></a>Installer l’agent
Vous devez maintenant installer le fichier téléchargé sur l’ordinateur cible.

1. Copiez le fichier sur l’ordinateur cible et exécutez le programme d’installation.
2. Sur la page d’**accueil**, sélectionnez **Suivant**.
3. Sur la page **Termes du contrat de licence**, lisez la licence, puis sélectionnez **J’accepte**.
4. Sur la page **Dossier de destination**, modifiez ou conservez le dossier d’installation par défaut, puis sélectionnez **Suivant**.
5. Sur la page **Options d’installation de l’agent**, choisissez de connecter l’agent à Azure Log Analytics, puis sélectionnez **Suivant**.
6. Sur la page **Azure Log Analytics**, collez **l’ID de l’espace de travail** et la **Clé de l’espace de travail (clé primaire)** que vous avez copiés dans le Bloc-notes au cours de la procédure précédente.
7. Si l’ordinateur doit communiquer avec un espace de travail Log Analytics dans le cloud Azure Government, sélectionnez **Azure - Gouvernement des États-Unis** dans la liste déroulante **Azure Cloud**. Si l’ordinateur a besoin de communiquer avec le service Log Analytics par le biais d’un serveur proxy, sélectionnez **Avancé**, puis indiquez l’URL et le numéro de port du serveur proxy.
8. Sélectionnez **Suivant** après avoir indiqué les paramètres de configuration nécessaires.

   ![Installer l’agent][5]

9. Sur la page **Prêt pour l’installation**, vérifiez vos choix, puis sélectionnez **Installer**.
10. Sur la page **Configuration effectuée**, sélectionnez **Terminer**.

Quand vous avez terminé, l’**agent Log Analytics** apparaît dans le **Panneau de configuration**. Vous pouvez vérifier votre configuration et vous assurer que l’agent est connecté.

Pour plus d’informations sur l’installation et la configuration de l’agent, consultez la page [Connecter des ordinateurs Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Vous pouvez maintenant effectuer le monitoring de vos machines virtuelles Azure et de vos ordinateurs extérieurs à Azure en un seul et même endroit. La fenêtre **Calcul** affiche une vue d’ensemble de toutes les machines virtuelles et de tous les ordinateurs, ainsi que des recommandations. Chaque colonne représente un ensemble de recommandations. La couleur correspond à l’état de sécurité actuel de l’ordinateur ou de la machine virtuelle pour une recommandation donnée. Security Center met également en évidence les éventuelles détections d’alertes de sécurité pour ces ordinateurs.

  ![Panneau Calcul][6]

Deux types d’icônes sont représentés sur le panneau **Calcul** :

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Ordinateur extérieur à Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Nettoyer les ressources
Vous pourrez supprimer l’agent de l’ordinateur Windows lorsque vous n’en aurez plus besoin.

Pour supprimer l’agent :

1. Ouvrez le **Panneau de configuration**.
2. Ouvrez **Programmes et fonctionnalités**.
3. Dans **Programmes et fonctionnalités**, sélectionnez **Agent Log Analytics**, puis cliquez sur **Désinstaller**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez provisionné l’agent Log Analytics sur un ordinateur Windows. Pour en savoir plus sur Security Center, enchaînez avec le didacticiel sur la configuration d’une stratégie de sécurité et l’évaluation de la sécurité des ressources.

> [!div class="nextstepaction"]
> [Tutoriel : Définir et évaluer les stratégies de sécurité](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
