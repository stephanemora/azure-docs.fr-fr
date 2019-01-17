---
title: Test de vérification des fonctionnalités interactives dans le service VaaS Azure Stack | Microsoft Docs
description: Découvrez comment créer des tests de vérification des fonctionnalités interactives pour Azure Stack avec le service VaaS (validation en tant que service).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5b20e170388aa6e9b73b7c5c26a286ec51b52209
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245835"
---
# <a name="interactive-feature-verification-testing"></a>Test de vérification des fonctionnalités interactives  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez utiliser le framework de test de vérification des fonctionnalités interactives pour demander des tests pour votre système. Quand vous demandez un test, Microsoft utilise le framework pour préparer des tests qui nécessitent des étapes interactives manuelles. Microsoft peut utiliser le framework pour chaîner plusieurs tests automatisés autonomes.

Cet article décrit un scénario manuel simple. Le test vérifie le remplacement d’un disque dans Azure Stack. Le framework collecte des journaux de diagnostic à chaque étape. Vous pouvez déboguer les problèmes à mesure que vous les rencontrez. Le framework permet également le partage de journaux produits par d’autres outils ou processus, et vous permet de fournir des commentaires sur le scénario.

## <a name="overview-of-a-test-pass"></a>Vue d’ensemble d’une passe de test

Un test du remplacement d’un disque est un scénario courant. Dans cet exemple, le test comprend sept étapes :

1.  Créez un workflow **Test Pass** (passe de test).
2.  Sélectionnez le test d’identification de disque **Disk Identification Test**.
3.  Démarrez le test.
4.  Choisissez les actions à effectuer dans le scénario de vérification interactive.
5.  Vérifiez le résultat du scénario.
6.  Envoyez le résultat du test à Microsoft.
7.  Vérifiez l’état dans le portail VaaS.

## <a name="create-a-new-test-pass"></a>Créer une passe de test

1.  Accédez au [portail de validation Azure Stack](https://www.azurestackvalidation.com) et connectez-vous.

2.  Créez une solution ou choisissez-en une existante.

3.  Sélectionnez **Démarrer** sur la vignette **Passes de test**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Entrez un nom pour le flux de travail **Test Pass**.

5.  Entrez l’environnement et les paramètres de test communs en suivant les instructions mentionnées dans l’article [Paramètres de flux de travail communs pour la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md).

6.  La chaîne de connexion de diagnostic doit respecter le format spécifié dans la section [Paramètres de test](azure-stack-vaas-parameters.md#test-parameters) de l’article [Paramètres communs des workflows](azure-stack-vaas-parameters.md).

7.  Entrez les paramètres obligatoires pour le test.

8.  Sélectionnez l’agent pour exécuter la série de tests interactifs.

> [!Note]  
> L’utilisateur administrateur de domaine et le mot de passe doivent être spécifiés pour le test de vérification des fonctionnalités interactives d’identification de disque.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Sélectionner le test

1.  Sélectionnez le **test d’identification de disque \<version>**.

    > [!Note]  
    > La version du test augmente à mesure que des améliorations sont apportées aux documentations et ressources d’accompagnement. La version la plus élevée doit toujours être utilisée, sauf indication contraire de Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Fournissez l’utilisateur administrateur de domaine et le mot de passe en sélectionnant **Modifier**.

3.  Sélectionnez l’agent/le DVM d’exécution de test appropriés pour lancer le test.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Sélectionnez **Envoyer** pour démarrer le test.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Démarrer le test

Les invites du test d’identification de disque s’affichent sur l’ordinateur qui exécute l’agent VaaS. En général, il s’agit du DVM ou du serveur de rebond (jumpbox) de l’instance Azure Stack.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Choisir les actions

1.  Suivez les liens **Documentation** et **Validation** pour passer en revue les instructions de Microsoft sur l’exécution de ce scénario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Sélectionnez **Suivant**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Suivez les instructions pour exécuter le script de prévérification.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Une fois le script de pré-vérification correctement effectué, exécutez le scénario manuel (Remplacement de disque) conformément aux liens **Documentation** et **Validation** disponibles sous l’onglet **Informations**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Ne fermez pas la boîte de dialogue pendant que vous effectuez le scénario manuel.

6.  Une fois le scénario manuel terminé, suivez les instructions pour exécuter le script de post-vérification.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  En cas de réussite du scénario manuel (Remplacement de disque), sélectionnez **Suivant**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Si vous fermez la fenêtre, le test s’arrête avant d’être terminé.

## <a name="check-the-status"></a>Vérifier l’état

1.  Une fois le test terminé, vous êtes invité à fournir des commentaires.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Ces questions aident Microsoft à évaluer le taux de réussite et la qualité de mise sur le marché du scénario.

## <a name="send-the-test-result"></a>Envoyer le résultat du test

1.  Attachez tous les fichiers journaux que vous voulez envoyer à Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Acceptez le CLUF pour l’envoi de commentaires.

3.  Sélectionnez **Envoyer** pour envoyer les résultats à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)
