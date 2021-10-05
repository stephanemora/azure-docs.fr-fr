---
title: Déployer des machines virtuelles jointes Azure AD dans Azure Virtual Desktop - Azure
description: Comment configurer et déployer des machines virtuelles jointes Azure AD dans Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: e6325c6511c6df9c3f3c021bc24a3f66b2e56c0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207732"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Déployer des machines virtuelles jointes Azure AD dans Azure Virtual Desktop

Cet article vous guide tout au long du processus de déploiement et d’accès aux machines virtuelles jointes à Azure Active Directory dans Azure Virtual Desktop. Les machines virtuelles jointes à Azure AD suppriment la nécessité de devoir contrôler la connexion entre la machine virtuelle et un contrôleur de domaine Active Directory (DC) local ou virtualisé, ou de déployer Azure AD Domain services (Azure AD DS). Dans certains cas, il évite de recourir à un contrôleur de domaine, ce qui simplifie le déploiement et la gestion de l’environnement. Ces machines virtuelles peuvent également être inscrites automatiquement dans Intune pour faciliter la gestion.

> [!NOTE]
> Les machines virtuelles jointes Azure AD ne sont actuellement prises en charge que dans le Cloud commercial Azure.

## <a name="supported-configurations"></a>Configurations prises en charge

Les configurations suivantes sont actuellement prises en charge avec les machines virtuelles jointes à Azure AD :

- Postes de travail personnels avec des profils utilisateur locaux.
- Postes de travail mis en pool utilisés comme zone de renvoi. Dans cette configuration, les utilisateurs accèdent d’abord à la machine virtuelle Azure Virtual Desktop avant de se connecter à un autre ordinateur sur le réseau. Les utilisateurs ne doivent pas enregistrer de données sur la machine virtuelle.
- Des applications ou des bureaux mis en pool dans lesquels les utilisateurs n’ont pas besoin d’enregistrer des données sur la machine virtuelle. Par exemple, pour les applications qui enregistrent des données en ligne ou se connectent à une base de données distante.

Les comptes d’utilisateurs peuvent être des utilisateurs du cloud uniquement ou hybrides du même locataire Azure AD.

## <a name="known-limitations"></a>Limitations connues

Les limitations connues suivantes peuvent avoir un impact sur l’accès à vos ressources locales ou Active Directory jointes à un domaine, et doivent être prises en compte pour décider si des machines virtuelles jointes Azure AD sont appropriées pour votre environnement. Nous recommandons actuellement des machines virtuelles jointes Azure AD pour les scénarios où les utilisateurs ont uniquement besoin d’accéder à des ressources cloud ou à une authentification basée sur Azure AD.

- Azure Virtual Desktop (classique) ne prend pas en charge les machines virtuelles jointes à Azure AD.
- Les machines virtuelles jointes à Azure AD ne prennent actuellement pas en charge les utilisateurs externes.
- Les machines virtuelles jointes Azure AD prennent en charge les profils utilisateur locaux pour l’instant.
- Les machines virtuelles jointes Azure AD ne peuvent pas accéder aux partages de fichiers Azure Files pour FSLogix ou l’attachement d’application MSIX. Vous aurez besoin de l’authentification Kerberos pour accéder à l’une de ces fonctionnalités.
- Le client du Store Windows ne prend actuellement pas en charge les machines virtuelles jointes à Azure AD.
- Azure Virtual Desktop ne prend pas actuellement en charge l’authentification unique pour les machines virtuelles jointes à Azure AD.

## <a name="deploy-azure-ad-joined-vms"></a>Déployer des machines virtuelles jointes Azure AD

Vous pouvez déployer des machines virtuelles jointes à Azure AD directement à partir du Portail Azure lors de la [création d’un pool hôte](create-host-pools-azure-marketplace.md) ou de l’[extension d’un pool hôte existant](expand-existing-host-pool.md). Dans l’onglet Machines virtuelles, indiquez si vous souhaitez joindre la machine virtuelle à Active Directory ou à Azure Active Directory. En sélectionnant **Azure Active Directory** vous avez la possibilité d’**inscrire la machine virtuelle à Intune** automatiquement afin de pouvoir gérer facilement les machines virtuelles [Windows 10 Entreprise](/mem/intune/fundamentals/windows-virtual-desktop) et [Windows 10 Enterprise multi-session](/mem/intune/fundamentals/windows-virtual-desktop-multi-session). N’oubliez pas que l’option Azure Active Directory joint des machines virtuelles au même locataire Azure AD que l’abonnement dans lequel vous vous trouvez.

> [!NOTE]
> - Les pools hôtes doivent contenir uniquement des machines virtuelles du même type de jonction de domaine. Par exemple, les machines virtuelles jointes à Active Directory doivent uniquement être associées à d’autres machines virtuelles AD, et vice versa.
> - Les machines virtuelles du pool hôte doivent être de type Windows 10 session unique ou multisession, version 2004 ou ultérieure.

### <a name="assign-user-access-to-host-pools"></a>Affecter l’accès utilisateur aux pools d’ordinateurs hôtes

