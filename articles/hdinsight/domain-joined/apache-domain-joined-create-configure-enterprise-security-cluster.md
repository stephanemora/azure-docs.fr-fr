---
title: Créer et configurer des clusters de Pack Sécurité Entreprise dans Azure HDInsight
description: Découvrez comment créer et configurer des clusters de Pack Sécurité Entreprise dans Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: e9cb9a902cf60fbd3b297a72a7dfa836ee18c835
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484581"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Créer et configurer des clusters de Pack Sécurité Entreprise dans Azure HDInsight

Le Pack Sécurité Entreprise (ESP) pour Azure HDInsight vous donne accès à l’authentification basée sur Active Directory, à la prise en charge multi-utilisateur et au contrôle d’accès en fonction du rôle pour vos clusters Apache Hadoop dans Azure. Les clusters ESP HDInsight permettent aux organisations, qui adhèrent aux stratégies de sécurité strictes de l’entreprise, de traiter les données sensibles en toute sécurité.

L’objectif de ce guide est d’apprendre à configurer correctement les ressources nécessaires afin que les utilisateurs sur site puissent se connecter à un cluster ESP HDInsight. Cet article décrit les étapes nécessaires pour créer un cluster Azure HDInsight compatible avec le Pack Sécurité Entreprise. Ces étapes expliquent la création d’une machine virtuelle IaaS Windows avec activation d’Active Directory et des services DNS. Ce serveur viendra en remplacement de votre environnement local **réel** et vous permettra de poursuivre les étapes d’installation et de configuration, que vous pourrez répéter ultérieurement dans votre propre environnement. Ce guide vous aidera également à créer un environnement d’identité hybride via la synchronisation du hachage de mot de passe avec Azure Active Directory.

Ce guide vient en complément de l’article [Utiliser le Pack Sécurité Entreprise dans HDInsight](apache-domain-joined-architecture.md)

Avant d’utiliser ce processus dans votre propre environnement, configurez Active Directory et les services DNS. De plus, activez Azure Active Directory et synchronisez les comptes d’utilisateurs locaux avec ce dernier.

