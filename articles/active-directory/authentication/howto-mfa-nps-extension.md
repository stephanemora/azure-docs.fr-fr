---
title: Fournir les fonctionnalités Azure MFA en utilisant NPS | Azure Active Directory
description: Ajouter des fonctionnalités de vérification en deux étapes basées sur le cloud à votre infrastructure d’authentification existante
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f07efc8fd77f1c34ef96d31f55089726942d05df
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871222"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Intégrer votre infrastructure NPS existante dans Azure Multi-Factor Authentication

L’extension de serveur NPS (Network Policy Server) pour Azure MFA permet d’ajouter des fonctionnalités de MFA basées sur le cloud à votre infrastructure d’authentification à l’aide de vos serveurs existants. Avec l’extension NPS, vous pouvez ajouter des vérifications basées sur des appels téléphoniques, des SMS ou des applications mobiles à votre flux d’authentification existant sans avoir à installer, configurer et gérer les nouveaux serveurs. 

Cette extension a été créée pour les organisations qui souhaitent protéger des connexions VPN sans déployer le serveur Azure MFA. L’extension NPS joue le rôle d’adaptateur entre RADIUS et Azure MFA sur le cloud pour fournir un second facteur d’authentification pour les utilisateurs fédérés ou synchronisés.

Lorsque vous utilisez l’extension NPS pour Azure MFA, le flux d’authentification inclut les composants suivants : 

1. **Le serveur NAS/VPN** reçoit les demandes des clients VPN et les convertit en demandes RADIUS à des serveurs NPS. 
2. **Le serveur NPS** se connecte à Active Directory pour procéder à l’authentification principale pour les demandes RADIUS et, en cas de réussite, les transmet à toutes les extensions installées.  
3. **L’extension NPS** déclenche une demande destinée à Azure MFA pour l’authentification secondaire. Une fois que l’extension reçoit la réponse, et si la demande MFA réussit, elle termine la demande d’authentification en fournissant au serveur NPS des jetons de sécurité qui incluent une revendication MFA, émise par Azure STS.  
4. **Azure MFA** communique avec Azure Active Directory pour récupérer les informations de l’utilisateur et procède à l’authentification secondaire à l’aide d’une méthode de vérification configurée par l’utilisateur.

Le diagramme suivant illustre ce flux de demande d’authentification de niveau supérieur : 

