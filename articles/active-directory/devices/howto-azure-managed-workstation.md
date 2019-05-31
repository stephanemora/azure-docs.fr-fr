---
title: Déployer Azure gérés des stations de travail - Azure Active Directory
description: Découvrez comment déployer des stations de travail gérés par Azure sécurisées pour réduire le risque de violation en raison d’une configuration incorrecte ou de compromission
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307226"
---
# <a name="deploy-a-secure-workstation"></a>Déployer une station de travail sécurisée

Maintenant que vous comprenez [pourquoi il est important de sécuriser l’accès de la station de travail ?](concept-azure-managed-workstation.md) il est temps de commencer le processus de déploiement à l’aide des outils disponibles. Ce guide utilise les profils définis pour créer une station de travail qui est plus sécurisée à partir du début.

![Déploiement d’une station de travail sécurisée](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Avant de déployer la solution, le profil que vous utilisez doit être sélectionné. Il est important de noter que vous pouvez appliquer un des profils et déplacer vers un autre, affectez le profil dans Intune en fonction de vos besoins. Plusieurs profils peuvent être utilisés simultanément dans un déploiement et affectées à l’aide des affectations de la balise ou de groupe.

| Profil | Faible | Amélioré | Élevé | Spécialisée | Sécurisé | Isolé |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Dans Azure AD | Oui | OUI | OUI | OUI | OUI | Oui |
| Gérés par Intune | Oui | OUI | OUI | OUI | OUI | Oui |
| Appareils inscrits à Azure AD | Oui |  |  |  |  | |   |
| APPAREIL joint à Azure AD |   | Oui | OUI | OUI | OUI | Oui |
| Sécurité de base Intune appliquée |   | Oui <br> (Amélioré) | Oui <br> (HighSecurity) | Oui <br> (NCSC) | Oui <br> (Sécurisé) |  N/D |
| Matériel répond aux normes de 10 Windows sécurisé |   | Oui | OUI | OUI | OUI | Oui |
| Microsoft Defender ATP est activée |   | Oui  | OUI | OUI | OUI | Oui |
| Suppression des droits d’administrateur |   |   | Oui  | OUI | OUI | Oui |
| Déploiement à l’aide de Microsoft Autopilot |   |   | Oui  | OUI | OUI | Oui |
| Applications installées uniquement par Intune |   |   |   | Oui | OUI |Oui |
| URL limitées à la liste approuvée uniquement |   |   |   | Oui | OUI |Oui |
| Internet (entrant/sortant bloqué) |   |   |   |  |  |Oui |

## <a name="license-requirements"></a>Conditions de licence :

Les concepts abordés dans ce guide suppose Microsoft 365 entreprise E5 ou une référence (SKU) équivalente. Certaines recommandations de ce guide peuvent être implémenté avec les références (SKU) inférieure. Vous trouverez des informations supplémentaires sur [gestion des licences Microsoft 365 entreprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Vous pouvez souhaiter configurer [gestion des licences par groupe](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pour vos utilisateurs automatiser l’approvisionnement de licences.

## <a name="azure-active-directory-configuration"></a>Configuration d’Azure Active Directory

Configuration de votre annuaire Azure Active Directory (Azure AD), afin de gérer vos utilisateurs, groupes et appareils pour vos stations de travail nécessite l’activation de services d’identité et de fonctionnalités avec une [compte d’administrateur](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Lorsque vous créez le compte d’administrateur de station de travail sécurisé, vous exposez le compte à votre station de travail en cours. Il est recommandé de qu'effectuer cette configuration initiale et toute la configuration globale à partir d’un appareil sans échec connu. Vous pouvez envisager les instructions pour [empêcher les infections de logiciels malveillants](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) afin de réduire le risque d’exposer tout d’abord l’expérience de première contre les attaques.

Les organisations doivent exiger une authentification multifacteur, au moins pour leurs administrateurs. Consultez [déployer MFA en nuage](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) pour obtenir des conseils d’implémentation.

### <a name="azure-ad-users-and-groups"></a>Groupes et utilisateurs azure AD

À partir du portail Azure, accédez à **Azure Active Directory** > **utilisateurs** > **nouvel utilisateur**. [Créer utilisateur de votre station de travail sécurisée](https://docs.microsoft.com/Intune/quickstart-create-user), qui sera votre administrateur de l’appareil.

* **Nom** -administrateur de la station de travail sécurisées
* **Nom d’utilisateur** - secure-ws-admin@identityitpro.com
* **Rôle d’annuaire** - **administrateur limité** et sélectionnez le rôle d’administration suivant
   * **Administrateur Intune**
* **Créer**

Nous allons créer deux groupes un pour les utilisateurs des stations de travail et un pour les stations de travail eux-mêmes. À partir du portail Azure, accédez à **Azure Active Directory** > **groupes** > **nouveau groupe**

Premier groupe pour les utilisateurs de station de travail. Vous pouvez souhaiter configurer [gestion des licences par groupe](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pour les utilisateurs dans ce groupe pour automatiser l’approvisionnement des licences aux utilisateurs.

* **Type de groupe** -sécurité
* **Nom du groupe** -sécuriser les utilisateurs de station de travail
* **Type d’appartenance** - attribué
* Ajoutez votre utilisateur d’administrateur de station de travail sécurisée au groupe
   * secure-ws-admin@identityitpro.com
* Vous pouvez ajouter tous les autres utilisateurs qui géreront les stations de travail sécurisées au groupe
* **Créer**

Deuxième groupe pour les appareils de la station de travail.

* **Type de groupe** -sécurité
* **Nom du groupe** -sécuriser les stations de travail
* **Type d’appartenance** - attribué
* **Créer**

### <a name="azure-ad-device-configuration"></a>Configuration d’appareil Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Spécifiez qui peut joindre des appareils à Azure AD

Configurer vos appareils définissant dans Active Directory pour permettre à votre groupe de sécurité administrative joindre des appareils à votre domaine. Pour configurer ce paramètre à partir du portail Azure, accédez à **Azure Active Directory** > **appareils** > **les paramètres de périphérique**. Choisissez **sélectionnés** sous **les utilisateurs peuvent joindre des appareils à Azure AD** et sélectionnez le groupe « Utilisateurs de station de travail sécurisé ».

#### <a name="removal-of-local-admin-rights"></a>Suppression des droits d’administrateur local

Dans le cadre du déploiement des utilisateurs de stations de travail de l’adresse IP virtuelle, DevOps et des stations de travail sécurisées au niveau aura sans droits d’administrateur sur leurs ordinateurs. Pour configurer ce paramètre à partir du portail Azure, accédez à **Azure Active Directory** > **appareils** > **les paramètres de périphérique**. Sélectionnez **aucun** sous **appareils joints à des administrateurs locaux supplémentaires sur Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exiger une authentification multifacteur pour joindre des appareils

Pour renforcer le processus de jonction d’appareils à Azure AD, accédez à **Azure Active Directory** > **appareils** > **paramètres du périphérique** Choisissez **Oui** sous **requièrent multi-Factor Auth pour joindre des appareils** puis choisissez **enregistrer**.

#### <a name="configure-mdm"></a>Configurer la gestion des appareils mobiles

À partir du portail Azure, accédez à **Azure Active Directory** > **mobilité (MDM et MAM)**  > **Microsoft Intune**. Modifier le paramètre **portée d’utilisateur de gestion des appareils mobiles** à **tous les** et choisissez **enregistrer** car nous permettra de n’importe quel appareil soit géré par Intune dans ce scénario. Vous trouverez plus d’informations dans l’article [Intune Quickstart : Configurer l’inscription automatique pour les appareils Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Nous allons créer des stratégies de configuration et de conformité Intune dans une étape ultérieure.

#### <a name="azure-ad-conditional-access"></a>Accès conditionnel Azure AD

Accès conditionnel Azure AD peut aider à conserver ces tâches d’administration privilégiés sur les appareils compatibles. Les utilisateurs, nous avons défini en tant que membres de la **sécuriser les utilisateurs de station de travail** groupe devront effectuer l’authentification multifacteur lors de la connexion aux applications de cloud. Nous suivrons les méthodes recommandées et exclure nos comptes d’accès d’urgence de la stratégie. Vous trouverez des informations supplémentaires dans l’article [gérer les comptes d’accès d’urgence dans Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Pour configurer l’accès conditionnel à partir du portail Azure, accédez à **Azure Active Directory** > **accès conditionnel** > **nouvelle stratégie**.

* **Nom** -stratégie nécessitant un périphérique sécurisé
* Affectations
   * **Utilisateurs et groupes**
      * Sont les suivants : **utilisateurs et groupes** : sélectionnez le **sécuriser les utilisateurs de station de travail** groupe créé précédemment
      * Exclure - **utilisateurs et groupes** -sélectionner des comptes d’accès d’urgence de votre organisation
   * **Applications cloud**
      * Sont les suivants : **toutes les applications cloud**
* Contrôles d’accès
   * **Grant** : sélectionnez **accorder l’accès** case d’option
      * **Imposer l’authentification multifacteur**
      * **Exiger que l’appareil soit marqué comme conforme**
      * Pour plusieurs contrôles - **nécessitent tous les contrôles sélectionnés**
* Activer la stratégie - **sur**

Les organisations peuvent éventuellement créer des stratégies pour bloquer les pays où les utilisateurs ne seraient pas accéder aux ressources de l’entreprise. Vous trouverez plus d’informations sur les stratégies d’accès conditionnel basé sur l’emplacement IP dans l’article [qu’est la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Configuration d’Intune

### <a name="configure-enrollment-status"></a>Configurer l’état de l’inscription

Comme indiqué dans la gestion de la chaîne logistique, en garantissant la station de travail sécurisée est un appareil propre approuvé doit de préférence lorsque vous achetez de nouveaux appareils, appareils d’être factory défini sur [Windows 10 Pro dans le mode S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), ce qui limite l’exposition et les vulnérabilités au cours de la gestion de la chaîne logistique. Une fois qu’un appareil est reçu à partir de votre fournisseur, l’appareil sera supprimé du mode S à l’aide d’Autopilot. Le guide suivant fournit des détails sur l’application le processus de transformation.

Nous voulons vérifier que les appareils sont entièrement configurés avant utilisation. Intune fournit un moyen de **bloque l’utilisation de l’appareil jusqu'à ce que toutes les applications et les profils sont installés**. 

1. À partir de la **Azure portal** accédez à :
1. **Microsoft Intune** > **l’inscription d’appareils** > **l’inscription Windows** > **Page d’état d’inscription (version préliminaire)**  >  **Par défaut** > **paramètres**.
1. Définissez **afficher la progression de l’installation application profil** à **Oui**.
1. Définissez **bloque l’utilisation de l’appareil jusqu'à ce que toutes les applications et les profils sont installés** à **Oui**.

### <a name="create-an-autopilot-deployment-profile"></a>Créer un profil de déploiement Autopilot

Après avoir créé un groupe d’appareils, vous devez créer un profil de déploiement afin que vous puissiez configurer les appareils Autopilot.

1. Dans le portail Azure Intune, choisissez **l’inscription d’appareils** > **l’inscription Windows** > **profils de déploiement**  >   **Créer un profil**.
1. Pour nom, entrez **sécuriser le profil de déploiement de station de travail**. Pour une Description, entrez **déploiement de postes de travail sécurisés**.
1. Jeu de convertir tous les appareils Autopilot sur Oui. Ce paramètre permet de s’assurer que tous les appareils dans la liste obtient inscrit auprès du service de déploiement Autopilot.  Autoriser les 48 heures pour l’inscription à traiter.
1. Pour le mode de déploiement, choisissez **déploiement automatique (version préliminaire)** . Appareils avec ce profil sont associés à l’utilisateur de l’inscription de l’appareil. Informations d’identification de l’utilisateur sont requises pour inscrire l’appareil.
1. Dans la jointure à Azure AD en tant que zone, **joints à Azure AD** doit être choisi et grisée.
1. Sélectionnez Out-of-box expérience OOBE (), de configurer l’option et laissez le champ d’autres la valeur par défaut, puis sélectionnez suivantes **Ok**:
   1. Type de compte d’utilisateur : **Standard**
1. Cliquez sur **Créer** pour créer le profil. Le profil de déploiement Autopilot est maintenant disponible à affecter aux appareils.
1. Choisissez **affectations** > **affecter à** > **groupes sélectionnés**
   1. **Sélectionnez les groupes à inclure** -sécuriser les utilisateurs de station de travail

### <a name="configure-windows-update"></a>Configurer la mise à jour de Windows

Une des choses plus importantes à qu'une organisation peut faire est de maintenir Windows 10 à jour. Afin de maintenir Windows 10 dans un état sécurisé, nous allons déployer un anneau de mise à jour pour gérer le rythme auquel les mises à jour sont appliquées aux postes de travail. Cette configuration peut être trouvée dans le **Azure portal** > **Microsoft Intune** > **mises à jour logicielles**  >  **Anneaux de mise à jour Windows 10**.

Nous allons **créer** un nouvel anneau de mise à jour avec les paramètres suivants a été remplacée par les valeurs par défaut.

* Nom - **Azure gérées les mises à jour de la station de travail**
* Canal - de maintenance **Windows Insider - rapide**
* Report de la mise à jour qualité (jours) - **3**
* Période de report de mise à jour de fonctionnalités (jours) - **3**
* Comportement de mise à jour automatique - **automatiquement installer et redémarrer sans contrôle de l’utilisateur final**
* Bloquer l’utilisateur à partir de la suspension des mises à jour Windows - **bloc**
* Exiger l’approbation de l’utilisateur à redémarrer en dehors des heures de travail - **requis**
* Autoriser l’utilisateur à redémarrer (redémarrage engagé) - **requis**
   * Transférer les utilisateurs engagés redémarrage après un redémarrage automatique (jours) - **3**
   * Rappel de redémarrage engagés de répétition (jours) - **3**
   * Définir une échéance pour en attente redémarre (jours) - **3**

Cliquez sur **créer** ensuite dans le **affectations** onglet Ajouter le **sécuriser les stations de travail** groupe comme groupe à inclure.

Vous trouverez des informations supplémentaires sur les stratégies de mise à jour de Windows dans [CSP de stratégie - mise à jour](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Intégration de Windows Defender ATP Intune

Windows Defender ATP et Microsoft Intune œuvrent ensemble pour aider à empêcher les failles de sécurité et limiter l’impact des violations. Les fonctionnalités assure la détection en temps réel. ATP fournira également notre audit détaillé de déploiement et la journalisation des appareils de point de terminaison.

Pour configurer l’intégration Windows Defender ATP Intune dans le portail Azure, accédez à **Microsoft Intune** > **la conformité des appareils** > **Windows Defender ATP** .

1. Dans l’étape 1 sous **configuration Windows Defender ATP**, cliquez sur **connecter Windows Defender ATP à Microsoft Intune dans le centre de sécurité Windows Defender**.
1. Dans le centre de sécurité Windows Defender :
   1. Sélectionnez **paramètres** > **fonctionnalités avancées**
   1. Pour **connexion Microsoft Intune**, choisissez **sur**
   1. Sélectionnez **enregistrer les préférences**
1. Une fois une connexion est établie, revenez à Intune, puis cliquez sur **Actualiser** en haut.
1. Définissez **connecter Windows version appareils 10.0.15063 et versions ultérieures à Windows Defender ATP** à **sur**.
1. **Save**

Vous trouverez des informations supplémentaires dans l’article [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Fin de renforcement du profil de station de travail

Pour mener à bien le renforcement de la solution, télécharger et exécuter le script basé sur le texte souhaité **au niveau du profil** dans le graphique suivant.

| Profil | Emplacement de téléchargement | Nom de fichier |
| --- | --- | --- |
| Sécurité faible | N/A |  N/A |
| Sécurité améliorée | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Sécurité élevée  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Spécialisée | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - SecurityBaseline.ps1 de Windows 10 (1803) |
| Conformité spécialisé * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Sécurisé | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Conformité spécialisé * est un script qui applique la configuration spécialisée fournie dans le SecurityBaseline de Windows 10 du NCSC.

Une fois le script sélectionné est correctement exécutée, les mises à jour pour les profils et les stratégies peuvent être rendues Microsoft Intune. Les scripts pour les profils étendu et Secure créent des stratégies et de profils pour vous, mais vous doivent attribuer la stratégie à votre **sécuriser les stations de travail** groupe.

* Profils de configuration d’appareil Intune créés par les scripts se trouve dans le **Azure portal** > **Microsoft Intune** > **configurationdel’appareil**  >  **Profils**.
* Vous trouverez les stratégies de conformité Intune créés par les scripts dans le **Azure portal** > **Microsoft Intune** > **la conformité des appareils**  >  **Stratégies**.

Pour passer en revue les modifications, vous pouvez également exporter les profils, et appliquer les modifications sur le fichier d’exportation comme indiqué dans le SECCON documentation basée sur et supplémentaires sécurisation renforcée qui est nécessaire.

Exporter le script en cours d’exécution les données Intune `DeviceConfiguration_Export.ps1` à partir de la [DeviceConfiguration GiuHub référentiel](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) fournira l’exportation en cours de tous les profils Intune existants.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurations supplémentaires et renforcement de la sécurité à prendre en compte

Les instructions fournies a activé une station de travail sécurisée, des contrôles supplémentaires doivent également être considérées, comme accéder d’autres navigateurs, sortantes HTTP autorisées et bloquées des sites Web et la capacité d’exécuter le script PowerShell personnalisé.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Règles entrantes et sortantes restrictives dans le fournisseur de service de configuration de pare-feu (CSP)

Une gestion supplémentaire des règles de trafic entrants et sortants peut être mis à jour pour refléter vos points de terminaison autorisées et bloquées. Comme nous continuons à renforcer la station de travail sécurisée, nous déplacer la restriction sur un refuser tout trafic entrant et sortant comme valeur par défaut et ajouter des sites autorisés pour sortant afin de refléter les sites web approuvés et courants. Vous trouverez les informations de configuration supplémentaires pour le fournisseur de service de configuration de pare-feu dans l’article [pare-feu CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Recommandations par défaut restreint sont :

* Refuser tout trafic entrant
* Refuser tout trafic sortant

Le projet Spamhaus gère une liste bon que les organisations peuvent utiliser comme point de départ pour le blocage des sites appelés [le domaine bloc liste (double)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>La gestion des applications locales

Suppression d’applications locales, y compris la suppression d’applications de productivité déplace la station de travail sécurisée à un état véritablement renforcé. Dans notre exemple, nous ajouter Chrome comme navigateur par défaut et limiter la possibilité de modifier le navigateur, y compris les plug-ins à l’aide d’Intune.

#### <a name="deploy-applications-using-intune"></a>Déployer des applications avec Intune

Dans certaines situations, les applications telles que le navigateur Google Chrome sont requises sur la station de travail sécurisée. L’exemple suivant fournit des instructions pour installer Chrome sur des appareils dans le groupe de sécurité **sécuriser les stations de travail** créé précédemment.

1. Télécharger le programme d’installation hors connexion [bundle de Chrome pour 64 bits de Windows](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Extrayez les fichiers et prenez note de l’emplacement de la `GoogleChromeStandaloneEnterprise64.msi` à installer à l’aide d’Intune
1. Dans le **Azure portal** accédez à **Microsoft Intune** > **les applications clientes** > **applications**  >  **Ajouter**
1. Sous **type d’application**, choisissez **Line-of-business**
1. Sous **fichier package d’application**, sélectionnez le `GoogleChromeStandaloneEnterprise64.msi` à partir de l’emplacement extrait et cliquez sur **OK**
1. Sous **informations sur l’application**, fournissez une description et le serveur de publication et sélectionnez **OK**
1. Cliquez sur **Ajouter**.
1. Sur le **affectations** sélectionnez **disponible pour les appareils inscrits** sous **type d’affectation**
1. Sous **groupes inclus**, ajoutez le **sécuriser les stations de travail** groupe créé précédemment
1. Cliquez sur **OK** puis **enregistrer**

Vous trouverez des conseils supplémentaires sur la configuration des paramètres de Chrome dans leur article du support technique [gérer le navigateur Chrome avec Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configuration du portail d’entreprise pour les applications personnalisées

En mode sécurisé, l’installation d’applications sera limitée au portail d’entreprise Intune. Toutefois, l’installation du portail requiert l’accès à Microsoft Store. Dans notre solution sécurisée, nous apporterons le portail disponible à tous les appareils à l’aide d’un mode hors connexion du portail d’entreprise.

Installer un Intune géré copie de la [portail d’entreprise](https://docs.microsoft.com/Intune/store-apps-company-portal-app) autorise la possibilité de transmettre les outils supplémentaires à la demande aux utilisateurs des stations de travail sécurisées.

Certaines organisations peuvent être nécessaires pour installer des applications Windows 32 bits ou les applications qui nécessitent d’autres opérations nécessaires pour déployer. Pour ces applications, le [outil de préparation du contenu de Microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) fournira un prêtes à l’emploi `.intunewin` fichier de format pour l’installation.

### <a name="setting-up-custom-settings-using-powershell"></a>Configuration des paramètres personnalisés à l’aide de PowerShell

Nous utiliserons également un exemple d’utilisation de PowerShell pour permettre l’extensibilité dans la gestion de l’ordinateur hôte. Le script configurera un arrière-plan par défaut sur l’ordinateur hôte. Cette fonctionnalité est également disponible dans les profils et sert uniquement à illustrer la fonctionnalité.

Dans la solution, il peut être nécessaire pour configurer certains paramètres et des contrôles personnalisés sur des stations de travail sécurisées. Dans notre exemple, nous allons modifier l’arrière-plan du poste de travail à l’aide de Powershell pour être en mesure d’identifier facilement l’appareil comme station de travail sécurisée prêt à être utilisé. Tandis que notre exemple utilise PowerShell pour effectuer cette tâche, il peut également être effectuée dans le portail Azure.

Notre exemple utilisera ce qui suit [image d’arrière-plan générique gratuit](https://i.imgur.com/OAJ28zO.png) et [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) à partir de la Center Scripting Microsoft pour autoriser Windows à charger l’arrière-plan sur Démarrer.

1. Téléchargez le script sur l’appareil local
1. Mettre à jour le customerXXXX et l’emplacement de téléchargement de l’arrière-plan que vous souhaitez pour utiliser dans le script pour refléter le fichier d’arrière-plan et le dossier que vous souhaitez que le déploiement à utiliser. Dans notre exemple, nous remplaçons customerXXXX sur les arrière-plans.  
1. Accédez à la **Azure portal** > **Microsoft Intune** > **configuration de l’appareil** > **PowerShell scripts** > **ajouter**
1. Fournir un **nom** pour le script et spécifiez le **emplacement du Script** où vous l’avez téléchargé à
1. Sélectionnez **Configurer**.
   1. Définissez **exécuter ce script utilisant les informations d’identification**à **Oui**
   1. Cliquez sur **OK**
1. Cliquez sur **Créer**
1. Sélectionnez **affectations** > **sélectionner des groupes**
   1. Ajouter le groupe de sécurité **sécuriser les stations de travail** créé précédemment et cliquez sur **enregistrer**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>À l’aide de la version préliminaire : Base de sécurité de gestion des appareils mobiles pour octobre 2018

Microsoft Intune a introduit la fonctionnalité de gestion de base de sécurité en fournissant aux administrateurs un moyen simple d’appliquer une méthode de sécurité de base commune. La ligne de base fournit un moyen similaire pour atteindre un verrouillé station de travail de profil étendu.

Pour la station de travail sécurisée, implémentation de que cette ligne de base n’est pas utilisé car il est en conflit avec le déploiement d’une configuration sécurisée.

|   |   |   |
| :---: | :---: | :---: |
| Par-dessus le verrouillage | Installation de périphérique | Services Bureau à distance |
| Exécution de l’application | Verrouillage de l’appareil | Gestion à distance |
| Gestion des applications | Service journal des événements | Appel de procédure distante |
| Lecture automatique | Expérience | Recherche |
| BitLocker | Exploit Guard | SmartScreen |
| Browser | Explorateur de fichiers | Configuration requise du système|
| Connectivité | Internet Explorer | Wi-Fi |
| Délégation des informations d’identification | Options de sécurité des stratégies locales | Gestionnaire de connexions de Windows |
| Informations d’identification de l’interface utilisateur | Guide de sécurité de MS | Windows Defender|
| Protection des données | MSS hérité | Espace de travail Windows Ink |
| Device Guard | Puissance | Windows PowerShell |

Vous trouverez plus d’informations sur cette fonctionnalité en préversion dans l’article [paramètres de ligne de base de sécurité Windows pour Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Inscrire et valider votre premier appareil

1. Pour inscrire votre appareil, vous avez besoin des informations suivantes :
   * **Numéro de série** - trouvée sur le châssis de l’appareil
   * **ID de produit Windows** - trouvé sous **système** > **sur** dans le menu Paramètres de Windows.
   * En cours d’exécution [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) fournira un hachage de fichier CSV pour l’inscription d’appareils avec toutes les informations requises. 
      * Exécutez `Get-WindowsAutoPilotInfo – outputfile device1.csv` pour afficher les informations dans un fichier CSV qui peut être importé dans Intune.

   > [!NOTE]
   > Le script requièrent des droits élevés et l’exécuter en tant que distant connecté. Vous pouvez utiliser la commande suivante pour autoriser le script s’exécute correctement. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Vous pouvez collecter ces informations en vous connectant à une version de Windows 10 1809 ou d’un appareil plus élevée pour collecter les informations ou votre revendeur de matériel peut fournir ces informations lorsqu’elles commandent de nouveaux appareils.
1. Collecter les informations requises et revenir à la **Azure portal**. Accédez à **Microsoft Intune** > **l’inscription d’appareils** > **l’inscription Windows** > **appareils : Gérer les appareils Windows Autopilot**, sélectionnez **importation**, puis choisissez le fichier CSV, vous avez créé ou ont été fournis.
1. Ajouter l’appareil maintenant inscrit au groupe de sécurité **sécuriser les stations de travail** créé précédemment.
1. À partir de l’appareil Windows 10 que vous souhaitez configurer, accédez à **Windows paramètres** > **mise à jour & sécurité** > **récupération**. Choisissez **prise en main** sous **réinitialiser ce PC** et suivez les invites pour réinitialiser et de reconfigurer l’appareil en utilisant les stratégies de conformité et de profil configurés.

Une fois l’appareil a été configuré, effectuer une révision et vérifiez la configuration. Vérifiez que le premier appareil est correctement configuré avant de poursuivre votre déploiement.

## <a name="assignment-and-monitoring"></a>Affectation et surveillance

Affectation d’utilisateurs et appareils nécessite le mappage de la [les profils sélectionnés](https://docs.microsoft.com/intune/device-profile-assign) à la sécurité de votre groupe et tous les nouveaux utilisateurs qui recevra l’autorisation au service seront nécessaires à ajouter au groupe de sécurité ainsi.

Surveillance des profils à peut être effectuée à l’aide de la surveillance [profils Microsoft Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Étapes suivantes

[Microsoft Intune](https://docs.microsoft.com/intune/index) documentation

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) documentation