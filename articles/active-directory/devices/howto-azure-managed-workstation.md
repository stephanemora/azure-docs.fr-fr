---
title: Déployer des stations de travail gérées par Azure - Azure Active Directory
description: Découvrez comment déployer des stations de travail sécurisées gérées par Azure pour réduire le risque de violation dû à une configuration incorrecte ou à une compromission.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56cd23494f65b1c74e44868496855c6e4a32bf7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365814"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Déployer une station de travail sécurisée gérée par Azure

Maintenant que vous [connaissez les stations de travail sécurisées](concept-azure-managed-workstation.md), il est temps de commencer le processus de déploiement. Avec ce guide, vous utilisez des profils définis pour créer une station de travail plus sécurisée dès le départ.

![Déploiement d’une station de travail sécurisée](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Sélectionnez un profil avant de déployer la solution. Vous pouvez utiliser plusieurs profils simultanément dans un déploiement et les attribuer avec des balises ou des groupes.

> [!NOTE]
> Appliquez les profils selon vos besoins. Vous pouvez changer de profil en l’attribuant dans Microsoft Intune.

| Profil | Faible | Amélioré | Élevé | Spécialisée | Sécurisé | Isolé |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utilisateur dans Azure AD | Oui | Oui | Oui | Oui | Oui | Oui |
| Géré par Intune | Oui | Oui | Oui | Oui | Oui | Oui |
| Appareil - Inscrit sur Azure AD | Oui |  |  |  |  | |   |
| Appareil - Joint sur Azure AD |   | Oui | Oui | Oui | Oui | Oui |
| Base de référence de sécurité Intune appliquée |   | Oui <br> (Amélioré) | Oui <br> (HighSecurity) | Oui <br> (NCSC) | Oui <br> (Sécurisé) | N/D |
| Matériel répondant aux normes Windows 10 sécurisées |   | Oui | Oui | Oui | Oui | Oui |
| Microsoft Defender ATP activé |   | Oui  | Oui | Oui | Oui | Oui |
| Suppression des droits d’administrateur |   |   | Oui  | Oui | Oui | Oui |
| Déploiement à l’aide de Microsoft Autopilot |   |   | Oui  | Oui | Oui | Oui |
| Applications installées uniquement par Intune |   |   |   | Oui | Oui |Oui |
| URL limitées à la liste approuvée |   |   |   | Oui | Oui |Oui |
| Internet bloqué (entrant/sortant) |   |   |   |  |  |Oui |

> [!NOTE]
> Dans le guide sur les stations de travail sécurisées, les **appareils** seront attribués avec des profils et stratégies. Les stratégies ne sont pas appliquées directement aux utilisateurs, ce qui permet le partage d’appareils (appareils partagés). Si une station de travail sécurisée n’est pas partagée dans votre déploiement ou si des stratégies utilisateur individuelles sont nécessaires, des profils de stratégie utilisateur peuvent être attribués à l’utilisateur et à l’appareil. 

## <a name="license-requirements"></a>Conditions de licence :

Les concepts abordés dans ce guide supposent que vous disposez de Microsoft 365 Entreprise E5 ou d’une référence SKU équivalente. Certaines recommandations de ce guide peuvent être implémentées avec des références SKU inférieures. Pour plus d’informations, consultez la [gestion des licences Microsoft 365 Entreprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Pour automatiser l’approvisionnement de la licence, envisagez la [gestion des licences par groupe](../enterprise-users/licensing-groups-assign.md) pour vos utilisateurs.

## <a name="azure-active-directory-configuration"></a>Configuration d’Azure Active Directory

Azure Active Directory (Azure AD) gère les utilisateurs, groupes et appareils de vos postes de travail d’administrateur. Activez les services d’identité et les fonctionnalités avec un [compte d’administrateur](../roles/permissions-reference.md).

Lorsque vous créez le compte d’administrateur de station de travail sécurisée, vous exposez le compte à votre station de travail actuelle. Veillez à utiliser un appareil sans échec connu pour effectuer cette configuration initiale et toute la configuration globale. Pour réduire les risques d’attaques pour une première expérience, envisagez de suivre les [conseils prodigués afin d’éviter les infections de logiciels malveillants](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Exigez une authentification multifacteur au moins pour les administrateurs. Consultez [Déployer une authentification multifacteur sur le cloud](../authentication/howto-mfa-getstarted.md) pour obtenir des conseils d’implémentation.

### <a name="azure-ad-users-and-groups"></a>Utilisateurs et groupes Azure AD

1. Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Nouvel utilisateur** .
1. Créez l’administrateur de l’appareil en suivant les étapes décrites dans le [didacticiel sur la création d’utilisateur](/Intune/quickstart-create-user).
1. Entrez :

   * **Nom**  : administrateur de station de travail sécurisée
   * **Nom d’utilisateur** - `secure-ws-admin@identityitpro.com`
   * **Rôle d’annuaire** - **Administrateur limité** et sélectionnez le rôle **Administrateur Intune** .

1. Sélectionnez **Create** (Créer).

Ensuite, vous créez deux groupes : les utilisateurs de station de travail et les appareils de station de travail.

Dans le portail Azure, accédez à **Azure Active Directory** > **Groupes** > **Nouveau groupe** .

1. Pour le groupe d’utilisateurs de station de travail, vous pouvez souhaiter configurer la [gestion des licences par groupe](../enterprise-users/licensing-groups-assign.md) pour automatiser l’approvisionnement des licences aux utilisateurs.
1. Pour le groupe d’utilisateurs de station de travail, entrez :

   * **Type de groupe**  : sécurité
   * **Nom du groupe**  : utilisateurs de station de travail sécurisée
   * **Type d’appartenance**  : attribué

1. Ajoutez votre administrateur de station de travail sécurisée : `secure-ws-admin@identityitpro.com`
1. Vous pouvez ajouter tous les autres utilisateurs qui géreront les stations de travail sécurisées.
1. Sélectionnez **Create** (Créer).
1. Pour le groupe d’appareils de station de travail, entrez :

   * **Type de groupe**  : sécurité
   * **Nom du groupe**  : stations de travail sécurisées
   * **Type d’appartenance**  : attribué

1. Sélectionnez **Create** (Créer).

### <a name="azure-ad-device-configuration"></a>Configuration d’appareil Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Indiquer qui peut joindre des appareils à Azure AD

Configurez les paramètres de vos appareils dans Active Directory pour permettre à votre groupe de sécurité administrative de joindre des appareils à votre domaine. Pour configurer ces paramètres à partir du portail Azure :

1. Accédez à **Azure Active Directory** > **Appareils** > **Paramètres de l’appareil** .
1. Choisissez **Sélectionné** sous **Les utilisateurs peuvent joindre des appareils à Azure AD** , puis sélectionnez le groupe « Utilisateurs de station de travail sécurisée ».

#### <a name="removal-of-local-admin-rights"></a>Suppression des droits d’administrateur en local

Cette méthode requiert que les utilisateurs de l’adresse IP virtuelle, DevOps et des stations de travail sécurisées n’aient pas de droits d’administrateur sur leurs ordinateurs. Pour configurer ces paramètres à partir du portail Azure :

1. Accédez à **Azure Active Directory** > **Appareils** > **Paramètres de l’appareil** .
1. Sélectionnez **Aucun** sous **Administrateurs locaux supplémentaires sur les appareils joints à Azure AD** .

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exiger l’authentification multifacteur pour joindre des appareils

Pour renforcer davantage le processus de jonction d’appareils à Azure AD :

1. Accédez à **Azure Active Directory** > **Appareils** > **Paramètres de l’appareil** .
1. Sélectionnez **Oui** sous **Exiger l’authentification multifacteur pour joindre des appareils** .
1. Sélectionnez **Enregistrer** .

#### <a name="configure-mobile-device-management"></a>Configurer la gestion des appareils mobiles

À partir du portail Azure :

1. Accédez à **Azure Active Directory** > **Mobilité (MDM et MAM)**  > **Microsoft Intune** .
1. Remplacez le paramètre d’ **étendue utilisateur MDM** par **Tout** .
1. Sélectionnez **Enregistrer** .

Ces étapes permettent de gérer n’importe quel appareil avec Intune. Pour plus d’informations, consultez [Démarrage rapide Intune : configurer l’inscription automatique pour les appareils Windows 10](/Intune/quickstart-setup-auto-enrollment). Vous créerez des stratégies de configuration et de conformité Intune à une étape ultérieure.

#### <a name="azure-ad-conditional-access"></a>Accès conditionnel Azure AD

L’accès conditionnel Azure AD permet de restreindre les tâches administratives privilégiées pour les appareils conformes. Les membres prédéfinis du groupe **Utilisateurs de station de travail sécurisée** doivent effectuer une authentification multifacteur lors de la connexion aux applications cloud. Une meilleure pratique consiste à exclure des comptes d’accès d’urgence de la stratégie. Pour plus d’informations, consultez [Gérer des comptes d’accès d’urgence dans Azure AD](../roles/security-emergency-access.md).

## <a name="intune-configuration"></a>Configuration Intune

### <a name="configure-enrollment-status"></a>Configurer l’état de l’inscription

Il est important de vérifier que votre station de travail sécurisée est un appareil propre approuvé. Lorsque vous achetez de nouveaux appareils, vous pouvez insister pour qu’ils soient configurés sur [Windows 10 Pro en mode S](/Windows/deployment/Windows-10-pro-in-s-mode), ce qui limite l’exposition aux vulnérabilités lors de la gestion de la chaîne d’approvisionnement. Une fois que vous recevez un appareil de votre fournisseur, vous pouvez utiliser Autopilot pour changer de mode. Le guide suivant fournit des détails sur l’application du processus de transformation.

Pour vous assurer que les appareils sont entièrement configurés avant leur utilisation, Intune propose un moyen de **bloquer l’utilisation de l’appareil jusqu'à ce que toutes les applications et tous les profils soient installés** .

À partir du **portail Azure**  :

1. Accédez à **Microsoft Intune** > **Inscription d’appareil** > **Inscription Windows** > **Page État d’inscription** > **Par défaut** > **Paramètres** .
1. Définissez l’option **Afficher la progression de l'installation des applications et des profils** sur **Oui** .
1. Définissez **Bloquer l'utilisation de l'appareil tant que les profils et les applications ne sont pas tous installés** sur **Oui** .

### <a name="create-an-autopilot-deployment-profile"></a>Créer un profil de déploiement Autopilot

Après avoir créé un groupe d’appareils, vous devez créer un profil de déploiement pour configurer les appareils Autopilot.

Dans Intune, dans le portail Azure :

1. Sélectionnez **Inscription d’appareil** > **Inscription Windows** > **Profils de déploiement** > **Créer un profil** .
1. Entrez :

   * Nom : **profil de déploiement de station de travail sécurisée** .
   * Description : **déploiement de stations de travail sécurisées** .
   * Définissez **Convertir tous les appareils ciblés en Autopilot** sur **Oui** . Ce paramètre permet de s’assurer que tous les appareils de la liste sont inscrits auprès du service de déploiement Autopilot. Le traitement de l’inscription peut prendre 48 heures.

1. Sélectionnez **Suivant** .

   * Comme **mode de déploiement** , choisissez le **déploiement automatique (préversion)** . Les appareils avec ce profil sont associés à l’utilisateur qui inscrit l’appareil. Les informations d’identification de l’utilisateur sont requises pour inscrire l’appareil. Il est important de noter que le déploiement d’un appareil en mode **Déploiement automatique** vous permettra de déployer des ordinateurs portables dans un modèle partagé. Aucune attribution d’utilisateur ne se produit avant la première attribution de l’appareil à un utilisateur. Par conséquent, toutes les stratégies utilisateur telles que BitLocker ne seront pas activées avant la première attribution d’utilisateur. Si vous souhaitez en savoir plus sur les méthodes de connexion à un appareil sécurisé, veuillez consulter la page [Profils sélectionnés](/intune/device-profile-assign).
   * La case **Joindre à Azure AD comme** doit afficher **Appareil joints Azure AD** et être grisée.
   * Sélectionnez votre langue (région) et le type de compte utilisateur **standard** . 

1. Sélectionnez **Suivant** .

   * Sélectionnez une balise d’étendue si vous en avez préconfiguré une.

1. Sélectionnez **Suivant** .
1. Choisissez **Affectations** > **Affecter à** > **Groupes sélectionnés** . Dans la zone **Sélectionner les groupes à inclure** , choisissez **Stations de travail sécurisées** .
1. Sélectionnez **Suivant** .
1. Cliquez sur **Créer** pour créer le profil. Le profil de déploiement Autopilot est maintenant disponible pour les appareils.

L’expérience utilisateur de l’inscription d’appareils dans AutoPilot diffère selon le type et le rôle de chaque appareil. Dans notre exemple de déploiement, nous illustrons un modèle dans lequel les appareils sécurisés sont déployés en bloc et peuvent être partagés, mais lorsqu’ils sont utilisés pour la première fois, l’appareil est attribué à un utilisateur. Si vous souhaitez en savoir plus, veuillez consulter la page [Inscription d’appareil Intune Autopilot](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configurer Windows Update

La mise à jour de Windows 10 est une des actions les plus importantes que vous pouvez effectuer. Pour mettre à jour de Windows de façon sécurisée, vous déployez un anneau de mise à jour pour gérer le rythme auquel les mises à jour sont appliquées aux stations de travail. 

Ce guide recommande de créer un anneau de mise à jour et de modifier les paramètres par défaut suivants :

Dans le portail Azure :

1. Accédez à **Microsoft Intune** > **Mises à jour logicielles** > **Anneaux de mise à jour Windows 10** .
1. Entrez :

   * Nom : **mises à jour de station de travail gérée par Azure**
   * Canal de service : **Windows Insider - Rapide**
   * Report de mise à jour de qualité (jours) :  **3**
   * Période de report de mise à jour de fonctionnalité (jours) :  **3**
   * Comportement de mise à jour automatique : **automatiquement installer et redémarrer sans contrôle de l’utilisateur final**
   * Empêcher l’utilisateur de suspendre les mises à jour Windows : **bloquer**
   * Exiger l’approbation de l’utilisateur pour redémarrer en dehors des heures de travail : **obligatoire**
   * Autoriser l’utilisateur à redémarrer (redémarrage engagé) : **obligatoire**
   * Transférer les utilisateurs vers un redémarrage engagé après un redémarrage automatique (jours) :  **3**
   * Période rappel de redémarrage engagé (jours) :  **3**
   * Définir une échéance pour les redémarrages en attente (jours) :  **3**

1. Sélectionnez **Create** (Créer).
1. Dans l’onglet **Affectations** , ajoutez le groupe **Stations de travail sécurisées** .

Pour plus d’informations sur les stratégies Windows Update, consultez [CSP de la stratégie-mise à jour](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Intégration de Windows Defender ATP et Intune

Windows Defender ATP et Microsoft Intune fonctionnent ensemble pour empêcher les violations de la sécurité. Ils peuvent également limiter l’impact des violations. Les fonctionnalités d’ATP permettent la détection de menaces en temps réel, ainsi qu’un audit étendu et la journalisation des appareils de point de terminaison.

Pour configurer l’intégration de Windows Defender ATP et Intune, accédez au portail Azure.

1. Accédez à **Microsoft Intune** > **Conformité des appareils** > **Windows Defender ATP** .
1. À l’étape 1 sous **Configuration de Windows Defender ATP** , sélectionnez **Connecter Windows Defender ATP à Microsoft Intune dans le Centre de sécurité Windows Defender** .
1. Dans le centre de sécurité Windows Defender :

   1. Sélectionnez **Paramètres** > **Fonctionnalités avancées** .
   1. Pour **Connexion Microsoft Intune** , choisissez **Activé** .
   1. Sélectionnez **Enregistrer les préférences** .

1. Une fois la connexion établie, revenez à Intune et sélectionnez **Actualiser** en haut.
1. Définissez **Connecter les appareils Windows versions 10.0.15063 et ultérieures à Windows Defender ATP** à **Activé** .
1. Sélectionnez **Enregistrer** .

Pour plus d’informations, consultez [Protection avancée contre les menaces Windows Defender](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Terminer le renforcement du profil de station de travail

Pour mener à bien le renforcement de la solution, téléchargez et exécutez le script approprié. Rechercher les liens de téléchargement correspondant au **niveau de profil** souhaité :

| Profil | Emplacement de téléchargement | Nom de fichier |
| --- | --- | --- |
| Sécurité faible | N/A | N/A |
| Sécurité améliorée | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Sécurité élevée | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Spécialisée | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformité spécialisée* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Sécurisé | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Conformité spécialisée est un script qui applique la configuration spécialisée fournie dans NCSC Windows10 SecurityBaseline.

Une fois que le script s’est exécuté correctement, vous pouvez mettre à jour les profils et les stratégies dans Intune. Les scripts pour les profils Amélioré et Sécurisé créent des stratégies et des profils pour vous, mais vous devez attribuer la règle à votre groupe d’appareils **Stations de travail sécurisées** .

* Voici où vous trouverez les profils de configuration d’appareil Intune créés par les scripts : **Portail Azure** > **Microsoft Intune** > **Configuration d’appareil** > **Profils** .
* Voici où vous trouverez les stratégies de conformité d’appareil Intune créées par les scripts : **Portail Azure** > **Microsoft Intune** > **Conformité d’appareil** > **Stratégies** .

Pour passer en revue les modifications apportées par les scripts, vous pouvez exporter les profils. De cette façon, vous pouvez déterminer les renforcements supplémentaires qui peuvent s’avérer nécessaires, comme indiqué dans la [documentation SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Exécutez le script d’exportation de données Intune `DeviceConfiguration_Export.ps1` à partir du [référentiel GiuHub DeviceConfiguration](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) pour exporter tous les profils Intune actuels.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurations supplémentaires et renforcement de la sécurité à prendre en compte

En suivant les conseils donnés ici, vous avez déployé une station de travail sécurisée. Toutefois, vous devez également envisager des contrôles supplémentaires. Par exemple :

* limiter l’accès à d’autres navigateurs
* autoriser le trafic HTTP sortant
* bloquer certains sites web
* définir des autorisations pour l’exécution de scripts PowerShell personnalisés

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Définir des règles dans le fournisseur de service de configuration de pare-feu (CSP)

Vous pouvez apporter des modifications supplémentaires à la gestion des règles de trafic entrant et sortant pour vos points de terminaison autorisés et bloqués, en fonction de vos besoins. Lorsque vous continuez à renforcer la station de travail sécurisée, vous pouvez assouplir la restriction qui interdit tout le trafic entrant et sortant. Vous pouvez ajouter des sites sortants autorisés pour inclure les sites web approuvés et courants. Pour plus d’informations, voir le [service de configuration du pare-feu](/Windows/client-management/mdm/firewall-csp).

La gestion restrictive du trafic d’URL inclut les options suivantes :

* Refuser tout le trafic entrant : définie dans le script de profil Station de travail sécurisée.
* Refuser tout le trafic sortant, à l’exception de certains services Azure et Microsoft, notamment Azure Cloud Shell, et de la possibilité d’autoriser la réinitialisation du mot de passe Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Si vous souhaitez augmenter la précision de vos règles de blocage, vous pouvez vous référer au projet Spamhaus qui gère la [Liste des domaines bloqués (DBL)](https://www.spamhaus.org/dbl/), une bonne ressource à utiliser en tant qu’ensemble avancé de règles à implémenter pour bloquer des sites.

### <a name="manage-local-applications"></a>Gérer les applications locales

La station de travail sécurisée passe à un état véritablement renforcé lorsque les applications locales sont supprimées, y compris les applications de productivité. Ici, vous ajoutez Chrome comme navigateur par défaut et utilisez Intune pour limiter la capacité d’un utilisateur à modifier le navigateur et ses plug-ins.

#### <a name="deploy-applications-using-intune"></a>Déployer des applications avec Intune

Dans certaines situations, des applications telles que le navigateur Google Chrome sont requises sur la station de travail sécurisée. L’exemple suivant fournit des instructions pour installer Chrome sur des appareils dans le groupe de sécurité **Stations de travail sécurisées** .

1. Téléchargez le programme d’installation hors connexion du [bundle Chrome pour Windows 64 bits](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Décompressez les fichiers et prenez note de l’emplacement du fichier `GoogleChromeStandaloneEnterprise64.msi`.
1. Dans le **portail Azure** , accédez à **Microsoft Intune** > **Applications clientes** > **Applications** > **Ajouter** .
1. Sous **Type d’application** , choisissez **Service de l'entreprise** .
1. Sous **Fichier de package d'application** , sélectionnez le fichier `GoogleChromeStandaloneEnterprise64.msi` à l’emplacement d’extraction, puis cliquez sur **OK** .
1. Sous **Informations sur l’application** , indiquez une description et un éditeur. Sélectionnez **OK** .
1. Sélectionnez **Ajouter** .
1. Sur l’onglet **Affectations** , sélectionnez **Disponible pour les appareils inscrits** sous **Type d’affectation** .
1. Sous **Groupes inclus** , ajoutez le groupe **Stations de travail sécurisées** .
1. Sélectionnez **OK** , puis cliquez sur **Enregistrer** .

Pour plus d’informations sur la configuration des paramètres de Chrome, consultez [Gérer le navigateur Chrome avec Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configuration du portail d’entreprise pour les applications personnalisées

En mode sécurisé, l’installation de l’application est limitée au portail d’entreprise Intune. Toutefois, l’installation du portail requiert l’accès à Microsoft Store. Dans votre solution sécurisée, vous pouvez rendre le portail d’entreprise disponible pour tous les appareils via un mode hors connexion.

Une copie géré par Intune du [portail d’entreprise](/Intune/store-apps-company-portal-app) vous octroie un accès à la demande à des outils supplémentaires que vous pouvez transmettre aux utilisateurs des stations de travail sécurisées.

Vous devrez peut-être installer des applications Windows 32 bits ou d’autres applications dont le déploiement nécessite une préparation spéciale. Dans ce cas, l’[outil de préparation du contenu Microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) peut fournir un fichier au format `.intunewin` prêt à l’emploi pour l’installation.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Accès conditionnel n’autorisant l’accès au portail Azure uniquement qu’à une station de travail sécurisée

Azure AD offre la possibilité de gérer et limiter qui et ce qui peut accéder à votre portail de gestion de cloud Azure. L’activation de l’[Accès conditionnel](../conditional-access/overview.md) garantit que seule votre station de travail sécurisée peut gérer ou modifier des ressources. Lors du déploiement de cette fonctionnalité, il est essentiel que vous considériez si l’[accès d’urgence](../roles/security-emergency-access.md) peut ou doit être utilisée uniquement pour des cas extrêmes et le compte géré par le biais d’une stratégie.

> [!NOTE]
> Vous devez créer un groupe d’utilisateurs et inclure votre utilisateur d’urgence capable de contourner la stratégie d’accès conditionnel. Pour notre exemple, nous avons un groupe de sécurité nommé **Emergency BreakGlass**

1. Accédez au **Portail Azure** > **Microsoft Intune** > **Accès conditionnel – Stratégies** > **Nouvelle stratégie** .
1. Spécifiez un **Nom** pour la stratégie.
1. Sélectionnez **Utilisateurs et groupes** > **Sélectionner les utilisateurs et les groupes** 
1. Sélectionnez **Inclure** > **Rôles d’annuaire** > Choisissez les rôles > Administrateur général, Administrateur de rôle privilégié, Administrateur d’authentification privilégié, Administrateur de sécurité, Administrateur de conformité, Administrateur de l’accès conditionnel, Administrateur d’application, Administrateur d’application cloud, Administrateur de service Intune
1. Sélectionnez **Exclure** > choisissez **Utilisateurs et groupes** > choisissez **Sélectionner les utilisateurs exclus** > sélectionnez votre groupe **Emergency BreakGlass** .
1. Sélectionnez **Applications ou actions cloud** > sélectionnez **Toutes les applications cloud**
1. Sélectionnez **Conditions** > sélectionnez **Plateformes d’appareils** > Choisissez Configurer **Oui** > sélectionnez **Sélectionner des plateformes d’appareil** > choisissez **Windows**
1. Sélectionnez **Contrôles d’accès** > sélectionnez **Accorder l’accès** **Oui** > choisissez **Exiger que l’appareil soit marqué comme conforme** . 
1. Sélectionnez **Activer la stratégie** > **Activé**
 
Cet ensemble de stratégies garantit que vos administrateurs doivent utiliser un appareil Windows compatible, défini par Intune et WDATP. 

Les organisations doivent également envisager de bloquer les protocoles d’authentification hérités dans leurs environnements. Il existe plusieurs façons d’accomplir cette tâche. Pour plus d’informations sur le blocage des protocoles d’authentification hérités, consultez l’article, [Guide pratique : Bloquer l’authentification héritée à Microsoft Azure AD avec l’accès conditionnel](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Utiliser PowerShell pour créer des paramètres personnalisés

Vous pouvez également utiliser PowerShell pour étendre les fonctionnalités de gestion de l’hôte. Cet exemple de script configure un arrière-plan par défaut sur l’hôte. Il s’agit d’une fonctionnalité qui est également disponible via le portail Azure et les profils. L’exemple de script sert uniquement à illustrer les fonctionnalités de PowerShell.

Vous devrez peut-être configurer certains paramètres et contrôles personnalisés sur vos stations de travail sécurisées. Cet exemple modifie l’arrière-plan de la station de travail à l’aide de la capacité de PowerShell permettant d’identifier facilement l’appareil comme station de travail prête à l’emploi et sécurisée.

Le script [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) du Centre de scripts Microsoft permet à Windows de charger cette [image d’arrière-plan générique et gratuite](https://i.imgur.com/OAJ28zO.png) au démarrage.

1. Téléchargez le script sur un appareil local.
1. Mettez à jour le clientXXXX et l’emplacement de téléchargement de l’image d’arrière-plan. Dans notre exemple, nous remplaçons clientXXXX pour les arrière-plans.
1. Accédez au **portail Azure** > **Microsoft Intune** > **Configuration d’appareil** > **Scripts PowerShell** > **Ajouter** .
1. Indiquez un **nom** pour le script et spécifiez l’ **emplacement du script** .
1. Sélectionnez **Configurer** .
   1. Définissez l’option **Exécuter ce script en utilisant les informations d'identification de l'utilisateur connecté** sur **Oui** .
   1. Sélectionnez **OK** .
1. Sélectionnez **Create** (Créer).
1. Sélectionnez **Affectations** > **Sélectionner des groupes** .
   1. Ajoutez le groupe de sécurité **Stations de travail sécurisées** .
   1. Sélectionnez **Enregistrer** .

## <a name="enroll-and-validate-your-first-device"></a>Inscrire et valider votre premier appareil

1. Pour inscrire un appareil, vous avez besoin des informations suivantes :
   * **Numéro de série**  : figurant sur le châssis de l’appareil.
   * **ID de produit Windows**  : indiqué sous **Système** > **À propos** dans le menu Paramètres de Windows.
   * Vous pouvez exécuter [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) pour obtenir un fichier de hachage CSV avec toutes les informations requises pour l’inscription de l’appareil.
   
     Exécutez `Get-WindowsAutoPilotInfo – outputfile device1.csv` pour afficher les informations dans un fichier CSV que vous pouvez importer dans Intune.

     > [!NOTE]
     > Le script requiert des droits élevés. Il s’exécute en tant que script distant signé. La commande `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` permet au script de s’exécuter correctement.

   * Vous pouvez collecter ces informations en vous connectant à un appareil Windows 10 de version 1809 ou supérieure. Votre revendeur de matériel peut également fournir ces informations.
1. Dans le **portail Azure** , accédez à **Microsoft Intune** > **Inscription d’appareil** > **Inscription Windows** > **Appareils - Gérer les appareils Windows Autopilot** .
1. Sélectionnez **Importer** et choisissez votre fichier CSV.
1. Ajoutez l’appareil au groupe de sécurité **Stations de travail sécurisées** .
1. Sur l’appareil Windows 10 que vous souhaitez configurer, accédez à **Paramètres Windows** > **Mise à jour et sécurité** > **Récupération** .
   1. Choisissez **Prise en main** sous **Réinitialiser ce PC** .
   1. Suivez les invites pour réinitialiser et reconfigurer l’appareil avec les stratégies de conformité et le profil configurés.

Une fois que vous avez configuré l’appareil, effectuez une révision et vérifiez la configuration. Vérifiez que le premier appareil est correctement configuré avant de poursuivre le déploiement.

## <a name="assign-devices"></a>Attribuer des appareils

Pour affecter des utilisateurs et des appareils, vous devez mapper les [profils sélectionnés](/intune/device-profile-assign) à votre groupe de sécurité. Tous les nouveaux utilisateurs qui ont besoin d’autorisations pour le service doivent également être ajoutés au groupe de sécurité.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Utilisation de Sentinel et Windows Defender ATP pour surveiller et traiter les incidents de sécurité

La surveillance du déploiement d’une station de travail sécurisée peut être accomplie en activant [Sentinel] et en utilisant [Gestion des menaces et vulnérabilités](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt). Les instructions ne fournissent pas de repérage de menaces exhaustif, mais propose des efforts de bon sens pour surveiller et traiter des incidents de sécurité potentiels.

Nous utilisons **Azure Sentinel** pour effectuer les opérations suivantes : 

* Collecter des données à partir d’Intune, du portail Azure et d’Azure AD pour la surveillance des utilisateurs et des appareils
* Aider à examiner les activités suspectes de configuration des utilisateurs et des appareils
* Et promouvoir votre capacité à explorer les enquêtes de sécurité avec WDATP

La surveillance de Sentinel nécessite une configuration des connecteurs à vos sources de données telles qu’Azure AD.

1. Dans le **portail Azure** , accédez à **Azure Sentinel (préversion)** > sélectionnez **Ajouter**
1. Dans **Choisir un espace de travail à ajouter à Azure Sentinel** , sélectionnez **Créer un espace de travail**
1. Entrez :
   * **Espace de travail Log Analytics** : « Surveillance de station de travail sécurisée »
   * **Abonnement**  : sélectionnez votre abonnement actif
   * **Groupe de ressources**  : sélectionnez **Créer** > RG Station de travail sécurisée > **OK**
   * **Emplacement**  : sélectionnez l’emplacement le mieux adapté à votre déploiement
   * **Niveau tarifaire**  : sélectionnez **Par Go (2018)**
1. Sélectionnez **OK** .

Ensuite, nous allons connecter les sources de données de station de travail sécurisée disponibles pour la surveillance.

1. Dans le **portail Azure** , accédez à **Espace de travail Azure Sentinel** > sélectionnez l’espace de travail **Surveillance de station de travail sécurisée**
1. Sélectionnez **Connecteurs de données**
1. Choisissez **Azure Active Directory** > Ouvrir la page du connecteur > Après examen de la Condition préalable. Passez à Configuration et sélectionnez **Se connecter** pour Journaux d’activité de connexion d’Azure AD et Journaux d’audit Azure AD.
1. Choisissez **Activité Azure** > Ouvrir la page du connecteur > Après examen de la Conditions préalable. Passez à Configurer les journaux d’activité Azure > sélectionnez votre abonnement > sélectionnez **Se connecter**

À mesure que les données sont collectées par Sentinel, vous pouvez observer l’activité en sélectionnant **Portail Azure** , puis en accédant à **Vue d’ensemble d’Azure Sentinel** 

Nous allons utiliser **Windows Defender ATP (WDATP)** pour effectuer les opérations suivantes :

* Observer et surveiller en permanence les vulnérabilités et les configurations incorrectes
* Utiliser WDATP pour hiérarchiser les menaces dynamiques dans la nature
* Assurer une corrélation des vulnérabilités avec les alertes de Détection de point de terminaison et réponse (EDR)
* Utiliser le tableau de bord pour identifier les vulnérabilités au niveau de l’ordinateur lors des investigations
* Envoyer des corrections à Intune

Configurer votre [tableau de bord Windows Defender ATP](https://securitycenter.windows.com/machines). Utilisation des instructions dans [Présentation du tableau de bord Gestion des menaces et des vulnérabilités](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Surveillance de l’activité des applications à l’aide de Microsoft Monitoring Agent (MMA)
À partir de la station de travail spécialisée, AppLocker est activé pour l’analyse de l’activité des applications sur une station de travail. Pour que la surveillance soit intégrée dans votre espace de travail Log Analytics, vous devez suivre un agent et une configuration MMA. 

> [!NOTE]
> Le profil de station de travail spécialisée contient les stratégies de surveillance d’AppLocker. Le déploiement des stratégies est requis pour la surveillance de l’activité des applications sur un client

Déployer l’agent MMA avec le script PowerShell Intune

1. Télécharger le [script de configuration sur un appareil local](https://aka.ms/securedworkstationgit).
1. Mettre à jour les paramètres **$WorkSpaceID** et **$WorkSpaceKey**
1. Accédez au **portail Azure** > **Microsoft Intune** > **Configuration d’appareil** > **Scripts PowerShell** > **Ajouter** .
1. Indiquez un **nom** pour le script et spécifiez l’ **emplacement du script** .
1. Sélectionnez **Configurer** .
   1. Définissez l’option **Exécuter ce script en utilisant les informations d'identification de l'utilisateur connecté** sur **Oui** .
   1. Sélectionnez **OK** .
1. Sélectionnez **Create** (Créer).
1. Sélectionnez **Affectations** > **Sélectionner des groupes** .
   1. Ajoutez le groupe de sécurité **Stations de travail sécurisées** .
   1. Sélectionnez **Enregistrer** .

Vous devez ensuite configurer Log Analytics pour recevoir les nouveaux journaux
1. Dans le **portail Azure** , accédez à **Espace de travail log Analytics** > sélectionnez « Surveillance de station de travail sécurisée »
1. Sélectionnez **Paramètres avancés** > **Données** > **Journaux des événements Windows**
1. Dans **Collecter les événements à partir des journaux des événements suivants** 
1. Entrez :
   * « Microsoft-Windows-AppLocker/EXE et DLL » > désélectionnez **informatif**
   * « Microsoft-Windows-AppLocker/MSI et script » > désélectionnez **informatif**
   * « Microsoft-Windows-AppLocker/package App-Deployment » > désélectionnez **informatif**
   * « Microsoft-Windows-AppLocker/Packaged app-Execution » > désélectionnez **informatif**
1. Sélectionnez **Enregistrer** .

La journalisation des applications sera disponible dans votre espace de travail Log Analytics sélectionné.

## <a name="monitoring"></a>Surveillance

* Apprendre à [Détecter les menaces avec Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md)
* [Examiner les incidents avec Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
* [Configurer des réponses automatisées aux menaces dans Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
* Comprendre comment examiner votre [Score d’exposition](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Examiner les [Recommandations de sécurité](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Gérer les [corrections](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) de sécurité
* Gérer la [Détection de point de terminaison et réponse](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Surveiller les profils avec la [surveillance de profil Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Microsoft Intune](/intune/index).
* Comprendre [Azure AD](../index.yml).
* Travailler avec [Microsoft Defender - Protection avancée contre les menaces](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Découvrir [Azure Sentinel](../../sentinel/index.yml)