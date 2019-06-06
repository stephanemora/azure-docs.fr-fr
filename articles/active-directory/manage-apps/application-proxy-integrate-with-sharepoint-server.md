---
title: Activer l’accès distant à SharePoint avec le proxy d’application Azure AD | Microsoft Docs
description: Aborde les notions de base relatives à l’intégration d’un serveur SharePoint local au proxy d’application Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5eff7925599931104440213112ce288fd521b61
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473778"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Activer l’accès distant à SharePoint avec le proxy d’application Azure AD

Cet article explique comment intégrer un serveur SharePoint local au proxy d’application Azure Active Directory (Azure AD).

Pour activer l’accès à distance à SharePoint avec le proxy d’application Azure AD, suivez les sections de cet article étape par étape.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà SharePoint 2013 ou une version plus récente dans votre environnement. En outre, prenez en compte les conditions préalables suivantes :

* SharePoint comprend la prise en charge native de Kerberos. Ainsi, les utilisateurs qui accèdent à des sites internes à distance à l’aide du proxy d’application Azure AD peuvent s’attendre à une expérience d’authentification unique (SSO).

* Ce scénario inclut des modifications de configuration pour votre serveur SharePoint. Nous vous recommandons d’utiliser un environnement intermédiaire. Ainsi, vous pouvez d’abord mettre à jour votre serveur de test, puis faciliter un cycle de test avant de passer en production.

* Nous exigeons SSL sur l’URL publiée. SSL est également exigé sur l’URL interne pour veiller à ce que les liens soient envoyés/mappés correctement.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Étape 1 : Configurer une délégation Kerberos contrainte (KCD)

