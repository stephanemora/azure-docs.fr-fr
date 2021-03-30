---
title: 'Azure AD Connect : Compte de service ADSync | Microsoft Docs'
description: Cette rubrique décrit le compte de service ADSync et les meilleures pratiques qui s'y rapportent.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722155"
---
# <a name="adsync-service-account"></a>Compte de service ADSync
Azure AD Connect installe un service local qui orchestre la synchronisation entre Active Directory et Azure Active Directory.  Le service de synchronisation Microsoft Azure AD Sync (ADSync) s’exécute sur un serveur de votre environnement local.  Les informations d’identification du service sont définies par défaut dans les installations Express, mais peuvent être personnalisées pour répondre aux exigences de sécurité de votre organisation.  Ces informations d’identification ne sont pas utilisées pour se connecter à vos forêts locales ou à Azure Active Directory.

En termes de planification, il est important de choisir le compte le service ADSync avant d'installer Azure AD Connect.  Toute tentative de modification des informations d'identification après l'installation entraînera un échec de démarrage du service, une perte d'accès à la base de synchronisation et un échec d'authentification avec vos répertoires connectés (Azure et AD DS).  Aucune synchronisation ne sera possible avant la restauration des informations d'identification d'origine.

Le service de synchronisation peut s’exécuter sous des comptes différents. Il peut s’exécuter sous un Compte de service virtuel (VSA), un Compte de service géré (gMSA/sMSA), ou un compte d’utilisateur normal. Les options prises en charge ont été modifiées avec la version d’avril 2017 et la version de mars 2021 d’Azure AD Connect, lorsque vous effectuez une nouvelle installation. Si vous mettez à niveau depuis une version antérieure d’Azure AD Connect, ces options supplémentaires ne sont pas disponibles. 


|Type de compte|Option d’installation|Description| 
|-----|------|-----|
|compte de service virtuel|Rapide et personnalisée, avril 2017 et versions ultérieures| Un compte de service virtuel est utilisé pour toutes les installations rapides, à l’exception des installations sur un contrôleur de domaine. Pour l’installation personnalisée, il s’agit de l’option par défaut, sauf si une autre option est utilisée.| 
|Compte de service administré|Installation personnalisée, avril 2017 et versions ultérieures|Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un compte de service administré de groupe. |
|Compte de service administré|Installation rapide et personnalisée, mars 2021 et versions ultérieures|Un compte de service administré autonome préfixé avec ADSyncMSA_ est créé lors de l’installation pour les installations rapides lorsqu’il est installé sur un contrôleur de domaine. Pour l’installation personnalisée, il s’agit de l’option par défaut, sauf si une autre option est utilisée.|
|Compte d’utilisateur|Rapide et personnalisée, avril 2017 à mars 2021|Un compte d’utilisateur préfixé avec AAD_ est créé lors de l’installation pour les installations rapides lorsqu’il est installé sur un contrôleur de domaine. Pour l’installation personnalisée, il s’agit de l’option par défaut, sauf si une autre option est utilisée.|
|Compte d’utilisateur|Installation rapide et personnalisée, mars 2017 et versions antérieures|Un compte d’utilisateur préfixé avec AAD_ est créé lors de l’installation pour les installations rapides. Lorsque vous utilisez l’installation personnalisée, vous pouvez spécifier un autre compte.| 

>[!IMPORTANT]
> Si vous utilisez Connect avec une version de mars 2017 ou antérieure, vous ne devez pas réinitialiser le mot de passe du compte de service, sinon Windows détruit les clés de chiffrement pour des raisons de sécurité. Vous ne pouvez pas modifier le compte sur un autre compte sans réinstaller Azure AD Connect. Si vous mettez à niveau vers la version d’avril 2017 ou une version ultérieure, il est possible de modifier le mot de passe du compte de service, mais vous ne pouvez pas modifier le compte utilisé. 

> [!IMPORTANT]
> Vous pouvez uniquement définir le compte de service lors de la première installation. Il n’est pas possible de modifier le compte de service une fois l’installation terminée. Si vous avez besoin de modifier le mot de passe du compte de service, cela est pris en charge et les instructions sont disponibles [ici](how-to-connect-sync-change-serviceacct-pass.md).

Le tableau suivant présente les options par défaut, recommandées et prises en charge pour le compte de service de synchronisation. 

Légende : 

