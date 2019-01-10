---
title: 'Azure AD Connect : Passer de la fédération à la synchronisation de hachage de mot de passe pour Azure AD | Microsoft Docs'
description: Informations sur le déplacement d’un environnement d’identité hybride de la fédération à la synchronisation de hachage de mot de passe.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a14e630c23af3e0228bf4806851f29cfab199215
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103976"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Passer de la fédération à la synchronisation de hachage de mot de passe pour Azure AD
Le document suivant fournit des conseils sur le passage d’AD FS à la synchronisation de hachage de mot de passe.

>[!NOTE]
>Un exemplaire de ce document est disponible au téléchargement [ici](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>Prérequis pour la migration 
Les prérequis suivants sont indispensables à la migration.
### <a name="update-azure-ad-connect"></a>Mettre à jour Azure AD Connect

Pour réaliser correctement les étapes de migration vers l’authentification directe, vous devez disposer au minimum d’[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0. Cette version contient des modifications significatives au niveau de la conversion de la connexion et réduit le temps global du passage de l’authentification fédérée à l’authentification cloud de quelques heures à quelques minutes.

> [!IMPORTANT]
> Des documents, outils et blogs obsolètes indiquent que la conversion des utilisateurs est une étape obligatoire lors de la conversion de domaines fédérés en domaines managés. Notez que la conversion des utilisateurs n’est plus nécessaire et que Microsoft travaille sur la mise à jour de la documentation et des outils en conséquence.

Mettez à jour Azure AD Connect vers la dernière version en suivant ces [instructions de mise à jour](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Autorisations requises pour la synchronisation du hachage de mot de passe

Azure AD Connect peut être configuré à l’aide des paramètres Express ou de l’installation personnalisée. Si vous avez utilisé l’Installation personnalisée, les [autorisations requises](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) pour la synchronisation de hachage de mot de passe peut ne pas être en place.

Le compte de service AD DS Azure AD Connect a besoin des autorisations suivantes pour pouvoir synchroniser les hachages de mot de passe.

* Répliquer les changements d’annuaires

* Répliquer les changements d’annuaire Tout

C’est le bon moment pour vérifier que ces autorisations sont en place pour tous les domaines de la forêt.

### <a name="plan-migration-method"></a>Planifier la méthode de migration

Il existe deux méthodes pour passer de l’authentification fédérée à la synchronisation de hachage de mot de passe et à l’authentification unique fluide. La méthode utilisée dépend de la configuration initiale de vos services AD FS. 



- **Option A : Utilisation d’Azure AD Connect**. Si AD FS a été initialement configuré à l’aide d’Azure AD Connect, le passage à la synchronisation du hachage de mot de passe comme méthode d’authentification utilisateur doit être effectué via l’Assistant Azure AD Connect.   
Lorsque vous utilisez Azure AD Connect, il exécute l’applet de commande Set-MsolDomainAuthentication automatiquement lorsque vous modifiez la méthode d’authentification utilisateur. Par conséquent, vous n’avez aucun contrôle sur l’annulation de la fédération de tous les domaines fédérés vérifiés de votre locataire Azure AD.

> [!NOTE]
> Pour le moment, vous ne pouvez pas éviter l’annulation de la fédération de tous les domaines de votre locataire lorsque vous définissez l’authentification utilisateur sur la synchronisation de hachage de mot de passe si AAD Connect a été initialement utilisé pour configurer AD FS pour vous.  



- **Option B : Utilisation d’Azure AD Connect avec PowerShell**. Cette méthode peut être utilisée uniquement si AD FS n’a pas initialement été configuré avec Azure AD Connect. Vous devez toujours modifier la méthode d’authentification de l’utilisateur via l’assistant Azure AD Connect. Toutefois, le cmdlet Set-MsolDomainAuthentication n’est pas automatiquement exécuté, car votre batterie de serveurs AD FS n’est pas reconnue. Par conséquent, vous bénéficiez d’un contrôle total sur les domaines convertis et l’ordre de conversion.

Pour savoir quelle méthode utiliser, effectuez les étapes de la section suivante.

#### <a name="verify-current-user-sign-in-settings"></a>Vérifier les paramètres de connexion utilisateur définis actuellement

Vérifiez vos paramètres de connexion utilisateur actuels en vous connectant au portail Azure AD [https://aad.portal.azure.com](https://aad.portal.azure.com/) avec un compte d’administrateur général.

Dans la section Connexion utilisateur, vérifiez que la fédération est activée et que l’authentification unique fluide et l’authentification directe sont désactivées. Vérifiez également que la synchronisation de mot de passe est désactivée, à moins qu’elle n’ait été activée antérieurement.

![Image 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Vérifier la configuration d’Azure AD Connect

   1. Accédez à votre serveur Azure AD Connect et lancez Azure AD Connect, puis sélectionnez Configurer. 
   2. Dans l’écran Tâches supplémentaires, sélectionnez Afficher la configuration actuelle, puis sélectionnez Suivant.</br>
   ![Image 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Dans l’écran Vérification de votre solution, prenez note de l’état de l’option Synchronisation de mot de passe.</br> 

   Si l’option Synchronisation de hachage du mot de passe est actuellement définie sur Désactivé, vous devez suivre les étapes décrites dans ce guide pour l’activer. Si l’option Synchronisation de hachage du mot de passe est actuellement définie sur Activé, vous pouvez ignorer la section [Étape 1 : Activer la synchronisation de hachage de mot de passe](#step-1--enable-password-hash-synchronization) de ce guide.
   4. Faites défiler l’écran Vérification de votre solution jusqu’à Services de fédération Active Directory (AD FS).</br>
 
   Si la configuration AD FS figure dans cette section, vous pouvez supposer qu’il a été initialement configuré via Azure AD Connect. Par conséquent, la conversion de vos domaines fédérés en domaines managés peut être pilotée via l’option « Modifier la connexion utilisateur » d’Azure AD Connect. Ce processus est détaillé dans la section **Option A : Passer de la fédération à la synchronisation de hachage de mot de passe à l’aide d’Azure AD Connect**.
   5. Si les services de fédération Active Directory (AD FS) ne figurent pas dans les paramètres actuels, vous devrez convertir manuellement les domaines fédérés en domaines managés via PowerShell. Cette procédure est détaillée dans la section **Option B : Passer de la fédération à la synchronisation de hachage de mot de passe à l’aide d’Azure AD Connect et de PowerShell**.

### <a name="document-current-federation-settings"></a>Documenter les paramètres de fédération actuels

Vous pouvez trouver le paramètre de fédération actuel en exécutant le cmdlet Get-MsolDomainFederationSettings.

La commande est la suivante :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Par exemple : 

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```
Validez tous les paramètres ayant été personnalisés dans la documentation de conception et de déploiement de la fédération, en particulier PreferredAuthenticationProtocol SupportsMfa et PromptLoginBehavior.

Vous trouverez plus de détails sur ces paramètres ci-dessous.

[Prise en charge du paramètre prompt=login des services de fédération Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Si la valeur SupportsMfa est actuellement définie sur « True », cela signifie que vous utilisez une solution MFA locale pour injecter un 2e facteur dans le flux d’authentification de l’utilisateur. Cette fonction ne sera plus disponible dans les scénarios d’authentification Azure AD. À la place, vous devrez utiliser le service cloud Azure MFA pour effectuer cette fonction. Évaluez minutieusement vos besoins d’authentification multifacteur avant de poursuivre et assurez-vous que vous comprenez comment utiliser Azure MFA, les implications en matière de gestion des licences et le processus d’inscription des utilisateurs finaux avant de convertir vos domaines. Notre guide de déploiement relatif à Azure MFA donne plus de détails, consultez-le à la page [https://aka.ms/deploymentplans](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Sauvegarde des paramètres de fédération

Même si aucune modification ne sera apportée aux autres parties de confiance de votre batterie AD FS pendant ce processus, nous vous recommandons de vérifier que vous disposez d’une sauvegarde de votre batterie de serveurs AD FS valide et actuelle qui peut être restaurée. Pour ce faire, utilisez l’outil gratuit Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Cet outil peut être utilisé pour sauvegarder et restaurer AD FS, sur une batterie de serveurs existante ou sur une nouvelle batterie.

Si vous choisissez de ne pas utiliser l’outil de restauration rapide AD FS, exportez au moins les approbations de partie de confiance de la « plateforme d’identité Microsoft Office 365 » et toutes les règles de revendication personnalisées associées que vous avez pu ajouter. Vous pouvez effectuer cette opération en utilisant l’exemple PowerShell suivant

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Considérations relatives au déploiement et utilisation d’AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Valider votre utilisation d’AD FS actuelle

Avant de passer d’une authentification fédérée à une authentification managée, examinez attentivement votre utilisation actuelle d’AD FS pour Azure AD/Office 365 et autres applications (approbations de partie de confiance). Vérifiez tout particulièrement les informations du tableau suivant :

| Si| Alors |
|-|-|
| Vous comptez conserver les services AD FS pour ces autres applications.| Vous utiliserez AD FS et Azure AD, et vous devrez penser à l’expérience de l’utilisateur final. Dans certains scénarios, les utilisateurs devront peut-être s’authentifier deux fois, une fois dans Azure AD (où ils passeront ensuite à l’authentification unique dans d’autres applications comme Office 365) et à nouveau dans toutes les applications encore liées à AD FS en tant qu’approbation de partie de confiance. |
| AD FS est extrêmement personnalisé. Il s’appuie sur des paramètres de personnalisation spécifiques du fichier onload.js qui ne peuvent pas être dupliqués dans Azure AD (par exemple, vous avez modifié l’expérience de connexion afin que les utilisateurs entrent uniquement un nom d’utilisateur au format SamAccountName et non un UPN, ou bien l’expérience de connexion est hautement personnalisée)| Vous devrez vérifier que vos spécifications de personnalisation actuelles peuvent être remplies par Azure AD avant de continuer. Reportez-vous aux sections relatives à la personnalisation AD FS pour plus d’informations et de conseils.|
| Vous bloquez les clients avec authentification héritée via AD FS.| Vous pouvez remplacer les contrôles pour bloquer les clients avec authentification héritée qui sont actuellement présents sur AD FS. Pour ce faire, associez des [contrôles d’accès conditionnel pour l’authentification héritée](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) et des [règles d’accès au client Exchange Online](https://aka.ms/EXOCAR).|
| Vous demandez aux utilisateurs d’effectuer une authentification multifacteur sur une solution de serveur MFA locale pour l’authentification à AD FS.| Vous ne pourrez pas injecter de 2e facteur d’authentification via la solution MFA locale dans le flux d’authentification d’un domaine managé, mais vous pourrez utiliser le service Azure MFA pour ce faire une fois le domaine converti. Si les utilisateurs n’utilisent pas Azure MFA aujourd’hui, cela implique une étape d’inscription de l’utilisateur final unique que vous devrez préparer et communiquer à vos utilisateurs finaux.|
| Vous utilisez actuellement des stratégies de contrôle d’accès (règles AuthZ) dans AD FS pour contrôler l’accès à Office 365.| Vous pouvez les remplacer par les [stratégies d’accès conditionnel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) et les [règles d’accès au client Exchange Online](https://aka.ms/EXOCAR) d’Azure AD qui sont équivalentes.|

### <a name="considerations-for-common-ad-fs-customizations"></a>Considérations relatives aux personnalisations AD FS courantes

#### <a name="inside-corporate-network-claim"></a>Revendication InsideCorporateNetwork

La revendication InsideCorporateNetwork est émise par AD FS si l’utilisateur qui s’authentifie se trouve dans le périmètre du réseau d’entreprise. Cette revendication peut ensuite être transmise à Azure AD et utilisée pour contourner l’authentification multifacteur en fonction de l’emplacement réseau de l’utilisateur. Consultez la rubrique [Adresses IP de confiance pour les utilisateurs fédérés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) pour savoir comment déterminer si cette option est actuellement activée dans AD FS.

La revendication InsideCorporateNetwork n’est plus disponible une fois que vos domaines passent à la synchronisation de hachage de mot de passe. Les [emplacements nommés dans Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) peuvent être utilisés pour remplacer cette fonctionnalité.

Une fois que les emplacements nommés sont configurés, toutes les stratégies d’accès conditionnel configurées de façon à inclure ou exclure les emplacements réseau « Tous les emplacements approuvés » ou « Adresses IP approuvées MFA » doivent être mises à jour pour refléter les emplacements nommés nouvellement créés.

Pour plus d’informations sur la condition d’emplacement dans l’accès conditionnel, consultez la page [Emplacements d’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Appareils avec jonction Azure AD Hybride

Joindre un appareil à Azure AD vous permet de créer des règles d’accès conditionnel qui forcent les appareils à respecter vos normes d’accès pour plus de sécurité et de conformité, et permet aux utilisateurs de se connecter à un appareil à l’aide d’un compte professionnel ou scolaire au lieu d’un compte personnel. La jonction Azure AD Hybride vous permet de joindre les appareils liés à vos domaines AD à Azure AD. Votre environnement fédéré peut avoir été configuré avec cette fonctionnalité.

Pour garantir le bon fonctionnement de la jonction hybride sur les nouveaux appareils joints à vos domaines après le passage à la synchronisation de hachage de mot de passe, Azure AD Connect doit être configuré pour synchroniser les comptes d’ordinateur Active Directory pour que les clients Windows 10 vers Azure AD. Avec les comptes d’ordinateur Windows 7 et Windows 8, la jonction hybride utilise l’authentification unique fluide pour inscrire l’ordinateur auprès d’Azure AD. Il est inutile de les synchroniser contrairement aux appareils Windows 10. Vous devrez toutefois déployer un fichier workplacejoin.exe mis à jour (via un package .msi) sur les clients de bas niveau afin qu’ils puissent s’inscrire avec l’authentification unique fluide. [Télécharger le package .msi](https://www.microsoft.com/download/details.aspx?id=53554). 

Pour plus d’informations, consultez [Comment configurer des appareils hybrides joints à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personnalisation

Votre organisation peut avoir [personnalisé vos pages de connexion ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) afin qu’elles affichent des informations plus pertinentes pour l’organisation. Dans ce cas, envisagez d’apporter des [personnalisations similaires à la page de connexion Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

S’il est possible d’apporter des personnalisations similaires, il faut s’attendre à certaines modifications visuelles. Vous devrez inclure les modifications prévues dans vos communications aux utilisateurs finaux.

> [!NOTE]
> La marque de société est une fonctionnalité disponible uniquement si vous avez acheté une licence pour l’édition Premium ou De base d’Azure AD, ou disposez d’une licence Office 365.

## <a name="planning-deployment-and-support"></a>Planification du déploiement et de la prise en charge

### <a name="plan-the-maintenance-window"></a>Planifier la fenêtre de maintenance

Tandis que le processus de conversion de domaine lui-même est relativement rapide, Azure AD est susceptible d’envoyer des demandes d’authentification à vos serveurs AD FS pendant une période maximale de 4 heures après la fin de la conversion. Pendant cette fenêtre de quatre heures, et selon les différents caches de services, il se peut que ces authentifications ne soient pas acceptées par Azure AD. Les utilisateurs reçoivent alors une erreur, car ils sont toujours en mesure de s’authentifier auprès d’AD FS, mais Azure AD n’accepte plus les jetons utilisateur puisque l’approbation de fédération a disparu.

> [!NOTE]
> Ce phénomène ne concerne que les utilisateurs qui accèdent aux services via un navigateur pendant cette période post-conversion, jusqu’à ce que le cache des services soit vidé. Les clients hérités (Exchange ActiveSync, Outlook 2010/2013) ne devraient pas être affectés, car Exchange Online conserve un cache de leurs informations d’identification pendant une certaine période. Ce cache utilisé pour authentifier de nouveau l’utilisateur en mode silencieux sans avoir à repasser par AD FS. Les informations d’identification stockées sur l’appareil pour ces clients sont utilisées pour les authentifier de nouveau en mode silencieux une fois que le cache est vidé. Par conséquent, les utilisateurs ne devraient pas recevoir d’invite de mot de passe suite à la conversion de domaine. À l’inverse, les clients avec authentification moderne (Office 2013/2016, applications IOS et Android) utilisent un jeton d’actualisation valide pour obtenir de nouveaux jetons d’accès et obtenir un accès continu aux ressources sans avoir à repasser par AD FS. Par conséquent, ils ne reçoivent aucune invite de mot de passe suite à la conversion de domaine et ils continuent de fonctionner sans configuration supplémentaire requise.

> [!IMPORTANT]
> N’arrêtez pas votre environnement AD FS ni ne supprimez l’approbation de partie de confiance Office 365 avant d’avoir vérifié que tous les utilisateurs réussissent à s’authentifier à l’aide de l’authentification cloud.

### <a name="plan-for-rollback"></a>Plan de restauration

En cas de problème majeur ne pouvant pas être résolu rapidement, vous pouvez décider de restaurer la solution de fédération. Il est important d’avoir un plan de repli en cas d’échec de votre déploiement. Si la conversion du domaine ou des utilisateurs échoue pendant le déploiement, ou si vous devez revenir à la fédération, vous devez comprendre comment limiter les dégâts d’une panne et réduire l’impact sur vos utilisateurs.

#### <a name="rolling-back"></a>Restauration

Consultez la documentation de conception et de déploiement de votre fédération pour connaître les détails spécifiques de votre déploiement. Le processus doit impliquer les étapes suivantes :

* Conversion des domaines managés en domaines fédérés à l’aide de Convert-MSOLDomainToFederated 

* Si nécessaire, configuration de règles de revendication supplémentaires.

### <a name="plan-change-communications"></a>Planifier les communications sur les modifications

Une partie importante de la planification du déploiement et de la prise en charge est de vous assurer que vos utilisateurs finaux sont informés de manière proactive des modifications et de ce qu’ils vont expérimenter ou de ce qu’ils devront faire. 

Une fois la synchronisation de hachage de mot de passe et l’authentification unique fluide déployées, l’expérience de connexion de l’utilisateur final changera lors de l’accès à Office 365 et aux autres ressources associées authentifiées via Azure AD. Les utilisateurs extérieurs au réseau voient désormais uniquement la page de connexion Azure AD. Ils ne sont plus redirigés vers la page de formulaire présentée par les serveurs proxy d’application web externes.

Plusieurs éléments doivent entrer en compte dans la planification de votre stratégie de communication. Il s’agit des actions suivantes :

* Avertir les utilisateurs des fonctionnalités à venir et publiées via :
  * E-mail et autres canaux de communication internes
  * Éléments visuels comme des affiches
  * Communications en direct (ou autres) de la part de la direction
* Déterminer la personne responsable de la personnalisation, de l’envoi des communications et du calendrier.

## <a name="implementing-your-solution"></a>Implémenter votre solution

Maintenant que vous avez planifié votre solution, vous êtes prêt à l’implémenter. L’implémentation comprend les étapes suivantes :

1. Activation de la synchronisation de hachage de mot de passe

2. Préparation pour l’authentification unique fluide

3. Modification de la méthode de connexion pour la synchronisation de hachage de mot de passe et activation de l’authentification unique fluide

### <a name="step-1--enable-password-hash-synchronization"></a>Étape 1 : Activer la synchronisation de hachage de mot de passe

La première étape de l’implémentation de cette solution consiste à activer la synchronisation de hachage de mot de passe dans l’Assistant Azure AD Connect. La synchronisation de hachage de mot de passe est une fonctionnalité facultative qui peut être activée dans les environnements utilisant la fédération sans aucun impact sur le flux d’authentification. Dans ce cas, Azure AD Connect démarre la synchronisation des hachages de mot de passe sans affecter les utilisateurs se connectant à l’aide de la fédération.

C’est pourquoi nous vous recommandons d’effectuer cette étape en amont, en préparation de la modification de la méthode de connexion des domaines. Cela vous laissera suffisamment de temps pour vérifier le bon fonctionnement de la synchronisation de hachage de mot de passe.

Pour activer la synchronisation de hachage de mot de passe :

   1. Sur le serveur Azure AD Connect, ouvrez l’Assistant et sélectionnez Configurer.
   2. Sélectionnez Personnaliser les options de synchronisation, puis cliquez sur Suivant.
   3. Dans l’écran Connexion à Azure AD, entrez un nom d’utilisateur et un mot de passe d’administrateur général.
   4. Dans l’écran Connecter vos répertoires, cliquez sur Suivant.
   5. Dans l’écran Filtrage domaine et unité organisationnelle, cliquez sur Suivant.
   6. Dans l’écran Fonctionnalités facultatives, sélectionnez la synchronisation de mot de passe, puis cliquez sur Suivant.
   ![Image 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Sélectionnez Suivant dans tous les autres écrans et choisissez Configurer sur le dernier écran.
   8. Azure AD Connect commence la synchronisation des hachages de mot de passe à la prochaine synchronisation.

Une fois la synchronisation de hachage de mot de passe activée, les hachages de mot de passe de tous les utilisateurs dans le périmètre de synchronisation d’Azure AD Connect sont ré-effectués et sont écrits sur Azure AD. En fonction du nombre d’utilisateurs, cette opération peut prendre de quelques minutes à plusieurs heures.

À des fins de planification, vous pouvez estimer qu’environ 20 000 utilisateurs peuvent être traités en 1 heure.

Pour confirmer le bon fonctionnement de la synchronisation de hachage de mot de passe, utilisez la tâche de résolution des problèmes de l’Assistant Azure AD Connect.

   1. Ouvrez une nouvelle session Windows PowerShell sur votre serveur Azure AD Connect avec l’option Exécuter en tant qu’administrateur.
   2. Exécutez la commande Set-ExecutionPolicy RemoteSigned ou Set-ExecutionPolicyUnrestricted.
   3. Lancez l’Assistant Azure AD Connect.
   4. Accédez à la page Tâches supplémentaires, sélectionnez Résoudre les problèmes, puis cliquez sur Suivant.
   5. Dans la page de résolution des problèmes, cliquez sur Lancer pour ouvrir le menu de dépannage de PowerShell.
   6. Dans le menu principal, sélectionnez Résoudre les problèmes de synchronisation du hachage de mot de passe.
   7. Dans le sous-menu, sélectionnez La synchronisation du hachage de mot de passe ne fonctionne pas du tout.

Si vous détectez des problèmes, utilisez les informations de [cet article](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) pour les résoudre.

### <a name="step-2--prepare-for-seamless-sso"></a>Étape 2 : Préparation pour l’authentification unique fluide

Pour que vos appareils utilisent l’authentification unique fluide, vous devez ajouter une URL Azure AD aux paramètres de zone intranet des utilisateurs via une stratégie de groupe dans Active Directory.

Par défaut, le navigateur calcule automatiquement la zone appropriée, Internet ou Intranet, à partir d’une URL spécifique. Par exemple, l’adresse « http://intranet.contoso.com/ » est mappée à la zone Intranet, tandis que l’adresse « http://contoso/ » est mappée à la zone Internet (car l’URL contient un point). Les navigateurs n’enverront pas de tickets Kerberos à un point de terminaison cloud, comme l’URL Azure AD, sauf si vous ajoutez explicitement l’URL à la zone intranet du navigateur.

Suivez les [étapes nécessaires pour déployer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) les modifications sur vos appareils.

> [!IMPORTANT]
> Ce changement ne modifie pas la façon dont vos utilisateurs se connectent à Azure AD. Toutefois, il est important d’appliquer cette configuration à tous vos appareils avant de passer à l’étape 3. Notez également que les utilisateurs se connectant à des appareils qui n’ont pas cette configuration doivent simplement entrer leur nom d’utilisateur et leur mot de passe pour se connecter à Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Étape 3 : Remplacement de la méthode de connexion par la synchronisation de hachage de mot de passe et activation de l’authentification unique fluide

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Option A : Passer de la fédération à la synchronisation de hachage de mot de passe à l’aide d’Azure AD Connect

Utilisez cette méthode si vous avez initialement configuré AD FS à l’aide d’Azure AD Connect. Vous ne pouvez pas utiliser cette méthode si vous n’avez pas initialement configuré AD FS à l’aide d’Azure AD Connect. Commencez par **modifier la méthode de connexion utilisateur**

   1. Sur le serveur Azure AD Connect, ouvrez l’Assistant.
   2. Sélectionnez Modifier la connexion utilisateur, puis Suivant.
   ![Image 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. Dans l’écran **Connexion à Azure AD**, entrez un nom d’utilisateur et un mot de passe d’**administrateur général**.
   4. Dans l’écran **Connexion utilisateur**, décochez le bouton radio Fédération avec AD FS et cochez Synchronisation de hachage du mot de passe, et n’oubliez pas de cocher la case Ne pas convertir les comptes utilisateur, comme il s’agit d’une étape déconseillée qui sera supprimée dans une prochaine version d’AAD Connect. Choisissez également l’option Activer l’authentification unique, puis cliquez sur **Suivant**.
   ![Image 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Depuis la version Azure AD Connect 1.1.880.0, la case à cocher Authentification unique fluide est activée par défaut.
   
   > [!IMPORTANT]
   > Vous pouvez ignorer les avertissements indiquant que la conversion des utilisateurs et la synchronisation de hachage de mot de passe complète sont des étapes obligatoires pour le passage de la fédération à l’authentification cloud. Veuillez noter que ces étapes ne sont plus obligatoires. Les futures versions d’Azure AD Connect ne proposeront pas l’option de conversion des utilisateurs. Si vous voyez toujours ces avertissements, vérifiez que vous exécutez la version la plus récente d’Azure AD Connect ainsi que la dernière version de ce guide. Pour plus d’informations, consultez la section [Mettre à jour Azure AD Connect](#_Update_Azure_AD).
   
   5. Dans l’écran Activer l’authentification unique, entrez les informations d’identification du compte d’administrateur de domaine, puis cliquez sur Suivant.
   ![Image 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Des informations d’identification d’administrateur de domaine sont requises pour activer l’authentification unique fluide, car le processus effectue des actions qui nécessitent ces autorisations élevées. Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie
   >  * Un compte d’ordinateur nommé AZUREADSSOACC (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
   >  * La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
   >  * Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.
   >  * Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie
   
   6. Dans l’écran Prêt à configurer, vérifiez que l’option « Démarrez le processus de synchronisation une fois la configuration terminée » est sélectionnée. Ensuite, sélectionnez Configurer.
   ![Image 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > À ce stade, tous vos domaines fédérés passeront à l’authentification managée, qui utilise la synchronisation de hachage de mot de passe comme méthode d’authentification.
       
   7. Ouvrez le Portail Azure AD, sélectionnez Azure Active Directory, puis Azure AD Connect.
   8. Vérifiez que la fédération est désactivée et que l’authentification unique fluide et la synchronisation de mot de passe sont activées.  
  ![Image 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Accédez à la section [Tests et étapes suivantes](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Ignorez la section Option B : Passer de la fédération à la synchronisation de hachage de mot de passe à l’aide d’Azure AD Connect et de PowerShell, car les étapes qui y sont décrites ne s’appliquent pas ici.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Option B : Passer de la fédération à la synchronisation de hachage de mot de passe à l’aide d’Azure AD Connect et de PowerShell

Utilisez cette option lorsque votre fédération n’a pas été initialement configurée à l’aide d’Azure AD Connect.

Au cours de ce processus, vous allez activer l’authentification unique fluide et convertir vos domaines fédérés en domaines managés.

   1. Sur le serveur Azure AD Connect, ouvrez l’Assistant.
   2. Sélectionnez Modifier la connexion utilisateur, puis Suivant. 
   3. Dans l’écran Connexion à Azure AD, entrez un nom d’utilisateur et un mot de passe d’administrateur général.
   4. Dans l’écran Connexion utilisateur, décochez le bouton radio Ne pas configurer et cochez à la place Synchronisation de hachage du mot de passe, sélectionnez Activer l’authentification unique, puis choisissez suivant.
   
   Avant la modification : ![Image 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Après la modification :  
   ![Image 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Depuis la version Azure AD Connect 1.1.880.0, la case à cocher Authentification unique fluide est activée par défaut.
   
   5. Dans l’écran Activer l’authentification unique, entrez les informations d’identification du compte d’administrateur de domaine, puis cliquez sur Suivant.
   
   > [!NOTE]
   > Vous devez fournir des informations d’identification d’administrateur de domaine pour activer l’authentification unique fluide, car le processus effectue des actions qui nécessitent ces autorisations élevées. Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie.
   > * Un compte d’ordinateur nommé AZUREADSSOACC (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
   > * La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
   > * Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.
   
   6. Dans l’écran Prêt à configurer, vérifiez que l’option « Démarrez le processus de synchronisation une fois la configuration terminée » est sélectionnée. Ensuite, sélectionnez Configurer.
   ![Image 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Lorsque vous sélectionnez Configurer, l’authentification unique fluide est configurée conformément à l’étape précédente. La configuration de la synchronisation de hachage de mot de passe n’est pas modifiée puisqu’elle a été activée précédemment.
   
   > [!IMPORTANT]
   > Aucune modification ne sera apportée à la façon dont les utilisateurs se connectent à ce stade.  
   
   7. Dans le Portail Azure AD, vérifiez que la fédération est toujours activée et que l’authentification unique fluide est elle aussi activée.
   ![Image 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Convertir les domaines fédérés en domaines managés

À ce stade, la fédération est toujours activée et opérationnelle pour vos domaines. Pour poursuivre le déploiement, chaque domaine fédéré doit être converti en domaine managé pour forcer l’authentification utilisateur à s’effectuer via la synchronisation de hachage de mot de passe.

> [!IMPORTANT]
> Il n’est pas obligatoire de convertir tous les domaines simultanément. Vous pouvez choisir de commencer par un domaine de test sur votre client de production ou par le domaine avec le moins d’utilisateurs.

La conversion est effectuée à l’aide du module Azure AD PowerShell.

   1. Ouvrez PowerShell et connectez-vous à Azure AD à l’aide d’un compte d’administrateur général.  
   2. Pour convertir le premier domaine, exécutez la commande suivante :  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Ouvrez le Portail Azure AD, sélectionnez Azure Active Directory, puis Azure AD Connect.
   4. Vérifiez que le domaine a été converti en domaine managé en exécutant la commande suivante :
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Tests et étapes suivantes

### <a name="test-authentication-with-phs"></a>Tester l’authentification avec synchronisation de hachage de mot de passe

Si votre client utilisait la fédération, les utilisateurs étaient redirigés de la page de connexion Azure AD vers votre environnement AD FS. Maintenant que le client est configuré pour utiliser la synchronisation de hachage de mot de passe au lieu de la fédération, les utilisateurs ne sont plus redirigés vers AD FS. Au lieu de cela, ils se connectent directement via la page de connexion Azure AD.

Ouvrez Internet Explorer en mode privé pour éviter que l’authentification unique fluide ne vous connecte automatiquement, et accédez à la page de connexion d’Office 365 ([https://portal.office.com](https://portal.office.com/)). Saisissez l’UPN de votre utilisateur et cliquez sur Suivant. Veillez à saisir l’UPN d’un utilisateur hybride ayant été synchronisé à partir de votre Active Directory local et qui utilisait auparavant l’authentification fédérée. L’utilisateur verra un écran lui permettant de saisir son nom d’utilisateur et son mot de passe.

![Image 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Image 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Une fois le mot de passe saisi, vous devriez être redirigé vers le Portail Office 365.

![Image 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Tester l’authentification unique fluide

Connectez-vous à un ordinateur joint au domaine, connecté au réseau d’entreprise. Ouvrez Internet Explorer et accédez à l’une des URL suivantes :  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (remplacez Contoso par votre domaine).

L’utilisateur est brièvement redirigé vers la page de connexion Azure AD. Le message « Tentative de connexion » s’affiche, et l’utilisateur ne devrait pas être invité à entrer un nom d’utilisateur ou un mot de passe.

![Image 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Ensuite, l’utilisateur est redirigé et connecté au panneau d’accès :

> [!NOTE]
> L’authentification unique fluide fonctionne sur les services Office 365 qui prennent en charge l’indication du domaine (par exemple, myapps.microsoft.com/contoso.com). À l’heure actuelle, le Portail Office 365 (portal.office.com) ne prend pas en charge l’indication du domaine. Par conséquent, les utilisateurs devront saisir leur UPN. Une fois qu’un UPN est saisi, l’authentification unique fluide peut récupérer le ticket Kerberos pour le compte de l’utilisateur et le connecter sans mot de passe. 

> [!TIP]
> Envisagez de déployer la [jonction Azure AD Hybride sur Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) pour une expérience d’authentification unique améliorée.

### <a name="removal-of-the-relying-party-trust"></a>Suppression de l’approbation de la partie de confiance

Lorsque vous avez vérifié le bon fonctionnement de l’authentification de tous les utilisateurs et clients via Azure AD, vous pouvez supprimer l’approbation de partie de confiance Office 365 en toute sécurité.

Si AD FS n’est pas utilisé à d’autres fins (d’autres approbations de partie de confiance ont été configurées), vous pouvez désactiver AD FS dès maintenant.

### <a name="rollback"></a>Restauration

En cas de problème majeur ne pouvant pas être résolu rapidement, vous pouvez décider de restaurer la solution de fédération.

Consultez la documentation de conception et de déploiement de votre fédération pour connaître les détails spécifiques de votre déploiement. Le processus doit impliquer les étapes suivantes :

* Conversion des domaines managés en domaines fédérés à l’aide de Convert-MSOLDomainToFederated

* Si nécessaire, configuration de règles de revendication supplémentaires.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Activer la synchronisation des mises à jour userPrincipalName

Historiquement, les mises à jour de l’attribut UserPrincipalName à l’aide du service de synchronisation du site ont été bloquées, sauf si les deux conditions suivantes sont remplies :

* L’utilisateur est géré (non fédéré).

* Aucune licence n’a été attribuée à l’utilisateur.

Pour obtenir des instructions sur la vérification ou l’activation de cette fonctionnalité, consultez l’article suivant :

[Synchroniser les mises à jour userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Résolution de problèmes

Votre équipe de support doit comprendre comment résoudre les problèmes d’authentification qui surviennent pendant ou après le passage de l’authentification fédérée à l’authentification managée. Utilisez la documentation de dépannage suivante pour aider votre équipe de support à se familiariser avec les procédures de dépannage courantes, et les mesures à prendre pour isoler et résoudre le problème.

[Résoudre les problèmes de synchronisation de hachage de mot de passe Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Résoudre les problèmes d’authentification unique fluide Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Substituer le déchiffrement Kerberos pour l’authentification unique fluide

Il est important de fréquemment substituer la clé de déchiffrement Kerberos du compte d’ordinateur AZUREADSSOACC (c’est-à-dire Azure AD) créé dans votre forêt AD locale. Nous vous recommandons vivement de substituer la clé de déchiffrement Kerberos au moins tous les 30 jours, pour vous aligner avec les modifications de mot de passe soumises par les membres du domaine Active Directory. Comme aucun appareil n’est associé à l’objet de compte d’ordinateur AZUREADSSOACC, la substitution doit être effectuée manuellement.

Suivez ces étapes sur le serveur local où vous exécutez Azure AD Connect pour initier la substitution de la clé de déchiffrement Kerberos.

[Comment puis-je substituer la clé de déchiffrement Kerberos du compte d’ordinateur AZUREADSSOACC ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Étapes suivantes

- [Principes de conception Azure AD Connect](plan-connect-design-concepts.md)
- [Choisir l’authentification appropriée](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologies prises en charge](plan-connect-design-concepts.md)