![Diagramme d'architecture](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Créer un environnement local

Vue d’ensemble : Dans cette section, vous allez utiliser un modèle de déploiement rapide Azure pour créer des machines virtuelles, configurer les services DNS et créer une forêt AD.

1. Accédez à l’article [Créer une machine virtuelle Azure avec une nouvelle forêt AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/) pour afficher le modèle de déploiement rapide.

1. Cliquez sur **Déployer dans Azure**.
1. Connectez-vous à votre abonnement Azure.
1. À la page **Créer une machine virtuelle Azure avec une nouvelle forêt AD**, procédez comme suit :
    1. Sélectionnez l’abonnement sur lequel vous souhaitez déployer les ressources dans la liste déroulante **Abonnement**.
    1. Sélectionnez **Créer** en regard de **Groupe de ressources**, et entrez le nom **OnPremADVRG**
    1. Entrez les informations suivantes dans le reste des champs du modèle :

        * **Emplacement** : USA Centre
        * **Nom d’utilisateur administrateur** : HDIFabrikamAdmin
        * **Mot de passe administrateur** : <VOTRE_MOT_DE_PASSE>
        * **Domaine** : HDIFabrikam.com
        * **Préfixe DNS** : hdifabrikam

        ![Modèle Créer une machine virtuelle Azure et une forêt AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Cliquez sur **Achat**.
    1. Supervisez le déploiement et attendez qu’il se termine.
    1. Vérifiez que les ressources sont créées sous le bon groupe de ressources `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurer des utilisateurs et groupes pour l’accès au cluster

Vue d’ensemble : Dans cette section, vous allez créer les utilisateurs qui auront accès au cluster HDInsight d’ici la fin de ce guide.

1. Connectez-vous au contrôleur de domaine via le Bureau à distance.
    1. Si vous avez utilisé le modèle mentionné au début, le contrôleur de domaine est une machine virtuelle appelée **adVM** dans le groupe de ressources `OnPremADVRG`.
    1. Accédez au portail Azure > **Groupes de ressources** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Cliquez sur l’onglet **RDP**, puis sélectionnez **Télécharger le fichier RDP**.
    1. Enregistrez le fichier sur votre ordinateur et ouvrez-le.
    1. Lorsque vous êtes invité à entrer vos informations d’identification, utilisez `HDIFabrikam\HDIFabrikamAdmin` comme nom d’utilisateur, puis entrez le mot de passe que vous avez choisi pour le compte d’administrateur.

1. Une fois votre session Bureau à distance ouverte sur la machine virtuelle du contrôleur de domaine, lancez **Utilisateurs et ordinateurs Active Directory** depuis le tableau de bord **Gestionnaire de serveur**. Cliquez sur **Outils** dans le coin supérieur droit, puis choisissez **Utilisateurs et ordinateurs Active Directory** dans la liste déroulante.

    ![Gestionnaire de serveur, ouvrir Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Créez deux utilisateurs, **HDIAdmin**, **HDIUser**. Ces deux utilisateurs vous permettront de vous connecter à des clusters HDInsight.

    1. Dans l’écran **Utilisateurs et ordinateurs Active Directory**, cliquez sur **Action** > **Nouveau** > **Utilisateur**.

        ![Créer un utilisateur Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Dans l’écran **Nouvel objet - Utilisateur**, entrez `HDIUser` comme **Nom d’utilisateur d’ouverture de session** et cliquez sur **Suivant**.

        ![Créer le premier utilisateur administrateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. Dans la fenêtre contextuelle qui s’affiche, entrez le mot de passe souhaité pour le nouveau compte. Cochez la case indiquant que **Le mot de passe n’expire jamais**. Cliquez sur **OK**.
    1. Cliquez sur **Terminer** pour créer le compte.
    1. Créez un autre utilisateur `HDIAdmin`.

        ![Créer un deuxième utilisateur administrateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Dans l’écran **Utilisateurs et ordinateurs Active Directory**, cliquez sur **Action** > **Nouveau** > **Groupe**. Créez le groupe `HDIUserGroup`.

    ![Créer un groupe Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![créer nouveau groupe2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Ajoutez l’utilisateur **HDIUser** créé à l’étape précédente en tant que membre du groupe **HDIUserGroup**.

    1. Cliquez avec le bouton droit sur **HDIUserGroup** et sélectionnez **Propriétés**.
    1. Accédez à l’onglet **Membres** et cliquez sur **Ajouter**.
    1. Entrez `HDIUser` dans la zone intitulée **Entrez les noms des objets à sélectionner** et cliquez sur **OK**.
    1. Répétez les étapes précédentes pour l’autre compte `HDIAdmin`

        ![ajouter des membres au groupe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Vous venez de créer votre environnement Active Directory, ainsi que deux utilisateurs et un groupe d’utilisateurs pour l’accès au cluster HDInsight.

Ces utilisateurs seront synchronisés avec Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Créer un Azure Active Directory

1. Connectez-vous au portail Azure.
1. Cliquez sur **Créer une ressource** et tapez **directory**. Sélectionnez **Azure Active Directory** > **Créer**.
1. Entrez **HDIFabrikam** sous **Nom de l’organisation**.
1. Entrez **HDIFabrikamoutlook** sous **Nom de domaine initial**.
1. Cliquez sur **Créer**.
1. À gauche dans le Portail Azure, cliquez sur **Azure Active Directory**.
1. Si nécessaire, cliquez sur **Changer de répertoire** pour définir le paramètre sur le répertoire que vous avez créé **HDIFabrikamoutlook**.
1. Dans la section **Gérer**, cliquez sur **Noms de domaine personnalisés** > **Ajouter un domaine personnalisé**.
1. Entrez **HDIFabrikam.com** comme **Nom de domaine personnalisé** et cliquez sur **Ajouter un domaine**.

![créer un azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![créer un domaine personnalisé](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurer votre locataire Azure AD

Vue d’ensemble : Maintenant, vous allez configurer votre locataire Azure AD afin de pouvoir synchroniser les utilisateurs depuis votre AD local vers le cloud.

1. Créez un administrateur de locataires AD.
    1. Connectez-vous au Portail Azure et sélectionnez votre locataire Azure AD **HDIFabrikam**
    1. Dans la section **Gérer**, sélectionnez **Utilisateurs**, puis **Nouvel utilisateur**.
    1. Entrez les informations suivantes pour le nouvel utilisateur :

        * Nom : fabrikamazureadmin
        * Nom d’utilisateur : fabrikamazureadmin@hdifabrikam.com
        * Mot de passe : mot de passe sécurisé de votre choix

    1. Cliquez sur la section **Groupes**, recherchez **Administrateurs AAD DC**, puis choisissez **Sélectionner**.

        ![Groupes](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Cliquez sur la section **Rôle d’annuaire** et sélectionnez **Administrateur général** sur la droite. Cliquez sur **OK**.

        ![Rôle d’annuaire](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Entrez un mot de passe pour l’utilisateur. Cliquez sur **Créer**.

1. Si vous souhaitez modifier le mot de passe pour l’utilisateur nouvellement créé <fabrikamazureadmin@hdifabrikam.com>, connectez-vous au Portail Azure avec cette identité, puis vous serez invité à modifier le mot de passe.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchroniser les utilisateurs locaux avec Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Télécharger et installer Microsoft Azure Active Directory Connect

1. [Téléchargez Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installez Microsoft Azure Active Directory Connect sur le contrôleur de domaine.
    1. Ouvrez le fichier exécutable que vous avez téléchargé à l’étape précédente et acceptez les termes du contrat de licence. Cliquez sur **Continuer**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Cliquez sur **Utiliser la configuration rapide** et terminez l’installation.

        ![Utiliser la configuration rapide](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurer la synchronisation avec le contrôleur de domaine local

1. Sur l’écran **Connexion à Azure AD**, entrez le nom d’utilisateur et le mot de passe de l’administrateur général pour l’instance Azure AD. Cliquez sur  **Suivant**. Il s’agit du nom d’utilisateur `fabrikamazureadmin@hdifabrikam.com` que vous avez créé lors de la configuration de votre locataire AD.
    ![Connexion à Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Dans l’écran **Connexion à Active Directory Domain Services**, entrez le nom d’utilisateur et le mot de passe d’un compte d’administrateur d’entreprise. Cliquez sur  **Suivant**. Il s’agit du nom d’utilisateur `HDIFabrikam\HDIFabrikamAdmin` et du mot de passe correspondant créé précédemment.

   ![Se connecter à Azure Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Dans la page **Configuration de la connexion à Azure AD**, cliquez sur **Suivant**.
    ![Configuration de la connexion à Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Dans l’écran Prêt à configurer, cliquez sur  **Installer**.
    ![installer](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Lorsque l’écran **Configuration terminée** s’affiche, cliquez sur **Quitter**.
    ![configuration terminée](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Une fois la synchronisation terminée, vérifiez que les utilisateurs créés sur l’annuaire IaaS Active Directory sont synchronisés avec Azure Active Directory.
    1. Connectez-vous au portail Azure.
    1. Sélectionnez **Azure Active Directory** > **HDIFabrikam** > **Utilisateurs**.

### <a name="create-an-user-assigned-managed-identity"></a>Créer une identité managée affectée par l’utilisateur

Créez une identité managée affectée par l’utilisateur qui sera utilisée pour configurer Azure Active Directory Domain Services (Azure AD-DS). Pour en savoir plus sur la création d’une identité managée affectée par l’utilisateur, reportez-vous à l’article [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Connectez-vous au portail Azure.
1. Cliquez sur **Créer une ressource** et tapez **identité managée**. Sélectionnez **Identité managée affectée par l’utilisateur** > **Créer**.
1. Entrez **HDIFabrikamManagedIdentity** comme **Nom de la ressource**.
1. Sélectionnez votre abonnement.
1. Sous **Groupe de ressources**, cliquez sur **Créer** et entrez **HDIFabrikam-CentralUS**.
1. Sous **Emplacement**, sélectionnez **USA Centre**.
1. Cliquez sur **Créer**.

![créer une identité managée affectée par l’utilisateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Activation d’Azure Active Directory Domain Services

Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Créez le réseau virtuel qui servira à héberger Azure AD-DS. Exécutez le code PowerShell suivant.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Connectez-vous au portail Azure.
1. Cliquez sur **Créer une ressource**, entrez **Domain Services** et sélectionnez **Azure AD Domain Services**.
1. Effectuez ensuite les tâches suivantes dans l’écran **Bases** :
    1. Sous **Nom de répertoire**, sélectionnez l’annuaire Azure Active Directory créé dans cet article, **HDIFabrikam**.
    1. Entrez le **nom de domaine DNS** **HDIFabrikam.com**.
    1. Sélectionnez votre abonnement.
    1. Spécifiez le groupe de ressources **HDIFabrikam-CentralUS** et l’**Emplacement** **USA Centre**.

        ![informations de base azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Dans l’écran **Réseau** renseigné, sélectionnez le réseau (**HDIFabrikam-VNET**) et le sous-réseau (**AADDS-subnet**) que vous avez créés à l’aide du script PowerShell précédent. Vous pouvez également utiliser l’option **Créer** pour créer un réseau virtuel maintenant.

    ![sélectionner le réseau](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Dans l’écran **Groupe d’administrateurs**, vous devez voir une notification indiquant qu’un groupe nommé **Administrateurs AAD DC** a déjà été créé pour administrer ce groupe. Vous pouvez éventuellement modifier l’appartenance à ce groupe, mais cela n’est pas obligatoire pour les étapes décrites dans cet article. Cliquez sur **OK**.

    ![afficher un groupe d’administrateurs](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Dans l’écran **Synchronisation**, activez la synchronisation en sélectionnant **Tous**, puis **OK**.

    ![activer la synchronisation](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Dans l’écran **Résumé**, vérifiez les détails concernant Azure AD-DS, puis cliquez sur **Ok**.

    ![vérifier les détails](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Après avoir activé Azure AD-DS, un serveur DNS (Domain Name System) local s’exécute sur les machines virtuelles AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurer votre réseau virtuel Azure AD-DS

Les étapes décrites dans cette section vous aideront à configurer votre réseau virtuel Azure AD-DS (**HDIFabrikam-AADDSVNET**) afin qu’il utilise vos serveurs DNS personnalisés.

1. Recherchez les adresses IP de vos serveurs DNS personnalisés. Cliquez sur la ressource AD-DS **HDIFabrikam.com**, sélectionnez  **Propriétés** dans la section **Gérer**  et notez les adresses IP répertoriées sous  **Adresse IP sur le réseau virtuel**.

    ![Rechercher les adresses IP DNS personnalisées pour Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Configurez **HDIFabrikam-AADDSVNET** sur les adresses IP personnalisées `10.0.0.4` et `10.0.0.5`.

    1. Dans la catégorie **Paramètres** , sélectionnez  **Serveurs DNS** . Ensuite, cliquez sur la case d’option située en regard de  **Personnalisé**, entrez la première adresse IP (10.0.0.4) dans la zone de texte ci-dessous, puis cliquez sur  **Enregistrer**.
    1. Ajoutez une adresse IP supplémentaire (10.0.0.5) en suivant la même procédure.

1. Dans notre scénario, Azure AD-DS a été configuré pour utiliser les adresses IP 10.0.0.4 et 10.0.0.5, en définissant la même adresse IP sur le réseau virtuel AADDS que celle indiquée dans l’image ci-dessous.

    ![afficher les serveurs dns personnalisés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Sécurisation du trafic LDAP

Le protocole LDAP (Lightweight Directory Access Protocol) est utilisé pour la lecture et l’écriture sur Active Directory. Vous pouvez rendre le trafic LDAP confidentiel et sécurisé à l’aide de la technologie SSL (Secure Sockets Layer)/TLS (Transport Layer Security). Vous pouvez activer le protocole LDAP sur SSL (LDAPS) en installant un certificat au format correct.

Pour plus d’informations sur le protocole LDAP sécurisé, consultez [Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine managé Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Dans cette section, vous allez créer un certificat auto-signé, télécharger le certificat et configurer le protocole LDAP sécurisé (LDAPS) pour le domaine managé Azure AD-DS **hdifabrikam**.

Le script suivant crée un certificat pour hdifabrikam. Le certificat est enregistré sous le chemin d’accès « LocalMachine ».

> [!Note] 
> Tous les utilitaires ou applications qui créent une demande PKCS \#10 valide peuvent être utilisés pour former la demande de certificat SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Vérifiez que le certificat est installé dans la banque personnelle de l\'ordinateur. Effectuez ensuite les tâches suivantes :

1. Démarrez Microsoft Management Console (MMC).
1. Ajoutez le composant logiciel enfichable Certificats qui gère les certificats sur l’ordinateur local.
1. Développez  **Certificats (ordinateur local)** , puis  **Personnel**, et enfin  **Certificats**. Un nouveau certificat doit être présent dans la banque personnelle. Ce certificat est émis avec le nom d’hôte complet.

    ![vérifier la création du certificat](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. Dans le volet de droite, cliquez sur le certificat créé à l’étape précédente avec le bouton droit de la souris, sélectionnez  **Toutes les tâches**, puis cliquez sur  **Exporter**.

1. Dans la page  **Exporter la clé privée** , cliquez sur  **Oui, exporter la clé privée**. La clé privée est requise pour la lecture des messages chiffrés à partir de l’ordinateur sur lequel la clé sera importée.

    ![Exporter la clé privée](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Dans la page  **Format du fichier d’exportation** , conservez les paramètres par défaut et cliquez sur  **Suivant**. 
1. Dans la page  **Mot de passe** , tapez un mot de passe pour la clé privée, sélectionnez **TripleDES-SHA1** comme **Chiffrement** et cliquez sur **Suivant**.
1. Dans la page  **Fichier à exporter** , tapez le chemin d’accès et le nom du fichier de certificat exporté, puis cliquez sur  **Suivant**.
1. L’extension du nom de fichier doit être .pfx. Ce fichier est configuré sur le Portail Azure pour établir une connexion sécurisée.
1. Activez le protocole LDAP sécurisé (LDAPS) pour un domaine managé Azure AD Domain Services.
    1. Sélectionnez le domaine **HDIFabrikam.com** dans le Portail Azure.
    1. Dans la section **Gérer**, cliquez sur **LDAP sécurisé**.
    1. Dans l’écran **LDAP sécurisé**, cliquez sur **Activer** sous **LDAP sécurisé**.
    1. Recherchez le fichier de certificat .pfx que vous avez exporté sur votre ordinateur.
    1. Saisissez le mot de passe du certificat.

    ![activer le protocole LDAP sécurisé](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Maintenant que vous avez activé le protocole LDAP sécurisé, veillez à ce qu’il soit accessible en activant le port 636.
    1. Cliquez sur le groupe de sécurité réseau **AADDS-HDIFabrikam.com-NSG** dans le groupe de ressources **HDIFabrikam-CentralUS**.
    1. Dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant** > **Ajouter**.
    1. Dans l’écran **Ajouter une règle de sécurité de trafic entrant**, entrez les propriétés suivantes, puis cliquez sur **Ajouter** :

        | Propriété | Valeur |
        |---|---|
        | Source | Quelconque |
        | Plages de ports source | * |
        | Destination | Quelconque |
        | Plage de ports de destination | 636 |
        | Protocole | Quelconque |
        | Action | AUTORISER |
        | Priorité | <Desired Number> |
        | Nom | Port_LDAP_636 |

    ![règle de sécurité de trafic entrant](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` est l’identité managée affectée par un utilisateur. Le rôle Contributeur aux services de domaine HDInsight est associé à cette identité managée qui pourra donc lire, créer, modifier et supprimer des opérations de services de domaine.

    ![créer une identité managée affectée par l’utilisateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Création d’un cluster HDInsight avec le Pack Sécurité Entreprise

Cette étape nécessite les prérequis suivants :

1. Créez un groupe de ressources `HDIFabrikam-WestUS` dans la région `West US`.
1. Créez un réseau virtuel qui hébergera le cluster HDInsight ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Créez une relation homologue entre le réseau virtuel qui hébergera AADDS (`HDIFabrikam-AADDSVNET`) et celui qui hébergera le cluster HDInsight ESP (`HDIFabrikam-HDIVNet`). Utilisez le code PowerShell suivant pour appairer ces deux réseaux virtuels.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Créez un compte Azure Data Lake Storage Gen2, **Hdigen2store**, configuré avec l’identité managée affectée par l’utilisateur **HDIFabrikamManagedIdentity**. Pour plus d’informations sur la création de comptes Data Lake Storage Gen2 compatibles avec les identités managées affectées par l’utilisateur, consultez l’article [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurez un serveur DNS personnalisé sur le réseau virtuel **HDIFabrikam-AADDSVNET**.
    1. Dans le Portail Azure, accédez à > **Groupes de ressources** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **Serveurs DNS**.
    1. Sélectionnez **Personnalisé** et entrez `10.0.0.4` et `10.0.0.5`.
    1. Cliquez sur **Enregistrer**.

        ![enregistrer les paramètres du serveur dns personnalisé](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Créez un cluster Spark HDInsight ESP.
    1. Cliquez sur **Personnalisé (taille, paramètres, applications)** .
    2. Entrez les détails de votre choix dans la section 1 **Bases**. Vérifiez que le **type de Cluster** est défini sur **Spark 2.3 (HDI 3.6)** et le **Groupe de ressources** sur **HDIFabrikam-CentralUS**

    1. Dans la section 2 **Sécurité + mise en réseau**, procédez comme suit:
        1. Cliquez sur **Activé** dans la section **Pack Sécurité Entreprise**.
        1. Cliquez sur **Utilisateur administrateur de cluster** et sélectionnez le compte **HDIAdmin** créé précédemment comme utilisateur administrateur local. Cliquez sur **Sélectionner**.

        1. Cliquez sur **Groupe d’accès au cluster**, puis sélectionnez **HDIUserGroup**. Tout utilisateur que vous ajouterez à ce groupe sera en mesure d’accéder aux clusters HDInsight.

            ![sélectionner le groupe d’accès au cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Effectuez les autres étapes de la configuration du cluster et vérifiez les détails dans le **Résumé du cluster**. Cliquez sur **Créer**.

1. Connectez-vous à l’interface utilisateur d’Ambari pour le cluster nouvellement créé à l’adresse `https://CLUSTERNAME.azurehdinsight.net` avec votre nom d’utilisateur administrateur `hdiadmin@hdifabrikam.com` et votre mot de passe.

    ![se connecter à Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Cliquez sur **Rôles** dans le tableau de bord du cluster.
1. Dans la page **Rôles**, entrez le groupe **hdiusergroup** pour l’affecter au rôle **Administrateur de cluster** dans la section **Affecter des rôles à**.

    ![affecter le rôle d’administrateur de cluster à hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Ouvrez votre client SSH et connectez-vous au cluster en utilisant le **hdiuser** créé précédemment dans Active Directory en local.

    ![connexion au cluster avec SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Si vous parvenez à vous connecter avec ce compte, c’est que vous avez configuré votre cluster ESP correctement pour la synchronisation avec Active Directory en local.

## <a name="next-steps"></a>Étapes suivantes

* [Introduction à la sécurité Apache Hadoop avec le Pack Sécurité Entreprise](apache-domain-joined-introduction.md)
