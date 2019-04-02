---
title: Qu’est-ce que Windows Virtual Desktop Preview ?  - Azure
description: Vue d’ensemble de Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 4443d71d5c0b84ac7dbc18129338229c40323d2c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401050"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Qu’est-ce que Windows Virtual Desktop Preview ? 

Actuellement disponible en préversion publique, Windows Virtual Desktop Preview est un service de virtualisation de bureau et d’application qui s’exécute dans le cloud.

Voici ce que vous pouvez faire quand vous exécutez Windows Virtual Desktop sur Azure :

* Configurer un déploiement Windows 10 multisession qui délivre une version complète de Windows 10 avec scalabilité
* Virtualiser Office 365 ProPlus et l’optimiser pour une exécution dans des scénarios virtuels multiutilisateurs
* Fournir des bureaux virtuels Windows 7 avec Mises à jour de sécurité étendues gratuites
* Tirer parti de vos applications et bureaux Windows Server et Services Bureau à distance existants sur n’importe quel ordinateur
* Virtualiser des bureaux et des applications
* Gérer des bureaux et des applications Windows 10, Windows Server et Windows 7 avec une expérience de gestion unifiée

## <a name="key-capabilities"></a>Fonctionnalités clés

Avec Windows Virtual Desktop, vous pouvez configurer un environnement scalable et flexible :

* Créez un environnement de virtualisation de bureaux complet dans votre abonnement Azure sans avoir à exécuter de serveurs de passerelle supplémentaires.
* Publiez autant de pools hôtes que nécessaire pour prendre en charge vos diverses charges de travail.
* Importez votre propre image pour les charges de travail de production ou de test à partir de la galerie Azure.
* Réduisez les coûts avec des ressources mises en pool et multisessions. Avec la nouvelle fonctionnalité multisession de Windows 10 Entreprise exclusive à Windows Virtual Desktop et le rôle Hôte de session Bureau à distance sur Windows Server, vous pouvez réduire considérablement le nombre de machines virtuelles et la charge sur le système d’exploitation tout en continuant à fournir les mêmes ressources à vos utilisateurs.
* Bénéficiez d’une propriété individuelle par le biais des bureaux personnels (persistants).

Vous pouvez déployer et gérer des bureaux virtuels :

* Utilisez les interfaces REST et PowerShell Windows Virtual Desktop pour configurer les pools hôtes, créer des groupes d’applications, affecter des utilisateurs et publier des ressources.
* Publiez des applications distantes individuelles ou des applications de bureau complètes à partir d’un pool hôte unique, créez des groupes d’applications individuels pour différents groupes d’utilisateurs, ou affectez des utilisateurs à plusieurs groupes d’applications afin de réduire le nombre d’images.
* Lors de la gestion de votre environnement, utilisez l’accès délégué intégré pour attribuer des rôles et recueillir des données de diagnostics afin de comprendre différentes erreurs dues aux utilisateurs ou à la configuration.
* Utilisez le nouveau service Diagnostics pour résoudre les problèmes.
* Gérez uniquement l’image et les machines virtuelles, pas l’infrastructure. Vous n’avez pas besoin de gérer personnellement les rôles Bureau à distance comme vous le faites avec les Services Bureau à distance, mais uniquement les machines virtuelles dans votre abonnement Azure.

Vous pouvez également affecter et connecter des utilisateurs à vos bureaux virtuels :

* Une fois affectés, les utilisateurs peuvent lancer n’importe quel client Windows Virtual Desktop pour se connecter à leurs bureaux et applications Windows publiés. Connectez-vous à partir de n’importe quel appareil par le biais d’une application native sur votre appareil ou du client web HTML5 Windows Virtual Desktop.
* Établissez une communication sécurisée entre les utilisateurs et le service par le biais de connexions inverses, vous évitant ainsi de devoir laisser des ports entrants ouverts.

## <a name="requirements"></a>Configuration requise

Il existe quelques conditions à remplir avant de configurer Windows Virtual Desktop et de connecter vos utilisateurs à leurs bureaux et applications Windows.

Tout d’abord, veillez à disposer des licences appropriées pour vos utilisateurs, en fonction du bureau et des applications que vous envisagez de déployer :

|SE|Licence obligatoire|
|---|---|
|Windows 10 Entreprise multisession ou Windows 10 session unique|Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5|
|Windows 7|Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence d’Accès Client (CAL) Services Bureau à distance avec Software Assurance|

Votre infrastructure doit contenir les éléments suivants pour prendre en charge Windows Virtual Desktop :

* Un annuaire [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Un annuaire Windows Server Active Directory synchronisé avec Azure Active Directory. Celui-ci peut être activé par le biais de :
  * Azure AD Connect
  * Services de domaine Azure AD
* Un abonnement Azure contenant un réseau virtuel qui contient ou est connecté à l’annuaire Windows Server Active Directory
  
Les machines virtuelles Azure que vous créez pour Windows Virtual Desktop doivent :

* être [standard jointes à un domaine](https://docs.microsoft.com/microsoft-desktop-optimization-pack/appv-v4/domain-joined-and-non-domain-joined-clients) ou [hybrides jointes à AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Les machines virtuelles ne peuvent pas être jointes à Azure AD.
* Exécuter l’une des images de système d’exploitation prises en charge suivantes :
  * Windows 10 Entreprise multisession
  * Windows Server 2016

>[!NOTE]
>Si vous avez besoin d’un abonnement Azure, vous pouvez demander un [essai gratuit d’un mois](https://azure.microsoft.com/free/). Si vous utilisez l’essai gratuit d’Azure, vous devez utiliser Azure AD Domain Services pour synchroniser votre annuaire Windows Server Active Directory avec Azure Active Directory.

Windows Virtual Desktop comprend les bureaux et applications Windows que fournissez aux utilisateurs ainsi que la solution de gestion, qui est hébergée en tant que service sur Azure par Microsoft. Pendant la préversion publique, les bureaux et applications peuvent être déployés sur des machines virtuelles dans n’importe quelle région Azure, et la solution de gestion et les données pour ces machines virtuelles résident aux États-Unis (région USA Est 2). Cela peut entraîner un transfert de données vers les États-Unis pendant que vous testez le service en préversion publique. Nous commencerons le scale out de la solution de gestion et de la localisation des données vers toutes les régions Azure dès la mise en disponibilité générale.

Pour bénéficier de performances optimales, vérifiez que votre réseau remplit les conditions suivantes :

* La latence aller-retour entre le réseau du client et la région Azure où les pools hôtes ont été déployés doit être inférieure à 150 ms
* Le trafic réseau peut circuler au-delà des frontières du pays quand des machines virtuelles qui hébergent des bureaux et des applications se connectent au service de gestion
* Pour optimiser les performances du réseau, nous vous recommandons de colocaliser les machines virtuelles de l’hôte de session dans la même région Azure que le service de gestion

## <a name="provide-feedback"></a>Fournir des commentaires

Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté. Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, vous devez créer un locataire. Pour en savoir plus sur la façon de créer un locataire, passez au tutoriel de création de locataire.

> [!div class="nextstepaction"]
> [Créer un locataire dans Windows Virtual Desktop Preview](tenant-setup-azure-active-directory.md)
