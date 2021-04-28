---
title: Se connecter à Explorateur Stockage Azure | Microsoft Docs
description: Documentation sur la connexion à Explorateur Stockage Azure
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568644"
---
# <a name="sign-in-to-storage-explorer"></a>Se connecter à l’Explorateur Stockage

La connexion est la méthode recommandée pour accéder à vos ressources de stockage Azure avec Explorateur Stockage. En vous connectant, vous tirez parti des autorisations adossées à Azure AD, comme les listes de contrôle d’accès RBAC et Gen2 POSIX. 

## <a name="how-to-sign-in"></a>Comment se connecter

Pour vous connecter à Explorateur Stockage, ouvrez la **boîte de dialogue Se connecter**. Vous pouvez ouvrir la **boîte de dialogue Se connecter** à partir de la barre d’outils verticale gauche ou en cliquant sur **Ajouter un compte...** dans le **panneau du compte**.

Une fois la boîte de dialogue ouverte, choisissez **Abonnement** comme type de ressource auquel vous voulez vous connecter, puis cliquez sur **Suivant**.

Vous devez maintenant choisir l’environnement Azure auquel vous voulez vous connecter. Vous pouvez choisir parmi les environnements connus, comme Azure ou Azure Chine, ou vous pouvez ajouter votre propre environnement. Une fois votre environnement sélectionné, cliquez sur **Suivant**.

À ce stade, le **navigateur web par défaut** de votre système d’exploitation est lancé et une page de connexion s’ouvre. Pour de meilleurs résultats, laissez cette fenêtre de navigateur ouverte tant que vous utilisez Explorateur Stockage ou au moins jusqu’à ce que vous ayez effectué toute l’authentification MFA attendue. Une fois la connexion effectuée, vous pouvez revenir à Explorateur Stockage.

## <a name="managing-accounts"></a>Gérer des comptes

Vous pouvez gérer et supprimer les comptes Azure auxquels vous vous êtes connecté à partir du **volet des comptes**. Vous pouvez ouvrir le **volet des comptes** en cliquant sur le bouton **Gérer les comptes** dans la barre d’outils verticale gauche.

Dans le **volet des comptes**, vous voyez tous les comptes auxquels vous vous êtes connecté. Sous chaque compte, vous voyez :
- Les locataires auxquels le compte appartient
- Pour chaque locataire, les abonnements auxquels vous avez accès

Par défaut, Explorateur Stockage vous connecte uniquement à votre locataire de base. Si vous voulez voir les abonnements et les ressources d’un autre locataire, vous devez activer ce locataire. Pour activer un locataire, cochez la case en regard de celui-ci. Une fois que vous avez terminé d’utiliser un locataire, vous pouvez décocher sa case pour le désactiver. Vous ne pouvez pas désactiver votre locataire de base.

Après l’activation d’un locataire, il peut être nécessaire d’entrer à nouveau vos informations d’identification pour que Explorateur Stockage puisse charger les abonnements ou accéder aux ressources du locataire. Le fait de devoir entrer à nouveau vos informations d’identification se produit généralement en raison d’une stratégie d’accès conditionnel comme l’authentification multiplateforme (MFA). Et même si vous avez déjà effectué l’authentification multifacteur pour un autre locataire, il peut être nécessaire de l’effectuer à nouveau. Pour entrer à nouveau vos informations d’identification, cliquez simplement sur **Entrer à nouveau les informations d’identification...** . Vous pouvez aussi cliquer sur **Détails de l’erreur...** pour voir exactement pourquoi les abonnements n’ont pas pu être chargés.

Une fois vos abonnements chargés, vous pouvez choisir ceux que vous voulez inclure/exclure dans le filtre en cochant ou en décochant leur case.

Si vous voulez supprimer l’intégralité de votre compte Azure, cliquez sur **Supprimer** en regard du compte.

## <a name="changing-where-sign-in-happens"></a>Changement de l’endroit où s’effectue la connexion

Par défaut, la connexion s’effectue dans le **navigateur web par défaut** de votre système d’exploitation. La connexion avec votre navigateur web par défaut simplifie l’accès aux ressources sécurisées via des stratégies d’accès conditionnel, comme l’authentification multifacteur. Si pour une raison quelconque, la connexion avec le **navigateur web par défaut** de votre système d’exploitation ne fonctionne pas, vous pouvez changez l’endroit ou la façon dont Explorateur Stockage effectue la connexion.

Sous **Paramètres** > **Application** > **Se connecter**, recherchez le paramètre **Se connecter avec**. Vous disposez de trois options :
- **Navigateur web par défaut** : la connexion s’effectue dans le **navigateur web par défaut** de votre système d’exploitation. Cette option est recommandée.
- **Connexion intégrée** : la connexion va s’effectuer dans une fenêtre d’Explorateur Stockage. Cette option peut être utile si vous essayez de vous connecter avec plusieurs comptes Microsoft à la fois. Vous pouvez rencontrer des problèmes avec certaines stratégies d’accès conditionnel si vous choisissez cette option.
- **Flux de code d’appareil** : Explorateur Stockage va vous donner un code à entrer dans une fenêtre du navigateur. Cette option n’est pas recommandée. Le flux de code d’appareil n’est pas compatible avec de nombreuses stratégies d’accès conditionnel.

## <a name="troubleshooting-sign-in-issues"></a>Résolution des problèmes de connexion

Si vous avez des difficultés à vous connecter ou si vous rencontrez des problèmes avec un compte Azure après vous être connecté, reportez-vous à la [section Se connecter du guide de résolution des problèmes d’Explorateur Stockage](./storage-explorer-troubleshooting.md#sign-in-issues).