Une fois que vous avez créé votre pool d’ordinateurs hôtes, vous devez affecter aux utilisateurs l’accès leur permettant d’accéder à leurs ressources. Pour accorder l’accès aux ressources, ajoutez chaque utilisateur au groupe d’applications. Suivez les instructions dans [Gérer des groupes d’applications](manage-app-groups.md) pour affecter l’accès utilisateur aux applications et aux ordinateurs de bureau. Nous vous recommandons d’utiliser des groupes d’utilisateurs plutôt que des utilisateurs individuels dans la mesure du possible.

Pour les machines virtuelles jointes à Azure AD, vous devez effectuer deux tâches supplémentaires en plus de la configuration requise pour les déploiements basés sur des Services de domaine Active Directory ou Azure Active Directory :  

- Accordez aux utilisateurs le rôle **Connexion de l’utilisateur à la machine virtuelle** pour qu’ils puissent se connecter aux machines virtuelles.
- Affectez aux administrateurs qui ont besoin de privilèges d’administrateur local le rôle **Connexion administrateur à l’ordinateur virtuel** .

Pour accorder aux utilisateurs l’accès à des machines virtuelles jointes à Azure AD, vous devez [configurer des attributions de rôles pour la machine virtuelle](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm). Vous pouvez affecter la **connexion utilisateur à la machine virtuelle** ou la **connexion administrateur à la machine virtuelle** sur les machines virtuelles, le groupe de ressources contenant les machines virtuelles ou l’abonnement. Nous vous recommandons d’attribuer le rôle de connexion de l’utilisateur de la machine virtuelle au groupe d’utilisateurs que vous avez utilisé pour le groupe d’applications au niveau du groupe de ressources afin qu’il s’applique à toutes les machines virtuelles du pool hôte.

## <a name="access-azure-ad-joined-vms"></a>Accéder aux machines virtuelles jointes à Azure AD

Cette section explique comment accéder à des machines virtuelles jointes à Azure AD à partir de différents clients Azure Virtual Desktop.

### <a name="connect-using-the-windows-desktop-client"></a>Se connecter à l’aide du client Windows Desktop

La configuration par défaut prend en charge les connexions à partir de Windows 10 à l’aide du [client Windows Desktop](user-documentation/connect-windows-7-10.md). Vous pouvez utiliser vos informations d’identification, votre carte à puce, l’[approbation de certificat Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) ou l’[approbation de clé Windows Hello Entreprise avec certificats](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs) pour vous connecter à l’hôte de session. Toutefois, pour accéder à l’hôte de session, votre ordinateur local doit remplir l’une des conditions suivantes :

- L’ordinateur local est joint à Azure AD au même locataire Azure AD que l’hôte de session
- L’ordinateur local est joint à Azure AD hybride au même locataire Azure AD que l’hôte de session
- L’ordinateur local exécute Windows 10, version 2004 et ultérieures, et il est inscrit à Azure AD auprès du même locataire Azure AD que l’hôte de session.

Pour activer l’accès à partir d’appareils Windows non joints à Azure AD, ajoutez **targetisaadjoined:i:1** comme [propriété RDP personnalisée](customize-rdp-properties.md) au pool hôte. Ces connexions sont limitées à la saisie des informations d’identification de nom d’utilisateur et de mot de passe lors de la connexion à l’hôte de session.

### <a name="connect-using-the-other-clients"></a>Se connecter à l’aide des autres clients

Pour accéder aux machines virtuelles jointes à Azure AD à l’aide des clients web, Android, macOS et iOS, vous devez ajouter **targetisaadjoined:i:1** comme [propriété RDP personnalisée](customize-rdp-properties.md) au pool hôte. Ces connexions sont limitées à la saisie des informations d’identification de nom d’utilisateur et de mot de passe lors de la connexion à l’hôte de session.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Activation de l’authentification multifacteur pour les machines virtuelles jointes à Azure AD

Vous pouvez activer l’[authentification multifacteur](set-up-mfa.md) pour les machines virtuelles jointes à Azure AD en définissant une stratégie d’accès conditionnel sur l’application Azure Virtual Desktop. Pour que les connexions fonctionnent, vous devez [désactiver l’authentification multifacteur par utilisateur héritée](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required). Si vous ne souhaitez pas limiter la connexion à des méthodes d’authentification forte comme Windows Hello Entreprise, vous devez également [exclure l’application de connexion de machine virtuelle Azure Windows](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required) de votre stratégie d’accès conditionnel.

## <a name="user-profiles"></a>Profils utilisateur

Azure Virtual Desktop ne prend actuellement en charge que les profils locaux pour les machines virtuelles jointes à Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé quelques machines virtuelles jointes à Azure AD, vous pouvez vous connecter à un client Azure Virtual Desktop pris en charge pour le tester dans une session utilisateur. Si vous voulez apprendre à vous connecter à une session avec vos nouvelles informations d’identification, consultez les articles suivants :

- [Se connecter avec le client Windows Desktop](user-documentation/connect-windows-7-10.md)
- [Se connecter avec le client web](user-documentation/connect-web.md)
- [Résoudre les problèmes de connexion aux machines virtuelles jointes à Azure AD](troubleshoot-azure-ad-connections.md)
