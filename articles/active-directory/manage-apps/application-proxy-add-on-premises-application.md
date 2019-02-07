---
title: Ajouter une application locale - Service Proxy d'application d'Azure Active Directory | Microsoft Docs
description: Le service Proxy d'application d'Azure Active Directory (Azure AD) permet aux utilisateurs d'accéder aux applications locales en se connectant avec leur compte Azure AD. Ce tutoriel vous explique comment préparer votre environnement pour utiliser le service Proxy d'application. Il a ensuite recours au portail Azure pour ajouter une application locale à votre client Azure AD.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 1f6962f0313b6b0ca187512185c990606789da6e
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661953"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutoriel : Ajouter une application locale pour un accès à distance via le service Proxy d'application d'Azure Active Directory

Le service Proxy d'application d'Azure Active Directory (Azure AD) permet aux utilisateurs d'accéder aux applications locales en se connectant avec leur compte Azure AD. Ce tutoriel prépare votre environnement à l'utilisation du service Proxy d'application. Lorsque votre environnement sera prêt, vous utiliserez le portail Azure pour ajouter une application locale à votre client Azure AD.

Ce didacticiel :

> [!div class="checklist"]
> * ouvre les ports pour le trafic sortant et permet l’accès à des URL spécifiques ;
> * installe le connecteur sur votre serveur Windows et l’inscrit auprès du service Proxy d’application ;
> * vérifie que le connecteur est correctement installé et inscrit ;
> * ajoute une application locale à votre locataire Azure AD ;
> * vérifie qu'un utilisateur de test peut se connecter à l'application à l'aide d'un compte Azure AD.

## <a name="before-you-begin"></a>Avant de commencer

Pour ajouter une application à votre locataire, vous devez :