Pour les applications locales qui utilisent l’authentification Windows, vous pouvez obtenir l’authentification unique (SSO) à l’aide du protocole d’authentification Kerberos et d’une fonctionnalité appelée délégation Kerberos contrainte (KCD). Quand elle est configurée, la délégation KCD permet au connecteur de proxy d’application d’obtenir un jeton Windows pour un utilisateur, même si ce dernier ne s’est pas connecté directement à Windows. Pour en savoir plus sur la KCD, consultez [Présentation de la délégation Kerberos contrainte](https://technet.microsoft.com/library/jj553400.aspx).

Pour configurer la délégation KCD pour un serveur SharePoint, utilisez les procédures décrites dans les sections séquentielles suivantes :

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Assurez-vous que l’application web SharePoint s’exécute sous un compte de domaine

Tout d’abord, vérifiez que l’application web SharePoint s’exécute sous un compte de domaine, et non sous le système local, le service local ou le service réseau. Effectuez cette opération afin de pouvoir attacher les noms de principal de service (SPN) à ce compte. Les SPN sont la façon dont le protocole Kerberos identifie les différents services. De plus, vous aurez besoin de ce compte plus tard pour configurer la délégation KCD.

> [!NOTE]
> Vous devez disposer d’un compte Azure AD précédemment créé pour le service. Nous vous suggérons d’autoriser une modification de mot de passe automatique. Pour plus d’informations sur l’ensemble des étapes et la résolution des problèmes, consultez [Configurer la modification automatique de mot de passe dans SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Pour vous assurer que vos sites sont en cours d’exécution sous un compte de service défini, effectuez les étapes suivantes :

1. Ouvrez le site **Administration centrale de SharePoint**.
2. Accédez à **Sécurité**, puis sélectionnez **Configurer les comptes de service**.
3. Sélectionnez **Pool d’applications web - SharePoint - 80**. Les options peuvent être légèrement différentes en fonction du nom de votre pool web, ou si le pool web utilise SSL par défaut.

   ![Choix disponibles pour configurer un compte de service](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Si le champ **Sélectionnez un compte pour ce composant** a la valeur **Service local** ou **Service réseau**, vous devez créer un compte. Si ce n’est pas le cas, vous avez terminé et pouvez passer à la section suivante.
5. Choisissez **Enregistrer le nouveau compte géré**. Une fois votre compte créé, vous devez définir l’option **Pool d’applications web** pour pouvoir utiliser le compte.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Définir un nom de principal de service pour le compte de service SharePoint

Avant de configurer la délégation Kerberos contrainte, vous devez :

* Identifier le compte de domaine exécutant l’application web SharePoint que le proxy d’Azure AD exposera.
* Choisir une URL interne qui sera configurée dans le proxy d’Azure AD et dans SharePoint. Cette URL interne ne doit pas être déjà utilisée dans l’application web et elle n’apparaîtra jamais dans le navigateur web.

En supposant que l’URL interne choisie est <https://sharepoint>, le nom de principal de service est alors :

```
HTTP/SharePoint
```

> [!NOTE]
> Veuillez respecter les recommandations suivantes pour l’URL interne :
> * Utiliser le protocole HTTPS
> * Ne pas utiliser de ports personnalisés
> * Dans le DNS, créer un hôte (A) pointant vers le serveur web frontal (ou un équilibreur de charge) SharePoint, et pas un Alias (CName)

Pour enregistrer ce nom de principal de service, exécutez la commande suivante à partir de l’invite de commandes en tant qu’administrateur du domaine :

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Cette commande définit le nom de principal de service _HTTP/SharePoint_ pour le compte de pool d’applications SharePoint _demo\spAppPoolAccount_.

Remplacez _HTTP/SharePoint_ par le nom de principal de service pour votre URL interne et _demo\spAppPoolAccount_ par le compte de pool d’applications dans votre environnement. La commande Setspn recherche le SPN avant de l’ajouter. S’il existe déjà, une erreur **Valeur SPN en double** apparaît. Dans ce cas, envisagez de supprimer le SPN existant s’il n’est pas défini sous le compte de pool d’applications correct.

Vous pouvez vérifier que le SPN a été ajouté en exécutant la commande Setspn avec l’option -L. Pour en savoir plus sur cette commande, consultez [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Vérifiez que le connecteur est approuvé pour la délégation pour le SPN ajouté au compte de pool d’applications SharePoint

Configurez la délégation KCD afin que le service de proxy d’application Azure AD puisse déléguer des identités d’utilisateur au compte de pool d’applications SharePoint. Pour configurer la délégation KCD, activez le connecteur de proxy d’application pour récupérer les tickets Kerberos des utilisateurs qui ont été authentifiés dans Azure AD. Puis ce serveur transmet le contexte à l’application cible, ou SharePoint dans ce cas.

Pour configurer la délégation KCD, répétez les étapes suivantes pour chaque ordinateur connecteur :

1. Connectez-vous en tant qu’administrateur de domaine à un centre de données, puis ouvrez **Utilisateurs et ordinateurs Active Directory**.
2. Trouvez l’ordinateur sur lequel le connecteur est en cours d’exécution. Dans cet exemple, il s’agit du même ordinateur que le serveur SharePoint.
3. Double-cliquez sur l’ordinateur, puis cliquez sur l’onglet **Délégation**.
4. Vérifiez que les paramètres des délégation sont définis sur **N’approuver cet ordinateur que pour la délégation aux services spécifiés**. Sélectionnez ensuite **Utiliser tout protocole d’authentification**.
5. Cliquez sur le bouton **Ajouter**, cliquez sur **Utilisateurs ou ordinateurs**, puis recherchez le compte de pool d’applications SharePoint, par exemple _demo\spAppPoolAccount_.
6. Dans la liste des noms de principal de service, sélectionnez celui que vous avez créé précédemment pour le compte de service.
7. Cliquez sur **OK**. Cliquez à nouveau sur **OK** pour enregistrer les modifications apportées.
  
   ![Paramètres de délégation](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Étape 2 : Configuration du proxy Azure AD

Maintenant que vous avez configuré la délégation Kerberos contrainte, vous êtes prêt à configurer un proxy d’application Azure AD.

1. Publiez votre site SharePoint avec les paramètres suivants. Pour obtenir des instructions pas à pas, consultez [Publication d’applications à l’aide du proxy d’application Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interne** : URL interne SharePoint qui a été choisie précédemment, tel que **<https://SharePoint/>** .
   * **Méthode de pré-authentification** : Azure Active Directory
   * **Traduire l’URL dans les en-têtes** : NON

   >[!TIP]
   >SharePoint utilise la valeur _En-tête d’hôte_ pour rechercher le site. Il génère également des liens en fonction de cette valeur. L’effet immédiat est que tout lien généré par SharePoint est une URL publiée correctement définie pour utiliser l’URL externe. Définir la valeur sur **OUI** permet également au connecteur de transférer la demande à l’application principale. Toutefois, la valeur **NON** signifie que le connecteur n’envoie pas le nom d’hôte interne. Au lieu de cela, il envoie l’en-tête d’hôte comme URL publiée à l’application principale.

   ![Publier SharePoint en tant qu’application](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Une fois que votre application est publiée, configurez les paramètres d’authentification unique en effectuant les étapes suivantes :

   1. Dans la page de l’application dans le portail, sélectionnez **Authentification unique**.
   2. Pour le mode d’authentification unique, sélectionnez **Authentification Windows intégrée**.
   3. Définissez l’option SPN d’application interne sur la valeur que vous avez définie précédemment. Pour cet exemple, vous définiriez **HTTP/SharePoint**.
   4. Dans « Identité de connexion déléguée », sélectionnez **Nom du compte SAM local**.

   ![Configurer l’authentification Windows intégrée pour l’authentification unique](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Pour terminer la configuration de votre application, accédez à la section **Utilisateurs et groupes** et affectez des utilisateurs pouvant accéder à cette application. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Étape 3 : Configurer SharePoint pour utiliser les URL Kerberos et du proxy Azure AD

L’étape suivante consiste à étendre l’application web SharePoint à une nouvelle zone, configurée avec Kerberos et le mappage d’accès de substitution approprié pour que SharePoint puisse gérer les demandes entrantes envoyées à l’URL interne et répondre avec des liens générés pour l’URL externe.

1. Démarrez **l’interpréteur de commandes de gestion SharePoint**.
2. Exécutez le script suivant pour étendre l’application web à la zone Extranet et activer l’authentification Kerberos :

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. Ouvrez le site **Administration centrale de SharePoint**.
4. Sous **Paramètres système**, sélectionnez **Configurer des mappages d’accès alternatifs**. La zone Mappages d’accès alternatif s’ouvre.
5. Sélectionnez votre site, par exemple **SharePoint - 80**. Pour le moment, l’URL interne de la zone Extranet n’est pas encore défini correctement :

   ![Zone Mappages des accès de substitution](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Cliquez sur **Ajouter des URL internes**.
7. Dans la zone de texte **Protocole, hôte et port URL**, tapez l’**URL interne** configurée dans le proxy Azure AD, par exemple <https://SharePoint/>.
8. Sélectionnez sur la zone **Extranet** dans la liste déroulante.
9. Cliquez sur **Enregistrer**.
10. Les mappages des accès de substitution doivent maintenant ressembler à ceci :

    ![Mappages des accès de substitution corrects](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Étape 4 : Vérifier qu’un certificat HTTPS est configuré pour le site IIS de la zone Extranet

La configuration de SharePoint est maintenant terminée, mais étant donné que l’URL interne de la zone Extranet est <https://SharePoint/>, un certificat doit être défini pour ce site.

1. Ouvrez la console Windows PowerShell.
2. Exécutez le script suivant pour générer un certificat auto-signé et l’ajouter à l’ordinateur Mon magasin :

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Les certificats auto-signés sont uniquement destinés à des fins de test. Pour les environnements de production, nous vous recommandons fortement d’utiliser des certificats émis par une autorité de certification à la place.

3. Ouvrez la console « Internet Information Services Manager ».
4. Développez le serveur dans l’arborescence, développez « Sites », sélectionnez le site « SharePoint – Proxy AAD » et cliquez sur **Liaisons**.
5. Sélectionnez la liaison HTTPS et cliquez sur **Modifier...** .
6. Dans le champ de certificat SSL, choisissez le certificat **SharePoint** et cliquez sur OK.

Vous pouvez maintenant accéder au site SharePoint en externe par l’intermédiaire du proxy d’application Azure AD.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md)
* [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-connectors.md)