![Diagramme du flux d’authentification](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planifier votre déploiement

L’extension NPS gère automatiquement la redondance, vous n’avez pas besoin d’une configuration spéciale.

Vous pouvez créer autant de serveurs NPS compatibles avec Azure MFA que vous le souhaitez. Si vous installez plusieurs serveurs, vous devez utiliser un certificat client différent pour chacun d'entre eux. La création d’un certificat pour chaque serveur signifie que vous pouvez mettre à jour chaque certificat individuellement et que vous n’avez pas à craindre une interruption de service sur tous vos serveurs.

Comme les serveurs VPN acheminent les demandes d’authentification, ils doivent connaître les nouveaux serveurs NPS compatibles avec Azure MFA.

## <a name="prerequisites"></a>Prérequis

L’extension NPS est conçue pour fonctionner avec votre infrastructure existante. Vérifiez que les conditions préalables suivantes sont remplies avant de commencer.

### <a name="licenses"></a>Licences

L’extension NPS pour Azure MFA est disponible pour les clients disposant de [licences pour Azure MFA](multi-factor-authentication.md) (incluses dans Azure AD Premium, EMS ou une licence autonome MFA). Les licences basées sur la consommation pour Azure MFA, telles que les licences par utilisateur ou par authentification, ne sont pas compatibles avec l’extension de serveur NPS. 

### <a name="software"></a>Logiciel

Windows Server 2008 R2 SP1 ou version ultérieure.

### <a name="libraries"></a>Bibliothèques

Ces bibliothèques sont installées automatiquement avec l’extension.

- [Visual C++ Redistributable Packages pour Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Module Microsoft Azure Active Directory pour Windows PowerShell version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Le module Microsoft Azure Active Directory pour Windows PowerShell est installé, s’il n’est pas déjà présent, via un script de configuration que vous exécutez en tant que partie du processus de configuration. Il est inutile d’installer ce module en avance s’il n’est pas déjà installé.

### <a name="azure-active-directory"></a>Azure Active Directory

Tous les utilisateurs de l’extension NPS doivent être synchronisés avec Azure Active Directory à l’aide d’Azure AD Connect et doivent être inscrits pour l’authentification MFA.

Lorsque vous installez l’extension, vous devez disposer de l’ID de répertoire et des informations d’identification de l’administrateur pour votre client Azure AD. Vous trouverez votre ID de répertoire dans le [portail Azure](https://portal.azure.com). Connectez-vous en tant qu’administrateur. Recherchez et sélectionnez **Azure Active Directory**, puis sélectionnez **Propriétés**. Copiez le GUID dans la zone **ID répertoire** et enregistrez. Vous utilisez ce GUID comme ID client lors de l’installation de l’extension NPS.

![Identification de votre ID de répertoire dans les propriétés Azure Active Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Configuration requise pour le réseau

Le serveur NPS (Network Policy Server) doit pouvoir communiquer avec les URL suivantes sur les ports 80 et 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

En outre, la connectivité aux adresses URL suivantes est nécessaire pour terminer la [configuration de l’adaptateur à l’aide du script PowerShell fourni](#run-the-powershell-script).

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Préparation de votre environnement

Avant d’installer l’extension NPS, vous souhaitez préparer votre environnement pour qu’il gère le trafic d’authentification.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Activer le rôle NPS sur un serveur joint à un domaine

Le serveur NPS se connecte à Azure Active Directory et authentifie les demandes MFA. Choisissez un serveur pour ce rôle. Nous vous recommandons de choisir un serveur qui ne gère pas les demandes provenant d’autres services, car l’extension NPS génère des erreurs pour toutes les demandes qui ne sont pas RADIUS. Vous devez configurer le serveur NPS en tant que serveur d’authentification principal et secondaire pour votre environnement ; il ne peut pas rediriger de demandes RADIUS par proxy vers un autre serveur.

1. Sur votre serveur, ouvrez l’**Assistant d’ajout de rôles et fonctionnalités** à partir du menu Démarrage rapide du Gestionnaire de serveur.
2. Choisissez **Installation basée sur un rôle ou une fonctionnalité** pour votre type d’installation.
3. Sélectionnez le rôle de serveur **Services de stratégie et d’accès réseau**. Une fenêtre peut s’afficher pour vous informer des fonctionnalités requises pour exécuter ce rôle.
4. Suivez les instructions de l’Assistant jusqu'à la page de confirmation. Sélectionnez **Installer**.

Maintenant que vous avez un serveur désigné pour NPS, vous devez également configurer ce serveur pour traiter les demandes RADIUS entrantes à partir de la solution VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurer votre solution VPN pour communiquer avec le serveur NPS

En fonction de la solution VPN utilisée, la procédure servant à configurer votre stratégie d’authentification RADIUS n’est pas la même. Configurez cette stratégie pour qu’elle pointe vers votre serveur NPS RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronisation des utilisateurs de domaine dans le cloud

Même si cette étape peut déjà avoir été effectuée sur votre client, il est judicieux de vérifier qu’Azure AD Connect a récemment synchronisé vos bases de données.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Connexion Azure AD**
3. Vérifiez que votre état de synchronisation est **Activée** et que la dernière synchronisation a eu lieu il y a moins d’une heure.

Si vous avez besoin de lancer un nouveau cycle de synchronisations, suivez les instructions figurant dans [ Planificateur de synchronisation Azure AD Connect](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Déterminer les méthodes d’authentification que vos utilisateurs peuvent employer

Il existe deux facteurs qui affectent les méthodes d’authentification disponibles avec un déploiement d’extension NPS :

1. L’algorithme de chiffrement de mot de passe utilisé entre le client RADIUS (VPN, Netscaler server ou autre) et les serveurs NPS.
   - **PAP** prend en charge toutes les méthodes d’authentification de l’authentification multifacteur Azure dans le cloud : appel téléphonique, message texte à sens unique, notification d’application mobile, jetons matériels OATH et code de vérification d’application mobile.
   - **CHAPv2** et **EAP** prennent en charge l’appel téléphonique et la notification d’application mobile.

      > [!NOTE]
      > Lorsque vous déployez l’extension NPS, utilisez ces facteurs pour déterminer quelles méthodes sont disponibles pour vos utilisateurs. Si votre point d’accès sans fil compatible 802.1X prend en charge PAP, mais que le client UX ne dispose pas des champs d’entrée pour un code de vérification, l’appel téléphonique et la notification d’application mobile sont les deux options prises en charge.
      >
      > En outre, si votre client VPN UX prend en charge le champ de saisie et que vous avez configuré la stratégie d'accès réseau, l'authentification peut aboutir, mais aucun des attributs RADIUS configurés dans la stratégie réseau ne sera appliqué à l'appareil d'accès réseau (tel qu'un serveur RRAS), ni au client VPN. Par conséquent, le client VPN peut disposer d’un accès plus ou moins étendu que voulu, ou n’avoir aucun accès du tout.
      >

2. Les méthodes d’entrée que l’application cliente (VPN, Netscaler server ou autre) peut gérer. Par exemple, le client VPN dispose-t-il de moyens permettant d’autoriser l’utilisateur à taper un code de vérification à partir d’un texte ou d’une application mobile ?

Vous pouvez [désactiver les méthodes d’authentification non prises en charge](howto-mfa-mfasettings.md#verification-methods) dans Azure.

### <a name="register-users-for-mfa"></a>Inscrire des utilisateurs pour l’authentification MFA

Avant de déployer et d’utiliser l’extension NPS, les utilisateurs devant effectuer la vérification en deux étapes doivent être inscrits pour l’authentification MFA. De manière plus immédiate, pour tester l’extension lorsque vous la déployez, il vous faut au moins un compte de test entièrement inscrit pour Multi-Factor Authentication.

Pour créer un compte de test, suivez la procédure suivante :

1. Connectez-vous à [https://aka.ms/mfasetup](https://aka.ms/mfasetup) avec un compte de test.
2. Suivez les instructions pour configurer une méthode de vérification.
3. [Créez une stratégie d’accès conditionnel](howto-mfa-getstarted.md#create-conditional-access-policy) afin de demander l’authentification multifacteur pour le compte de test.

> [!IMPORTANT]
>
> Vérifiez que les utilisateurs sont bien inscrits à Azure Multi-Factor Authentication. S’ils ne disposent que d’une inscription à la réinitialisation de mot de passe en libre-service (SSPR), *StrongAuthenticationMethods* est activé pour leur compte. Azure Multi-Factor Authentication est appliqué lorsque *StrongAuthenticationMethods* est configuré, même avec une simple inscription à SSPR.
>
> L’inscription de sécurité combinée peut être activée pour configurer SSPR et Azure Multi-Factor Authentication en même temps. Pour plus d’informations, consultez [Activation de l’inscription combinée des informations de sécurité dans Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Vous pouvez également [obliger les utilisateurs à se réinscrire à certaines méthodes d’authentification](howto-mfa-userdevicesettings.md#manage-user-authentication-options) s’ils n’avaient activé que SSPR.

## <a name="install-the-nps-extension"></a>Installer l’extension NPS

> [!IMPORTANT]
> Installez l’extension NPS sur un serveur autre que le point d’accès VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Télécharger et installer l’extension NPS pour Azure MFA

1. [Téléchargez l’extension NPS](https://aka.ms/npsmfa) à partir du Centre de téléchargement Microsoft.
2. Copiez le fichier binaire sur le serveur NPS (Network Policy Server) que vous souhaitez configurer.
3. Exécutez le fichier *setup.exe* et suivez les instructions d’installation. Si vous rencontrez des erreurs, vérifiez que les deux bibliothèques de la section Configuration requise ont été installées avec succès.

#### <a name="upgrade-the-nps-extension"></a>Mettre à niveau l’extension NPS

Lors de la mise à niveau d’une extension NPS déjà installée, procédez comme suit pour éviter la réinitialisation du serveur sous-jacent :

1. Désinstaller la version existante
1. Exécuter le nouveau programme d’installation
1. Redémarrer le service du serveur NPS (Network Policy Server) (IAS)

### <a name="run-the-powershell-script"></a>Exécution du script PowerShell

Le programme d’installation crée un script PowerShell à cet emplacement : `C:\Program Files\Microsoft\AzureMfa\Config` (C:\ étant le lecteur d’installation). Ce script PowerShell effectue les opérations suivantes à chaque exécution :

- Vous pouvez créer un certificat auto-signé.
- Associer la clé publique du certificat au principal du service sur Azure AD.
- Stocker le certificat dans le magasin de certificats de l’ordinateur local.
- Accorder à l’utilisateur réseau un accès à la clé privée du certificat.
- Redémarrer le serveur NPS.

À moins que vous ne souhaitiez utiliser vos propres certificats (au lieu des certificats auto-signés générés par le script PowerShell), exécutez le script PowerShell pour terminer l’installation. Si vous installez l’extension sur plusieurs serveurs, chacun d’eux doit avoir son propre certificat.

1. Exécutez Windows PowerShell en tant qu’administrateur.
2. Remplacez les répertoires.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Exécutez le script PowerShell créé par le programme d’installation.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Connectez-vous à Azure AD en tant qu’administrateur.
5. PowerShell vous invite à entrer votre ID client. Utilisez l’ID de répertoire GUID que vous avez copié à partir du portail Azure dans la section Configuration requise.
6. PowerShell affiche un message de réussite une fois le script terminé.  

Répétez ces étapes sur les serveurs NPS supplémentaires que vous souhaitez configurer pour l’équilibrage de charge.

Si votre certificat d’ordinateur précédent est arrivé à expiration, et qu’un nouveau certificat a été généré, vous devez supprimer tous les certificats arrivés à expiration. En effet, des certificats arrivés à expiration peuvent provoquer des problèmes lors du démarrage de l’extension NPS.

> [!NOTE]
> Si vous utilisez vos propres certificats au lieu de générer des certificats avec le script PowerShell, vous devez veiller à ce qu’ils respectent la convention de nommage du serveur NPS. Le nom de l’objet doit être **CN=\<ID_locataire\>,OU= Extension NPS Microsoft**. 

### <a name="microsoft-azure-government-additional-steps"></a>Microsoft Azure Government - Étapes supplémentaires

Pour les clients qui utilisent le cloud Azure Government, les étapes de configuration supplémentaires suivantes sont demandées sur chaque serveur NPS.

> [!IMPORTANT]
> Configurez ces paramètres de Registre uniquement si vous êtes client Azure Government.

1. Si vous êtes client Azure Government, ouvrez l’**Éditeur du Registre** sur le serveur NPS.
1. Accédez à `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Définissez les valeurs de clé suivantes :

    | Clé de Registre       | Valeur |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Répétez les deux étapes précédentes afin de définir les valeurs de clé de Registre pour chaque serveur NPS.
1. Redémarrez le service NPS pour chaque serveur NPS.

    Pour un impact minimal, retirez un par un les différents serveurs NPS de la rotation NLB (équilibrage de la charge réseau) et attendez que toutes les connexions soient drainées.

### <a name="certificate-rollover"></a>Substitution de certificat

Dans la version 1.0.1.32 de l’extension NPS, la lecture de plusieurs certificats est désormais prise en charge. Cette fonctionnalité peut contribuer à faciliter la propagation des mises à jour de certificats avant leur arrivée à expiration. Si votre organisation exécute une version antérieure de l’extension NPS, vous devez la mettre à niveau vers la version 1.0.1.32, ou une version ultérieure.

Les certificats créés par le script `AzureMfaNpsExtnConfigSetup.ps1` sont valides pendant 2 ans. Les départements informatiques doivent surveiller les certificats, afin de gérer leur arrivée à expiration. Les certificats de l’extension NPS sont placés dans le magasin de certificats de l’ordinateur local, sous Personnel, et sont envoyés à l’ID de locataire fourni au script.

Lorsque la date d’expiration d’un certificat approche, un nouveau certificat doit être généré pour le remplacer.  Pour cela, vous devez exécuter à nouveau le script `AzureMfaNpsExtnConfigSetup.ps1`, en indiquant le même ID de locataire à l’invite. Ce processus doit être répété sur chaque serveur NPS au sein de votre environnement.

## <a name="configure-your-nps-extension"></a>Configurer votre extension NPS

Cette section comprend des considérations relatives à la conception ainsi que des suggestions visant à garantir la réussite des déploiements d’extension NPS.

### <a name="configuration-limitations"></a>Limites de configuration

- L’extension NPS pour Azure MFA n’inclut pas les outils permettant de migrer les utilisateurs et les paramètres du serveur MFA vers le cloud. De ce fait, nous vous conseillons d’utiliser l’extension pour les nouveaux déploiements, plutôt que pour les déploiements existants. Si vous utilisez l’extension sur un déploiement existant, vos utilisateurs doivent suivre de nouveau la procédure de vérification pour remplir leurs détails MFA dans le cloud.  
- L’extension NPS utilise l’UPN de l’annuaire Active Directory local pour identifier l’utilisateur sur Azure MFA afin de procéder à l’authentification secondaire. L’extension peut être configurée pour utiliser un identificateur autre que l’UPN (un ID de connexion alternatif ou un champ Active Directory personnalisé, par exemple). Pour plus d’informations, consultez l’article [Options de configuration avancée de l’extension de serveur NPS pour l’authentification multifacteur](howto-mfa-nps-extension-advanced.md).
- Tous les protocoles de chiffrement ne prennent pas en charge toutes les méthodes de vérification.
   - **PAP** prend en charge l’appel téléphonique, le message texte à sens unique, la notification de l’application mobile et le code de vérification de l’application mobile
   - **CHAPv2** et **EAP** prennent en charge l’appel téléphonique et la notification d’application mobile

### <a name="control-radius-clients-that-require-mfa"></a>Contrôler les clients RADIUS nécessitant l’authentification MFA

Une fois que vous activez l’authentification MFA pour un client RADIUS à l’aide de l’extension NPS, toutes les authentifications de ce client doivent utiliser la méthode MFA. Si vous souhaitez activer l’authentification MFA pour certains clients RADIUS et d’autres non, vous pouvez configurer deux serveurs NPS et installer l’extension sur un seul d’entre eux. Configurez les clients RADIUS pour lesquels vous souhaitez exiger l’authentification MFA de sorte qu’ils envoient des demandes au serveur NPS configuré avec l’extension, et les autres clients RADIUS pour qu’ils les envoient au serveur NPS non configuré avec l’extension.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Anticiper la présence d’utilisateurs qui ne sont pas inscrits pour l’authentification MFA

Si vous avez des utilisateurs qui ne sont pas inscrits pour l’authentification MFA, vous pouvez déterminer ce qui se passe lorsqu’ils tentent de s’authentifier. Utilisez le paramètre de Registre *REQUIRE_USER_MATCH* dans le chemin d’accès au Registre *HKLM\Software\Microsoft\AzureMFA* pour contrôler le comportement de la fonctionnalité. Ce paramètre ne dispose que d’une seule option de configuration :

| Clé | Valeur | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Non défini (équivaut à TRUE) |

L’objectif de ce paramètre est de déterminer l’action à exécuter lorsqu’un utilisateur n’est pas inscrit pour l’authentification MFA. Lorsque la clé n’existe pas, n’est pas définie ou est définie sur TRUE, et si l’utilisateur n’est pas inscrit, l’extension échoue à la demande d’authentification MFA. Lorsque la clé est définie sur FALSE et que l’utilisateur n’est pas inscrit, l’authentification s’effectue sans procéder à l’authentification MFA. Si un utilisateur est inscrit dans MFA, il doit s’authentifier avec MFA même si REQUIRE_USER_MATCH est défini sur FALSE.

Vous pouvez choisir de créer cette clé et de lui affecter la valeur FALSE pour vos utilisateurs qui sont en cours d’intégration et ne sont peut-être pas encore inscrits pour l’authentification Azure MFA. Toutefois, étant donné que la définition de la clé permet aux utilisateurs qui ne sont pas inscrits pour l’authentification MFA de se connecter, vous devez supprimer cette clé avant de passer en production.

## <a name="troubleshooting"></a>Dépannage

### <a name="nps-extension-health-check-script"></a>Script de vérification de l’intégrité de l’extension NPS

Le script suivant permet de vérifier l’intégrité de base lors de la résolution des problèmes relatifs à l’extension NPS.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Comment vérifier que le certificat client est installé comme prévu ?

Recherchez le certificat auto-signé créé par le programme d’installation dans le magasin de certificats et vérifiez que la clé privée dispose d’autorisations accordées à l’utilisateur **NETWORK SERVICE**. Le certificat porte un nom d’objet de type **CN \<tenantid\>, UO = Extension Microsoft NPS**

Les certificats auto-signés générés par le script *AzureMfaNpsExtnConfigSetup.ps1* script ont également une durée de vie de deux ans. Lorsque vous vérifiez que le certificat est installé, assurez-vous également qu’il n’est pas arrivé à expiration.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Comment vérifier que mon certificat client est associé à mon client dans Azure Active Directory ?

Ouvrez une invite de commandes PowerShell et exécutez les commandes suivantes :

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Ces commandes impriment tous les certificats qui associent votre client à votre instance de l’extension NPS dans votre session PowerShell. Recherchez votre certificat en exportant votre certificat client en tant que fichier « Codé à base 64 X.509 (.cer) » sans la clé privée et comparez-le à la liste de PowerShell.

La commande suivante crée un fichier nommé « npscertificate » sur votre lecteur « C: » au format .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Une fois que vous exécutez cette commande, accédez à votre lecteur C, recherchez le fichier et double-cliquez dessus. Accédez aux détails et faites défiler jusqu'à « empreinte », comparez avec l’empreinte du certificat installé sur le serveur. Les empreintes des certificats doivent correspondre.

Les horodatages Valid-From (Valide à partir de) et Valid-Until (Valide jusqu’à), qui sont dans un format explicite, peuvent être utilisés pour filtrer les certificats de toute évidence non appropriés si la commande en renvoie plusieurs.

---

### <a name="why-cannot-i-sign-in"></a>Pourquoi ne puis-je pas me connecter ?

Vérifiez que votre mot de passe n’a pas expiré. L’Extension NPS ne prend pas en charge la variation des mots de passe dans le cadre du flux de travail de connexion. Contactez le service informatique de votre entreprise pour obtenir de l’aide.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Pourquoi mes demandes échouent-elles avec une erreur de jeton ADAL ?

Cette erreur peut être due à plusieurs raisons. Suivez ces étapes pour résoudre le problème :

1. Redémarrez votre serveur NPS.
2. Vérifiez que le certificat client est installé comme prévu.
3. Vérifiez que le certificat est associé à votre client sur Azure AD.
4. Vérifiez que `https://login.microsoftonline.com/` est accessible depuis le serveur exécutant l’extension.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Pourquoi l’authentification échoue-t-elle avec une erreur dans les journaux d’activité HTTP indiquant que l’utilisateur est introuvable ?

Vérifiez qu’AD Connect est en cours d’exécution et que l’utilisateur se trouve dans Windows Active Directory et Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Pourquoi existe-t-il des erreurs de connexion HTTP dans les journaux d’activité pour tous mes échecs d’authentification ?

Vérifiez que https://adnotifications.windowsazure.com est accessible depuis le serveur exécutant l’extension NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Pourquoi l’authentification ne fonctionne-t-elle pas, même si le certificat est valide ?

Si votre certificat d’ordinateur précédent est arrivé à expiration, et qu’un nouveau certificat a été généré, vous devez supprimer tous les certificats arrivés à expiration. En effet, des certificats arrivés à expiration peuvent provoquer des problèmes lors du démarrage de l’extension NPS.

Pour vous assurer que vous disposez d’un certificat valide, vérifiez le magasin de certificats du compte d’ordinateur local à l’aide de MMC, pour savoir si le certificat a atteint sa date d’expiration. Pour générer un nouveau certificat valide, exécutez à nouveau les étapes de la section [Exécution du script PowerShell](#run-the-powershell-script).

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gestion des protocoles TLS/SSL et des suites de chiffrement

Il est recommandé de désactiver ou de supprimer les suites de chiffrement plus anciennes et plus faibles, sauf si votre organisation en a besoin. Vous trouverez plus d’informations sur la manière d’effectuer cette tâche dans l’article [Gestion des protocoles SSL/TLS et des suites de chiffrement pour AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Résolution de problèmes supplémentaires

Vous trouverez des conseils supplémentaires et des solutions possibles dans l’article [Résoudre les messages d’erreur liés à l’extension NPS pour Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble et configuration du serveur NPS dans Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Configurez d’autres ID de connexion, ou créez une liste d’exceptions pour les adresses IP qui ne nécessitent pas de vérification en deux étapes dans [Options de configuration avancée de l’extension de serveur NPS pour l’authentification multifacteur](howto-mfa-nps-extension-advanced.md)

- Apprendre à intégrer la [passerelle des services Bureau à distance](howto-mfa-nps-extension-rdg.md) et des [serveurs VPN](howto-mfa-nps-extension-vpn.md) à l’aide de l’extension NPS

- [Résoudre les messages d’erreur liés à l’extension NPS pour Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
