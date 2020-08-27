---
title: Guide de déploiement FortiGate | Microsoft Docs
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FortiGate SSL VPN.
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657102"
---
# <a name="fortigate-deployment-guide"></a>Guide de déploiement FortiGate

## <a name="contents"></a>Contents

- Échanger la licence FortiGate
- Télécharger le microprogramme
- Déployer la machine virtuelle FortiGate
   - Définir une adresse IP publique statique et affecter un nom de domaine complet
   - Créer une règle de groupe de sécurité réseau de trafic entrant pour un port TCP
- Créer une application Azure personnalisée pour FortiGate
- Préparer la correspondance de groupe
   - Créer des groupes pour les utilisateurs
- Configurer la machine virtuelle FortiGate
   - Installer la licence
   - Mettre à jour le microprogramme
   - Changer le port de gestion en port TCP
   - Charger le certificat de signature SAML Azure Active Directory
   - Charger et configurer un certificat SSL personnalisé
   - Effectuer la configuration de ligne de commande
   - Créer des portails VPN et une stratégie de pare-feu
- Tester la connexion à l’aide d’Azure

## <a name="redeeming-the-fortigate-license"></a>Échanger la licence FortiGate

Le produit de pare-feu nouvelle génération Fortinet FortiGate est disponible en tant que machine virtuelle dans Azure IaaS. Il existe deux modes de licence pour cette machine virtuelle :

- Paiement à l’utilisation
- BYOL (apportez votre propre licence)

Dans le cadre du partenariat avec Fortinet pour fournir des conseils sur l’accès hybride sécurisé (SHA), Fortinet peut fournir des licences aux membres de l’équipe SHA de mise en production Azure AD. Au cas où aucune licence n’est fournie, le déploiement PAYG fonctionne également.

Au cas où une licence a été émise, Fortinet fournit un code d’inscription à échanger en ligne.

![FortiGate SSL VPN](registration-code.png)

1. Inscrivez-vous à l’adresse https://support.fortinet.com/
2. Après l’inscription, connectez-vous à https://support.fortinet.com/
3. Accédez à **Asset** (Ressource) - > **Register/Activate** (S’inscrire/Activer).
4. Entrez le code d’inscription fourni par Fortinet.
5. Spécifiez le code d’inscription, sélectionnez **The product will be used by a non-government user** (Le produit sera utilisé par un utilisateur non gouvernemental), puis cliquez sur **Next** (Suivant).
6. Entrez une description de produit (p. ex., FortiGate), définissez le partenaire Fortinet comme **Other** (Autre) - > **Microsoft**, puis cliquez sur **Next** (Suivant).
7. Acceptez le contrat d’enregistrement du produit Fortinet (**Fortinet Product Registration Agreement**), puis cliquez sur **Next** (Suivant).
8. Acceptez les conditions générales (**Terms**) et cliquez sur **Confirm** (Confirmer).
9. Cliquez sur **License File Download** (Télécharger le fichier de licence) et enregistrez la licence pour une utilisation ultérieure.


## <a name="download-firmware"></a>Télécharger le microprogramme

Au moment de la rédaction de cet article, la version du microprogramme requise pour l’authentification SAML n’est pas fournie avec la machine virtuelle Azure Fortinet FortiGate. La version la plus récente doit être obtenue auprès de Fortinet.

1. Connectez-vous à l’adresse https://support.fortinet.com/
2. Accédez à **Download** (Télécharger) - > **Firmware Images** (Images de microprogramme).
3. Cliquez sur **Download** (Télécharger) à droite de **Release Notes** (Notes de publication).
4. Cliquez sur **v6.**
5. Cliquez sur **6.**
6. Cliquez sur **6.4.**
7. Téléchargez **FGT_VM64_AZURE-v6-build1637-FORTINET.out** en cliquant sur le lien **HTTPS** sur la même ligne.
8. Enregistrez le fichier pour une utilisation ultérieure.


## <a name="deploy-the-fortigate-vm"></a>Déployer la machine virtuelle FortiGate

