---
title: Guide de déploiement FortiGate | Microsoft Docs
description: Configurez et utilisez le produit de pare-feu nouvelle génération Fortinet FortiGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273328"
---
# <a name="fortigate-deployment-guide"></a>Guide de déploiement FortiGate

Utilisez ce guide de déploiement pour apprendre à configurer et à utiliser le produit de pare-feu nouvelle génération Fortinet FortiGate.

## <a name="redeem-the-fortigate-license"></a>Échanger la licence FortiGate

Le produit de pare-feu nouvelle génération Fortinet FortiGate est disponible en tant que machine virtuelle dans Azure IaaS (Infrastructure as a Service). Il existe deux modes de licence pour cette machine virtuelle : paiement à l’utilisation et BYOL (apportez votre propre licence).

Fortinet peut fournir des licences aux membres de l’équipe SHA (Secure Hybrid Access) de mise en production Azure Active Directory (Azure AD). Si aucune licence n’est fournie, le déploiement de type paiement à l'utilisation fonctionne également.

Si une licence a été émise, Fortinet fournit un code d’inscription à échanger en ligne.

![Capture d’écran du code d’inscription VPN SSL FortiGate.](registration-code.png)

1. Inscrivez-vous à l’adresse https://support.fortinet.com/.
2. Après l’inscription, connectez-vous à https://support.fortinet.com/.
3. Accédez à **Asset** > **Register/Activate** (Ressource - Enregistrer/Activer).
4. Entrez le code d’inscription fourni par Fortinet.
5. Spécifiez le code d’inscription, sélectionnez **The product will be used by a non-government user** (Le produit sera utilisé par un utilisateur non gouvernemental), puis cliquez sur **Next** (Suivant).
6. Entrez une description de produit (p. ex., FortiGate), définissez le partenaire Fortinet comme **Other** > **Microsoft** (Autre - Microsoft), puis cliquez sur **Next** (Suivant).
7. Acceptez le contrat d’enregistrement du produit Fortinet (**Fortinet Product Registration Agreement**), puis sélectionnez **Next** (Suivant).
8. Acceptez les conditions générales (**Terms**) et sélectionnez **Confirm** (Confirmer).
9. Sélectionnez **License File Download** (Télécharger le fichier de licence) et enregistrez la licence pour une utilisation ultérieure.


## <a name="download-firmware"></a>Télécharger le microprogramme

La version du microprogramme requise pour l’authentification SAML n’est actuellement pas fournie avec la machine virtuelle Azure Fortinet FortiGate. La version la plus récente doit être obtenue auprès de Fortinet.

1. Connectez-vous à https://support.fortinet.com/.
2. Accédez à **Download** > **Firmware Images** (Télécharger - Images du microprogramme).
3. Sélectionnez **Download** (Télécharger) à droite de **Release Notes** (Notes de publication).
4. Sélectionnez **V6.** > **6.** > **6.4.** .
5. Téléchargez **FGT_VM64_AZURE-v6-build1637-FORTINET.out** en sélectionnant le lien **HTTPS** sur la même ligne.
6. Enregistrez le fichier pour une utilisation ultérieure.


## <a name="deploy-the-fortigate-vm"></a>Déployer la machine virtuelle FortiGate

1. Accédez à https://portal.azure.com et connectez-vous à l’abonnement dans lequel vous souhaitez déployer la machine virtuelle FortiGate.
2. Créez un groupe de ressources ou ouvrez le groupe de ressources dans lequel vous souhaitez déployer la machine virtuelle FortiGate.
3. Sélectionnez **Ajouter**.
4. Dans la zone **Search the Marketplace** (Recherche sur le marketplace), entrez *Forti*. Sélectionnez **Fortinet FortiGate Next-Generation Firewall** (Pare-feu Fortigate nouvelle génération).
5. Sélectionnez le plan logiciel : BYOL (apportez votre propre licence) si vous disposez d’une licence ou Paiement à l’utilisation si ce n’est pas le cas. Sélectionnez **Create** (Créer).
6. Complétez la configuration de la machine virtuelle.

    ![Capture d’écran Créer une machine virtuelle.](virtual-machine.png)

