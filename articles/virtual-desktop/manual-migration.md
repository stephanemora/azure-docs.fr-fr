---
title: Migration manuelle à partir d’Azure Virtual Desktop (classique) – Azure
description: Guide pratique pour migrer manuellement d’Azure Virtual Desktop (classique) vers Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 633925c8e7a990bcdfa822f77996072f87ae7e90
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753302"
---
# <a name="migrate-manually-from-azure-virtual-desktop-classic"></a>Migration manuelle à partir d’Azure Virtual Desktop (classique)

Azure Virtual Desktop (classique) crée son environnement de service avec des cmdlets PowerShell, des API REST et des objets de service. Un « objet » dans un environnement de service Azure Virtual Desktop consiste en un élément créé par Azure Virtual Desktop. Les objets de service incluent des locataires, des pools d’hôtes, des groupes d’applications et les hôtes de session.

Toutefois, Azure Virtual Desktop (classique) n’est pas intégré avec Azure. À défaut d’intégration avec Azure, le portail Azure ne gère pas automatiquement tous les objets que vous créez, car ceux-ci ne sont pas connectés à votre abonnement Azure.

La récente mise à jour majeure d’Azure Virtual Desktop marque un glissement du service vers une intégration complète avec Azure. Les objets créés dans Azure Virtual Desktop sont automatiquement gérés par le Portail Azure.

Dans cet article, nous expliquons pourquoi il est préférable de passer à la dernière version d’Azure Virtual Desktop. Ensuite, nous vous indiquons comment effectuer une migration manuelle d’Azure Virtual Desktop (classique) vers la dernière mise à jour d’Azure Virtual Desktop.

## <a name="why-migrate"></a>Pourquoi migrer ?

Les mises à jour majeures sont parfois incommodes, en particulier celles que vous devez effectuer manuellement. Il existe cependant des raisons pour lesquelles vous ne pouvez pas migrer automatiquement :

- Les objets de service existants créés avec la version classique n’ont aucune représentation dans Azure. Leur étendue ne va pas au-delà du service Azure Virtual Desktop.
- Avec la dernière mise à jour, l’ID d’application du service a été modifié : le consentement aux applications tel qu’il se faisait pour Azure Virtual Desktop (classique) a été supprimé. Il n’est pas possible de créer des objets Azure avec Azure Virtual Desktop, sauf s’ils sont authentifiés avec le nouvel ID d’application.

En dépit de ses contraintes, la migration à partir de la version classique reste importante. Voici ce que vous pouvez faire après la migration :

- gérer Azure Virtual Desktop sur le Portail Azure ;
- affecter des groupes d’utilisateurs Azure Active Directory (AD) à des groupes d’applications ;
- utiliser la fonctionnalité Log Analytics améliorée pour résoudre des problèmes liés à votre déploiement ;
- utiliser des contrôles d’accès en fonction du rôle natifs Azure (Azure RBAC) pour gérer l’accès administratif.

## <a name="when-should-i-migrate"></a>Quand effectuer la migration ?

Lorsque vous vous interrogez sur l’opportunité d’opérer la migration, vous devez également prendre en compte la situation actuelle et future de votre déploiement.

Il existe en particulier quelques scénarios où nous vous recommandons de migrer manuellement :

- Vous disposez d’une configuration de pool d’hôtes de test avec un petit nombre d’utilisateurs.
- Vous disposez d’une configuration de pool d’hôtes de production avec un petit nombre d’utilisateurs, mais vous envisagez de passer à terme à des centaines d’utilisateurs.
- Vous disposez d’une configuration simple qui peut être facilement répliquée. Par exemple, si vos machines virtuelles utilisent une image de la galerie.

> [!IMPORTANT]
> Si vous utilisez une configuration avancée qui a mis beaucoup de temps à se stabiliser ou qui compte de nombreux utilisateurs, nous vous déconseillons d’effectuer une migration manuelle.

## <a name="prepare-for-migration"></a>Préparation de la migration

Avant de commencer, vous devez vous assurer que votre environnement est prêt pour la migration.

Avant de commencer le processus de migration, vérifiez les points suivants :

- Vous disposez d’un abonnement Azure dans lequel vous allez créer des objets de service Azure.
- Les rôles suivants vous sont attribués :
    
    - Contributeur
    - Administrateur de l'accès utilisateur
    
    Le rôle Contributeur vous permet de créer des objets Azure sur votre abonnement, et le rôle Administrateur de l’accès utilisateur vous permet d’attribuer des utilisateurs à des groupes d’applications.

## <a name="how-to-migrate-manually"></a>Comment migrer manuellement

Maintenant que vous êtes préparé pour le processus de migration, il est temps de passer à l’action.

Pour migrer manuellement d’Azure Virtual Desktop (classique) vers Azure Virtual Desktop, procédez comme suit :

1. Suivez les instructions fournies dans [Créer un pool d’hôtes avec le portail Azure](create-host-pools-azure-marketplace.md) pour créer tous les objets de niveau supérieur via le portail Azure.
2. Si vous souhaitez intégrer les machines virtuelles que vous utilisez déjà, suivez les instructions indiquées dans [Inscription des machines virtuelles dans le pool d’hôtes Azure Virtual Desktop](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) pour les inscrire manuellement auprès du pool d’hôtes créé à l’étape 1.
3. Créez des groupes d’applications RemoteApp.
4. Publiez des utilisateurs ou des groupes d’utilisateurs sur le nouveau bureau et les groupes d’applications RemoteApp.
5. Mettez à jour votre stratégie d’accès conditionnel pour autoriser les nouveaux objets en suivant les instructions fournies dans [Configurer l’authentification multifacteur](set-up-mfa.md).

Pour éviter les temps d’arrêt, vous devez commencer par inscrire vos hôtes de session existants auprès des pools d’hôtes intégrés à Azure Resource Manager dans plusieurs petits groupes à la fois. Ensuite, transférez lentement vos utilisateurs vers les nouveaux groupes d’applications intégrés à Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes

Une fois la migration effectuée, découvrez comment fonctionne Azure Virtual Desktop en consultant [nos tutoriels](create-host-pools-azure-marketplace.md). Apprenez-en davantage sur les fonctionnalités de gestion avancées décrites dans [Développer un pool d’hôtes existant](expand-existing-host-pool.md) et [Personnaliser les propriétés RDP](customize-rdp-properties.md).

Pour plus d’informations sur les objets de service, consultez [Environnement Azure Virtual Desktop](environment-setup.md).