1. Accédez à https://portal.azure.com et connectez-vous à l’abonnement dans lequel vous souhaitez déployer la machine virtuelle FortiGate.
2. Créez un groupe de ressources ou ouvrez le groupe de ressources dans lequel vous souhaitez déployer la machine virtuelle FortiGate.
3. Cliquez sur **Ajouter**
4. Entrez « Forti » dans la boîte de dialogue **Rechercher dans la Place de marché** et sélectionnez **Fortinet FortiGate Next-** **Generation Firewall** (Pare-feu nouvelle génération Fortinet FortiGate).
5. Sélectionnez le plan logiciel (BYOL si vous avez une licence ou PAYG sinon), puis cliquez sur **Créer**
6. Complétez la configuration de la machine virtuelle.

    ![FortiGate SSL VPN](virtual-machine.png)

7. Définissez le type d’authentification sur **Mot de passe** et fournissez des informations d’identification d’administration pour la machine virtuelle.
8. Cliquez sur **Vérifier + créer**.
9. Cliquez sur **Créer**
10. Attendez la fin du déploiement de la machine virtuelle.


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Définir une adresse IP publique statique et affecter un nom de domaine complet

Pour bénéficier d’une expérience utilisateur cohérente, il est souhaitable de configurer l’adresse IP publique affectée à la machine virtuelle FortiGate comme affectée de manière statique. En outre, le mappage à un nom de domaine complet est également utile pour les mêmes raisons.

_Définir une adresse IP publique statique_

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans l’écran **Vue d’ensemble**, cliquez sur l’adresse IP publique.

    ![FortiGate SSL VPN](public-ip-address.png)

3. Cliquez sur **Statique** puis sur **Enregistrer**

_Attribuer un nom de domaine complet_

Si vous possédez un nom de domaine routable publiquement pour l’environnement dans lequel la machine virtuelle FortiGate est en cours de déploiement, créez un enregistrement d’hôte (A) pour la machine virtuelle mappée à l’adresse IP publique affectée de manière statique ci-dessus.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Créer une règle de groupe de sécurité réseau de trafic entrant pour un port TCP

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Cliquez sur **Mise en réseau** dans le menu de gauche. L’interface réseau est listée et les règles de port entrant s’affichent.
3. Cliquez sur **Ajouter une règle de port d’entrée**
4. Créez une nouvelle règle de port entrant pour TCP 8443.

    ![FortiGate SSL VPN](port-rule.png)

5. Cliquez sur **Ajouter**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Créer une application Azure personnalisée pour FortiGate

1. Accédez à https://portal.azure.com et ouvrez le panneau Azure Active Directory pour le locataire qui fournira l’identité des connexions FortiGate.
2. Cliquez sur **Applications d’entreprise** dans le menu de gauche.
3. Cliquez sur **Nouvelle application**
4. Cliquez sur **Application ne figurant pas dans la galerie**
5. Fournissez un nom (p. ex., FortiGate) et cliquez sur **Ajouter**
6. Cliquez sur **Utilisateurs et groupes** dans le menu de gauche.
7. Ajoutez des utilisateurs qui pourront se connecter et cliquez sur **Attribuer**
8. Cliquez sur **Authentification unique** dans le menu de gauche.
9. Cliquez sur **SAML**
10. Sous **Configuration SAML de base**, cliquez sur le crayon pour modifier la configuration.
11. Configurer
    - Identificateur (ID d’entité) avec `https://<address>/remote/saml/metadata`
    - URL de réponse (URL Assertion Consumer Service) avec `https://<address>/remote/saml/login`
    - URL de déconnexion avec `https://<address>/remote/saml/logout`

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

    Enregistrez chacune de ces URL pour une utilisation ultérieure :

    - L’ID d’entité
    - URL de réponse
    - URL de déconnexion
12. Cliquez sur **Enregistrer**.
13. Fermez la configuration SAML de base.
14. Sous **3 – Certificat de signature SAML**, téléchargez **Certificat (Base64)** et enregistrez-le pour une utilisation ultérieure.
15. Sous **4 – Configurer (nom de l’application)** , copiez l’URL de connexion Azure, l’identificateur Azure AD et l’URL de déconnexion Azure, puis enregistrez-les pour une utilisation ultérieure.
    - URL de connexion Azure
    - Identificateur Azure AD
    - URL de déconnexion Azure
