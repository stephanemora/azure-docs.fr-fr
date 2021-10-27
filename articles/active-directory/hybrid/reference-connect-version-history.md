---
title: 'Azure AD Connect : Historique de publication des versions | Microsoft Docs'
description: Cet article répertorie toutes les versions d’Azure AD Connect et d’Azure AD Sync.
author: billmath
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/21/2021
ms.subservice: hybrid
ms.author: rodejo
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: daaf76866b42196808d88d56725d4edbd54543d5
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064663"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect : Historique de publication des versions
L’équipe Azure Active Directory (Azure AD) met régulièrement à jour Azure AD Connect avec de nouvelles fonctions et fonctionnalités. Tous les ajouts ne sont pas applicables à toutes les configurations.

Cet article est conçu pour vous aider à conserver la trace des versions publiées, et à comprendre les modifications apportées par la dernière version.

Ce tableau présente la liste des rubriques connexes :

Rubrique |  Détails
--------- | --------- |
Étapes de mise à niveau à partir d’Azure AD Connect | Différentes méthodes pour [effectuer une mise à niveau à partir d’une version précédente vers la dernière](how-to-upgrade-previous-version.md) version Azure AD Connect.
Autorisations requises | Pour plus d’informations sur les autorisations requises afin d’appliquer une mise à jour, consultez [Comptes et autorisations](reference-connect-accounts-permissions.md#upgrade).


>[!IMPORTANT]
> **Le 31 août 2022, toutes les versions 1.x d’Azure Active Directory (Azure AD) Connect seront supprimées, car elles incluent des composants SQL Server 2012 qui ne seront plus pris en charge.** Effectuez une mise à niveau vers la version la plus récente d’Azure AD Connect (version 2.x) avant cette date ou [évaluez et basculez vers Azure AD cloud sync](../cloud-sync/what-is-cloud-sync.md).
> 
> Vous devez vous assurer que vous exécutez une version récente d'Azure AD Connect pour bénéficier d'une expérience de support optimale. 
> 
> Si vous exécutez une version hors service d’Azure AD Connect, elle peut s’arrêter subitement de fonctionner et vous risquez de ne pas bénéficier des correctifs de sécurité, améliorations des performances, outils de diagnostic et de résolution des problèmes et améliorations des services les plus récents. Et si vous avez besoin d’aide, nous ne serons peut-être pas en mesure de vous fournir le niveau de service dont votre organisation a besoin.
> 
> Consultez cet article pour en savoir plus sur [Azure Active Directory Connect v 2.0](whatis-azure-ad-connect-v2.md), sur les modifications apportées à la version 2.0 et sur la manière dont cette modification vous affecte.
>
> Veuillez consulter [cet article](./how-to-upgrade-previous-version.md) pour en savoir plus sur la mise à niveau d'Azure AD Connect vers la version la plus récente.
>
> Pour obtenir des informations sur l'historique des versions mises hors service, consultez [Archive de l'historique des versions d'Azure AD Connect](reference-connect-version-history-archive.md).

>[!NOTE]
>La publication d’une nouvelle version d’Azure AD Connect est un processus qui nécessite plusieurs étapes de contrôle qualité pour garantir la fonctionnalité de l’opération du service. Pendant que nous effectuons ce processus, le numéro d’une nouvelle version ainsi que l’état de la version sont mis à jour pour refléter l’état le plus récent.
La mise à niveau automatique ne concernera pas toutes les versions d’Azure AD Connect. L’état de la version indique si une version est disponible en mise à niveau automatique ou en téléchargement uniquement. Si la mise à niveau automatique a été activée sur votre serveur Azure AD Connect, celui-ci sera automatiquement mis à niveau vers la dernière version d’Azure AD Connect qui est disponible en mise à niveau automatique. Certaines configurations Azure AD Connect seulement sont éligibles à la mise à niveau automatique. 

>Pour clarifier l’utilisation de la mise à niveau automatique, il est prévu de vous transmettre toutes les mises à jour importantes et tous les correctifs critiques. Il ne s’agit pas nécessairement de la dernière version, car toutes les versions ne nécessitent pas/incluent un correctif d’un problème de sécurité critique (juste un exemple parmi tant d’autres). Les problèmes critiques sont en général résolus avec une nouvelle version fournie via la Mise à niveau automatique. S’il n’y a pas de problèmes de ce type, aucune mise à jour n’est effectuée à l’aide de la Mise à niveau automatique et, en général, vous devriez être satisfait si vous utilisez la dernière version de la Mise à niveau automatique.
Toutefois, si vous souhaitez bénéficier des dernières fonctionnalités et mises à jour, le meilleur moyen de savoir si elles existent est de consulter cette page et de les installer comme bon vous semble. 

>Veuillez suivre ce lien pour en savoir plus sur la [mise à niveau automatique](how-to-connect-install-automatic-upgrade.md)


## <a name="download-links"></a>Liens de téléchargement
 - Si vous utilisez Windows Server version 2016 ou plus récente, vous devez utiliser Azure AD Connect v 2.0. Vous pouvez télécharger la dernière version d’Azure AD Connect 2.0 en cliquant sur [ce lien](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
 - Si vous utilisez toujours une version antérieure de Windows Server, vous devez utiliser Azure AD Connect version 1.6. Vous pouvez télécharger la dernière version d’Azure AD Connect V1 en cliquant sur [ce lien](https://www.microsoft.com/download/details.aspx?id=103336). 
 - Nous n’appliquons que les modifications critiques apportées aux versions V1, et vous pouvez ne pas trouver certaines des fonctionnalités et des correctifs pour v2 dans les versions v1. Vous devez donc effectuer une mise à niveau vers la version v2 dès que possible.

## <a name="16160"></a>1.6.16.0
>[!NOTE] 
>Il s’agit d’une version de mise à jour de correctif d’Azure AD Connect. Cette version est destinée à être utilisée par les clients qui exécutent une version antérieure du Serveur Windows et qui ne peuvent pas mettre à niveau leur serveur vers le Serveur Windows version 2016 ou plus récente. Vous ne pouvez pas utiliser cette version pour mettre à jour un serveur Azure AD Connect 2.0. Cette version n’est pas prise en charge sur Windows Server 2016 ou une version ultérieure. Cette version comprend des composants SQL Server 2012 et sera mise hors service le 31 août 2022. Vous devrez mettre à niveau le système d’exploitation de votre serveur et la version d’AADConnect avant cette date.
>Nous allons commencer à mettre à niveau automatiquement les locataires éligibles lorsque cette version sera disponible au téléchargement, l’exécution de la mise à niveau automatique prendra quelques semaines.

### <a name="release-status"></a>État de la version
13/10/2021 : publié pour le téléchargement et la mise à niveau automatique.

### <a name="bug-fixes"></a>Résolution des bogues
- Nous avons corrigé un bogue à cause duquel le processus de mise à niveau automatique tentait de mettre à niveau les serveurs AADConnect qui exécutent les versions antérieures 2008 ou 2008 R2 du système d’exploitation Windows et échouait. Ces versions de Windows Server ne sont plus prises en charge. Dans cette version, nous ne tentons la mise à niveau automatique que sur les machines qui exécutent Windows Server 2012 ou une version ultérieure.
- Nous avons corrigé un problème où, dans certaines conditions, miisserver se plantait en raison d’une exception de violation d’accès.

## <a name="20280"></a>2.0.28.0

>[!NOTE] 
> Il s’agit d’une version de mise à jour de maintenance d’Azure AD Connect. Cette version requiert Windows Server 2016 ou une version plus récente.

### <a name="release-status"></a>État de la version
30/09/2021 : Publiée pour téléchargement uniquement, non disponible pour la mise à niveau automatique.

### <a name="bug-fixes"></a>Résolution des bogues
 - Sur la page Autorisations de réécriture de groupe de l’Assistant, nous avons supprimé un bouton de téléchargement pour un script PowerShell et modifié le texte de la page de l’Assistant de manière à inclure un lien En savoir plus, qui renvoie vers un article en ligne où le script PowerShell est disponible.

 - Nous avons résolu un bogue dans lequel l’Assistant bloquait de manière incorrecte l’installation lorsque la version .NET sur le serveur était supérieure à 4,6, en raison de clés de Registre manquantes. Ces clés de Registre ne sont pas obligatoires et ne doivent bloquer l’installation que si elles sont délibérément définies sur false.

- Nous avons résolu un bogue dans lequel une erreur était levée en cas de détection d’objets fantômes pendant l’initialisation d’une étape de synchronisation. Cela bloquait l’étape de synchronisation ou supprimait les objets temporaires. Les objets fantômes sont maintenant ignorés.
Remarque : un objet fantôme est un espace réservé pour un objet qui n’est pas encore présent ou qui n’a pas encore été vu, par exemple si un objet source a une référence à un objet cible qui n’est pas là, nous créons l’objet cible en tant que fantôme.

### <a name="functional-changes"></a>Modifications fonctionnelles

 - Une modification a été apportée pour permettre à un utilisateur de désélectionner des objets et des attributs dans la liste d’inclusion, même s’ils sont en cours d’utilisation. Au lieu de bloquer cela, nous fournissons maintenant un avertissement. 

## <a name="16142"></a>1.6.14.2
>[!NOTE] 
>Il s’agit d’une version de mise à jour de correctif d’Azure AD Connect. Cette version est destinée à être utilisée par les clients qui exécutent une version antérieure du Serveur Windows et qui ne peuvent pas mettre à niveau leur serveur vers le Serveur Windows version 2016 ou plus récente. Vous ne pouvez pas utiliser cette version pour mettre à jour un serveur Azure AD Connect 2.0.
>Nous allons commencer à mettre à niveau automatiquement les locataires éligibles lorsque cette version sera disponible au téléchargement, l’exécution de la mise à niveau automatique prendra quelques semaines.

### <a name="release-status"></a>État de la version
21/09/2021 : publié pour le téléchargement et la mise à niveau automatique.

### <a name="functional-changes"></a>Modifications fonctionnelles
 - nous avons ajouté les dernières versions des connecteurs MIM (1.1.1610.0). Pour plus d’informations, consultez [la page historique des versions des connecteurs MIM](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)
 - Nous avons ajouté une option de configuration pour désactiver la fonctionnalité de correspondance souple dans Azure AD Connect. Nous conseillons aux clients de désactiver la correspondance souple, sauf s’ils en ont besoin pour prendre en charge des comptes cloud uniquement. Cet [article](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) montre comment désactiver la correspondance souple.

### <a name="bug-fixes"></a>Résolution des bogues
 - Nous avons résolu un bogue dans lequel les paramètres DesktopSSO n’étaient pas conservés après la mise à niveau à partir d’une version antérieure.
 - Nous avons résolu un bogue qui provoquait l'\* échec des applets de commande d’autorisation Set-ADSync.

## <a name="20251"></a>2.0.25.1

>[!NOTE] 
> Il s’agit d’une version de mise à jour de correctif de Azure AD Connect. Cette version requiert Windows Server 2016 ou une version plus récente et résout un problème de sécurité qui est présent dans la version 2.0 d’Azure AD Connect, ainsi que d’autres correctifs de bogues.

### <a name="release-status"></a>État de la version
14/09/2021 : Publiée pour téléchargement uniquement, indisponible pour la mise à niveau automatique.

### <a name="bug-fixes"></a>Résolution des bogues

 - Nous avons résolu un problème de sécurité où un chemin d’accès sans guillemets était utilisé pour pointer vers le service Azure AD Connect. Ce chemin d’accès est désormais un chemin d’accès entre guillemets.
 - Résolution d’un problème de configuration de l’importation avec l’écriture différée activée lors de l’utilisation du compte de connecteur AD existant.
 - Nous avons résolu un problème dans Set-ADSyncExchangeHybridPermissions et d’autres applets de commande associées, qui étaient rompues à partir de 1.6 en raison d’un type d’héritage non valide.
 - L’applet de commande que nous avons publiée dans une version précédente pour définir la version TLS comportait un problème lorsqu’elle remplaçait les clés, détruisant les valeurs qu’elles contiennent. Nous avons résolu cela en créant uniquement une nouvelle clé si elle n’existe pas déjà. Un avertissement est également ajouté pour permettre aux utilisateurs de savoir que les modifications du registre TLS ne sont pas exclusives à Azure AD Connect et peuvent avoir un impact sur d’autres applications sur le même serveur.
 - Nous avons ajouté une vérification pour appliquer la mise à niveau automatique de v 2.0 pour exiger Windows Server 2016 ou une version plus récente.
 - Nous avons ajouté l’autorisation « Réplication des modifications de répertoire » dans l’applet de commande Set-ADSyncBasicReadPermissions.
 - Nous avons apporté une modification pour empêcher l’utilisation conjointe de UseExistingDatabase et de la configuration d’importation, car ceux-ci peuvent contenir des paramètres de configuration en conflit.
 - Nous avons apporté une modification pour permettre à un utilisateur ayant le rôle d’administrateur d’application de modifier la configuration du service de proxy d’application.
 - Nous avons supprimé l’étiquette « (préversion) » des étiquettes des paramètres de Import/Export : cette fonctionnalité est généralement disponible depuis un moment déjà.
 - Nous modifions certaines étiquettes qui font toujours référence à « Administrateur d’entreprise » : nous utilisons maintenant le nom de rôle « Administrateur général ».
 - Nous avons créé de nouvelles applets de commande PowerShell Kerberos AAD « \* -AADKerberosServer » pour ajouter une règle de transformation des revendications au principal du service AAD.

### <a name="functional-changes"></a>Modifications fonctionnelles
 - nous avons ajouté les dernières versions des connecteurs MIM (1.1.1610.0). Pour plus d’informations, consultez [la page historique des versions des connecteurs MIM](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)
 - Nous avons ajouté une option de configuration pour désactiver la fonctionnalité de correspondance souple dans Azure AD Connect. Nous conseillons aux clients de désactiver la correspondance souple, sauf s’ils en ont besoin pour prendre en charge des comptes cloud uniquement. Cet [article](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) montre comment désactiver la correspondance souple.

## <a name="20100"></a>2.0.10.0

### <a name="release-status"></a>État de la version
19/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique

>[!NOTE] 
>Il s’agit d’une version de mise à jour de correctif de Azure AD Connect. Cette version requiert Windows Server 2016 ou une version plus récente. Ce correctif logiciel résout un problème présent dans la version 2.0 ainsi que dans Azure AD Connect version 1.6. Si vous exécutez Azure AD Connect sur un serveur Windows plus ancien, vous devez installer la build [1.6.13.0](#16130) à la place.

### <a name="release-status"></a>État de la version
19/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique

### <a name="known-issues"></a>Problèmes connus
 - Dans certaines circonstances, le programme d’installation de cette version affiche une erreur mentionnant que TLS 1.2 n’est pas activé et arrête l’installation. Cela est dû à une erreur dans le code qui vérifie le paramètre de Registre pour TLS 1.2. Nous corrigerons cela dans une version future. Les clients qui rencontrent ce problème doivent suivre les instructions relatives à l’activation de TLS 1.2 qui se trouvent dans l’article «[application de TLS 1.2 pour Azure AD Connect](reference-connect-tls-enforcement.md)».

### <a name="bug-fixes"></a>Résolution des bogues

 - Nous avons résolu un bogue où, lorsqu’un domaine est renommé, la synchronisation du hachage de mot de passe échoue avec une erreur indiquant « un cast spécifié n’est pas valide » dans le journal des événements. Il s’agit d’une régression par rapport aux versions antérieures.

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>Il s’agit d’une version de mise à jour de correctif d’Azure AD Connect. Cette version est destinée aux clients qui exécutent Azure AD Connect sur un serveur doté de Windows Server 2012 ou 2012 R2.

19/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique

### <a name="bug-fixes"></a>Résolution des bogues

 - Nous avons résolu un bogue où, lorsqu’un domaine est renommé, la synchronisation du hachage de mot de passe échoue avec une erreur indiquant « un cast spécifié n’est pas valide » dans le journal des événements. Il s’agit d’une régression par rapport aux versions antérieures.

### <a name="functional-changes"></a>Modifications fonctionnelles
Elle ne fait l’objet d’aucune modification fonctionnelle.

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>État de la version
17/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique.

### <a name="bug-fixes"></a>Résolution des bogues
>[!NOTE] 
>Il s’agit d’une version de mise à jour de correctif d’Azure AD Connect. Cette version requiert Windows Server version 2016 ou plus récente. Cette version corrige un problème présent dans la version 2.0.8.0, mais absent d’Azure AD Connect version 1.6

 - Nous avons résolu un bogue où, lors de la synchronisation d’un grand nombre de transactions de synchronisation de hachage de mot de passe, la longueur de l’entrée du journal des événements dépasse la longueur maximale autorisée pour une entrée d’événement de synchronisation de hachage de mot de passe. Nous allons maintenant fractionner l’entrée de journal en plusieurs entrées, car elle est trop longue.

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>Il s’agit d’une version de mise à jour de sécurité d’Azure AD Connect. Cette version requiert Windows Server version 2016 ou plus récente. Si vous utilisez une version antérieure de Windows Server, utilisez la [version 1.6.11.3](#16113).
>Cette version corrige une vulnérabilité, comme décrit dans[cette CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949). Pour plus d’informations au sujet de cette vulnérabilité, reportez-vous à cette CVE.
>Vous pouvez télécharger la dernière version d’Azure AD Connect 2.0 en cliquant sur [ce lien](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

### <a name="release-status"></a>État de la version
10/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique 

### <a name="functional-changes"></a>Modifications fonctionnelles
Elle ne fait l’objet d’aucune modification fonctionnelle.

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>Il s’agit de la version de mise à jour de sécurité d’Azure AD Connect. Cette version est destinée à être utilisée par les clients qui exécutent une version antérieure de Windows Server et qui ne peuvent pas mettre à niveau leur serveur vers Windows Server version 2016 ou plus récente. Vous ne pouvez pas utiliser cette version pour mettre à jour un serveur Azure AD Connect 2.0.
>Cette version corrige une vulnérabilité, comme décrit dans[cette CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949). Pour plus d’informations au sujet de cette vulnérabilité, reportez-vous à cette CVE.
>Vous pouvez télécharger la dernière version d’Azure AD Connect 1.6 en cliquant sur [ce lien](https://www.microsoft.com/download/details.aspx?id=103336).

### <a name="release-status"></a>État de la version
10/08/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique

### <a name="functional-changes"></a>Modifications fonctionnelles
Cette version ne fait l’objet d’aucune modification fonctionnelle.

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>Il s’agit d’une version majeure d’Azure AD Connect. Consultez l’[article Azure Active Directory V2.0](whatis-azure-ad-connect-v2.md) pour plus d’informations.

### <a name="release-status"></a>État de la version
20/07/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique
### <a name="functional-changes"></a>Modifications fonctionnelles
 - Nous avons mis à niveau les composants de Base de données locale de SQL Server vers SQL 2019. 
 - Cette version requiert Windows Server version 2016 ou plus récente en raison des exigences de SQL Server 2019. Notez qu’une mise à niveau sur place de Windows server sur un serveur Azure AD Connect n’est pas prise en charge. vous devrez donc peut-être utiliser une [migration swing](how-to-upgrade-previous-version.md#swing-migration).
 -    Dans cette version, nous appliquons l’utilisation de TLS 1.2. Si vous avez activé votre serveur Windows pour TLS 1.2, AADConnect utilise ce protocole. Si TLS 1.2 n’est pas activé sur le serveur, un message d’erreur s’affiche lorsque vous tentez d’installer AADConnect, et l’installation ne se poursuit pas tant que vous n’avez pas activé TLS 1.2. Notez que vous pouvez utiliser les nouvelles cmdlets « Set-ADSyncToolsTls12 » pour activer TLS 1.2 sur votre serveur.
 -    Avec cette version, vous pouvez recourir à un utilisateur avec le rôle d’utilisateur « administrateur des identités hybrides » pour l’authentification lorsque vous installez Azure AD Connect. Vous n’avez plus besoin du rôle d’administrateur général pour cela.
 - Nous avons mis à niveau la bibliothèque d’exécution Visual C++ vers la version 14 comme condition préalable pour SQL Server 2019    
 -    Cette version utilise la bibliothèque MSAL pour l’authentification et nous avons supprimé l’ancienne bibliothèque ADAL, qui sera supprimée en 2022.    
 -    Nous n’appliquons plus d’autorisations sur AdminSDHolders, en suivant les conseils de sécurité Windows. Nous avons remplacé le paramètre « SkipAdminSdHolders » par « IncludeAdminSdHolders » dans le module ADSyncConfig.psm1.
 -    Les mots de passe sont à présent réévalués lorsqu’un mot de passe arrivé à expiration est « non expiré », que le mot de passe lui-même soit modifié ou non. Si, pour un utilisateur, le mot de passe est défini sur « Doit modifier le mot de passe à la prochaine ouverture de session » et que cet indicateur est désactivé (ce qui signifie que le mot de passe n’est pas arrivé à expiration), l’état « non expiré » et le hachage de mot de passe sont synchronisés vers Azure AD. Lorsque l’utilisateur tente de se connecter à Azure AD, il peut entrer le mot de passe non expiré.
Pour synchroniser un mot de passe arrivé à expiration entre Active Directory et Azure Active Directory, utilisez la fonctionnalité [Synchronisation des mots de passe temporaires](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon) dans Azure AD Connect. Notez que vous devez activer la réécriture du mot de passe pour utiliser cette fonctionnalité afin que le mot de passe utilisé pour les mises à jour soit également écrit dans Active Directory.
 - Nous avons ajouté deux nouvelles cmdlets au module ADSyncTools pour activer ou récupérer les paramètres TLS 1.2 du serveur Windows. 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
Vous pouvez utiliser ces cmdlets pour récupérer l’état d’activation TLS 1.2 ou le définir selon vos besoins. Notez que TLS 1.2 doit être activé sur le serveur pour que l’installation ou AADConnect aboutisse.

 -    Nous avons réorganisé ADSyncTools avec plusieurs cmdlets nouvelles et améliorées. L’[article ADSyncTools](reference-connect-adsynctools.md) contient des informations complémentaires sur ces cmdlets. 
  Les cmdlets suivantes ont été ajoutées ou mises à jour 
    - Clear-ADSyncToolsMsDsConsistencyGuid    
    - ConvertFrom-ADSyncToolsAadDistinguishedName    
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName    
    - ConvertTo-ADSyncToolsCloudAnchor    
    - ConvertTo-ADSyncToolsImmutableID    
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects    
    - Export-ADSyncToolsRunHistory    
    - Get-ADSyncToolsAadObject    
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects    
    - Import-ADSyncToolsRunHistory    
    - Remove-ADSyncToolsAadObject    
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport    
    - Trace-ADSyncToolsLdapQuery    
-    Nous utilisons maintenant le point de terminaison v2 pour l’importation et l’exportation, et nous avons résolu le problème dans la cmdlet Get-ADSyncAADConnectorExportApiVersion. Pour plus d’informations sur le point de terminaison v2, consultez l’[article sur le point de terminaison Azure AD Connect Sync V2](how-to-connect-sync-endpoint-api-v2.md).
-    Nous avons ajouté les nouvelles propriétés utilisateur suivantes pour la synchronisation de l’installation locale d’AD vers Azure AD    
    - employeeType    
    - employeeHireDate    
-    Cette version nécessite l’installation de PowerShell version 5.0 ou ultérieure sur le serveur Windows. Notez que cette version fait partie de Windows Server version 2016 et ultérieures.    
-    Nous avons augmenté les limites d’appartenance de synchronisation de groupe à 250 000 avec le nouveau point de terminaison v2.
-    Nous avons mis à jour le connecteur LDAP générique et le connecteur SQL générique vers les versions les plus récentes. Découvrez-en plus sur ces connecteurs ici :
    - [Documentation de référence sur le connecteur LDAP générique](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [Documentation de référence sur le connecteur SQL générique](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-    Dans le centre d’administration M365, nous indiquons maintenant la version du client AADConnect à chaque fois qu’une activité d’exportation est effectuée vers Azure AD. Cela garantit que le centre d’administration M365 dispose toujours de la version du client AADConnect la plus récente et qu’il peut détecter si vous utilisez une version obsolète.

### <a name="bug-fixes"></a>Résolution des bogues
- Nous avons résolu un bogue d’accessibilité dans lequel le lecteur d’écran annonce un rôle incorrect du lien « En savoir plus ».
-    Nous avons résolu un bogue dans lequel les règles de synchronisation avec des valeurs de priorité élevées (par exemple, 387163089) provoquent l’échec d’une mise à niveau. Nous avons mis à jour la procédure stockée « mms_UpdateSyncRulePrecedence » afin de convertir le numéro de priorité en valeur entière avant d’incrémenter la valeur.
-    Nous avons résolu un bogue dans lequel les autorisations d’écriture différée de groupe ne sont pas définies sur le compte de synchronisation si une configuration d’écriture différée de groupe est importée. Nous définissons maintenant les autorisations de réécriture du groupe si l’écriture différée de groupe est activée sur la configuration importée.
-    Nous avons mis à jour la version de l’agent Azure AD Connect Health sur 3.1.110.0 pour résoudre un échec d’installation.
-    Nous constatons un problème avec les attributs autres que ceux définis par défaut des configurations exportées dans lequel les attributs d’extension d’annuaire sont configurés. Lors de l’importation de ces configurations vers un nouveau serveur/une nouvelle installation, la liste d’inclusion des attributs est remplacée par l’étape de configuration de l’extension d’annuaire, de sorte qu’après l’importation, seuls les attributs par défaut et l’extension d’annuaire sont sélectionnés dans le gestionnaire de services de synchronisation (les attributs par défaut ne sont pas inclus dans l’installation, de sorte que l’utilisateur doit les réactiver manuellement à partir du gestionnaire de services de synchronisation s’il veut que les règles de synchronisation importées fonctionnent). Nous actualisons maintenant le connecteur AAD avant de configurer l’extension d’annuaire pour conserver les attributs existants de la liste d’inclusion d’attributs.
-    Nous avons résolu un problème d’accessibilité dans lequel l’épaisseur de police de l’en-tête de page est définie sur « Light ». L’épaisseur de police est maintenant définie sur « Bold » pour le titre de la page, qui s’applique à l’en-tête de toutes les pages.
-    La fonction Get-AdObject dans ADSyncSingleObjectSync.ps1 a été renommée en Get-AdDirectoryObject pour éviter toute ambiguïté avec la cmdlet Active Directory.
-    La fonction SQL « mms_CheckSynchronizationRuleHasUniquePrecedence » autorise la priorité des doublons sur les règles de synchronisation sortante sur des connecteurs différents. Nous avons supprimé la condition qui autorise la priorité des règles en double.
-    Nous avons résolu un bogue dans lequel la cmdlet de synchronisation d’objets unique échoue si les données de l’attribut sont NULL, c’est-à-dire lors de l’exportation de l’opération de suppression    
-    Nous avons résolu un bogue à l’origine de l’échec de l’installation, car le service de démarrage ADSync ne peut pas être démarré. Nous ajoutons maintenant le compte de service de synchronisation au groupe d’utilisateurs intégré local avant de démarrer le service de démarrage.
-    Nous avons résolu un problème d’accessibilité dans lequel l’onglet actif sur l’assistant AAD Connect n’indique pas la couleur correcte dans le thème Contraste élevé. Le code couleur sélectionné a été remplacé en raison d’une condition manquante dans la configuration du code couleur normal.
-    Nous avons résolu un problème dans lequel les utilisateurs étaient autorisés à désélectionner les objets et les attributs utilisés dans les règles de synchronisation à l’aide de l’interface utilisateur et de PowerShell. Nous affichons maintenant un message d’erreur convivial si vous essayez de désélectionner un attribut ou un objet utilisé dans une règle de synchronisation.
-    Nous avons apporté des mises à jour à la « migration du code des paramètres » pour vérifier et résoudre le problème de compatibilité descendante lorsque le script est exécuté sur une version antérieure d’Azure AD Connect.    
-    Correction d’un bogue dans lequel, quand PHS tente de rechercher un objet incomplet, il n’utilise pas le même algorithme pour résoudre le contrôleur de domaine qu’il a utilisé initialement pour extraire les mots de passe. En particulier, il ignore les informations de contrôleur de domaine avec affinité. La recherche d’objets incomplète doit utiliser la même logique pour localiser le contrôleur de groupe dans les deux instances.
-    Nous avons corrigé un bogue dans lequel AADConnect ne pouvait pas lire les objets Proxy d’application à l’aide de Microsoft Graph en raison d’un problème d’autorisations avec l’appel direct de Microsoft Graph basé sur l’identifiant client AAD Connect. Pour résoudre ce problème, nous avons supprimé la dépendance à Microsoft Graph et nous utilisons plutôt AAD PowerShell pour gérer les objets d’application App Proxy.
-    Nous avons supprimé la limite du membre d’écriture différée de la règle de synchronisation « Out to AD-Group SOAInAAD Exchange »    
-    Nous avons résolu un bogue dans lequel, lors de la modification des autorisations de compte de connecteur, si un objet se trouve dans une étendue qui n’a pas changé depuis la dernière importation différentielle, une importation différentielle ne permet pas de l’importer. Nous affichons désormais des alertes d’avertissement pour l’utilisateur impliqué dans le problème.
-    Nous avons résolu un problème d’accessibilité dans lequel le lecteur d’écran ne lit pas la position des cases d’option. Nous avons ajouté un texte positionnel au champ de texte d’accessibilité de la case d’option.
-    Nous avons mis à jour l’offre groupée de l’agent d’authentification Pass-Thru. L’ancienne offre groupée ne comportait pas d’URL de réponse correcte pour l’application native du plan d’assurance santé (HIP) dans US Gov.    
-    Nous avons corrigé un bogue dans lequel une exception « stopped-extension-dll-exception » se produit lors de l’exportation du connecteur AAD après une installation propre d’AADConnect version 1.6.X.X, qui utilise par défaut l’API DirSyncWebServices V2 et qui s’appuie sur une base de données existante.    Auparavant, l’installation de la version 2 de l’exportation de paramètres était effectuée à des fins de mise à niveau uniquement. Désormais, elle peut également être effectuée dans le cadre d’une nouvelle installation.
-    Le module « ADSyncPrep.psm1 » n’est plus utilisé et est supprimé de l’installation.

### <a name="known-issues"></a>Problèmes connus
-    L’assistant AADConnect indique que l’option « Importer les paramètres de synchronisation » est en « préversion », alors que cette fonctionnalité est en disponibilité générale.
-    Certains connecteurs Active Directory peuvent être installés dans un ordre différent lorsque vous utilisez la sortie du script de migration des paramètres pour installer le produit. 
-    La page Options de connexion utilisateur mentionne « Administrateur d’entreprise » dans l’assistant Azure AD Connect. Ce terme n’est plus utilisé et doit être remplacé par « Administrateur général ».
-    L’option « Exporter les paramètres » ne fonctionne pas lorsque l’option de connexion a été configurée pour utiliser PingFederate.
-    Alors qu’Azure AD Connect peut maintenant être déployé en utilisant le rôle d’administrateur d’identités hybrides, la configuration de la réinitialisation du mot de passe en libre-service nécessitera toujours un utilisateur avec le rôle d’administrateur général.
-    Lors de l’importation de la configuration d’AADConnect durant le déploiement en vue de se connecter avec un locataire différent de la configuration originale AADConnect, les attributs de l’extension d’annuaire ne sont pas configurés correctement.

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> L’API de point de terminaison Azure AD Connect Sync V2 est désormais disponible dans les environnements Azure suivants :
> - Azure Commercial
> - Le cloud Azure Chine
> - Cloud Azure US Government
> - Cette version ne sera pas disponible dans le cloud Azure Allemagne.

### <a name="release-status"></a>État de la version
31/03/2021 : Publiée pour téléchargement uniquement, indisponible pour mise à niveau automatique

### <a name="bug-fixes"></a>Résolution des bogues
- Cette version corrige un bogue dans la version 1.6.2.4 où, après la mise à niveau vers cette version, la fonctionnalité Azure AD Connect Health n’a pas été inscrite correctement et n’a pas été exécutée. Les clients qui ont déployé la version 1.6.2.4 sont invités à mettre à jour leur serveur Azure AD Connect avec cette version, ce qui permet d’inscrire correctement la fonctionnalité Health. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Mise à jour pour le 30 mars 2021 : nous avons découvert un problème dans cette version. Après l’installation de cette version, les services Health ne sont pas inscrits. Nous vous recommandons de ne pas installer cette version. Nous publierons un correctif prochainement.
> Si vous avez déjà installé cette version, vous pouvez inscrire manuellement les services Health à l’aide de l’applet de commande, comme indiqué dans [cet article](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Cette version sera mise à disposition en téléchargement uniquement.
> - La mise à niveau vers cette version nécessite une synchronisation complète en raison des modifications apportées à la règle de synchronisation.
> - Cette version utilise par défaut le serveur AADConnect sur le nouveau point de terminaison V2. Notez que ce point de terminaison n’est pas pris en charge dans le cloud Allemagne. Si vous devez déployer cette version dans cet environnement, vous devez suivre [ces instructions](./how-to-connect-sync-endpoint-api-v2.md#rollback) pour revenir au point de terminaison V1. Sinon, des erreurs de synchronisation seront générées.

### <a name="release-status"></a>État de la version
19/03/2021 : Publiée pour téléchargement, indisponible pour mise à niveau automatique

### <a name="functional-changes"></a>Modifications fonctionnelles

 - Mise à jour des règles de synchronisation par défaut pour limiter l’appartenance à des groupes d’écriture différée à 50 000 membres.
   - Ajout de nouvelles règles de synchronisation par défaut pour limiter le nombre d’appartenances à l’écriture différée de groupe (Out to AD - Group Writeback Member Limit) et la synchronisation de groupe pour des groupes Azure Active Directory (Out to AAD - Group Writeup Member Limit).
   - Ajout d’un attribut de membre à la règle « Out to AD - Group SOAInAAD - Exchange » pour limiter les membres de groupes à écriture différée à 50 000
 - Mise à jour des règles de synchronisation pour prendre en charge l’écriture différée des groupes V2 : si la règle « In from AAD - Group SOAInAAD » est clonée et AADConnect est mis à niveau.
     - La règle mise à jour est désactivée par défaut et la valeur de targetWritebackType est donc « null ».
     - AADConnect effectue l’écriture différée de tous les groupes cloud (y compris les groupes de sécurité Azure Active Directory activés pour l’écriture différée) en tant que groupes de distribution.
   \- Si la règle « Out to AD - Group SOAInAAD » est clonée et AADConnect est mis à niveau.
     - La règle mise à jour est désactivée par défaut. Toutefois, une nouvelle règle de synchronisation « Out to AD - Group SOAInAAD - Exchange », qui est ajoutée, est activée.
     - En fonction de la priorité de la règle de synchronisation personnalisée clonée, AADConnect transmettra les attributs Mail et Exchange.
     - Si la règle de synchronisation personnalisée clonée ne transmet pas certains attributs Mail et Exchange, la nouvelle règle de synchronisation Exchange les ajoutera.
     - Notez que l’écriture différée de groupe v2 est actuellement en préversion privée et non disponible publiquement.
 - Ajout de la prise en charge de la [synchronisation sélective du hachage de mot de passe](./how-to-connect-selective-password-hash-synchronization.md)
 - Ajout de la nouvelle [applet de commande de synchronisation d’objets uniques](./how-to-connect-single-object-sync.md). Utilisez cette applet de commande pour résoudre les problèmes de configuration de la synchronisation d’Azure AD Connect. 
 -  Azure AD Connect prend désormais en charge le rôle d’administrateur d’identité hybride pour la configuration du service.
 - Mise à jour de l’agent AADConnectHealth vers 3.1.83.0
 - Nouvelle version du [module PowerShell ADSyncTools](./reference-connect-adsynctools.md), qui comporte plusieurs applets de commande nouvelles ou améliorées. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Mise à jour de la journalisation des erreurs pour les échecs d’acquisition de jetons.
 - Mise à jour des liens « En savoir plus » sur la page de configuration pour fournir plus de détails sur les informations liées.
 - Suppression de la colonne Explicite de la page de recherche CS dans l’ancienne interface utilisateur de synchronisation
 - Une interface utilisateur supplémentaire a été ajoutée au processus d’écriture différée de groupe pour inviter l’utilisateur à fournir des informations d’identification ou à configurer ses propres autorisations à l’aide du module ADSyncConfig si les informations d’identification n’ont pas déjà été fournies dans une étape antérieure.
 - Création automatique de MSA pour le compte de service ADSync sur un contrôleur de domaine. 
 - Ajout de la possibilité de définir et d’accéder au groupe de fonctionnalités Azure Active Directory DirSync d’écriture différée V2 dans les applets de commande existantes :
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Ajout de 2 applets de commande pour lire la version de l’API AWS
    - Get-ADSyncAADConnectorImportApiVersion : pour accéder à l’importation de la version de l’API AWS
    - Get-ADSyncAADConnectorExportApiVersion : pour accéder à l’exportation de la version de l’API AWS

 - Les modifications apportées aux règles de synchronisation sont désormais suivies pour aider à résoudre les problèmes de modification dans le service. L’applet de commande « Get-ADSyncRuleAudit » récupère les modifications suivies.
 - Mise à jour de la cmdlet Add-ADSyncADDSConnectorAccount dans le [module PowerShell ADSyncConfig](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module) pour permettre à un utilisateur du groupe ADSyncAdmin de modifier le compte AD DS Connector. 

### <a name="bug-fixes"></a>Résolution des bogues
 - Mise à jour de la couleur de premier plan pour répondre aux exigences de luminosité sur un arrière-plan blanc. Ajout de conditions supplémentaires pour l’arborescence de navigation afin de définir la couleur du texte de premier plan sur le blanc lorsqu’une page désactivée est sélectionnée pour répondre aux exigences de luminosité.
 - Augmentation de la granularité de l’applet de commande Set-ADSyncPasswordHashSyncPermissions : mise à jour du script des autorisations PHS (Set-ADSyncPasswordHashSyncPermissions) pour inclure un paramètre facultatif « ADobjectDN ». 
 - Correctif de bogue d’accessibilité. Le lecteur d’écran désigne maintenant l’élément UX qui contient la liste des forêts par « **Liste des forêts** » au lieu de **Liste de la liste des forêts** ».
 - Mise à jour de la sortie du lecteur d’écran pour certains éléments dans l’Assistant Azure AD Connect. Mise à jour de la couleur de pointage du bouton pour répondre aux exigences de contraste. Mise à jour de la couleur du titre de Synchronization Service Manager pour répondre aux exigences de contraste.
 - Correction d’un problème lors de l’installation d’AADConnect à partir de la configuration exportée avec des attributs d’extension personnalisés : ajout d’une condition pour ignorer la vérification des attributs d’extension dans le schéma cible lors de l’application de la règle de synchronisation.
 - Les autorisations appropriées sont ajoutées à l’installation si la fonctionnalité d’écriture différée de groupe est activée.
 - Correction de la priorité de la règle de synchronisation par défaut en double lors de l’importation
 - Résolution d’un problème qui provoquait une erreur de mise en lots pendant l’importation différentielle de l’API V2 quand un objet en conflit était réparé dans le portail Health.
 - Résolution d’un problème dans le moteur de synchronisation qui entraînait un état de liaison incohérente des objets CS
 - Ajout de compteurs d’importation à la sortie de Get-ADSyncConnectorStatistics.
 - Correction du problème de désélection de domaine inaccessible (sélectionné précédemment) dans certains cas dans l’Assistant pass2.
 - L’importation et l’exportation de stratégies modifiées échouent si la règle personnalisée a une priorité en double 
 - Correction d’un bogue dans la logique de sélection du domaine.
 - Corrige un problème lié au build 1.5.18.0 si vous utilisez mS-DS-ConsistencyGuid comme ancre source et que vous avez cloné la règle In from AD - Group Join.
 - Les nouvelles installations d’AADConnect utilisent le seuil de suppression d’exportation stocké dans le cloud, si disponible et si un autre différent n’a pas été transmis.
 - Correction du problème où AADConnect ne lit pas les modifications apportées à AD displayName des appareils joints hybrides

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>État de la version
29/07/2020 : publiée pour téléchargement

### <a name="functional-changes"></a>Modifications fonctionnelles
Il s’agit d’une version du correctif de bogue. Elle ne fait l’objet d’aucune modification fonctionnelle.

### <a name="fixed-issues"></a>Problèmes résolus

- Résolution d’un problème empêchant l’administrateur d’activer l’« authentification unique transparente » si le compte d’ordinateur AZUREADSSOACC est déjà présent dans « Active Directory ».
- Résolution d’un problème qui provoquait une erreur de mise en lots pendant l’importation différentielle de l’API V2 quand un objet en conflit était réparé dans le portail Health.
- Résolution d’un problème de configuration d’importation/exportation provoquant l’importation d’une règle personnalisée désactivée comme activée.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>État de la version
10/07/2020 : publiée pour téléchargement

### <a name="functional-changes"></a>Modifications fonctionnelles
Cette version comprend une préversion publique de la fonctionnalité permettant d’exporter la configuration d’un serveur de Azure AD Connect existant dans un fichier .JSON qui peut ensuite être utilisé lors de l’installation d’un nouveau serveur de Azure AD Connect pour créer une copie du serveur d’origine.

Une description détaillée de cette nouvelle fonctionnalité est disponible dans [cet article](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Problèmes résolus
- Correction d’un bogue où de faux avertissement pendant la mise à niveau concernant la taille de la base de données locale sur les builds localisés.
- Correction d’un bogue où il y aurait une fausse erreur dans les événements de l’application pour l’échange de nom de compte/nom de domaine.
- Correction d’une erreur qui entraînait l’échec de l’installation de Azure AD Connect sur un contrôleur de domaine, avec l’erreur « membre introuvable ».


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>État de la version
07/05/2020 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus
Ce correctif corrige un problème où des domaines non sélectionnés étaient incorrectement sélectionnés à partir de l’interface utilisateur de l’Assistant si seuls des conteneurs petits-enfants étaient sélectionnés.


>[!NOTE]
>Cette version comprend la nouvelle API de point de terminaison v2 pour la synchronisation d’Azure AD Connect.  Ce nouveau point de terminaison v2 est actuellement en préversion publique.  Cette version ou une version ultérieure est requise pour utiliser la nouvelle API de point de terminaison v2.  Toutefois, l’installation seule de cette version n’active pas le point de terminaison v2. Vous continuerez d’utiliser le point de terminaison v1, sauf si vous activez le point de terminaison v2.  Vous devez suivre les étapes décrites dans [API de point de terminaison v2 pour la synchronisation d’Azure AD Connect (préversion publique)](how-to-connect-sync-endpoint-api-v2.md) pour l’activer et vous abonner à la préversion publique.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>État de la version
23/04/2020 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus
Ce correctif corrige un problème introduit dans la version 1.5.20.0, dans lequel un administrateur de locataire avec MFA ne pouvait pas activer DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>État de la version
20/04/2020 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus
Ce correctif logiciel résout un problème dans la build 1.5.20.0 si vous avez cloné la règle **In from AD - Group Join** et que vous n’avez pas cloné la règle **In from AD - Group Common**.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>État de la version
04/09/2020 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus
- Cette build de correctif logiciel résout un problème lié au build 1.5.18.0 si la fonctionnalité de filtrage de groupe est activée et utilise mS-DS-ConsistencyGuid comme point d’ancrage source.
- Correction d’un problème dans le module PowerShell ADSyncConfig, où l’appel de la commande DSACLS utilisée dans toutes les cmdlets Set-ADSync* provoquait l’une des erreurs suivantes :
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Si vous avez cloné la règle de synchronisation **In from AD - Group Join**, que vous n’avez pas cloné la règle de synchronisation **In from AD - Group Common** et que vous envisagez une mise à niveau, effectuez les étapes suivantes dans le cadre de la mise à niveau :
> 1. Pendant la mise à niveau, désactivez l’option **Démarrez le processus de synchronisation une fois la configuration terminée**.
> 2. Modifiez la règle de synchronisation de jointure clonée et ajoutez les deux transformations suivantes :
>     - Définissez le flux direct `objectGUID` sur `sourceAnchorBinary`.
>     - Définissez le flux d’expression `ConvertToBase64([objectGUID])` sur `sourceAnchor`.     
> 3. Activez le planificateur à l’aide de `Set-ADSyncScheduler -SyncCycleEnabled $true`.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>État de la version
02/04/2020 : publiée pour téléchargement

### <a name="functional-changes-adsyncautoupgrade"></a>Modifications fonctionnelles ADSyncAutoUpgrade 

- Ajout de la prise en charge de la fonctionnalité mS-DS-ConsistencyGuid pour les objets groupe. Cela vous permet de déplacer des groupes entre les forêts et de reconnecter des groupes dans AD à Azure AD lorsque le paramètre objectID du groupe AD a changé, par exemple lorsqu’un serveur AD est reconstruit après un désastre. Pour plus d’informations, consultez [Déplacement de groupes entre des forêts](how-to-connect-migrate-groups.md).
- L’attribut mS-DS-ConsistencyGuid est défini automatiquement sur tous les groupes synchronisés et vous n’avez rien à faire pour activer cette fonctionnalité. 
- Suppression de l’applet de commande Get-ADSyncRunProfile, car elle n’est plus utilisée. 
- Modification de l’avertissement qui s’affiche lorsque vous tentez d’utiliser un compte d’administrateur d’entreprise ou d’administrateur de domaine pour le compte de connecteur AD DS pour fournir davantage de contexte. 
- Ajout d’une nouvelle applet de commande pour supprimer des objets de l’espace de connecteur. L’ancien outil CSDelete.exe est supprimé et il est remplacé par la nouvelle applet de commande Remove-ADSyncCSObject. L’applet de commande Remove-ADSyncCSObject prend un objet CsObject en entrée. Cet objet peut être récupéré à l’aide de l’applet de commande Get-ADSyncCSObject.

>[!NOTE]
>L’ancien outil CSDelete.exe a été supprimé et remplacé par la nouvelle applet de commande Remove-ADSyncCSObject. 

### <a name="fixed-issues"></a>Problèmes résolus

- Correction d’un bogue dans le sélecteur de forêt/UO d’écriture différée de groupe lors de la réexécution de l’Assistant Azure AD Connect après la désactivation de la fonctionnalité. 
- Introduction d’une nouvelle page d’erreur qui s’affiche si les valeurs de Registre DCOM requises sont manquantes avec un nouveau lien d’aide. Les informations sont également écrites dans des fichiers journaux. 
- Résolution d’un problème lié à la création du compte de synchronisation Azure Active Directory, à cause duquel l’activation des extensions d’annuaire ou de PHS peut échouer parce que le compte n’a pas été propagé sur tous les réplicas de service avant la tentative d’utilisation. 
- Correction d’un bogue dans l’utilitaire de compression des erreurs de synchronisation qui ne gérait pas correctement les caractères de substitution. 
- Correction d’un bogue dans la mise à niveau automatique qui a laissé le serveur dans l’état suspendu du planificateur. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>État de la version
9/12/2019 : Publication pour le téléchargement. Non disponible par le biais de la mise à niveau automatique.
### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
- Nous avons mis à jour la synchronisation du hachage de mot de passe pour Azure AD Domain Services afin de prendre en compte le remplissage dans les hachages Kerberos.  Cela permet d’améliorer les performances pendant la synchronisation du mot de passe d’Azure AD avec Azure AD Domain Services.
- Nous avons ajouté la prise en charge de sessions fiables entre l’agent d’authentification et Service Bus.
- Nous avons ajouté un cache DNS pour les connexions WebSocket entre l’agent d’authentification et les services cloud.
- Nous avons ajouté la possibilité de cibler un agent spécifique à partir du cloud pour tester la connectivité de l’agent.

### <a name="fixed-issues"></a>Problèmes résolus
- La version 1.4.18.0 comportait un bogue dans lequel la cmdlet PowerShell concernant DSSO utilisait les informations d'identification Windows de connexion au lieu des informations d'identification d'administrateur fournies lors de l'exécution de PowerShell. Par conséquent, il n’était pas possible d’activer DSSO dans plusieurs forêts par le biais de l’interface utilisateur Azure AD Connect. 
- Correctif permettant d’activer DSSO simultanément dans toutes les forêts par le biais de l’interface utilisateur Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>État de la version
08/11/2019 : publiée pour téléchargement. Non disponible par le biais de la mise à niveau automatique.

>[!IMPORTANT]
>En raison d’une modification de schéma interne dans cette version d’Azure AD Connect, si vous gérez les paramètres de configuration d’une relation d’approbation AD FS à l’aide de MSOnline PowerShell, vous devez mettre à jour votre module MSOnline PowerShell vers la version 1.1.183.57 ou ultérieure

### <a name="fixed-issues"></a>Problèmes résolus

Cette version résout un problème avec les appareils Azure AD Hybride joints existants. Cette version contient une nouvelle règle de synchronisation d’appareil qui corrige ce problème.
Notez que cette modification de règle peut entraîner la suppression d’appareils obsolètes d’Azure AD. Cela n’est pas préoccupant car Azure AD n’utilise pas ces objets d’appareil durant l’autorisation d’accès conditionnel. Pour certains clients, le nombre d’appareils qui seront supprimés par le biais de cette modification de règle peut dépasser le seuil de suppression. Si vous constatez que les suppressions d’objets d’appareil dans Azure AD dépassent le seuil de suppression des exportations, il est conseillé d’autoriser la poursuite de ces suppressions. [Guide pratique pour autoriser le déroulement des suppressions quand elles dépassent le seuil de suppression](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>État de la version
28/09/2019 : Mise en production pour la mise à niveau automatique afin de sélectionner des locataires. Non disponible pour téléchargement.

Cette version corrige un bogue qui avait pour effet que certains serveurs étaient mis à niveau automatiquement vers la version 1.4.18.0 et rencontraient des problèmes en lien avec la réinitialisation de mot de passe en libre-service (SSPR) et la réécriture du mot de passe.

### <a name="fixed-issues"></a>Problèmes résolus

Dans certaines circonstances, les serveurs mis à niveau automatiquement vers la version 1.4.18.0 ne réactivaient pas la réinitialisation de mot de passe en libre-service et la réécriture du mot de passe une fois la mise à niveau terminée. Cette mise en production de la mise à niveau automatique corrige ce problème et réactive la réinitialisation de mot de passe en libre-service et la réécriture du mot de passe.

Nous avons résolu un bogue dans l’utilitaire de compression des erreurs de synchronisation qui ne gérait pas correctement les caractères de substitution.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Nous sommes en train de faire des recherches sur un incident rencontrés par certains clients avec des appareils joints à Azure AD Hybride existants après avoir effectué la mise à niveau vers cette version d’Azure AD Connect. Nous conseillons aux clients qui ont déployé Azure AD Hybride de différer la mise à niveau vers cette version jusqu’à ce que la cause racine de ce problème soit entièrement comprise et atténuée. Des informations supplémentaires seront fournies dès que possible.

>[!IMPORTANT]
>Avec cette version d’Azure AD Connect, certains clients peuvent voir une partie ou l’ensemble de leurs appareils Windows disparaître d’Azure AD. Cela n’est pas préoccupant car Azure AD n’utilise pas ces identités d’appareil durant l’autorisation d’accès conditionnel. Pour plus d’informations, voir [Comprendre la disparition des appareils Azure AD Connect 1.4.xx.x](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)


### <a name="release-status"></a>État de la version
25/9/2019 : Publiée pour la mise à niveau automatique uniquement.

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
- De nouveaux outils de résolution des problèmes permettent de corriger les scénarios « utilisateur non synchronisé », « groupe non synchronisé » ou « membre de groupe non synchronisé ».
- Ajout d’une prise en charge des clouds nationaux dans le script de résolution des problèmes d’Azure AD Connect.
- Les clients doivent être informés que les points de terminaison WMI dépréciés pour MIIS_Service sont désormais supprimés. Toutes les opérations WMI doivent à présent être effectuées par le biais des applets de commande PS.
- Amélioration de la sécurité en réinitialisant la délégation contrainte sur l’objet AZUREADSSOACC.
- Lors de l’ajout ou de la modification d’une règle de synchronisation, si des attributs sont utilisés dans la règle et qu’ils se trouvent dans le schéma du connecteur, mais ne sont pas ajoutés au connecteur, ces attributs sont automatiquement ajoutés au connecteur. Il en va de même pour le type d’objet affecté par la règle. Si quelque chose est ajouté au connecteur, le connecteur sera marqué pour une importation complète au cours du prochain cycle de synchronisation.
- L’utilisation d’un administrateur d’entreprise ou de domaine en tant que compte de connecteur n’est plus prise en charge dans les nouveaux déploiements d’Azure AD Connect. Cette version n’aura pas d’impact sur les déploiements en cours d’Azure AD Connect à l’aide d’un administrateur d’entreprise ou de domaine en tant que compte de connecteur.
- Dans le gestionnaire de synchronisation, une synchronisation complète est exécutée lors de la création, de la modification ou de la suppression d’une règle. Une fenêtre contextuelle s’affiche pour toute modification de règle, notifiant l’utilisateur que l’importation complète ou la synchronisation totale va être exécutée.
- Ajout d’étapes d’atténuation pour les erreurs de mot de passe à la page Connecteurs > Propriétés > Connectivité.
- Ajout, dans la page des propriétés du connecteur, d’un avertissement de dépréciation du gestionnaire du service de synchronisation. Cet avertissement prévient l’utilisateur que des modifications doivent être apportées via l’Assistant Azure AD Connect.
- Ajout d’une nouvelle erreur pour les problèmes liés à la stratégie de mot de passe d’un utilisateur.
- Prévention d’une erreur de configuration du filtrage de groupe par les filtres de domaine et d’UO. Le filtrage de groupe affiche une erreur lorsque le domaine ou l’unité d’organisation du groupe entré est déjà filtré et empêche l’utilisateur de poursuivre tant que le problème n’est pas résolu.
- Les utilisateurs ne peuvent plus créer de connecteur pour Active Directory Domain Services ou Windows Azure Active Directory dans l’interface utilisateur Synchronization Service Manager.
- Résolution du problème d’accessibilité des contrôles d’interface utilisateur personnalisés dans Synchronization Service Manager.
- Activation de six tâches de gestion de la fédération pour toutes les méthodes de connexion dans Azure AD Connect.  (Auparavant, seule la tâche « Mettre à jour le certificat TLS/SSL AD FS » était disponible pour l'ensemble des connexions.)
- Ajout d’un avertissement lors de la modification de la méthode de connexion, qui passe de la fédération à la synchronisation de hachage de mot de passe (PHS) ou à l’authentification directe (PTA), convertissant tous les utilisateurs et domaines Azure AD en authentification managée.
- Suppression des certificats de signature de jeton dans la tâche « Réinitialiser Azure AD et l’approbation AD FS » et ajout d’une sous-tâche distincte pour mettre à jour ces certificats.
- Ajout d'une nouvelle tâche de gestion de fédération appelée « Gérer les certificats » qui comporte des sous-tâches permettant de mettre à jour les certificats de signature de jeton ou les certificats TLS de la batterie de serveurs AD FS.
- Ajout d’une nouvelle sous-tâche de gestion de la fédération appelée « Spécifier le serveur principal », qui permet aux administrateurs de préciser un nouveau serveur principal pour la batterie de serveurs AD FS.
- Ajout d’une nouvelle tâche de gestion de la fédération appelée « Gérer les serveurs », qui comporte des sous-tâches permettant de déployer un serveur AD FS, un serveur proxy d’application web et de spécifier le serveur principal.
- Ajout d’une nouvelle tâche de gestion de la fédération appelée « Afficher la configuration de la fédération », qui affiche les paramètres AD FS actuels.  (En raison de cet ajout, les paramètres AD FS ont été supprimés de la page « Vérification de votre solution ».)

### <a name="fixed-issues"></a>Problèmes résolus
- Résolution du problème d’erreur de synchronisation dans le scénario suivant lequel un objet utilisateur qui prend le contrôle de son objet contact correspondant dispose d’une autoréférence (par exemple, l’utilisateur est son propre manager).
- Les fenêtres contextuelles d’aide s’affichent désormais sur le focus clavier.
- Pour la mise à niveau automatique, si une application en conflit s’exécute pendant 6 heures, il y a possibilité de la supprimer et de poursuivre la mise à niveau.
- Limitation du nombre d’attributs qu’un client peut sélectionner à 100 par objet lors de la sélection des extensions d’annuaire. De cette façon, l’erreur ne peut pas se produire pendant l’exportation, car Azure présente un maximum de 100 attributs d’extension par objet.
- Correction d’un bogue permettant de renforcer le script de connectivité AD.
- Correction d’un bogue permettant de consolider l’installation d’Azure AD Connect sur une machine qui utilise un service WCF de canaux nommés.
- Amélioration des diagnostics et de la résolution des problèmes liés aux stratégies de groupe qui n’autorisent pas le démarrage du service ADSync lors de l’installation initiale.
- Correction d’un bogue responsable de l’écriture incorrecte du nom d’affichage d’un ordinateur Windows.
- Correction d’un bogue responsable de l’écriture incorrecte du type de système d’exploitation d’un ordinateur Windows.
- Correction d’un bogue responsable de la synchronisation de manière inattendue d’ordinateurs non-Windows 10. Notez que l’effet de cette modification se traduit désormais par la suppression des ordinateurs non-Windows 10 qui, auparavant, étaient synchronisés. Cela n’a pas d’incidence sur les fonctionnalités, car la synchronisation des ordinateurs Windows est uniquement utilisée pour la jonction de domaine Azure AD Hybride, qui ne fonctionne que pour les appareils Windows 10.
- Ajout de plusieurs nouvelles applets de commande (internes) au module PowerShell ADSync.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Il existe un problème connu lié à la mise à niveau d’Azure AD Connect d’une version antérieure vers 1.3.21.0, où le portail Microsoft 365 ne reflète pas la version mise à jour même si Azure AD Connect a été mis à niveau avec succès.
>
> Pour résoudre ce problème, vous devez importer le module **AdSync**, puis exécuter l’applet de commande PowerShell `Set-ADSyncDirSyncConfiguration` sur le serveur Azure AD Connect.  Vous pouvez procéder comme suit :
>
>1.    Ouvrez PowerShell en mode Administrateur.
>2.    Exécutez `Import-Module "ADSync"`.
>3.    Exécutez `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>État de la version 

14/05/2019 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus 

- Correction d’une vulnérabilité avec élévation de privilège qui existe dans Microsoft Azure Active Directory Connect build 1.3.20.0.  Sous certaines conditions, cette vulnérabilité peut permettre à un attaquant d’exécuter deux applets de commande PowerShell dans le contexte d’un compte privilégié et d’effectuer des actions privilégiées.  Cette mise à jour de sécurité résout le problème en désactivant ces cmdlets. Pour plus d’informations, reportez-vous à la [mise à jour de sécurité](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
