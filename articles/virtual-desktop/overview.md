---
title: Qu’est-ce que Windows Virtual Desktop ? - Azure
description: Présentation de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/27/2020
ms.author: helohr
ms.openlocfilehash: 6cb8362b2de1f8345f693b00df021afe84f2d5a5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471769"
---
# <a name="what-is-windows-virtual-desktop"></a>Qu’est-ce que Windows Virtual Desktop ? 

Windows Virtual Desktop est un service de virtualisation de bureau et d’application qui s’exécute dans le cloud.

Voici ce que vous pouvez faire quand vous exécutez Windows Virtual Desktop sur Azure :

* Configurer un déploiement Windows 10 multisession qui délivre une version complète de Windows 10 avec extensibilité
* Virtualiser Office 365 ProPlus et l’optimiser pour une exécution dans des scénarios virtuels multiutilisateurs
* Fournir des bureaux virtuels Windows 7 avec Mises à jour de sécurité étendues gratuites
* Tirer parti de vos applications et bureaux Windows Server et Services Bureau à distance existants sur n’importe quel ordinateur
* Virtualiser des bureaux et des applications
* Gérer des bureaux et des applications Windows 10, Windows Server et Windows 7 avec une expérience de gestion unifiée

## <a name="introductory-video"></a>Vidéo d’introduction

