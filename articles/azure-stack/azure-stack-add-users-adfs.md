---
title: Ajouter des utilisateurs pour Azure Stack ADFS | Microsoft Docs
description: Découvrez comment ajouter des utilisateurs pour les déploiements ADFS d’Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120810"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Ajouter des utilisateurs Azure Stack dans AD FS
Vous pouvez utiliser le composant logiciel enfichable **Utilisateurs et ordinateurs Active Directory** pour ajouter des utilisateurs supplémentaires à un environnement Azure Stack en utilisant AD FS comme son fournisseur d’identité.

## <a name="add-windows-server-active-directory-users"></a>Ajouter des utilisateurs Windows Server Active Directory
> [!TIP]
> Cet exemple utilise le répertoire actif ASDK azurestack.local par défaut. 

1.  Connectez-vous à un ordinateur avec un compte fournissant un accès aux outils d’administration Windows, puis ouvrez une nouvelle console MMC (Microsoft Management Console).
2.  Cliquez sur **Fichier > Ajouter ou supprimer un composant logiciel enfichable**.
3.  Sélectionnez **Utilisateurs et ordinateurs Active Directory** > **AzureStack.local** > **Utilisateurs**.
4.  Cliquez sur **Action** > **Nouveau** > **Utilisateur**.
5.  Dans la fenêtre Nouvel objet – Utilisateur, entrez et confirmez un mot de passe
6.  Cliquez sur **Suivant** pour finaliser les valeurs et cliquez sur Terminer pour créer l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes
[Créer les principaux du service](azure-stack-create-service-principals.md)