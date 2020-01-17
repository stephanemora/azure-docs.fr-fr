---
title: Présentation - Autorisation Azure AD Domain Services - Azure Files
description: Azure Files prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) avec Azure Active Directory (Azure AD) Domain Services. Vos machines virtuelles Windows jointes à un domaine peuvent alors accéder aux partages de fichiers Azure en utilisant les informations d’identification Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 93db726a2cac14109e542972ce851943b290962f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460288"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Vue d’ensemble de la prise en charge de l’authentification Azure Active Directory Domaine Service (Azure AD DS) d’Azure Files pour l’accès SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Pour savoir comment activer l’authentification Azure AD DS pour Azure Files, consultez [Activer l’authentification Azure Active Directory Domain service sur SMB pour Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossaire 
Vous devez comprendre certains termes clés relatifs à l’authentification Azure AD Domain Service sur SMB pour Azure Files :

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) est le service cloud Microsoft multilocataire de gestion des annuaires et des identités. Azure AD associe des services d’annuaires principaux, la gestion de l’accès aux applications et la protection des identités dans une même solution. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Services de domaine Azure AD**  
    Azure AD Domain Services fournit des services de domaine gérés, comme la jonction de domaine, les stratégies de groupe, le protocole LDAP et l’authentification Kerberos/NTLM. Ces services sont entièrement compatibles avec Windows Server Active Directory. Pour plus d’informations, consultez [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

-   **Contrôle d’accès en fonction du rôle (RBAC) Azure**  
    Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. À l’aide de RBAC, vous pouvez gérer l’accès aux ressources en accordant aux utilisateurs les autorisations minimales dont ils ont besoin pour effectuer leur travail. Pour plus d’informations sur RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) dans Azure ?](../../role-based-access-control/overview.md)

