---
title: Qu’est-ce que Windows Virtual Desktop Preview ?  - Azure
description: Vue d’ensemble de Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/17/2019
ms.author: helohr
ms.openlocfilehash: 03105232cb92a65f8c38d6d755910739a3fc4720
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305046"
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

## <a name="introductory-video"></a>Vidéo d’introduction

Dans la vidéo suivante, Scott Manchester présente certaines des fonctionnalités Windows Virtual Desktop :

<br></br><iframe src="https://www.youtube-nocookie.com/embed/30dOLcZ4_9U" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

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

Nous prévoyons d’ajouter la prise en charge des systèmes d’exploitation suivants. Par conséquent, vérifiez que vous disposez des [licences appropriées](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour vos utilisateurs, en fonction du poste de travail et des applications que vous envisagez de déployer :

|OS|Licence obligatoire|
|---|---|
|Windows 10 Entreprise multisession ou Windows 10 Entreprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Entreprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence d’Accès Client (CAL) Services Bureau à distance avec Software Assurance|

Votre infrastructure doit contenir les éléments suivants pour prendre en charge Windows Virtual Desktop :

* Un annuaire [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Un annuaire Windows Server Active Directory synchronisé avec Azure Active Directory. Celui-ci peut être activé par le biais de :
  * Azure AD Connect
  * Services de domaine Azure AD
* Un abonnement Azure contenant un réseau virtuel qui contient ou est connecté à l’annuaire Windows Server Active Directory
  
Les machines virtuelles Azure que vous créez pour Windows Virtual Desktop doivent :

* être [standard jointes à un domaine](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) ou [hybrides jointes à AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Les machines virtuelles ne peuvent pas être jointes à Azure AD.
* Exécuter l'une des [images de système d'exploitation prises en charge](#supported-virtual-machine-os-image) suivantes.

>[!NOTE]
>Si vous avez besoin d’un abonnement Azure, vous pouvez demander un [essai gratuit d’un mois](https://azure.microsoft.com/free/). Si vous utilisez l’essai gratuit d’Azure, vous devez utiliser Azure AD Domain Services pour synchroniser votre annuaire Windows Server Active Directory avec Azure Active Directory.

Windows Virtual Desktop comprend les bureaux et applications Windows que fournissez aux utilisateurs ainsi que la solution de gestion, qui est hébergée en tant que service sur Azure par Microsoft. Pendant la préversion publique, les bureaux et applications peuvent être déployés sur des machines virtuelles dans n’importe quelle région Azure, et la solution de gestion et les données pour ces machines virtuelles résident aux États-Unis (région USA Est 2). Cela peut entraîner un transfert de données vers les États-Unis pendant que vous testez le service en préversion publique. Nous commencerons le scale out de la solution de gestion et de la localisation des données vers toutes les régions Azure dès la mise en disponibilité générale.

Pour bénéficier de performances optimales, vérifiez que votre réseau remplit les conditions suivantes :

* La latence aller-retour entre le réseau du client et la région Azure où les pools hôtes ont été déployés doit être inférieure à 150 ms
* Le trafic réseau peut circuler au-delà des frontières du pays ou de la région lorsque des machines virtuelles qui hébergent des bureaux et des applications se connectent au service de gestion
* Pour optimiser les performances du réseau, nous vous recommandons de colocaliser les machines virtuelles de l’hôte de session dans la même région Azure que le service de gestion

## <a name="supported-remote-desktop-clients"></a>Clients Bureau à distance pris en charge

Les clients Bureau à distance suivants prennent en charge Windows Virtual Desktop :

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-image"></a>Image de système d'exploitation de machine virtuelle prise en charge

Windows Virtual Desktop prend en charge les images de système d'exploitation suivantes :

* Windows 10 Entreprise multisession
* Windows Server 2016

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, vous devez créer un locataire. Pour en savoir plus sur la façon de créer un locataire, passez au tutoriel de création de locataire.

> [!div class="nextstepaction"]
> [Créer un locataire dans Windows Virtual Desktop Preview](tenant-setup-azure-active-directory.md)