7. Définissez **Authentication type** (Type d’authentification) sur **Password** (Mot de passe) et fournissez des informations d’identification d’administration pour la machine virtuelle.
8. Sélectionnez **Vérifier + créer** > **Créer**.
9. Attendez la fin du déploiement de la machine virtuelle.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Définir une adresse IP publique statique et affecter un nom de domaine complet

Pour bénéficier d’une expérience utilisateur cohérente, configurez l’adresse IP publique affectée à la machine virtuelle FortiGate comme affectée de manière statique. En outre, mappez-la à un nom de domaine complet (FQDN).

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans l’écran **Vue d’ensemble**, sélectionnez l’adresse IP publique.

    ![Capture d’écran du VPN SSL FortiGate.](public-ip-address.png)

3. Sélectionnez **Statique** > **Enregistrer**.

Si vous possédez un nom de domaine routable publiquement pour l’environnement dans lequel la machine virtuelle FortiGate est en cours de déploiement, créez un enregistrement d’hôte (A) pour la machine virtuelle. Cet enregistrement opère un mappage à l’adresse IP publique précédente qui est affectée de manière statique.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Créer une règle de groupe de sécurité réseau de trafic entrant pour un port TCP

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, sélectionnez **Networking** (Mise en réseau). L’interface réseau est listée et les règles de port entrant s’affichent.
3. Sélectionnez **Ajouter une règle de port d’entrée**.
4. Créez une nouvelle règle de port entrant pour TCP 8443.

    ![Capture d’écran Ajouter une règle de sécurité de trafic entrant.](port-rule.png)

5. Sélectionnez **Ajouter**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Créer une application Azure personnalisée pour FortiGate