- Le texte **Gras** indique l’option par défaut et, dans la plupart des cas, l’option recommandée. 
- Le texte *Italique* indique l’option recommandée lorsqu’il ne s’agit pas de l’option par défaut. 
- Non gras - Option prise en charge 
- Compte local - Compte d’utilisateur local sur le serveur 
- Compte de domaine - Compte d’utilisateur de domaine 
- sMSA - [Compte de service géré autonome](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA - [Compte de service géré de groupe](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB</br> Rapide**|**LocalDB/LocalSQL</br> Personnalisée**|**Remote SQL</br> Personnalisée**|
|-----|-----|-----|-----|
|**ordinateur joint à un domaine**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> Compte local</br> Compte du domaine| *gMSA* </br>Compte du domaine|
|Contrôleur de domaine| **sMSA**|**sMSA** </br>*gMSA*</br> Compte du domaine|*gMSA*</br>Compte du domaine| 

## <a name="virtual-service-account"></a>compte de service virtuel 

Un compte de service virtuel est un type spécial de compte local géré qui ne dispose pas d’un mot de passe et qui est automatiquement géré par Windows. 

 ![Compte de service virtuel](media/concept-adsync-service-account/account-1.png)

Le compte de service virtuel est destiné à être utilisé dans les scénarios où le moteur de synchronisation et SQL sont sur le même serveur. Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un compte de service géré de groupe à la place. 

Le compte de service virtuel ne peut pas être utilisé sur un contrôleur de domaine en raison de problèmes avec l’[API de protection des données Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). 

## <a name="managed-service-account"></a>Compte de service administré 

Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un compte de service administré de groupe. Pour plus d’informations sur la préparation de votre annuaire Active Directory pour le compte de service géré de groupe, consultez [Présentation des comptes de service géré de groupe](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Pour utiliser cette option, sur la page [Installer les composants requis](how-to-connect-install-custom.md#install-required-components), sélectionnez **Utiliser un compte de service existant**, puis sélectionnez **Compte de service administré**. 

 ![compte de service administré](media/concept-adsync-service-account/account-2.png)

Il est également possible d’utiliser un Compte de service géré autonome. Toutefois, dans la mesure où vous pouvez uniquement les utiliser sur l’ordinateur local, il n’existe aucun avantage à les utiliser à la place du compte de service virtuel par défaut. 

### <a name="auto-generated-standalone-managed-service-account"></a>Compte de service géré autonome généré automatiquement 

Si vous installez Azure AD Connect sur un contrôleur de domaine, un compte de service administré autonome est créé par l’assistant d’installation (sauf si vous spécifiez le compte à utiliser dans les paramètres personnalisés). Le compte a pour préfixe **ADSyncMSA_** et est associé au service de synchronisation à utiliser. 

Ce compte est un compte de domaine géré qui n’a pas de mot de passe et est automatiquement géré par Windows. 

Ce compte est destiné à être utilisé dans les scénarios où le moteur de synchronisation et SQL sont sur le même contrôleur de domaine. 

## <a name="user-account"></a>Compte d’utilisateur 

Un compte de service local est créé par l’Assistant d’installation (sauf si vous spécifiez le compte à utiliser dans les paramètres personnalisés). Le compte a pour préfixe AAD_ et est associé au service de synchronisation à utiliser. Si vous installez Azure AD Connect sur un contrôleur de domaine, le compte est créé dans le domaine. Le compte de service AAD_ doit se trouver dans le domaine si : 
- Vous utilisez un serveur distant exécutant SQL Server. 
- Vous utilisez un proxy qui nécessite une authentification. 

 ![compte d'utilisateur](media/concept-adsync-service-account/account-3.png)

Le compte est créé avec un mot de passe long et complexe qui n’expire pas. 

Ce compte permet de stocker les mots de passe des autres comptes de manière sécurisée. Ces autres mots de passe de compte sont stockés dans la base de données. Les clés privées pour les clés de chiffrement sont protégées par le chiffrement à clé secrète des services de chiffrement avec l’API de protection des données (DPAPI) Windows. 

Si vous utilisez un serveur SQL Server complet, le compte de service est le propriétaire de la base de données créée pour le moteur de synchronisation. Le service ne fonctionne pas comme prévu avec d’autres autorisations. Une connexion SQL est également créée. 

En outre, le compte se voit octroyer des autorisations sur les fichiers, les clés de Registre et autres objets liés au moteur de synchronisation. 


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