* Un [abonnement Microsoft Azure AD De base ou Premium](https://azure.microsoft.com/pricing/details/active-directory). 
* Un compte d’administrateur d’application.

### <a name="windows-server"></a>Windows Server
Pour utiliser le proxy d’application, vous devez disposer d’un serveur Windows exécutant Windows Server 2012 R2 ou ultérieur. Vous allez installer le connecteur de proxy d’application sur le serveur. Ce serveur de connecteurs doit se connecter aux services Proxy d'application d'Azure et aux applications locales que vous envisagez de publier.

Pour bénéficier d'une haute disponibilité dans votre environnement de production, nous vous recommandons d'utiliser plusieurs serveurs. Pour ce tutoriel, un seul serveur Windows suffira.

**Recommandations pour le serveur de connecteurs**

1. Placez physiquement le serveur de connecteurs à proximité des serveurs d'applications afin d'optimiser les performances entre le connecteur et l'application. Pour plus d'informations, consultez [Considérations sur la topologie réseau](application-proxy-network-topology.md).

2. Le serveur de connecteurs et les serveurs d'applications web doivent appartenir au même domaine Active Directory. Les serveurs doivent impérativement se trouver dans le même domaine pour utiliser l'authentification unique (SSO) avec l'authentification Windows intégrée (IWA) et la délégation Kerberos contrainte (KCD). Si le serveur de connecteurs et les serveurs d'applications web se trouvent dans des domaines Active Directory différents, vous devez utiliser la délégation basée sur les ressources pour l'authentification unique. Pour plus d’informations, consultez [KCD pour l’authentification unique avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md).

**Configuration logicielle requise**

TLS 1.2 doit être activé sur le serveur de connecteurs Windows Server avant l'installation du connecteur du Proxy d'application. Jusqu’à nouvel ordre, les connecteurs existants avec des versions antérieures à 1.5.612.0 continueront de fonctionner sur les versions antérieures de TLS. 

Pour activer TLS 1.2 :

1. Définissez les clés de Registre suivantes :
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Redémarrez le serveur

  
## <a name="prepare-your-on-premises-environment"></a>Préparer votre environnement local
Pour préparer votre environnement pour le proxy d’application Azure AD, vous devez d’abord activer la communication avec les centres de données Azure. En présence d'un pare-feu, assurez-vous qu'il est ouvert pour que le connecteur puisse envoyer des requêtes HTTPS (TCP) au service Proxy d'application.

### <a name="open-ports"></a>Ouvrir les ports

Ouvrez les ports suivants pour le trafic **sortant**. 

   | Numéro de port | Utilisation |
   | --- | --- |
   | 80 | Téléchargement de la liste de révocation de certificats lors de la validation du certificat SSL |
   | 443 | Toutes les communications sortantes avec le service de proxy d’application |

Si votre pare-feu régule le trafic en fonction des utilisateurs d'origine, ouvrez également les ports 80 et 443 au trafic provenant des services Windows exécutés en tant que service réseau.

Si vous utilisez déjà le service Proxy d'application, une version plus ancienne du connecteur est peut-être déjà installée.  Suivez ce tutoriel pour installer la dernière version du connecteur. Les versions antérieures à la version 1.5.132.0 requièrent également l'ouverture des ports suivants : 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Autoriser l'accès à des URL

Autorisez l'accès aux URL suivantes :

| URL | Utilisation |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Communication entre le connecteur et le service cloud Proxy d'application |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure utilise ces URL pour vérifier les certificats |
| login.windows.net<br>login.microsoftonline.com | Le connecteur utilise ces URL lors du processus d'inscription. |

Si votre pare-feu ou votre proxy autorise la mise en liste verte de DNS, vous pouvez y placer les connexions à \*.msappproxy.net et \*.servicebus.windows.net. Si ce n’est pas le cas, vous devez autoriser l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ces dernières sont mises à jour chaque semaine.

## <a name="install-and-register-a-connector"></a>Installer et inscrire un connecteur
Pour utiliser le service Proxy d'application, vous devez installer un connecteur sur chacun des serveurs Windows que vous choisissez d'utiliser avec le service Proxy d'application. Le connecteur est un agent qui gère la connexion sortante des serveurs d'applications locales vers le service Proxy d'applications d'Azure AD. Vous pouvez installer un connecteur sur des serveurs sur lesquels d'autres agents d'authentification, tels qu'Azure AD Connect, sont également installés.

Pour installer le connecteur :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu'administrateur d'application du répertoire qui utilise le service Proxy d'application. Par exemple, si le domaine du client est contoso.com, l'administrateur doit être admin@contoso.com ou tout autre alias administratif sur ce domaine.
2. Votre répertoire actuel apparaît sous votre nom d'utilisateur dans le coin supérieur droit. Vérifiez que vous êtes connecté à un répertoire qui utilise le service Proxy d'application. Si vous avez besoin de changer de répertoire, sélectionnez cette icône.
3. Dans le panneau de gauche, cliquez sur **Azure Active Directory**, puis sur **Proxy d'application**.
4. Cliquez sur **Télécharger le service de connecteur**.
5. Lisez les conditions d'utilisation du service.  Lorsque vous êtes prêt, cliquez sur **Accepter les conditions d'utilisation et télécharger**.
6. En bas de la fenêtre, un message s'affiche pour vous inviter à télécharger **AADApplicationProxyConnectorInstaller.exe**. Cliquez sur **Exécuter** pour installer le connecteur. Un Assistant d'installation s'ouvre. 
7. Suivez les instructions de l’Assistant pour effectuer l’installation. Lorsque vous êtes invité à inscrire le connecteur auprès du service Proxy d'application de votre client Azure AD, fournissez vos informations d'identification d'administrateur d'application.
    - Pour Internet Explorer (IE), si l'option **Configuration de sécurité renforcée d'Internet Explorer** est **Activée**, l'écran d'inscription peut ne pas s'afficher. Suivez les instructions du message d’erreur pour obtenir l’accès. Vérifiez que la configuration de sécurité renforcée d'Internet Explore est **Désactivée**.

### <a name="general-remarks"></a>Remarques générales

Si vous avez déjà installé un connecteur, réinstallez-le pour obtenir la dernière version.

Si vous choisissez d'utiliser plusieurs serveurs Windows pour vos applications locales, vous devez installer et inscrire le connecteur sur chaque serveur. Vous pouvez organiser les connecteurs en groupes de connecteurs. Pour plus d'informations, consultez [Groupes de connecteurs](application-proxy-connector-groups.md). 

Si votre organisation utilise des serveurs proxy pour se connecter à Internet, vous devez les configurer pour le proxy d’application.  Pour plus d’informations, consultez [Utiliser des serveurs proxy locaux existants](application-proxy-configure-connectors-with-proxy-servers.md). 

Pour plus d'informations sur les connecteurs, la planification de capacité et leur mise à jour, consultez [Présentation des connecteurs du service Proxy d'application d'Azure AD](application-proxy-connectors.md). 

Si vous utilisez l’application Qlik Sense, installez toujours le connecteur le plus récent. Qlik Sense utilise WebSockets, qui est pris en charge uniquement sur les versions 1.5.612.0 ou ultérieures du connecteur.


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Vérifier que le connecteur est correctement installé et inscrit

Vous pouvez utiliser le portail Azure ou votre serveur Windows pour vérifier qu'un nouveau connecteur est correctement installé.

### <a name="verify---azure-portal"></a>Vérifier - Portail Azure
Pour vérifier que le connecteur est correctement installé et inscrit :

1. Connectez-vous au répertoire de votre client sur le [portail Azure](https://portal.azure.com).
2. Cliquez sur **Azure Active Directory**, puis sur **Proxy d'application**. Tous les connecteurs et les groupes de connecteurs apparaissent sur cette page. 
3. Sélectionnez un connecteur pour vérifier ses détails. Une étiquette verte active indique que votre connecteur peut se connecter au service. Toutefois, même si l'étiquette est verte, un problème réseau peut empêcher le connecteur de recevoir les messages. 

    ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Pour obtenir de l'aide sur l'installation d'un connecteur, consultez [Problèmes liés à l'installation d'un connecteur du Proxy d'application](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Vérifier - Serveur Windows
Pour vérifier que le connecteur est correctement installé et inscrit :

1. Ouvrez le Gestionnaire des services Windows en cliquant sur la touche **Windows** et en entrant *services.msc*.
2. Vérifiez que l'état des deux services suivants est **En cours d'exécution**.
   - **connecteur de proxy d’application Microsoft AAD** active la connectivité.
   - **Le programme de mise à jour du connecteur de proxy d’application Microsoft AAD** est un service de mise à jour automatique. Régulièrement, ce service recherche de nouvelles versions du connecteur et le met à jour si besoin.

    ![Services de connecteur de proxy d’application - capture d’écran](./media/application-proxy-enable/app_proxy_services.png)

3. Si l'état des services n'est pas **En cours d'exécution**, cliquez sur chaque service avec le bouton droit de la souris et choisissez **Démarrer**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Ajouter une application locale à Azure AD

Maintenant que vous avez préparé votre environnement et installé un connecteur, vous êtes prêt à ajouter des applications locales à Azure AD.  

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Nouvelle application**.

    ![Ajouter une application d’entreprise](./media/application-proxy-publish-azure-portal/add-app.png)

3. Sélectionnez **Tout**, puis **Application locale**.  

    ![Ajout de votre propre application](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Dans le panneau **Ajouter votre propre application locale**, fournissez les informations suivantes relatives à votre application :

    ![Configuration de votre application](./media/application-proxy-publish-azure-portal/configure-app.png)

    | Champ | Description |
    | :---- | :---------- |
    | **Nom** | Nom de l'application qui apparaîtra sur le panneau d'accès et sur le portail Azure. |
    | **URL interne** | URL permettant d'accéder à l'application depuis votre réseau privé. Vous pouvez spécifier un chemin spécifique sur le serveur principal à publier, alors que le reste du serveur n’est pas publié. De cette façon, vous pouvez publier des sites différents sur le même serveur en tant qu’applications distinctes et donner à chacun d’eux son propre nom et ses propres règles d’accès.<br><br>Si vous publiez un chemin d’accès, vérifiez qu’il inclut l’ensemble des images, des scripts et des feuilles de style nécessaires pour votre application. Par exemple, si votre application se trouve à l’adresse https://yourapp/app et utilise des images situées à l’adresse https://yourapp/media, vous devrez publier l’adresse https://yourapp/ comme chemin d’accès. Cette URL interne n’est pas nécessairement la page d’accueil que vos utilisateurs voient. Pour plus d’informations, consultez [Définir une page d’accueil personnalisée pour les applications publiées](application-proxy-configure-custom-home-page.md). |
    | **URL externe** | Adresse permettant aux utilisateurs d'accéder à l'application de l'extérieur de votre réseau. Si vous ne souhaitez pas utiliser le domaine de proxy d’application par défaut, lisez la documentation relative aux [domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-authentification** | Façon dont le service Proxy d'application vérifie les utilisateurs avant de leur donner accès à votre application.<br><br>**Azure Active Directory** : le service Proxy d'application redirige les utilisateurs pour la connexion à Azure AD, qui authentifie leurs autorisations pour le répertoire et l'application. Nous vous recommandons de laisser cette option par défaut, afin que vous puissiez tirer parti des fonctionnalités de sécurité Azure AD, comme l’accès conditionnel et l’authentification multifacteur. **Azure Active Directory** est nécessaire pour la supervision de l’application avec Microsoft Cloud App Security.<br><br>**Direct** : les utilisateurs n'ont pas besoin de s'authentifier auprès d'Azure Active Directory pour accéder à l'application. Vous pouvez toujours configurer les exigences d’authentification sur le serveur principal. |
    | **Groupe de connecteurs** | Les connecteurs gèrent l'accès à distance à votre application et les groupes de connecteurs vous aident à organiser des connecteurs et des applications par région, réseau ou objectif. Si aucun groupe de connecteurs n’est encore créé, votre application est assignée au groupe **Par défaut**.<br><br>Si votre application utilise des WebSockets pour se connecter, tous les connecteurs du groupe doivent être de la version 1.5.612.0 ou ultérieure.|

5. Si nécessaire, configurez des **paramètres supplémentaires**. Pour la plupart des applications, vous devez conserver ces paramètres dans leur état par défaut. 

    | Champ | Description |
    | :---- | :---------- |
    | **Expiration de l'application principale** | Définissez cette valeur sur **Long** uniquement si l'authentification et la connexion de votre application sont lentes. |
    | **Utiliser un cookie HTTP-only** | Définissez cette valeur sur **Oui** pour que les cookies du service Proxy d'application incluent un indicateur HTTPOnly dans l'en-tête de réponse HTTP. Si vous utilisez les services Bureau à distance, définissez cette valeur sur **Non**.|
    | **Utiliser un cookie sécurisé**| Définissez cette valeur sur **Oui** pour transmettre les cookies sur un canal sécurisé, comme une requête HTTPS chiffrée.
    | **Utiliser un cookie persistant**| Conservez cette valeur sur **Non**. Ce paramètre doit être utilisé uniquement pour les applications qui ne peuvent pas partager de cookies entre les processus. Pour plus d’informations sur les paramètres de cookies, consultez [Paramètres de cookies pour l’accès aux applications locales dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Traduire l'URL dans les en-têtes** | Conservez la valeur **Oui**, sauf si votre application a demandé l'en-tête d'hôte d'origine dans la requête d'authentification. |
    | **Traduire les URL dans le corps de l'application** | Conservez la valeur **Non**, sauf si vous avez codé en dur des liens HTML vers d'autres applications locales et que vous n'utilisez pas de domaines personnalisés. Pour plus d’informations, consultez [Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD](application-proxy-configure-hard-coded-link-translation.md).<br><br>Définissez cette valeur sur **Oui** si vous envisagez de superviser cette application avec Microsoft Cloud App Security (MCAS). Pour plus d’informations, consultez [Configurer la supervision de l’accès aux applications en temps réel avec Microsoft Cloud App Security et Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |
   


6. Sélectionnez **Ajouter**.

## <a name="test-the-application"></a>Test de l’application

Vous êtes prêt à tester que l’application a été correctement ajoutée. Dans les étapes suivantes, vous allez ajouter un compte d’utilisateur à l’application et essayer de vous connecter.

### <a name="add-a-user-for-testing"></a>Ajouter un utilisateur de test
Avant d'ajouter un utilisateur à l'application, vérifiez que le compte d'utilisateur dispose déjà des autorisations d'accès à l'application depuis le réseau de l'entreprise.

Pour ajouter un utilisateur de test :

1. Dans le panneau **Démarrage rapide**, sélectionnez **Attribuer un utilisateur à des fins de test**.

    ![Attribuer un utilisateur à des fins de test](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Ajouter un utilisateur**.

    ![Ajouter un utilisateur ou groupe](./media/application-proxy-publish-azure-portal/add-user.png)

3. Dans le panneau **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**, puis le compte à ajouter. 
4. Sélectionnez **Attribuer**.

### <a name="test-the-sign-on"></a>Tester l'authentification

Pour tester l'authentification auprès de l'application :

1. Dans votre navigateur, accédez à l’URL externe que vous avez configurée au cours de l’étape de publication. 
2. L’écran d’accueil doit s’afficher.
3. Essayez de vous connecter comme l’utilisateur que vous avez créé à la section précédente.

    ![Tester votre application publiée](./media/application-proxy-publish-azure-portal/test-app.png)

Pour tout dépannage, consultez [Résoudre les problèmes de proxy d'application et les messages d'erreur](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez préparé votre environnement local pour utiliser le service Proxy d'application, puis vous avez installé et inscrit le connecteur du Proxy d'application. Ensuite, vous avez ajouté une application à votre locataire Azure AD. Vous avez vérifié qu’un utilisateur peut se connecter à l’application à l’aide d’un compte Azure AD.

Voici les étapes que vous avez effectuées :
> [!div class="checklist"]
> * Ouverture de ports pour le trafic sortant et autorisation d'accès à des URL spécifiques
> * Installation du connecteur sur votre serveur Windows et inscription de celui-ci auprès du service Proxy d'application
> * Vérification que le connecteur était correctement installé et inscrit
> * Ajout d'une application locale sur votre client Azure AD
> * Vérification qu'un utilisateur de test pouvait se connecter à l'application à l'aide d'un compte Azure AD

Vous êtes prêt à configurer l’application pour une authentification unique. Utilisez le lien suivant pour choisir une méthode d’authentification unique et pour rechercher des tutoriels dédiés à l’authentification unique. 

> [!div class="nextstepaction"]
>[Configurer l’authentification unique](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