1. Accédez à https://portal.azure.com et ouvrez le volet Azure AD pour le locataire qui fournira l’identité des connexions FortiGate.
2. Dans le menu de gauche, sélectionnez **Applications d’entreprise**.
3. Sélectionnez **Nouvelle application** > **Application ne figurant pas dans la galerie**.
4. Entrez un nom (par exemple, FortiGate), puis sélectionnez **Ajouter**.
5. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.
6. Ajoutez des utilisateurs qui pourront se connecter et sélectionnez **Attribuer**.
7. Dans le menu de gauche, sélectionnez **Single Sign-On** (Authentification unique).
8. Sélectionnez **SAML**.
9. Sous **Configuration SAML de base**, sélectionnez l’icône de crayon pour modifier la configuration.
10. Configurez ce qui suit :
    - **Identificateur (ID d’entité)** avec `https://<address>/remote/saml/metadata`.
    - **URL de réponse (URL Assertion Consumer Service)** avec `https://<address>/remote/saml/login`.
    - **URL de déconnexion** avec `https://<address>/remote/saml/logout`.

    `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

11. Enregistrez chacune de ces URL pour une utilisation ultérieure : ID d’entité, URL de réponse et URL de déconnexion.
12. Sélectionnez **Enregistrer** et fermez la **Configuration SAML de base**.
13. Sous **3 – SAML Signing Certificate** (3 – Certificat de signature SAML), téléchargez **Certificate (Base64)** et enregistrez-le pour une utilisation ultérieure.
14. Sous **4 – Set up (App Name)** (4 – Configurer [nom de l’application]), copiez l’URL de connexion Azure, l’identificateur Azure AD et l’URL de déconnexion Azure, puis enregistrez-les pour une utilisation ultérieure.
15. Sous **2 – User Attributes and Claims** (2 – Attributs utilisateur et revendications), cliquez sur l’icône de crayon pour modifier la configuration.
16. Sélectionnez **Add new claim** (Ajouter une nouvelle revendication), puis définissez le nom sur **nom d’utilisateur**.
17. Définissez l’attribut Source sur **user.userprincipalname**.
18. Sélectionnez **Save** > **Add a group claim** > **All groups** (Enregistrer - Ajouter une revendication de groupe- Tous les groupes).
19. Sélectionnez **Customize the name of the group claim** (Personnaliser le nom de la revendication de groupe) et définissez le nom sur **group** (groupe).
20. Sélectionnez **Enregistrer**.


## <a name="prepare-for-group-matching"></a>Préparer la correspondance de groupe

FortiGate offre différentes expériences de portail de l’utilisateur après la connexion, en fonction de l’appartenance à un groupe. Par exemple, il peut y avoir une expérience pour le groupe Marketing et une autre pour le groupe Finance. Comment créer des groupes pour les utilisateurs :

1. Accédez à https://portal.azure.com et ouvrez le volet Azure AD pour le locataire qui fournira l’identité des connexions FortiGate.
2. Sélectionnez **Groupes** > **Nouveau groupe**.
3. Créez un groupe avec les détails suivants :
    - Type de groupe = Sécurité
    - Nom du groupe = `a meaningful name`
    - Description du groupe = `a meaningful description for the group`
    - Type d’appartenance = Attribuée
    - Membres = `users for the user experience that will map to this group`
4. Répétez les étapes 3 et 4 pour toute expérience utilisateur supplémentaire.
5. Une fois les groupes créés, sélectionnez chaque groupe et enregistrez l’**ID d’objet** de chacun.
6. Enregistrez ces ID d’objets et noms de groupes pour une utilisation ultérieure.


## <a name="configure-the-fortigate-vm"></a>Configurer la machine virtuelle FortiGate

Les sections suivantes vous expliquent comment configurer la machine virtuelle FortiGate.

### <a name="install-the-license"></a>Installer la licence

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Si le déploiement utilise le modèle BYOL (apportez votre propre licence), vous serez invité à charger une licence. Sélectionnez le fichier de licence créé précédemment et chargez-le. Sélectionnez **OK** et redémarrez la machine virtuelle FortiGate.

    ![Capture d’écran de la licence de la machine virtuelle FortiGate.](license.png)

5. Après le redémarrage, connectez-vous à nouveau avec les informations d’identification d’administrateur pour valider la licence.

### <a name="update-firmware"></a>Mise à jour du microprogramme

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, sélectionnez **System** > **Firmware** (Système - Microprogramme).
5. Dans **Firmware Management** (Gestion du microprogramme), sélectionnez **Browse** (Parcourir) et choisissez le fichier de microprogramme téléchargé précédemment.
6. Ignorez l’avertissement et sélectionnez **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde).

    ![Capture d’écran de Firmware Management.](backup-configure-upgrade.png)

7. Sélectionnez **Continuer**.
8. Lorsque vous êtes invité à enregistrer la configuration FortiGate (sous la forme d’un fichier .conf), sélectionnez **Save** (Enregistrer).
9. Attendez que le microprogramme soit téléchargé et appliqué. Attendez que la machine virtuelle FortiGate redémarre.
10. Après le redémarrage de la machine virtuelle FortiGate, connectez-vous à nouveau avec les informations d’identification d’administrateur.
11. Lorsque vous êtes invité à configurer le tableau de bord, sélectionnez **Later** (Ultérieurement).
12. Au début de la vidéo du tutoriel, sélectionnez **OK**

### <a name="change-the-management-port-to-tcp"></a>Changer le port de gestion en port TCP

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, sélectionnez **System** (Système).
5. Sous **Administration Settings** (Paramètres d’administration), changez le port HTTPS en spécifiant **8443** et sélectionnez **Apply** (Appliquer).
6. Une fois la modification appliquée, le navigateur tente de recharger la page d’administration, mais l’opération échoue. Désormais, l’adresse de la page d’administration est `https://<address>`.

    ![Capture d’écran de Upload Remote Certificate (Charger un certificat distant).](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Charger le certificat de signature SAML Azure AD

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, sélectionnez **System** > **Certificates** (Système - Certificats).
5. Sélectionnez **Importer** > **Certificat distant**.
6. Accédez au certificat téléchargé à partir du déploiement de l’application personnalisée FortiGate dans le locataire Azure. Sélectionnez-le, puis cliquez sur **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Charger et configurer un certificat SSL personnalisé

Vous pouvez configurer la machine virtuelle FortiGate avec votre propre certificat SSL qui prend en charge le nom de domaine complet que vous utilisez. Si vous avez accès à un certificat SSL empaqueté avec la clé privée au format .PFX, il peut être utilisé à cet effet.

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, sélectionnez **System** > **Certificates** (Système - Certificats).
5. Sélectionnez **Import** > **Local Certificate** > **PKCS #12 Certificate** (Importer - Certificat local - Certificat PKCS #12).
6. Accédez au fichier .PFX contenant le certificat SSL et la clé privée.
7. Spécifiez le mot de passe .PFX et un nom explicite pour le certificat. Sélectionnez ensuite **OK**.
8. Dans le menu de gauche, sélectionnez **System** > **Settings** (Système - Paramètres).
9. Sous **Administration Settings** (Paramètres d’administration), développez la liste en regard de **HTTPS server certificate** (Certificat de serveur HTTPS) et sélectionnez le certificat SSL importé précédemment.
10. Sélectionnez **Appliquer**.
11. Fermez la fenêtre du navigateur et accédez à `https://<address>`.
12. Connectez-vous avec les informations d'identification d'administrateur FortiGate. Vous devez maintenant voir le certificat SSL correct en cours d’utilisation.


### <a name="perform-command-line-configuration"></a>Effectuer la configuration de ligne de commande

Les sections suivantes décrivent la procédure pour différentes configurations à l’aide de la ligne de commande.

#### <a name="for-saml-authentication"></a>Pour l’authentification SAML

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, sélectionnez **Serial Console** (Console série).
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate. Pour l’étape suivante, les URL enregistrées précédemment sont nécessaires :
    - L’ID d’entité
    - URL de réponse
    - URL de déconnexion
    - URL de connexion Azure
    - Identificateur Azure AD
    - URL de déconnexion Azure
4. Sur la console série, exécutez les commandes suivantes :

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > L’URL de déconnexion Azure contient un caractère `?`. Une séquence de touches spéciale est requise pour fournir correctement ce caractère à la console série FortiGate. En règle générale, l’URL est `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`. Pour la spécifier dans la console série, tapez :
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Sélectionnez ensuite CTRL+V, puis collez le reste de l’URL pour terminer la ligne : 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Pour confirmer la configuration, exécutez la commande suivante :

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Pour la correspondance de groupe

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, sélectionnez **Serial Console** (Console série).
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate.
4. Sur la console série, exécutez les commandes suivantes :

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Pour chaque groupe supplémentaire qui aura une expérience de portail différente dans FortiGate, répétez ces commandes (en commençant par la deuxième ligne du code).

#### <a name="for-authentication-timeout"></a>Pour le délai d’expiration de l’authentification

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, sélectionnez **Serial Console** (Console série).
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate.
4. Sur la console série, exécutez les commandes suivantes :

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Créer des portails VPN et une stratégie de pare-feu

1. Accédez à `https://<address>`. Où `<address>` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
3. Dans le menu de gauche, sélectionnez **VPN** > **SSL-VPN Portals** > **Create New** (VPN- Portails SSL-VPN - Créer nouveau).
6. Spécifiez un nom (correspondant en général au groupe Azure utilisé pour fournir l’expérience de portail personnalisée).
7. Cliquez sur le signe plus ( **+** ) en regard de **Source IP Pools** (Pools d’adresses IP sources), sélectionnez le pool par défaut et cliquez sur **Close** (Fermer).
8. Personnalisez l’expérience de ce groupe. Pour le test, il est possible que ce soit la personnalisation du message du portail et du thème. C’est là également que vous pouvez créer des signets personnalisés qui dirigent les utilisateurs vers des ressources internes.
9. Sélectionnez **OK**.
10. Répétez les étapes 5-9 pour chaque groupe Azure qui bénéficiera d’une expérience de portail personnalisée.
11. Sous VPN, sélectionnez **SSL-VPN Settings** (Paramètres SSL-VPN).
12. Sélectionnez le signe plus ( **+** ) en regard de **Listen on Interfaces** (Écouter sur les interfaces), sélectionnez **Port1**, puis **Close** (Fermer).
14. Si vous avez précédemment installé un certificat SSL personnalisé, modifiez **Server Certificate** (Certificat de serveur) pour utiliser le certificat SSL personnalisé dans le menu déroulant.
15. Sous **Authentication/Portal Mapping** (Mappage de l’authentification/du portail), sélectionnez **Create New** (Créer nouveau). Choisissez le premier groupe Azure et mettez-le en correspondance avec le portail du même nom. Sélectionnez ensuite **OK**.
18. Répétez les étapes 15-17 pour chaque paire groupe Azure et portail.
19. Sous **Authentication/Portal Mapping** (Mappage de l’authentification/du portail), modifiez **All Other Users/Groups** (Tous les autres utilisateurs/groupes).
20. Configurez le portail sur **full-access** (accès complet) et sélectionnez **OK** > **Apply** (OK - Appliquer).
23. Faites défiler la page **SSL-VPN Setting** (Paramètre SSL-VPN) jusqu’en haut, puis sélectionnez **No SSL-VPN policies exist. Click here to create a new SSL-VPN policy using these settings.** (Aucune stratégie SSL-VPN n’existe. Cliquez ici pour créer une nouvelle stratégie SSL-VPN avec ces paramètres).
24. Spécifiez un nom, tel que **VPN Grp**. Ensuite, définissez **Outgoing Interface** (Interface sortante) sur **port**, puis sélectionnez **Source**.
27. Sous **Address** (Adresse), sélectionnez **all** (tout).
28. Sous **User** (Utilisateur), sélectionnez le premier groupe Azure.
29. Sélectionnez **Close** > **Destination** (Fermer - Destination). Sous **Address** (Adresse), il s’agit généralement du réseau interne. Sélectionnez **login.microsoft.com** pour les tests.
32. Sélectionnez **Close** > **Service** > **All** (Fermer - Service - Tout). Sélectionnez ensuite **Close** > **OK** (Fermer - OK).
37. Dans le menu de gauche, sélectionnez **Policy & Objects** > **Firewall Policy** (Stratégie & Objets - Stratégie de pare-feu).
39. Développez **SSL-VPN tunnel interface (ssl.root)**  > **port** (Interface du tunnel SSL-VPN (ssl.root) - port).
40. Cliquez avec le bouton droit sur la stratégie VPN créée précédemment ( **VPN Grp 1** ) et sélectionnez **Copy** (Copier).
41. Cliquez avec le bouton droit sous la stratégie VPN et sélectionnez **Paste** > **Below** (Coller - En dessous).
42. Modifiez la nouvelle stratégie en lui attribuant un nom différent (par exemple, **VPN Grp2**). Modifiez également le groupe auquel elle s’applique (un autre groupe Azure).
43. Cliquez avec le bouton droit sur la nouvelle stratégie et définissez l’état sur **Enabled** (Activé).


## <a name="test-sign-in-by-using-azure"></a>Tester la connexion à l’aide d’Azure

1. Accédez à `https://<address>` via une session de navigation privée.  
2. L’opération de connexion doit rediriger vers Azure AD pour la connexion.
3. Une fois que vous avez fourni les informations d’identification d’un utilisateur qui a été affecté à l’application FortiGate dans le locataire Azure, le portail de l’utilisateur approprié doit s’afficher.

    ![Capture d’écran du VPN SSL FortiGate](test-sign-in.png)