16. Sous **2 – Attributs utilisateur et revendications**, cliquez sur le crayon pour modifier la configuration.
17. Cliquez sur **Ajouter une nouvelle revendication**
18. Définissez le nom sur **username**
19. Définissez l’attribut Source sur **user.userprincipalname**
20. Cliquez sur **Enregistrer**.
21. Cliquez sur **Ajouter une revendication de groupe**
22. Sélectionnez **Tous les groupes**
23. Cochez **Personnaliser le nom de la revendication de groupe**
24. Définissez le nom sur **group**
25. Cliquez sur **Enregistrer**.


## <a name="prepare-for-group-matching"></a>Préparer la correspondance de groupe

FortiGate offre différentes expériences de portail de l’utilisateur après la connexion, en fonction de l’appartenance à un groupe. Par exemple, il peut y avoir une expérience pour le groupe Marketing et une autre pour le groupe Finance.

Configurez cela comme suit :

### <a name="create-groups-for-users"></a>Créer des groupes pour les utilisateurs

1. Accédez à https://portal.azure.com et ouvrez le panneau Azure Active Directory pour le locataire qui fournira l’identité des connexions FortiGate.
2. Cliquez sur **Groupes**
3. Cliquez sur **Nouveau groupe**
4. Créez un groupe avec
    - Type de groupe = Sécurité
    - Nom du groupe = `a meaningful name`
    - Description du groupe = `a meaningful description for the group`
    - Type d’appartenance = Attribuée
    - Membres = `users for the user experience that will map to this group`
5. Répétez les étapes 3 et 4 pour toute expérience utilisateur supplémentaire.
6. Une fois les groupes créés, sélectionnez chaque groupe et enregistrez son ID d’objet.
7. Enregistrez ces ID d’objets et noms de groupes pour une utilisation ultérieure.


## <a name="configure-the-fortigate-vm"></a>Configurer la machine virtuelle FortiGate

### <a name="install-the-license"></a>Installer la licence

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Si le déploiement utilise le modèle BYOL, une invite de chargement de licence s’affiche. Sélectionnez le fichier de licence créé précédemment et chargez-le, cliquez sur **OK**, puis redémarrez la machine virtuelle FortiGate :

    ![FortiGate SSL VPN](license.png)

5. Après le redémarrage, connectez-vous à nouveau avec les informations d’identification d’administrateur pour valider la licence.

### <a name="update-firmware"></a>Mettre à jour le microprogramme

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, cliquez sur **System** (Système).
5. Dans le menu de gauche, sous Système, cliquez sur **Firmware** (Microprogramme).
6. Dans la page Firmware Management (Gestion du microprogramme), cliquez sur **Browse** (Parcourir) et sélectionnez le fichier de microprogramme téléchargé précédemment.
7. Ignorez l’avertissement et cliquez sur **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde) :

    ![FortiGate SSL VPN](backup-configure-upgrade.png)

8. Cliquez sur **Continuer**.
9. Lorsque vous êtes invité à enregistrer la configuration FortiGate (sous la forme d’un fichier .conf), cliquez sur **Save** (Enregistrer).
10. Attendez que le microprogramme se charge, qu’il soit appliqué et que la machine virtuelle FortiGate redémarre.
11. Après le redémarrage de la machine virtuelle FortiGate, connectez-vous à nouveau avec les informations d’identification d’administrateur.
12. Lorsque vous êtes invité à effectuer l’installation du tableau de bord, cliquez sur **Later** (Plus tard).
13. Au début de la vidéo du tutoriel, cliquez sur **OK**