Découvrez Windows Virtual Desktop, pourquoi cette solution est unique et les nouveautés dans cette vidéo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Pour obtenir d’autres vidéos sur Windows Virtual Desktop, consultez [notre sélection](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Fonctionnalités clés

Avec Windows Virtual Desktop, vous pouvez configurer un environnement extensible et flexible :

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

## <a name="requirements"></a>Spécifications

Il existe quelques conditions à remplir avant de configurer Windows Virtual Desktop et de connecter vos utilisateurs à leurs bureaux et applications Windows.

Nous prévoyons d’ajouter la prise en charge des systèmes d’exploitation suivants. Par conséquent, vérifiez que vous disposez des [licences appropriées](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour vos utilisateurs, en fonction du poste de travail et des applications que vous envisagez de déployer :

|Système d''exploitation|Licence obligatoire|
|---|---|
|Windows 10 Entreprise multisession ou Windows 10 Entreprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Entreprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence d’Accès Client (CAL) des services Bureau à distance avec Software Assurance|

Votre infrastructure doit contenir les éléments suivants pour prendre en charge Windows Virtual Desktop :

* Un annuaire [Azure Active Directory](/azure/active-directory/)
* Un annuaire Windows Server Active Directory synchronisé avec Azure Active Directory. Pour le configurer, utilisez l’un des éléments suivants :
  * Azure AD Connect (pour les organisations hybrides)
  * Services de domaine Azure AD (pour les organisations cloud ou hybrides)
* Un abonnement Azure contenant un réseau virtuel qui contient ou est connecté à l’annuaire Windows Server Active Directory
  
Les machines virtuelles Azure que vous créez pour Windows Virtual Desktop doivent :

* être [standard jointes à un domaine](../active-directory-domain-services/active-directory-ds-comparison.md) ou [hybrides jointes à AD](../active-directory/devices/hybrid-azuread-join-plan.md). Les machines virtuelles ne peuvent pas être jointes à Azure AD.
* exécuter l’une des images de [système d’exploitation prises en charge](#supported-virtual-machine-os-images) suivantes.

>[!NOTE]
>Si vous avez besoin d’un abonnement Azure, vous pouvez demander un [essai gratuit d’un mois](https://azure.microsoft.com/free/). Si vous utilisez l’essai gratuit d’Azure, vous devez utiliser Azure AD Domain Services pour synchroniser votre annuaire Windows Server Active Directory avec Azure Active Directory.

Les machines virtuelles Azure que vous créez pour Windows Virtual Desktop doivent avoir accès aux URL suivantes :

|Adresse|Port sortant|Objectif|
|---|---|---|
|*.wvd.microsoft.com|Port TCP 443|Trafic de service|
|*.blob.core.windows.net|Port TCP 443|Agent, mises à jour de pile SXS et trafic de l’agent|
|*.core.windows.net|Port TCP 443|Trafic de l’agent|
|*.servicebus.windows.net|Port TCP 443|Trafic de l’agent|
|prod.warmpath.msftcloudes.com|Port TCP 443|Trafic de l’agent|
|catalogartifact.azureedge.net|Port TCP 443|Place de marché Azure|
|kms.core.windows.net|Port TCP 1688|Activation de Windows 10|

>[!IMPORTANT]
>Il est essentiel d’ouvrir ces URL pour un déploiement Windows Virtual Desktop fiable. Il n’est pas possible de bloquer l’accès à ces URL, car cela affecterait le fonctionnement du service. Ces URL correspondent seulement aux sites et aux ressources Windows Virtual Desktop, et n’incluent pas les URL d’autres services comme Azure Active Directory.

>[!NOTE]
>Actuellement, Windows Virtual Desktop ne dispose pas d’une liste de plages d’adresses IP que vous pouvez mettre en liste verte pour autoriser le trafic réseau. Pour le moment, seules certaines URL spécifiques peuvent être ajoutées à la liste verte.
>
>Vous devez utiliser le caractère générique (*) pour les URL impliquant du trafic de service. Si vous préférez ne pas utiliser * pour le trafic lié à l’agent, voici comment trouver les URL sans caractères génériques :
>
>1. Inscrivez vos machines virtuelles dans le pool d’hôtes Windows Virtual Desktop.
>2. Ouvrez **Observateur d’événements** et accédez à **Journaux Windows** > **Application** > **WVD-Agent**, puis recherchez l’ID d’événement 3702.
>3. Placez en liste verte les URL que vous trouvez sous l’ID d’événement 3702. Les URL sous l’ID d’événement 3702 sont spécifiques à la région. Vous devez répéter le processus de mise en liste verte avec les URL appropriées pour chaque région où vous voulez déployer vos machines virtuelles.

Windows Virtual Desktop comprend les bureaux et applications Windows que vous fournissez aux utilisateurs  ainsi que la solution de gestion, qui est hébergée en tant que service sur Azure par Microsoft. Les bureaux et applications peuvent être déployés sur des machines virtuelles dans n’importe quelle région Azure, et la solution de gestion et les données pour ces machines virtuelles résident aux États-Unis. Cela peut entraîner le transfert des données vers les États-Unis.

Pour bénéficier de performances optimales, vérifiez que votre réseau remplit les conditions suivantes :

* La latence aller-retour entre le réseau du client et la région Azure où les pools hôtes ont été déployés doit être inférieure à 150 ms
* Le trafic réseau peut circuler au-delà des frontières du pays ou de la région lorsque des machines virtuelles qui hébergent des bureaux et des applications se connectent au service de gestion
* Pour optimiser les performances du réseau, nous vous recommandons de colocaliser les machines virtuelles de l’hôte de session dans la même région Azure que le service de gestion

## <a name="supported-remote-desktop-clients"></a>Clients Bureau à distance pris en charge

Les clients Bureau à distance suivants prennent en charge Windows Virtual Desktop :

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (préversion)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Images de système d’exploitation de machine virtuelle prises en charge

Windows Virtual Desktop prend en charge les images de système d'exploitation x64 suivantes :

* Windows 10 Entreprise multisession, version 1809 ou ultérieure
* Windows 10 Entreprise, version 1809 ou ultérieure
* Windows 7 Entreprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop ne prend pas en charge les images de système d’exploitation x86 (32 bits), Windows 10 Entreprise N ou Windows 10 Entreprise KN. Windows 7 ne prend pas non plus en charge les solutions de profil VHD ou VHDX hébergées sur le stockage Azure managé en raison d’une limitation de la taille de secteur.

Les options d’automatisation et de déploiement qui sont disponibles dépendent du système d’exploitation et de la version que vous choisissez, comme indiqué dans le tableau suivant : 

|Système d’exploitation|Galerie d’images Azure|Déploiement manuel d’une machine virtuelle|Intégration du modèle Azure Resource Manager|Provisionner des pools d’hôtes sur la Place de marché Azure|Mises à jour de l’agent Windows Virtual Desktop|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multisession, version 1903|Oui|Oui|Oui|Oui|Automatique|
|Windows 10 multisession, version 1809|Oui|Oui|Non|Non|Automatique|
|Windows 10 Entreprise, version 1903|Oui|Oui|Oui|Oui|Automatique|
|Windows 10 Entreprise, version 1809|Oui|Oui|Non|Non|Automatique|
|Windows 7 Entreprise|Oui|Oui|Non|Non|Manuel|
|Windows Server 2019|Oui|Oui|Non|Non|Automatique|
|Windows Server 2016|Oui|Oui|Oui|Oui|Automatique|
|Windows Server 2012 R2|Oui|Oui|Non|Non|Automatique|

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, vous devez créer un locataire. Pour en savoir plus sur la façon de créer un locataire, passez au tutoriel de création de locataire.

> [!div class="nextstepaction"]
> [Créer un locataire dans Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