-   **Authentification Kerberos**

    Kerberos est un protocole d’authentification utilisé pour vérifier l’identité d’un utilisateur ou d’un hôte. Pour plus d’informations sur Kerberos, consultez [Vue d’ensemble de l’authentification Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocole SMB (Server Message Block)**  
    SMB est un protocole de partage de fichier réseau standard. SMB est aussi appelé Common Internet File System ou CIFS. Pour plus d’informations sur SMB, consultez [Vue d’ensemble du protocole SMB et du protocole CIFS de Microsoft](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Avantages de l’authentification Azure AD Domain Service
L’authentification Azure AD Domain Service pour Azure Files offre plusieurs avantages comparée à l’utilisation de l’authentification par clé partagée :

-   **Étendre au cloud avec Azure AD et Azure AD Domain Service l’expérience d’accès au partage de fichiers traditionnel basé sur l’identité**  
    Si vous envisagez une méthode « lift-and-shift » pour déplacer votre application vers le cloud en remplaçant les serveurs de fichiers classiques par Azure Files, votre application peut alors s’authentifier par le biais des informations d’identification Azure AD et accéder aux données de fichier. Azure Files prend en charge l’utilisation des informations d’identification Azure AD, pour l’accès à Azure Files via SMB, depuis les machines virtuelles Windows jointes au domaine Azure AD DS. Vous pouvez aussi choisir de synchroniser tous vos objets Active Directory locaux dans Azure AD pour conserver les noms d’utilisateur, les mots de passe et autres attributions de groupe.

-   **Appliquer un contrôle d’accès granulaire sur les partages de fichiers Azure**  
    Vous pouvez accorder des autorisations à une identité spécifique au niveau du partage, du répertoire ou du fichier. Par exemple, supposons que vous avez plusieurs équipes qui utilisent un partage de fichiers Azure pour la collaboration de projet. Vous pouvez accorder à toutes les équipes un accès aux répertoires non sensibles, et limiter l’accès aux répertoires contenant les données financières sensibles uniquement à l’équipe Finance. 

-   **Sauvegarder les ACL en même temps que les données**  
    Vous pouvez utiliser Azure Files pour sauvegarder vos partages de fichiers locaux existants. Azure Files conserve vos ACL avec vos données quand vous sauvegardez un partage de fichiers dans Azure Files sur SMB.

## <a name="how-it-works"></a>Fonctionnement
Azure Files utilise Azure AD Domain Services pour prendre en charge l’authentification Kerberos avec les informations d’identification Azure AD à partir de machines virtuelles jointes à un domaine. Pour pouvoir utiliser Azure AD avec Azure Files, vous devez d’abord activer Azure AD Domain Services et joindre le domaine des machines virtuelles à partir desquelles vous envisagez d’accéder aux données de fichier. Votre machine virtuelle jointe à un domaine doit résider dans le même réseau virtuel qu’Azure AD Domain Services. 

Quand une identité associée à une application s’exécutant sur une machine virtuelle tente d’accéder aux données dans Azure Files, la demande est envoyée à Azure AD Domain Services pour authentifier l’identité. Si l’authentification réussit, Azure AD Domain Services renvoie un jeton Kerberos. L’application envoie une demande qui inclut le jeton Kerberos, et Azure Files utilise ce jeton pour autoriser la demande. Azure Files reçoit seulement le jeton et ne conserve pas les informations d’identification Azure AD.

![Capture d’écran montrant le diagramme d’authentification Azure AD sur SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Activer l’authentification Azure AD Domain Service pour l’accès SMB
Vous pouvez activer l’authentification Azure AD Domain Service pour Azure Files sur vos comptes de stockage nouveaux et existants, créés après le 24 septembre 2018. 

Avant d’activer cette fonctionnalité, vérifiez qu’Azure AD Domain Services est déployé pour le locataire Azure AD principal auquel est associé votre compte de stockage. Si vous n’avez pas encore configuré Azure AD Domain Services, suivez les instructions pas à pas fournies dans [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Le déploiement d’Azure AD Domain Services prend généralement 10 à 15 minutes. Une fois Azure AD Domain Services déployé, vous pouvez activer l’authentification Azure AD sur SMB pour Azure Files. Pour plus d’informations, consultez [Activer l’authentification Azure Active Directory Domain Service sur SMB pour Azure Files](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurer les autorisations au niveau du partage pour Azure Files
Une fois l’authentification Azure AD Domain Service activée, vous pouvez configurer des rôles RBAC personnalisés pour les identités Azure AD et attribuer des droits d’accès à tous les partages de fichiers dans le compte de stockage.

Quand une application s’exécutant sur une machine virtuelle jointe à un domaine tente de monter un partage de fichiers Azure ou d’accéder à un répertoire ou un fichier, les informations d’identification Azure AD de l’application sont examinées pour vérifier qu’elles ont des autorisations sur le partage et des autorisations NTFS. Pour plus d’informations sur la configuration des autorisations au niveau du partage, consultez [Activer l’authentification Azure Active Directory Domain Service sur SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurer les autorisations au niveau du répertoire ou du fichier pour Azure Files 
Azure Files applique les autorisations de fichiers NTFS standard au niveau du répertoire et du fichier, y compris au niveau du répertoire racine. La configuration des autorisations au niveau du répertoire ou du fichier est prise en charge uniquement sur SMB. Montez le partage de fichiers cible à partir de votre machine virtuelle et configurez des autorisations à l’aide de l’Explorateur de fichier Windows ou de la commande Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Utiliser la clé de compte de stockage pour les autorisations de superutilisateur 
Un utilisateur avec la clé de compte de stockage peut accéder à Azure Files avec des autorisations de superutilisateur. Les autorisations de superutilisateur surpassent toutes les restrictions de contrôle d’accès configurées au niveau du partage avec RBAC et appliquées par Azure AD. Les autorisations de superutilisateur sont nécessaires pour monter un partage de fichiers Azure. 

> [!IMPORTANT]
> Dans le cadre des bonnes pratiques de sécurité, évitez de partager vos clés de compte de stockage et utilisez autant que possible les autorisations Azure AD.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Conserver les ACL de répertoire et de fichier pour l’importation de données dans des partages de fichiers Azure
Azure Files prend désormais en charge la conservation des ACL de répertoire ou de fichier quand vous copiez des données dans des partages de fichiers Azure. Vous pouvez copier les ACL dans un fichier ou un répertoire dans Azure Files. Par exemple, vous pouvez utiliser [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) avec l’indicateur `/copy:s` pour copier les données et les ACL dans un partage de fichiers Azure. La conservation des ACL est activée par défaut et il est inutile d’activer explicitement la fonctionnalité d’authentification Azure AD Domain Service sur votre compte de stockage. 

## <a name="pricing"></a>Tarifs
L’activation de l’authentification Azure AD sur SMB sur votre compte de stockage n’engendre aucuns frais de service supplémentaires. Pour plus d’informations sur les prix, consultez les pages [Tarifs Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) et [Tarifs Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Files et l’authentification Azure AD sur SMB, consultez ces ressources :

- [Présentation d’Azure Files](storage-files-introduction.md)
- [Activer l’authentification Azure Active Directory sur SMB pour Azure Files](storage-files-active-directory-enable.md)
- [FORUM AUX QUESTIONS](storage-files-faq.md)