### <a name="change-the-management-port-to-tcp"></a>Changer le port de gestion en port TCP

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, cliquez sur **System** (Système).
5. Sous Administration Settings (Paramètres d’administration), changez le port HTTPS en spécifiant **8443**
6. Cliquez sur **Appliquer**
7. Une fois la modification appliquée, le navigateur tente de recharger la page d’administration, mais l’opération échoue. Désormais, l’adresse de la page d’administration est `https://<address>`

    ![FortiGate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Charger le certificat de signature SAML Azure Active Directory

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, cliquez sur **System** (Système).
5. Sous Système, cliquez sur **Certificats**
6. Cliquez sur **Import** (Importer) - > **Remote Certificate** (Certificat distant).
7. Accédez au certificat téléchargé à partir du déploiement de l’application personnalisée FortiGate dans le locataire Azure, sélectionnez-le, puis cliquez sur **OK**

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Charger et configurer un certificat SSL personnalisé

Vous pouvez configurer la machine virtuelle FortiGate avec votre propre certificat SSL qui prend en charge le nom de domaine complet que vous utilisez. Si vous avez accès à un certificat SSL empaqueté avec la clé privée au format .PFX, il peut être utilisé à cet effet.

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Continuez malgré toute erreur de certificat éventuelle.
3. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
4. Dans le menu de gauche, cliquez sur **System** (Système).
5. Sous Système, cliquez sur **Certificats**
6. Cliquez sur **Import** (Importer) - > **Local Certificate** (Certificat local).
7. Cliquez sur **Certificat PKCS #12**
8. Accédez au fichier .PFX contenant le certificat SSL et la clé privée.
9. Spécifiez le mot de passe .PFX.
10. Spécifiez un nom explicite pour le certificat.
11. Cliquez sur **OK**
12. Dans le menu de gauche, cliquez sur **System** (Système).
13. Sous System (Système), cliquez sur **Settings** (Paramètres).
14. Sous Administration Settings (Paramètres d’administration), développez la liste déroulante en regard du certificat de serveur HTTPS et sélectionnez le certificat SSL importé ci-dessus.
15. Cliquez sur **Appliquer**
16. Fermez la fenêtre du navigateur, puis revenez à `https://<address>`
17. Connectez-vous avec les informations d’identification d’administrateur FortiGate et vérifiez que le certificat SSL approprié est utilisé.


### <a name="perform-command-line-configuration"></a>Effectuer la configuration de ligne de commande

_Configurer l’authentification SAML via la ligne de commande_

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, cliquez sur **Console série**
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate.

    Pour l’étape suivante, les URL enregistrées précédemment seront nécessaires. En l’occurrence :

    - L’ID d’entité
    - URL de réponse
    - URL de déconnexion
    - URL de connexion Azure
    - Identificateur Azure AD
    - URL de déconnexion Azure
4. Dans la console série, exécutez les commandes suivantes :

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
    > L’URL de déconnexion Azure contient un caractère ?. Une séquence de touches spéciale est requise pour fournir correctement ce caractère à la console série FortiGate. En règle générale, l’URL est :

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Pour fournir cela dans la console série, procédez en tapant

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Ensuite, tapez Ctrl+V,

    Collez ensuite le reste de l’URL pour terminer la ligne.

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Pour confirmer la configuration, exécutez :

    ```
    show user saml
    ```

_Configurer la correspondance de groupe via la ligne de commande_

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, cliquez sur **Console série**
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate.
4. Dans la console série, exécutez les commandes suivantes :

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

    Répétez les commandes à partir de edit `group 1 name` pour chaque groupe supplémentaire qui aura une expérience de portail différente dans FortiGate.

_Configurer le délai d’authentification via la ligne de commande_

1. Accédez à https://portal.azure.com et ouvrez les paramètres de la machine virtuelle FortiGate.
2. Dans le menu de gauche, cliquez sur **Console série**
3. Connectez-vous à la console série avec les informations d’identification d’administrateur de la machine virtuelle FortiGate.
4. Dans la console série, exécutez les commandes suivantes :

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Créer des portails VPN et une stratégie de pare-feu

1. Accédez à `https://<address>`.

    Où `address` est le nom de domaine complet ou l’adresse IP publique affecté(e) à la machine virtuelle FortiGate.

2. Connectez-vous à l’aide des informations d’identification d’administrateur fournies lors du déploiement de la machine virtuelle FortiGate.
3. Dans le menu de gauche, cliquez sur **VPN**
4. Sous VPN, cliquez sur **SSL-VPN Portals** (Portails SSL-VPN).
5. Cliquez sur **Create New** (Créer nouveau).
6. Spécifiez un nom (correspondant en général au groupe Azure utilisé pour fournir l’expérience de portail personnalisé).
7. Cliquez sur le signe plus ( **+** ) en regard de Source IP Pools (Pools d’adresses IP sources), sélectionnez le pool par défaut et cliquez sur **Fermer**
8. Personnalisez l’expérience de ce groupe. Pour le test, il est possible que ce soit la personnalisation du message du portail et du thème. C’est là également que vous pouvez créer des signets personnalisés qui dirigent les utilisateurs vers des ressources internes.
9. Cliquez sur **OK**
10. Répétez les étapes 5 à 9 pour chaque groupe Azure qui bénéficiera d’une expérience de portail personnalisée.
11. Sous VPN, cliquez sur **SSL-VPN Settings** (Paramètres SSL-VPN).
12. Cliquez sur le signe plus ( **+** ) en regard de Listen on Interfaces (Être à l’écoute des interfaces).
13. Sélectionnez **Port1** et cliquez sur **Close** (Fermer).


14. Si un certificat SSL personnalisé a été installé précédemment, modifiez le certificat de serveur pour utiliser ce certificat SSL personnalisé dans le menu déroulant.
15. Sous Authentication/Portal Mapping (Mappage de l’authentification/du portail), cliquez sur **Create New** (Créer nouveau).
16. Choisissez le premier groupe Azure et mettez-le en correspondance avec le portail du même nom.
17. Cliquez sur **OK**
18. Répétez les étapes 15 à 17 pour chaque paire groupe/portail Azure.
19. Sous le mappage de l’authentification/du portail, modifiez **All Other Users/Groups** (Tous les autres utilisateurs/groupes).
20. Configurez le portail sur **full-access** (accès complet).
21. Cliquez sur **OK**
22. Cliquez sur **Appliquer**
23. Faites défiler la page des paramètres SSL-VPN jusqu’en haut, puis cliquez sur l’avertissement **No SSL-VPN policies**
    **exist. Click here to create a new SSL-VPN policy using these settings** (Aucune stratégie SSL-VPN n’existe. Cliquez ici pour créer une nouvelle stratégie SSL-VPN avec ces paramètres).
24. Spécifiez un nom, tel que **VPN Grp**.
25. Configurez Outgoing Interface (Interface sortante) sur **port**
26. Cliquez sur **Source**
27. Sous Address (Adresse), sélectionnez **all** (tout).
28. Sous User (Utilisateur), sélectionnez le premier groupe Azure.
29. Cliquez sur **Fermer**.
30. Cliquez sur **Destination**
31. Sous Address (Adresse), il s’agit généralement du réseau interne. Sélectionnez login.microsoft.com pour les tests.
32. Cliquez sur **Fermer**.
33. Cliquez sur **Service**
34. Cliquez sur **All** (Tout).
35. Cliquez sur **Fermer**.
36. Cliquez sur **OK**
37. Dans le menu de gauche, cliquez sur **Policy & Objects** (Stratégie et objets).
38. Sous Policy & Objects (Stratégie et objets), cliquez sur **Firewall Policy** (Stratégie de pare-feu).
39. Développez **SSL-VPN tunnel interface (ssl.root) -> port** (Interface du tunnel SSL-VPN (ssl.root) -> port).
40. Cliquez avec le bouton droit sur la stratégie VPN créée précédemment ( **VPN Grp 1** ) et sélectionnez **Copier**
41. Cliquez avec le bouton droit sous la stratégie VPN et sélectionnez **Coller** - > **En dessous**
42. Modifiez la nouvelle stratégie en lui attribuant un nom différent (par exemple, **VPN Grp2** ) et en changeant le groupe auquel elle s’applique (en spécifiant un autre groupe Azure).
43. Cliquez avec le bouton droit sur la nouvelle stratégie et définissez l’état sur **Enabled** (Activé).


## <a name="test-sign-in-using-azure"></a>Tester la connexion à l’aide d’Azure

1. Accédez à `https://<address>` via une session de navigation privée. 
2. L’opération de connexion doit rediriger vers Azure Active Directory pour la connexion.
3. Une fois que vous avez fourni les informations d’identification d’un utilisateur qui a été affecté à l’application FortiGate dans le locataire Azure, le portail de l’utilisateur approprié doit s’afficher.

    ![FortiGate SSL VPN](test-sign-in.png)
