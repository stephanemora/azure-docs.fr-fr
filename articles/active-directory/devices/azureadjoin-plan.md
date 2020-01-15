---
title: Guide pratique pour planifier l’implémentation de la jointure Azure Active Directory
description: Explique les étapes nécessaires pour implémenter des appareils joints à Azure AD dans votre environnement.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69086ee8d8d64ada7379bdb6af24c8d604e00aed
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665107"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Procédure : Planifier votre implémentation de la jointure d’Azure AD

La jonction Azure AD vous permet de joindre des appareils directement à Azure AD sans qu’il soit nécessaire de se joindre à Active Directory local, tout en conservant vos utilisateurs productifs et sécurisés. La jonction Azure AD est prête à l’emploi par les entreprises pour les déploiements à grande échelle et les déploiements limités.   

Cet article vous fournit les informations nécessaires pour planifier votre implémentation de la jonction Azure AD.
 
## <a name="prerequisites"></a>Conditions préalables requises

Cet article suppose que vous avez lu la [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation de jointure à Azure AD, familiarisez-vous avec les éléments suivants :

|   |   |
|---|---|
|![Vérification][1]|Passer en revue vos scénarios|
|![Vérification][1]|Passer en revue votre infrastructure d’identité|
|![Vérification][1]|Évaluer votre gestion des appareils|
|![Vérification][1]|Comprendre les considérations relatives aux applications et aux ressources|
|![Vérification][1]|Comprendre vos options de provisionnement|
|![Vérification][1]|Configurer l’itinérance des états d’entreprise|
|![Vérification][1]|Configurer un accès conditionnel|

## <a name="review-your-scenarios"></a>Passer en revue vos scénarios 

Si la jonction Azure AD Hybride peut être préférée pour certains scénarios, la jonction Azure AD vous permet d’effectuer la transition vers un modèle orienté cloud avec Windows. Si vous envisagez de moderniser votre gestion des appareils et de réduire les coûts informatiques liés aux appareils, la jonction Azure AD constitue un point de départ intéressant pour atteindre ces objectifs.  
 
Vous devez envisager la jonction Azure AD si vos objectifs répondent aux critères suivants :

- Vous adoptez Microsoft 365 comme suite de productivité pour vos utilisateurs.
- Vous voulez gérer les appareils avec une solution cloud de gestion des appareils.
- Vous voulez simplifier le provisionnement des appareils pour les utilisateurs géographiquement dispersés.
- Vous envisagez de moderniser votre infrastructure d’application.

## <a name="review-your-identity-infrastructure"></a>Passer en revue votre infrastructure d’identité  

La jonction Azure AD fonctionne à la fois avec les environnements gérés et les environnements fédérés.  

### <a name="managed-environment"></a>Environnement géré

Un environnement géré peut être déployé via la [synchronisation du hachage de mot de passe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ou via l’[authentification directe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) avec l’authentification unique fluide.

Ces scénarios ne nécessitent pas la configuration d’un serveur de fédération pour l’authentification.

### <a name="federated-environment"></a>Environnement fédéré

Un environnement fédéré doit avoir un fournisseur d’identité qui prend en charge les protocoles WS-Trust et WS-Fed :

- **WS-Fed :** ce protocole est nécessaire pour joindre un appareil à Azure AD.
- **WS-Trust :** ce protocole est nécessaire pour se connecter à un appareil joint à Azure AD.

Lorsque vous utilisez AD FS, vous devez activer les points de terminaison WS-Trust suivants : `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Si votre fournisseur d’identité ne prend pas en charge ces protocoles, la jonction Azure AD ne fonctionne pas en mode natif. À compter de Windows 10 1809, vos utilisateurs peuvent se connecter à un appareil joint à Azure AD avec un fournisseur d’identité SAML via la [connexion web sur Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Actuellement, la connexion web est une fonctionnalité d’évaluation et n’est pas destinée aux déploiements de production.

>[!NOTE]
> Actuellement, la jointure Azure AD ne fonctionne pas avec [AD FS 2019 configuré avec des fournisseurs d’authentification externes comme méthode d’authentification principale](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). La jointure Azure AD est par défaut l’authentification par mot de passe comme méthode principale, ce qui entraîne des échecs d’authentification dans ce scénario


### <a name="smartcards-and-certificate-based-authentication"></a>Authentification basée sur les cartes à puce et les certificats

Vous ne pouvez pas utiliser l’authentification basée sur des cartes à puce ou sur des certificats pour joindre des appareils à Azure AD. Cependant, vous pouvez utiliser des cartes à puce pour vous connecter à des appareils joints à Azure AD si vous avez configuré AD FS.

**Recommandation :** Implémentez Windows Hello Entreprise pour l'authentification forte sans mot de passe à des appareils Windows 10.

### <a name="user-configuration"></a>Configuration des utilisateurs

Si vous créez des utilisateurs dans :

- **Active Directory local** : vous devez les synchroniser avec Azure AD en utilisant [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD** : aucune configuration supplémentaire n’est nécessaire.

Les UPN locaux qui sont différents dans les UPN Azure AD ne sont pas pris en charge sur les appareils joints à Azure AD. Si vos utilisateurs utilisent un nom d’utilisateur principal local, vous devez prévoir d’utiliser leur nom d’utilisateur principal dans Azure AD.

## <a name="assess-your-device-management"></a>Évaluer votre gestion des appareils

### <a name="supported-devices"></a>Appareils pris en charge

La jonction Azure AD :

- Est applicable seulement aux appareils Windows 10. 
- N’est pas applicable aux versions antérieures de Windows ou à d’autres systèmes d’exploitation. Si vous avez des appareils Windows 7/8.1, vous devez les mettre à niveau vers Windows 10 pour déployer la jonction Azure AD.
- N’est pas pris en charge sur les appareils avec module de plateforme sécurisée (TPM) en mode FIPS.
 
**Recommandation :** Utilisez toujours la version la plus récente de Windows 10 pour bénéficier de fonctionnalités à jour.

### <a name="management-platform"></a>Plateforme de gestion

La gestion des appareils joints à Azure AD est basée sur une plateforme MDM (comme Intune) et les fournisseurs de solutions Cloud GPM. Windows 10 a un agent MDM intégré qui fonctionne avec toutes les solutions MDM compatibles.

> [!NOTE]
> Les stratégies de groupe ne sont pas prises en charge sur les appareils joints à Azure AD, car ils ne sont pas connectés à Active Directory local. Vous pouvez administrer les appareils joints à Azure AD uniquement via la gestion des périphériques mobiles

Il existe deux approches pour la gestion des appareils joints à Azure AD :

- **MDM uniquement** : un appareil est géré exclusivement par un fournisseur MDM, comme Intune. Toutes les stratégies sont délivrées dans le cadre du processus d’inscription MDM. Pour les clients Azure AD Premium ou EMS, l’inscription MDM est une étape automatisée qui fait partie de la jonction Azure AD.
- **Cogestion** : un appareil est géré par un fournisseur MDM et SCCM. Dans cette approche, l’agent SCCM est installé sur un appareil géré par MDM pour administrer certains aspects.

Si vous utilisez des stratégies de groupe, évaluez votre parité de stratégie MDM avec l’[outil d’analyse de la migration MDM](https://github.com/WindowsDeviceManagement/MMAT). 

Passez en revue les stratégies prises en charge ou non pour déterminer si vous pouvez utiliser une solution MDM au lieu de stratégies de groupe. Pour les stratégies non prises en charge, considérez les points suivants :

- Les stratégies non prises en charge sont-elles nécessaires pour les appareils ou les utilisateurs joints à Azure AD ?
- Les stratégies non prises en charge sont-elles applicables dans un déploiement cloud ?

Si votre solution MDM n’est pas disponible via la galerie d’applications Azure AD, vous pouvez l’ajouter en suivant la procédure décrite dans [Intégration d’Azure Active Directory à MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Via la cogestion, vous pouvez utiliser SCCM pour gérer certains aspects de vos appareils, les stratégies étant délivrées via votre plateforme MDM. Microsoft Intune permet la cogestion avec SCCM. Pour plus d’informations, consultez [Cogestion pour les appareils Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Si vous utilisez un produit MDM autre qu’Intune, vérifiez auprès de votre fournisseur MDM les scénarios de cogestion applicables.

**Recommandation :** Envisagez la gestion uniquement MDM pour les appareils joints à Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Comprendre les considérations relatives aux applications et aux ressources

Nous recommandons de migrer les applications d’un système local vers le cloud pour une meilleure expérience utilisateur et un meilleur contrôle d’accès. Cependant, les appareils joints à Azure AD peuvent sans problème accéder à la fois aux applications locales et aux applications cloud. Pour plus d’informations, consultez [Fonctionnement de l’authentification unique auprès de ressources locales sur des appareils joints à Azure AD](azuread-join-sso.md).

Les sections suivantes listent les considérations à prendre en compte pour différents types d’applications et de ressources.

### <a name="cloud-based-applications"></a>Applications cloud

Si une application est ajoutée à la galerie d’applications Azure AD, les utilisateurs bénéficient de l’authentification unique via les appareils joints à Azure AD. Aucune configuration supplémentaire n’est nécessaire. Les utilisateurs bénéficient de l’authentification unique sur les navigateurs Microsoft Edge et Chrome. Pour Chrome, vous devez déployer l’[extension Windows 10 Accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Toutes les applications Win32 qui :

- S’appuient sur le gestionnaire de comptes web pour les demandes de jeton bénéficient également de l’authentification unique sur les appareils joints à Azure AD. 
- Ne s’appuient pas sur le gestionnaire de comptes web peuvent inviter les utilisateurs à s’authentifier. 

### <a name="on-premises-web-applications"></a>Applications web locales

Si vos applications sont créées de façon personnalisée et/ou hébergées localement, vous devez les ajouter aux sites de confiance de votre navigateur pour :

- Activer le fonctionnement de l’authentification Windows intégrée 
- Offrir une expérience d’authentification unique sans invites aux utilisateurs. 

Si vous utilisez AD FS, consultez [Vérifier et gérer l’authentification unique avec AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recommandation :** Envisagez l'hébergement dans le cloud (Azure, par exemple) et l'intégration à Azure AD pour une meilleure expérience.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Applications locales s’appuyant sur des protocoles hérités

Les utilisateurs bénéficient de l’authentification unique sur les appareils joints à Azure AD si l’appareil a accès à un contrôleur de domaine. 

**Recommandation :** Déployez le [proxy Azure AD App](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) pour permettre un accès sécurisé à ces applications.

### <a name="on-premises-network-shares"></a>Partages de réseau local

Vos utilisateurs bénéficient de l’authentification unique à partir d’appareils joints à Azure AD quand un appareil a accès à un contrôleur de domaine local.

### <a name="printers"></a>Imprimantes

Pour les imprimantes, vous devez déployer l’[impression cloud hybride](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) pour la découverte des imprimantes sur les appareils joints à Azure AD. 

Les imprimantes ne peuvent pas être découvertes automatiquement dans un environnement cloud uniquement, mais vos utilisateurs peuvent également utiliser le chemin UNC des imprimantes pour les ajouter directement. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Applications locales s’appuyant sur l’authentification de la machine

Les appareils joints à AD Azure ne prennent pas en charge les applications locales qui s’appuient sur l’authentification de la machine. 

**Recommandation :** Envisagez la mise hors service de ces applications et leur déplacement vers leurs alternatives plus modernes.

### <a name="remote-desktop-services"></a>Services Bureau à distance

La connexion Bureau à distance à des appareils joints à Azure AD nécessite que l’ordinateur hôte soit joint à Azure AD ou à Azure AD Hybride. Le Bureau à distance depuis un appareil non joint ou non-Windows n’est pas pris en charge. Pour plus d’informations, consultez [Se connecter à un PC distant joint à Azure AD](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Comprendre vos options de provisionnement

Vous pouvez provisionner la jonction Azure AD en utilisant les approches suivantes :

- **Libre-service dans OOBE/Paramètres** : dans le mode libre-service, les utilisateurs sont soumis au processus de jonction Azure AD pendant l’expérience OOBE Windows ou depuis Paramètres Windows. Pour plus d’informations, consultez [Joindre votre appareil professionnel au réseau de votre organisation](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** : Windows Autopilot permet la préconfiguration des appareils pour une meilleure expérience dans OOBE pour effectuer une jonction Azure AD. Pour plus d’informations, consultez [Vue d’ensemble de Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Inscription en bloc** : l’inscription en bloc permet une jonction Azure AD pilotée par l’administrateur avec un outil de provisionnement en bloc pour configurer les appareils. Pour plus d’informations, consultez [Inscription en bloc pour les appareils Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Voici une comparaison de ces trois approches 
 
|   | Configuration en libre-service | Windows Autopilot | Inscription en bloc |
| --- | --- | --- | --- |
| Nécessite une interaction utilisateur pour la configuration | Oui | Oui | Non |
| Nécessite du travail de la part du département informatique | Non | Oui | Oui |
| Flux applicables | OOBE et Paramètres | OOBE uniquement | OOBE uniquement |
| Droits d’administrateur local pour l’utilisateur principal | Oui, par défaut | Configurable | Non |
| Nécessite une prise en charge OEM | Non | Oui | Non |
| Versions prises en charge | 1511+ | 1709+ | 1703+ |
 
Choisissez votre ou vos approches de déploiement en consultant le tableau ci-dessus, et en examinant les points suivants pour l’adoption de l’une ou l’autre approche :  

- Vos utilisateurs sont-ils suffisamment expérimentés pour effectuer la configuration eux-mêmes ? 
   - Le libre-service peut être plus approprié pour ces utilisateurs. Envisagez Windows Autopilot pour améliorer l’expérience utilisateur.  
- Vos utilisateurs se trouvent-ils à distance ou dans les locaux de l’entreprise ? 
   - Le libre-service ou Autopilot sont plus appropriés pour les utilisateurs à distance, car ils permettent une configuration facile. 
- Préférez-vous une configuration pilotée par l’utilisateur ou gérée par un administrateur ? 
   - L’inscription en bloc est plus appropriée pour les déploiements pilotés par un administrateur afin de configurer des appareils avant de les confier aux utilisateurs.     
- Achetez-vous des appareils auprès de seulement un ou deux fabricants OEM, ou bien vos appareils OEM proviennent-ils de fournisseurs en plus grand nombre ?  
   - Si vous achetez auprès d’un nombre limité de fabricants OEM qui prennent également en charge Autopilot, vous pouvez également bénéficier d’une intégration plus étroite avec Autopilot. 

## <a name="configure-your-device-settings"></a>Configurer les paramètres de vos appareils

Le portail Azure vous permet de contrôler le déploiement d’appareils joints à Azure AD dans votre organisation. Pour configurer les paramètres concernés, dans la **page Azure Active Directory**, sélectionnez `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Les utilisateurs peuvent joindre des appareils à Azure AD

Définissez cette option sur **Tous** ou sur **Sélectionné**, en fonction de l’étendue de votre déploiement et de qui vous voulez autoriser à configurer un appareil joint à Azure AD. 

![Les utilisateurs peuvent joindre des appareils à Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administrateurs locaux supplémentaires sur les appareils joints à Azure AD

Choisissez **Sélectionné** et sélectionnez les utilisateurs que vous voulez ajouter au groupe local des administrateurs sur tous les appareils joints à Azure AD. 

![Administrateurs locaux supplémentaires sur les appareils joints à Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Exiger Multi-factor Auth pour joindre des appareils

Sélectionnez **« Oui »** si vous exigez que les utilisateurs effectuent MFA lors de la jonction d’appareils à Azure AD. Pour les utilisateurs joignant des appareils à Azure AD avec MFA, l’appareil lui-même devient un second facteur.

![Exiger Multi-factor Auth pour joindre des appareils](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Configurer vos paramètres de mobilité

Avant de pouvoir configurer vos paramètres de mobilité, il peut être nécessaire d’ajouter d’abord un fournisseur MDM.

**Pour ajouter un fournisseur MDM** :

1. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur `Mobility (MDM and MAM)`. 
1. Cliquez sur **Ajouter une application**.
1. Sélectionnez votre fournisseur MDM dans la liste.

   ![Ajouter une application](./media/azureadjoin-plan/04.png)

Sélectionnez votre fournisseur MDM pour configurer les paramètres concernés. 

### <a name="mdm-user-scope"></a>Étendue des utilisateurs MDM

Sélectionnez **Certain(e)s** ou **Tous**, en fonction de l’étendue de votre déploiement. 

![Étendue des utilisateurs MDM](./media/azureadjoin-plan/05.png)

En fonction de votre étendue, une des actions suivantes se produit : 

- **L'utilisateur est dans l'étendue MDM** : Si vous disposez d'un abonnement Azure AD Premium, l'inscription MDM est automatisée, ainsi que la jonction Azure AD. Tous les utilisateurs de l’étendue doivent avoir une licence appropriée pour votre MDM. Si l’inscription MDM échoue dans ce scénario, la jonction Azure AD est également annulée.
- **L'utilisateur n'est pas dans l'étendue MDM** : Si les utilisateurs ne sont pas dans l'étendue MDM, la jonction Azure AD se termine sans aucune inscription MDM. Ceci aboutit à un appareil non géré.

### <a name="mdm-urls"></a>URL MDM

Il existe trois URL liées à votre configuration MDM :

- URL des conditions d’utilisation de MDM
- URL de découverte MDM 
- URL de conformité MDM

![Ajouter une application](./media/azureadjoin-plan/06.png)

Chaque URL a une valeur par défaut prédéfinie. Si ces champs sont vides, contactez votre fournisseur MDM pour plus d’informations.

### <a name="mam-settings"></a>Paramètres GAM

GAM ne s’applique pas à la jonction Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Configurer l’itinérance des états d’entreprise

Si vous voulez activer l’itinérance des états dans Azure AD, afin que les utilisateurs puissent synchroniser leurs paramètres entre différents appareils, consultez [Activer Enterprise State Roaming dans Azure Active Directory](enterprise-state-roaming-enable.md). 

**Recommandation** : Activez ce paramètre même pour des appareils joints à Azure AD hybrides.

## <a name="configure-conditional-access"></a>Configurer un accès conditionnel

Si vous avez un fournisseur MDM configuré pour vos appareils joints à Azure AD, ce fournisseur signale l’appareil comme étant conforme dès que l’appareil est inscrit pour la gestion. 

![Conformité de l’appareil](./media/azureadjoin-plan/46.png)

Vous pouvez utiliser cette implémentation pour [exiger des appareils gérés pour l’accès aux applications cloud avec l’accès conditionnel](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Joindre un nouvel appareil Windows 10 à Azure AD lors d’une première exécution](azuread-joined-devices-frx.md)
> [Joindre votre appareil professionnel au réseau de votre organisation](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
