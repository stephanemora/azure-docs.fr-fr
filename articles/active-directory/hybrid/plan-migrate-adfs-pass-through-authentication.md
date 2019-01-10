---
title: 'Azure AD Connect : Passer de la fédération à l’authentification directe pour Azure AD | Microsoft Docs'
description: Obtenez des informations sur la migration de votre environnement d’identité hybride de la fédération à l’authentification directe.
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
ms.openlocfilehash: c7d236769d5e9adca0402affc2d0eccdf78a6837
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107750"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Migrer de la fédération à l’authentification directe pour Azure AD
Le document suivant fournit des conseils pour passer des services de fédération Active Directory (AD FS) à l’authentification directe.

>[!NOTE]
>Un exemplaire de ce document est disponible au téléchargement [ici](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Conditions préalables pour l'authentification directe
Les prérequis suivants sont indispensables à la migration.
### <a name="update-azure-ad-connect"></a>Mettre à jour Azure AD Connect

Pour réaliser correctement les étapes de migration vers l’authentification directe, vous devez disposer au minimum d’[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0. Cette version contient des modifications significatives au niveau de la conversion de la connexion et réduit le temps global du passage de l’authentification fédérée à l’authentification cloud de quelques heures à quelques minutes.

> [!IMPORTANT]
> Des documents, outils et blogs obsolètes indiquent que la conversion des utilisateurs est une étape obligatoire lors de la conversion de domaines fédérés en domaines managés. **La conversion des utilisateurs** n’est plus nécessaire. Microsoft travaille sur la mise à jour de la documentation et des outils en conséquence.

Mettez à jour Azure AD Connect vers la dernière version en suivant ces [instructions de mise à jour](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planifier le numéro et le positionnement de l’agent d’authentification

Pour bénéficier de l’authentification directe, vous devez déployer des agents légers sur le serveur Azure AD Connect et sur vos serveurs Windows locaux. Pour réduire la latence, installez les agents aussi près que possible de vos contrôleurs de domaine Active Directory.

Pour la plupart des clients, deux ou trois agents d’authentification suffisent pour bénéficier d’une haute disponibilité et d’une haute capacité. Par ailleurs, un locataire peut disposer d’un maximum de 12 agents inscrits. Le premier agent est toujours installé sur le serveur AAD Connect lui-même. Consultez la page relative aux [informations sur les estimations du trafic et le guide des performances](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) pour comprendre les limitations appliquées à l’agent et les options de déploiement.

### <a name="plan-migration-method"></a>Planifier la méthode de migration

Il existe deux méthodes pour passer de l’authentification fédérée à l’authentification directe et à l’authentification unique fluide. La méthode utilisée dépend de la configuration initiale de vos services AD FS.

- **Utilisation d’Azure AD Connect**. Si AD FS a été initialement configuré à l’aide d’Azure AD Connect, le passage à l’authentification directe *doit* être effectué via l’assistant Azure AD Connect.

Lorsque vous utilisez Azure AD Connect, le cmdlet Set-MsolDomainAuthentication est exécuté automatiquement lorsque vous modifiez la méthode d’authentification de l’utilisateur. Par conséquent, vous n’avez aucun contrôle sur l’annulation de la fédération de tous les domaines fédérés vérifiés de votre locataire Azure AD.  
‎  
> [!NOTE]
> Pour le moment, vous ne pouvez pas éviter l’annulation de la fédération de tous les domaines de votre locataire lorsque vous modifiez l’authentification de l’utilisateur pour la définir sur l’authentification directe lorsque AAD Connect a été initialement utilisé pour configurer AD FS.  
‎
- **Utilisation d’Azure AD Connect avec PowerShell**. Cette méthode peut être utilisée uniquement si AD FS n’a pas initialement été configuré avec Azure AD Connect. Vous devez toujours modifier la méthode d’authentification de l’utilisateur via l’assistant Azure AD Connect. Toutefois, le cmdlet Set-MsolDomainAuthentication n’est pas automatiquement exécuté, car votre batterie de serveurs AD FS n’est pas reconnue. Par conséquent, vous bénéficiez d’un contrôle total sur les domaines convertis et l’ordre de conversion.

Pour savoir quelle méthode utiliser, effectuez les étapes de la section suivante.

#### <a name="verify-current-user-sign-in-settings"></a>Vérifier les paramètres de connexion utilisateur définis actuellement

Vérifiez vos paramètres de connexion utilisateur actuels en vous connectant au portail Azure AD [https://aad.portal.azure.com](https://aad.portal.azure.com/) avec un compte administrateur général. 

Dans la section **Connexion utilisateur**, vérifiez que l’option **Fédération** est **activée** et que les options **Authentification unique fluide** et **Authentification directe** sont **désactivées**. 

![Image 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Vérifier la configuration de la fédération

   1. Accédez à votre serveur Azure AD Connect, lancez Azure AD Connect, puis sélectionnez **Configurer**.
   2. Dans l’écran **Tâches supplémentaires**, sélectionnez **Afficher la configuration actuelle**, puis sélectionnez **Suivant**.</br>
   ![Image 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. Faites défiler l’écran **Vérification de votre solution** jusqu’à **Services de fédération Active Directory (AD FS)**.</br>
   Si la configuration AD FS figure dans cette section, vous pouvez supposer qu’AD FS a été initialement configuré via Azure AD Connect. Par conséquent, la conversion de vos domaines fédérés en domaines managés peut être pilotée via l’option **Modifier la connexion utilisateur** d’Azure AD Connect. Ce processus est détaillé dans la section **Option 1 : Configurer l’authentification directe à l’aide d’Azure AD Connect**.  
‎
   4. Si les services de fédération Active Directory (AD FS) ne figurent pas dans les paramètres actuels, vous devrez convertir manuellement les domaines fédérés en domaines managés via PowerShell. Cette procédure est détaillée dans la section **Option 2 : Passer de la fédération à l’authentification directe à l’aide d’Azure AD Connect et de PowerShell**.

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


Validez tous les paramètres susceptibles d’avoir été personnalisés selon la documentation de conception et de déploiement de la fédération, en particulier les paramètres **PreferredAuthenticationProtocol**, **SupportsMfa** et **PromptLoginBehavior**.

Vous trouverez plus de détails sur ces paramètres ci-dessous.

[Prise en charge du paramètre prompt=login des services de fédération Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Si la valeur SupportsMfa est actuellement définie sur « True », cela signifie que vous utilisez une solution MFA locale pour injecter un 2e facteur dans le flux d’authentification de l’utilisateur. Cette fonction ne sera plus disponible dans les scénarios d’authentification Azure AD. À la place, vous devrez utiliser le service cloud Azure MFA pour effectuer cette fonction. Évaluez minutieusement vos besoins d’authentification multifacteur avant de poursuivre la procédure, et assurez-vous de bien comprendre l’utilisation d’Azure MFA, les implications en matière de gestion des licences et le processus d’inscription des utilisateurs finaux avant de convertir vos domaines.

#### <a name="backup-federation-settings"></a>Sauvegarder les paramètres de fédération

Même si aucune modification ne sera apportée aux autres parties de confiance de votre batterie AD FS pendant ce processus, nous vous recommandons de vérifier que vous disposez d’une sauvegarde de votre batterie de serveurs AD FS valide et actuelle qui peut être restaurée. Pour ce faire, utilisez l’outil gratuit Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Cet outil peut être utilisé pour sauvegarder et restaurer AD FS sur une batterie de serveurs existante ou une nouvelle batterie.

Si vous préférez ne pas utiliser l’outil de restauration rapide AD FS, exportez au moins les approbations de partie de confiance de la « plateforme d’identité Microsoft Office 365 » et toutes les règles de revendication personnalisées associées que vous avez ajoutées. Vous pouvez effectuer cette opération en utilisant l’exemple PowerShell suivant

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Considérations relatives au déploiement et utilisation d’AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Valider votre utilisation d’AD FS actuelle

Avant de passer d’une authentification fédérée à une authentification managée, examinez attentivement votre utilisation actuelle d’AD FS pour Azure AD/Office 365 et autres applications (approbations de partie de confiance). Vérifiez tout particulièrement les informations du tableau suivant :

| Si| Alors |
|-|-|
| Vous comptez conserver les services AD FS pour ces autres applications.| Vous utiliserez AD FS et Azure AD, et vous devrez penser à l’expérience de l’utilisateur final. Dans certains scénarios, les utilisateurs devront peut-être s’authentifier deux fois ; une fois dans Azure AD (où ils passeront ensuite à l’authentification unique dans d’autres applications comme Office 365) et à nouveau dans toutes les applications encore liées à AD FS en tant qu’approbation de partie de confiance. |
| AD FS est extrêmement personnalisé. Il s’appuie sur des paramètres de personnalisation spécifiques du fichier onload.js, qui ne peuvent pas être dupliqués dans Azure AD (par exemple, vous avez modifié l’expérience de connexion afin que les utilisateurs entrent uniquement un nom d’utilisateur au format SamAccountName et non un UPN, ou bien l’expérience de connexion est hautement personnalisée).| Vous devrez vérifier que vos spécifications de personnalisation actuelles peuvent être respectées par Azure AD avant de continuer. Reportez-vous aux sections relatives à la personnalisation AD FS pour plus d’informations et de conseils.|
| Vous bloquez les clients avec authentification héritée via AD FS.| Vous pouvez remplacer les contrôles pour bloquer les clients avec authentification héritée qui sont actuellement présents sur AD FS. Pour ce faire, associez des [contrôles d’accès conditionnel pour l’authentification héritée](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) et des [règles d’accès au client Exchange Online](https://aka.ms/EXOCAR). |
| Vous demandez aux utilisateurs d’effectuer une authentification multifacteur sur une solution de serveur MFA locale pour l’authentification à AD FS.| Vous ne pourrez pas injecter de 2e facteur d’authentification via la solution MFA locale dans le flux d’authentification d’un domaine managé, mais vous pourrez utiliser le service Azure MFA pour ce faire une fois le domaine converti. Si, à l’heure actuelle, les utilisateurs n’utilisent pas Azure MFA, vous devrez préparer une étape d’inscription unique pour l’utilisateur final et la communiquer auprès des utilisateurs finaux. |
| Vous utilisez actuellement des stratégies de contrôle d’accès (règles AuthZ) dans AD FS pour contrôler l’accès à Office 365.| Vous pouvez les remplacer par les [stratégies d’accès conditionnel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) et les [règles d’accès au client Exchange Online](https://aka.ms/EXOCAR) d’Azure AD qui sont équivalentes.|

### <a name="considerations-for-common-ad-fs-customizations"></a>Considérations relatives aux personnalisations AD FS courantes

#### <a name="inside-corporate-network-claim"></a>Revendication InsideCorporateNetwork

La revendication InsideCorporateNetwork est émise par AD FS si l’utilisateur qui s’authentifie se trouve dans le périmètre du réseau d’entreprise. Cette revendication peut ensuite être transmise à Azure AD et utilisée pour contourner l’authentification multifacteur en fonction de l’emplacement réseau de l’utilisateur. Consultez la page [Adresses IP de confiance pour les utilisateurs fédérés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) pour obtenir des informations sur l’activation de cette option dans AD FS.

La revendication InsideCorporateNetwork n’est plus disponible une fois que vos domaines sont convertis à l’authentification directe. Les [emplacements nommés dans Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) peuvent être utilisés pour remplacer cette fonctionnalité.

Une fois que les emplacements nommés sont configurés, toutes les stratégies d’accès conditionnel configurées de façon à inclure ou exclure les emplacements réseau « Tous les emplacements approuvés » ou « Adresses IP approuvées MFA » doivent être mises à jour pour refléter les emplacements nommés nouvellement créés.

Pour plus d’informations sur la condition d’emplacement dans l’accès conditionnel, consultez la page [Emplacements d’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Appareils joints Azure AD hybrides

En joignant un appareil à Azure AD, vous pouvez créer des règles d’accès conditionnel qui forcent les appareils à respecter vos normes d’accès pour plus de sécurité et de conformité, et permettent aux utilisateurs de se connecter à un appareil à l’aide d’un compte professionnel ou scolaire au lieu d’un compte personnel. La jonction Azure AD hybride vous permet de joindre les appareils liés à vos domaines AD à Azure AD. Votre environnement fédéré peut avoir été configuré avec cette fonctionnalité.

Pour garantir le bon fonctionnement de la jonction hybride sur les nouveaux appareils joints à votre domaine après le passage à l’authentification directe, Azure AD Connect doit être configuré pour synchroniser les comptes d’ordinateur Active Directory pour clients Windows 10 vers Azure AD. Avec les comptes d’ordinateur Windows 7 et Windows 8, la jonction hybride utilise l’authentification unique fluide pour inscrire l’ordinateur auprès d’Azure AD. Il est inutile de les synchroniser contrairement aux appareils Windows 10. Vous devrez toutefois déployer un fichier workplacejoin.exe mis à jour (via un package .msi) sur les clients de bas niveau afin qu’ils puissent s’inscrire avec l’authentification unique fluide. [Téléchargez le package .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Pour plus d’informations sur cette exigence, consultez la page [Comment configurer des appareils hybrides joints à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personnalisation

Votre organisation peut avoir [personnalisé vos pages de connexion ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) afin qu’elles affichent des informations plus pertinentes pour l’organisation. Dans ce cas, envisagez d’apporter des [personnalisations similaires à la page de connexion Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

S’il est possible d’apporter des personnalisations similaires, il faut s’attendre à certaines modifications visuelles. Vous devrez inclure les modifications prévues dans vos communications aux utilisateurs finaux.

> [!NOTE]
> La marque de société est une fonctionnalité disponible uniquement si vous avez acheté une licence pour l’édition Premium ou De base d’Azure AD, ou disposez d’une licence Office 365.

## <a name="planning-for-smart-lockout"></a>Planification du verrouillage intelligent

Le verrouillage intelligent Azure AD offre une protection contre les attaques de mot de passe par force brute et empêche le verrouillage du compte Active Directory local lorsque l’authentification directe est utilisée et qu’une stratégie de groupe de verrouillage de compte est définie dans Active Directory. 

Consultez la page [Fonctionnalités et configuration du verrouillage intelligent](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout) pour en savoir plus.

## <a name="planning-deployment-and-support"></a>Planification du déploiement et assistance

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

Une partie importante de la planification du déploiement et de la prise en charge consiste à garantir que vos utilisateurs finaux sont bien informés des modifications, de leur expérience à venir ou de ce qu’ils devront faire. 

Une fois que l’authentification directe et l’authentification unique fluide sont déployées, l’expérience de connexion de l’utilisateur final change lorsqu’il accède à Office 365 et aux autres ressources associées authentifiées via Azure AD. Les utilisateurs extérieurs au réseau voient désormais uniquement la page de connexion Azure AD. Ils ne sont plus redirigés vers la page de formulaire présentée par les serveurs proxy d’application web externes.

Plusieurs éléments doivent entrer en compte dans la planification de votre stratégie de communication. Il s’agit des actions suivantes :

* Avertir les utilisateurs des fonctionnalités à venir et publiées via :
  * E-mail et autres canaux de communication internes
  * Éléments visuels comme des affiches
  * Communications en direct (ou autres) de la part de la direction
* Déterminer la personne responsable de la personnalisation, de l’envoi des communications et du calendrier.

## <a name="implementing-your-solution"></a>Implémenter votre solution

Maintenant que vous avez planifié votre solution, vous êtes prêt à l’implémenter. L’implémentation comprend les étapes suivantes :

   1. Préparation pour l’authentification unique fluide
   2. Passage à la méthode de connexion à authentification directe et activation de l’authentification unique fluide

## <a name="step-1--prepare-for-seamless-sso"></a>Étape 1 : Préparation pour l’authentification unique fluide

Pour que vos appareils utilisent l’authentification unique fluide, vous devez ajouter une URL Azure AD aux paramètres de zone intranet des utilisateurs via une stratégie de groupe dans Active Directory.

Par défaut, le navigateur calcule automatiquement la zone appropriée, Internet ou Intranet, à partir d’une URL spécifique. Par exemple, l’adresse « http://intranet.contoso.com/ » est mappée à la zone Intranet, tandis que l’adresse « http://contoso/ » est mappée à la zone Internet (car l’URL contient un point). Les navigateurs n’enverront pas de tickets Kerberos à un point de terminaison cloud, comme l’URL Azure AD, sauf si vous ajoutez explicitement l’URL à la zone intranet du navigateur.

Suivez les [étapes nécessaires pour déployer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) les modifications sur vos appareils.

> [!IMPORTANT]
> Ce changement ne modifie pas la façon dont vos utilisateurs se connectent à Azure AD. Toutefois, veillez à ce que cette configuration soit appliquée à tous vos appareils avant de passer à l’étape 3. Notez également que les utilisateurs qui se connectent sur des appareils qui ne bénéficient pas encore de cette configuration doivent simplement entrer leur nom d’utilisateur et leur mot de passe pour se connecter à Azure AD.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>Étape 2 : Remplacement de la méthode de connexion par l’authentification directe et activation de l’authentification unique fluide

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>Option A : Configuration de l’authentification directe à l’aide d’Azure AD Connect

Utilisez cette méthode si vous avez initialement configuré AD FS à l’aide d’Azure AD Connect. Vous ne pouvez pas utiliser cette méthode si vous n’avez pas initialement configuré AD FS à l’aide d’Azure AD Connect.

> [!IMPORTANT]
> Notez que si vous suivez les étapes ci-dessous, l’intégralité de vos domaines passeront du mode fédéré au mode mangé. Pour en savoir plus, consultez la section Planifier la méthode de migration.[](#_Plan_Migration_Method)

Commencez par modifier la méthode d’authentification :

   1. Sur le serveur Azure AD Connect, ouvrez l’assistant.
   2. Sélectionnez **Modifier la connexion utilisateur**, puis **Suivant**. 
   3. Dans l’écran **Connexion à Azure AD**, entrez un nom d’utilisateur et un mot de passe d’administrateur général.
   4. Dans l’écran **Connexion utilisateur**, désélectionnez le bouton radio **Fédération avec AD FS** et sélectionnez **Authentification directe**, sélectionnez **Activer l’authentification unique**, puis cliquez sur **Suivant**.
   5. Dans l’écran Activer l’authentification unique, entrez les informations d’identification du compte d’administrateur de domaine, puis cliquez sur Suivant.  

   > [!NOTE]
   > Vous devez fournir des informations d’identification d’administrateur de domaine pour activer l’authentification unique fluide, car le processus effectue des actions qui nécessitent ces autorisations élevées. Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie
   >
   > * Un compte d’ordinateur nommé AZUREADSSOACC (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
   > * La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
   > * Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.
   > * Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie.

   6. Dans l’écran **Prêt à configurer**, vérifiez que la case **Démarrez le processus de synchronisation une fois la configuration terminée** est cochée. Ensuite, sélectionnez **Configurer**.</br>
   ![image](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Ouvrez le **Portail Azure AD**, sélectionnez **Azure Active Directory**, puis **Azure AD Connect**.
   8. Vérifiez que la **fédération est désactivée** et que l'**authentification unique fluide** et l'**authentification directe** sont **activées**.</br>
   ![image](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Ensuite, vous devez déployer les méthodes d’authentification supplémentaires. Ouvrez le **Portail Azure**, accédez à **Azure Active Directory, Azure AD Connect** et cliquez sur **Authentification directe**.

Sur la page **Authentification directe**, cliquez sur le bouton Télécharger. Sur l’écran **Télécharger l’agent**, cliquez sur **Accepter les conditions et télécharger**.

Le téléchargement des agents d’authentification supplémentaires commence. Installez l’agent d’authentification secondaire sur un serveur joint à un domaine. 

> [!NOTE]
> Le premier agent est toujours installé sur le serveur Azure AD Connect lui-même, car il fait partie des modifications de la configuration apportées dans la section Connexion de l’utilisateur de l’outil Azure AD Connect. Les agents d’authentification supplémentaire doivent être installés sur un serveur distinct. Nous vous recommandons d’avoir entre 2 et 3 agents d’authentification supplémentaires disponibles. 

Exécutez l’installation de l’agent d’authentification. Pendant l’installation, vous devez fournir les informations d’identification d’un compte d’**administrateur général**.

![Image 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Image 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

Une fois que l’agent d’authentification est installé, vous pouvez revenir à la page d’intégrité de l’agent d’authentification directe afin de vérifier l’état des agents supplémentaires.


Accédez aux tests et aux étapes suivantes.

> [!IMPORTANT]
> Ignorez la section relative à l’option B : Passer de la fédération à l’authentification directe à l’aide d’Azure AD Connect et de PowerShell. Les étapes qui y sont décrites ne s’appliquent pas ici.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>Option B : Passer de la fédération à l’authentification directe à l’aide d’Azure AD Connect et de PowerShell

Utilisez cette option lorsque votre fédération n’a pas été initialement configurée à l’aide d’Azure AD Connect. Vous devez d’abord activer l’authentification directe :

   1. Sur le serveur Azure AD Connect, ouvrez l’assistant.
   2. Sélectionnez **Modifier la connexion utilisateur**, puis **Suivant**.
   3. Dans l’écran **Connexion à Azure AD**, entrez un nom d’utilisateur et un mot de passe d’administrateur général.
   4. Dans l’écran **Connexion utilisateur**, désélectionnez le bouton radio **Ne pas configurer** et sélectionnez **Authentification directe**, sélectionnez **Activer l’authentification unique**, puis cliquez sur **Suivant**.
   5. Dans l’écran **Activer l’authentification unique**, entrez les informations d’identification du compte d’administrateur de domaine, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Vous devez fournir des informations d’identification d’administrateur de domaine pour activer l’authentification unique fluide, car le processus effectue des actions qui nécessitent ces autorisations élevées. Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont utilisées uniquement pour activer la fonctionnalité, puis elles sont supprimées une fois l’opération réussie.
   >
   > * Un compte d’ordinateur nommé AZUREADSSOACC (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
   > * La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
   > * Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.

   6. Dans l’écran **Prêt à configurer**, vérifiez que la case **Démarrez le processus de synchronisation une fois la configuration terminée** est cochée. Ensuite, sélectionnez **Configurer**.</br>
   ‎![image](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   Les étapes suivantes s’appliquent lorsque vous sélectionnez Configurer :
   * Le premier agent d’authentification directe est installé
   * La fonctionnalité d’authentification directe est activée
   * L’authentification unique fluide est activée  
   7. Vérifiez que l’option **Fédération** est toujours **activée**. Les options **Authentification unique fluide** et **Authentification directe** doivent désormais être activées.
   ![Image 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Sélectionnez **Authentification directe** et vérifiez que l’état est **actif**.</br>
   
   Si l’agent d’authentification est inactif, suivez [ces étapes de dépannage](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) avant de passer aux prochaines étapes du processus de conversation de domaine. Si vous convertissez vos domaines avant d’être sûr que vos agents d’authentification directe ont bien été installés et qu’ils sont à l’état « Actif » dans le portail Azure, vous risquez de causer une panne des authentifications.  
   9. Ensuite, déployez les agents d’authentification supplémentaires. Ouvrez le **Portail Azure**, accédez à **Azure Active Directory**, **Azure AD Connect** et cliquez sur **Authentification directe**.
   10. Sur la page **Authentification directe**, cliquez sur le bouton **Télécharger**. Sur l’écran **Télécharger l’agent**, cliquez sur **Accepter les conditions et télécharger**.
   
   Le téléchargement des agents d’authentification supplémentaires commence. Installez l’agent d’authentification secondaire sur un serveur joint à un domaine.

  > [!NOTE]
  > Le premier agent est toujours installé sur le serveur Azure AD Connect lui-même, car il fait partie des modifications de la configuration apportées dans la section Connexion de l’utilisateur de l’outil Azure AD Connect. Les agents d’authentification supplémentaire doivent être installés sur un serveur distinct. Nous vous recommandons d’avoir entre 2 et 3 agents d’authentification supplémentaires disponibles.
  
   11. Exécutez l’installation de l’agent d’authentification. Pendant l’installation, vous devez fournir les informations d’identification d’un compte d’**administrateur général**.</br>
   ![Image 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Image 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. Une fois que l’agent d’authentification est installé, vous pouvez revenir à la page d’intégrité de l’agent d’authentification directe afin de vérifier l’état des agents supplémentaires.

À ce stade, la fédération est toujours activée et opérationnelle pour vos domaines. Pour poursuivre le déploiement, chaque domaine doit être converti du mode fédéré au mode managé afin que l’authentification directe commence à traiter les demandes d’authentification du domaine.

Vous n’êtes pas obligé de convertir tous les domaines en même temps. Vous pouvez choisir de commencer par un domaine test sur votre locataire de production ou par le domaine comptant le moins d’utilisateurs.

La conversion est effectuée à l’aide du module Azure AD PowerShell.

   1. Ouvrez **PowerShell** et connectez-vous à Azure AD à l’aide d’un compte d’**administrateur général**.
   2. Pour convertir le premier domaine, exécutez la commande suivante :
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Ouvrez le **Portail Azure AD**, sélectionnez **Azure Active Directory**, puis **Azure AD Connect**.  
   4. Une fois que vous avez converti l'intégralité de vos domaines fédérés, vérifiez que la **fédération est désactivée** et que l'**authentification unique fluide** et l'**authentification directe** sont **activées**.</br>
   ![image](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Tests et étapes suivantes

### <a name="test-pass-through-authentication"></a>Tester l’authentification directe 

Si votre client utilisait la fédération, les utilisateurs étaient redirigés de la page de connexion Azure AD vers votre environnement AD FS. Maintenant que le client est configuré pour utiliser l’authentification directe au lieu de la fédération, les utilisateurs ne sont plus redirigés vers AD FS, mais se connectent directement via la page de connexion Azure AD.

Ouvrez Internet Explorer en mode privé pour éviter que l’authentification unique fluide ne vous connecte automatiquement, et accédez à la page de connexion d’Office 365 ([https://portal.office.com](https://portal.office.com/)). Saisissez l’**UPN** de votre utilisateur et cliquez sur **Suivant**. Veillez à saisir l’UPN d’un utilisateur hybride ayant été synchronisé à partir de votre Active Directory local et qui utilisait auparavant l’authentification fédérée. L’utilisateur verra un écran lui permettant de saisir son nom d’utilisateur et son mot de passe.

![Image 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Image 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

Une fois que vous avez saisi le mot de passe, vous devriez être redirigé vers le Portail Office 365.

![Image 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Tester l’authentification unique fluide

   1. Connectez-vous à un ordinateur joint au domaine qui est connecté au réseau d’entreprise. 
   2. Ouvrez **Internet Explorer** ou **Chrome** et accédez à l’une des URL suivantes :   
      ‎  
      [https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (remplacez Contoso par votre domaine).

      L’utilisateur est brièvement redirigé vers la page de connexion Azure AD. Le message « Tentative de connexion » s’affiche, et l’utilisateur n’est pas invité à entrer son nom d’utilisateur ni mot de passe.</br>
   ![Image 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Ensuite, l’utilisateur est redirigé vers le panneau d’accès, puis connecté :

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

[Synchroniser les mises à jour userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Substituer la clé de déchiffrement Kerberos pour l’authentification unique fluide

Il est important de fréquemment substituer la clé de déchiffrement Kerberos du compte d’ordinateur AZUREADSSOACC (c’est-à-dire Azure AD) créé dans votre forêt AD locale. Nous vous recommandons vivement de substituer la clé de déchiffrement Kerberos au moins tous les 30 jours, pour vous aligner avec les modifications de mot de passe soumises par les membres du domaine Active Directory. Comme aucun appareil n’est associé à l’objet de compte d’ordinateur AZUREADSSOACC, la substitution doit être effectuée manuellement.

Suivez ces étapes sur le serveur local où vous exécutez Azure AD Connect pour initier la substitution de la clé de déchiffrement Kerberos.

[Comment puis-je substituer la clé de déchiffrement Kerberos du compte d’ordinateur AZUREADSSOACC ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Surveillance et journalisation

Les serveurs qui exécutent les agents d’authentification doivent être surveillés afin d’assurer la disponibilité de la solution. En plus des compteurs de performances générales du serveur, les agents d’authentification exposent les objets de performance qui peuvent être utilisés pour comprendre les erreurs et les statistiques associés à l’authentification.

Les agents d’authentification consignent les opérations dans les journaux des événements Windows sous « Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin ».

Les journaux de dépannage peuvent être activés si nécessaire.

Consultez la page [Surveillance et journalisation](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

- [Principes de conception Azure AD Connect](plan-connect-design-concepts.md)
- [Choisir l’authentification appropriée](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologies prises en charge](plan-connect-design-concepts.md)
