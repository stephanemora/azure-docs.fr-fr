---
title: Créer et configurer des clusters de Package de sécurité d’entreprise dans Azure HDInsight
description: Découvrez comment créer et configurer des clusters de Package de sécurité d’entreprise dans Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: 5b5b83fe0028e43ca35bf883b29cb71bad6ca2c8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253701"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Créer et configurer des clusters de Package de sécurité d’entreprise dans Azure HDInsight

Le Package de sécurité d’entreprise pour Azure HDInsight vous donne accès à l’authentification basée sur Active Directory, prise en charge multi-utilisateur et contrôle d’accès en fonction du rôle pour vos clusters Apache Hadoop dans Azure. Les clusters HDInsight ESP permettent aux organisations, qui adhèrent aux stratégies de sécurité stricte de l’entreprise, pour traiter les données sensibles en toute sécurité.

L’objectif de ce guide est configuré correctement les ressources nécessaires afin que local utilisateurs peuvent se connecter à un ESP activé HDInsight cluster. Cet article décrit les étapes nécessaires pour créer un Cluster HDInsight de Azure Enterprise Security Package est activé. Couvrent les étapes de création d’une machine virtuelle IaaS de Windows avec Active Directory et les Services DNS (Domain Name) est activée. Ce serveur se comportera comme un remplacement pour votre **réel** environnement local et vous permettra de continuer à travers les étapes d’installation et la configuration afin que vous pouvez répéter les plus loin dans votre propre environnement. Ce guide vous aidera également à créer un environnement d’identité hybride à l’aide de la synchronisation du hachage de mot de passe avec Azure Active Directory.

Ce guide est destiné à compléter [utilisez Enterprise Security Package dans HDInsight](apache-domain-joined-architecture.md)

Avant d’utiliser ce processus dans votre propre environnement, le programme d’installation Active Directory et les Services DNS (Domain Name). En outre, activez Azure Active Directory et synchronisation des comptes d’utilisateurs à Azure Active Directory sur site.

![Diagramme d'architecture](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Créer l’environnement local

Vue d’ensemble : Dans cette section, vous allez utiliser un modèle de déploiement rapide Azure pour créer des machines virtuelles, configurez les Services DNS (Domain Name) et une nouvelle forêt AD.

1. Accédez à [créer une machine virtuelle Azure avec une nouvelle forêt AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), pour afficher le modèle de déploiement rapide.

1. Cliquez sur **déployer vers Azure**.
1. Connectez-vous à votre abonnement Azure.
1. Sur le **créer une machine virtuelle Azure avec une nouvelle forêt AD** écran, procédez comme suit :
    1. Sélectionnez l’abonnement où vous souhaitez les ressources déployées à partir de la **abonnement** liste déroulante.
    1. Sélectionnez **créer** regard **groupe de ressources** et entrez le nom **OnPremADVRG**
    1. Entrez les informations suivantes pour le reste des champs de modèle :

        * **Emplacement** : USA Centre
        * **Nom d’utilisateur administrateur** : HDIFabrikamAdmin
        * **Mot de passe administrateur**: < YOUR_PASSWORD >
        * **Domaine**: HDIFabrikam.com
        * **Préfixe DNS**: hdifabrikam

        ![Modèle créer la machine virtuelle Azure et la forêt Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Cliquez sur **achat**
    1. Surveiller le déploiement et attendre qu’elle se termine.
    1. Vérifiez les ressources sont créés sous le groupe de ressources correct `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurer des utilisateurs et groupes pour l’accès au cluster

Vue d’ensemble : Dans cette section, vous allez créer les utilisateurs qui auront accès au cluster HDInsight à la fin de ce guide.

1. Se connecter au contrôleur de domaine à l’aide du Bureau à distance.
    1. Si vous avez utilisé le modèle mentionné au début, le contrôleur de domaine est une machine virtuelle appelée **adVM** dans le `OnPremADVRG` groupe de ressources.
    1. Accédez au portail Azure > **groupes de ressources** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Cliquez sur le **RDP** onglet, puis cliquez sur **télécharger le fichier RDP**.
    1. Enregistrez le fichier sur votre ordinateur et ouvrez-le.
    1. Lorsque vous y êtes invité pour les informations d’identification, utilisez `HDIFabrikam\HDIFabrikamAdmin` comme le nom d’utilisateur, puis entrez le mot de passe que vous avez choisi pour le compte d’administrateur.

1. Une fois votre session Bureau à distance s’ouvre sur la machine virtuelle du contrôleur de domaine, lancez **Active Directory Users and Computers** à partir de la **le Gestionnaire de serveur** tableau de bord. Cliquez sur **outils** dans le coin supérieur droit, puis **Active Directory Users and Computers** dans la liste déroulante.

    ![Le Gestionnaire de serveur Open Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Créez deux nouveaux utilisateurs, **HDIAdmin**, **HDIUser**. Ces deux utilisateurs permet de vous connecter à des clusters HDInsight.

    1. Dans le **Active Directory Users and Computers** , cliquez sur **Action** > **New** > **utilisateur**.

        ![Créer utilisateur Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Dans le **nouvel objet - utilisateur** écran, entrez `HDIUser` en tant que le **nom d’ouverture de session utilisateur** et cliquez sur **suivant**.

        ![Créer le premier utilisateur administrateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. Dans le menu contextuel qui s’affiche, entrez le mot de passe pour le nouveau compte. Cochez la case indiquant que **mot de passe n’expire jamais**. HDIClick **OK**.
    1. Cliquez sur **Terminer** pour créer le nouveau compte.
    1. Créez un autre utilisateur `HDIAdmin`.

        ![Créer un deuxième utilisateur administrateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Dans le **Active Directory Users and Computers** , cliquez sur **Action** > **New** > **groupe**. Créer `HDIUserGroup` en tant que nouveau groupe.

    ![Créer le nouveau groupe Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Créer nouveau groupe2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Ajouter le **HDIUser** créé à l’étape précédente pour le **HDIUserGroup** en tant que membre.

    1. Cliquez avec le bouton droit sur le **HDIUserGroup** et cliquez sur **propriétés**.
    1. Accédez à **membres** onglet et cliquez sur **ajouter**.
    1. Entrée `HDIUser` dans la zone intitulée **Entrez les noms des objets à sélectionner** et cliquez sur **OK**.
    1. Répétez les étapes précédentes pour l’autre compte `HDIAdmin`

        ![ajouter des membres au groupe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Vous venez de créer votre environnement Active Directory, ainsi que deux utilisateurs et un groupe d’utilisateurs pour l’accès au cluster HDInsight.

Ces utilisateurs seront synchronisés avec Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Créer un nouveau Azure Active Directory

1. Connectez-vous au portail Azure.
1. Cliquez sur **créer une ressource** et type **directory**. Sélectionnez **Azure Active Directory** > **créer**.
1. Entrez **HDIFabrikam** sous **Nom_organisation**.
1. Entrez **HDIFabrikamoutlook** sous **nom de domaine Initial**.
1. Cliquez sur **Créer**.
1. Sur la gauche dans le portail Azure, cliquez sur **Azure Active Directory**.
1. Si nécessaire, cliquez sur **changer de répertoire** à modifier dans le nouveau répertoire que vous avez créé **HDIFabrikamoutlook**.
1. Sous **gérer** cliquez sur **noms de domaine personnalisés** > **ajouter un domaine personnalisé**.
1. Entrez **HDIFabrikam.com** sous **nom de domaine personnalisé** et cliquez sur **ajouter un domaine**.

![Créez un répertoire azure actif](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![créer un nouveau domaine personnalisé](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurer votre client Azure AD

Vue d’ensemble : Maintenant vous allez configurer votre client Azure AD afin que vous pouvez synchroniser les utilisateurs et groupes en local AD dans le cloud.

1. Créer un administrateur de locataire Active Directory.
    1. Connectez-vous au portail Azure et sélectionnez votre client Azure AD **HDIFabrikam**
    1. Sélectionnez **utilisateurs** sous **gérer** , puis **nouvel utilisateur**.
    1. Entrez les informations suivantes pour le nouvel utilisateur :

        * Nom : fabrikamazureadmin
        * Nom d’utilisateur : fabrikamazureadmin@hdifabrikam.com
        * Mot de passe : un mot de passe sécurisé de votre choix

    1. Cliquez sur le **groupes** section, recherchez **AAD DC Administrators**, puis cliquez sur **sélectionnez**.

        ![Groupes](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Cliquez sur le **rôle d’annuaire** section et sélectionnez **administrateur général** sur le côté droit. Cliquez sur **OK**.

        ![Rôle d’annuaire](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Entrez un mot de passe de l’utilisateur. Cliquez sur **Créer**.

1. Si vous souhaitez modifier le mot de passe pour l’utilisateur nouvellement créé <fabrikamazureadmin@hdifabrikam.com>. Se connecter pour le portail Azure en utilisant l’identité, puis vous sera invité à modifier le mot de passe.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchroniser les utilisateurs locaux à Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Téléchargez et installez Microsoft Azure Active Directory se connecter

1. [Téléchargez Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installer Microsoft Azure Active Directory connect sur le contrôleur de domaine.
    1. Ouvrez le fichier exécutable que vous avez téléchargé à l’étape précédente et j’accepte les termes du contrat de licence. Cliquez sur **Continuer**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Cliquez sur **utiliser les paramètres express** et terminer l’installation.

        ![Utiliser les paramètres express](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurer la synchronisation avec le contrôleur de domaine local

1. Sur le **se connecter à Azure AD** écran, entrez le nom d’utilisateur et le mot de passe de l’administrateur général pour Azure AD. Cliquez sur **suivant**. Il s’agit du nom d’utilisateur `fabrikamazureadmin@hdifabrikam.com` que vous avez créé lors de la configuration de votre client AD.
    ![Connexion à Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Sur le **se connecter aux Services de domaine Active Directory** écran, entrez le nom d’utilisateur et le mot de passe pour un compte administrateur d’entreprise. Cliquez sur **suivant**. Il s’agit du nom d’utilisateur `HDIFabrikam\HDIFabrikamAdmin` et son mot de passe correspondant que vous avez créé précédemment.

   ![Se connecter aux Services de domaine Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Sur le **configuration de la connexion Azure AD** , cliquez sur **suivant**.
    ![Configuration de connexion AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Dans l’écran prêt à configurer, cliquez sur **installer**.
    ![installer](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Lorsque le **d’effectuer la Configuration** écran s’affiche, cliquez sur **Exit**.
    ![Configuration terminée](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Une fois la synchronisation terminée confirmer si les utilisateurs que vous avez créé sur IAAS Active Directory sont synchronisés avec Azure Active Directory.
    1. Connectez-vous au portail Azure.
    1. Sélectionnez **Azure Active Directory** > **HDIFabrikam** > **utilisateurs**.

### <a name="create-an-user-assigned-managed-identity"></a>Créer une identité gérée affectée à l’utilisateur

Créer une identité gérée affectée à l’utilisateur qui sera utilisée pour configurer Azure Active Directory Domain Services (Azure AD-DS). Pour plus d’informations sur la création d’une identité gérée affectée à l’utilisateur, consultez [créer, attribuer un rôle à une identité gérée affectée à l’utilisateur à l’aide du portail Azure, de la suppression ou de la liste](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Connectez-vous au portail Azure.
1. Cliquez sur **créer une ressource** et type **identité gérée**. Sélectionnez **utilisateur affecté identité gérée** > **créer**.
1. Entrez **HDIFabrikamManagedIdentity** en tant que le **nom de la ressource**.
1. Sélectionnez votre abonnement.
1. Sous **groupe de ressources** cliquez sur **créer** et entrez **HDIFabrikam-CentralUS**.
1. Sélectionnez **centre des États-Unis** sous **emplacement**.
1. Cliquez sur **Créer**.

![créer une nouvelle identité gérée affectée à l’utilisateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Activation d’Azure Active Directory Domain Services

Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Créez le réseau virtuel à l’hôte Azure AD-DS. Exécutez le code powershell suivant.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Connectez-vous au portail Azure.
1. Cliquez sur **créer une ressource**, entrez **les services de domaine** et sélectionnez **Azure AD Domain Services**.
1. Sur le **notions de base** écran, procédez comme suit :
    1. Sous **nom du répertoire** sélectionnez Azure Active Directory créée pour ce didacticiel, **HDIFabrikam**.
    1. Entrez un **nom de domaine DNS** de **HDIFabrikam.com**.
    1. Sélectionnez votre abonnement.
    1. Spécifiez le groupe de ressources **HDIFabrikam-CentralUS** et **emplacement** de **centre des États-Unis**.

        ![informations de base Azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Sur le **réseau** terminée, sélectionnez le réseau (**HDIFabrikam-réseau virtuel**) et le sous-réseau (**AADDS-sous-réseau**) que vous avez créé avec le script powershell précédent. Vous pouvez également utiliser le **créer** option permettant de créer un réseau virtuel maintenant.

    ![Sélectionnez le réseau](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Sur le **groupe administrateur** écran, vous devez voir une notification indiquant qu’un groupe nommé **AAD DC Administrators** a déjà été créé pour administrer ce groupe. Vous pouvez éventuellement modifier l’appartenance à ce groupe, mais il n’est pas requis pour les étapes de ce didacticiel. Cliquez sur **OK**.

    ![afficher le groupe administrateur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Sur le **synchronisation** écran, permettent d’effectuer la synchronisation en sélectionnant **tous les** puis cliquez sur **OK**.

    ![Activer la synchronisation](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Sur le **Résumé** écran, vérifiez les détails pour Azure AD-DS, puis cliquez sur **Ok**.

    ![Vérifiez les détails](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Après avoir activé Azure AD-DS, un serveur DNS (Domain Name System) local s’exécute sur les machines virtuelles AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurer votre réseau virtuel Azure AD-DS

Les étapes décrites dans cette section vous aideront à configurer votre réseau virtuel Azure AD-DS (**HDIFabrikam-AADDSVNET**) à utiliser vos serveurs DNS personnalisés.

1. Recherchez les adresses IP de vos serveurs DNS personnalisés. Cliquez sur le **HDIFabrikam.com** ressource de AD DS, cliquez sur **propriétés** sous **gérer**   et examinez les adresses IP répertoriées sous **IP Adresse sur réseau virtuel**.

    ![Recherchez des adresses IP DNS personnalisés pour Azure AD-DS.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Configurer **HDIFabrikam-AADDSVNET** à des adresses IP personnalisé `10.0.0.4` et `10.0.0.5`.

    1. Sélectionnez **serveurs DNS** sous le **paramètres** catégorie. puis cliquez sur le bouton radio en regard **personnalisé**, entrez la première adresse IP (10.0.0.4) dans la zone de texte ci-dessous, puis cliquez sur **enregistrer**.
    1. Ajouter des adresses IP (10.0.0.5) à l’aide de la même procédure.

1. Dans notre scénario, Azure AD-DS a été configurés pour utiliser l’adresse des adresses IP 10.0.0.4 et 10.0.0.5, utilisant la même configuration sur un réseau virtuel AADDS comme indiqué dans l’image ci-dessous.

    ![afficher les serveurs dns personnalisés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Sécurisation du trafic LDAP

Lightweight Directory Access LDAP (Protocol) est utilisé pour lire et écrire dans Active Directory. Vous pouvez rendre LDAP du trafic confidentiel et sécurisé à l’aide de SSL Secure Sockets Layer () / technologie de sécurité TLS (Transport Layer). Vous pouvez activer le protocole LDAP sur SSL (LDAPS) en installant un certificat correctement mis en forme.

Pour plus d’informations sur le protocole LDAP sécurisé, consultez [configurer LDAP sécurisé (LDAPS) pour un Azure AD Domain Services de domaine managé](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Dans cette section, vous créez un certificat auto-signé, téléchargez le certificat et configurer LDAP sécurisé (LDAPS) pour le **hdifabrikam** domaine géré par Azure AD-DS.

Le script suivant crée un certificat pour hdifabrikam. Le certificat est enregistré sous le chemin d’accès « LocalMachine ».

> [!Note] 
> Un utilitaire ou une application qui crée un PKCS valide \#demande 10 peut être utilisé pour former la demande de certificat SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Vérifiez que le certificat est installé sur l’ordinateur\'magasin personnel de s. Effectuez ensuite les tâches suivantes :

1. Démarrez Microsoft Management Console (MMC).
1. Ajouter le composant logiciel enfichable Certificats qui gère les certificats sur l’ordinateur local.
1. Développez **certificats (ordinateur Local)** , développez **personnel**, puis développez **certificats**. Un nouveau certificat doit exister dans le magasin personnel. Ce certificat est émis au nom d’hôte entièrement qualifié.

    ![vérifier la création du certificat](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. Dans le volet droit, cliquez sur le certificat que vous avez créé à l’étape précédente, pointez sur **toutes les tâches**, puis cliquez sur **exporter**.

1. Sur le **exporter la clé privée** , cliquez sur **Oui, exporter la clé privée,** . La clé privée est nécessaire pour les messages chiffrés à lire à partir de l’ordinateur où la clé sera importée.

    ![Exporter la clé privée](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Sur le **Format de fichier d’exportation** page, laissez les paramètres par défaut, puis cliquez sur **suivant**. 
1. Sur le **mot de passe** , tapez un mot de passe pour la clé privée, sélectionnez **TripleDES-SHA1** pour **chiffrement** et cliquez sur **suivant**.
1. Sur le **fichier à exporter** page, tapez le chemin d’accès et le nom du fichier de certificat exporté, puis cliquez sur **suivant**.
1. Le nom de fichier doit être extension .pfx, ce fichier est configuré sur le portail Azure pour établir une connexion sécurisée.
1. Activer LDAP sécurisé (LDAPS) pour un domaine géré par Azure AD Domain Services.
    1. Sélectionnez le domaine **HDIFabrikam.com** à partir du portail Azure.
    1. Cliquez sur **LDAP sécurisé** sous **gérer**.
    1. Sur le **LDAP sécurisé** , cliquez sur **activer** sous **LDAP sécurisé**.
    1. Rechercher le fichier de certificat .pfx que vous avez exporté sur votre ordinateur.
    1. Entrez le mot de passe du certificat.

    ![Activer le protocole ldap sécurisé](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Maintenant que vous avez activé le protocole LDAP sécurisé, veillez à ce qu’elle soit accessible en activant le port 636.
    1. Cliquez sur le groupe de sécurité réseau **AADDS-HDIFabrikam.com-NSG** dans le **HDIFabrikam-CentralUS** groupe de ressources.
    1. Sous **paramètres** cliquez sur **règles de sécurité entrantes** > **ajouter**.
    1. Sur le **ajouter une règle de sécurité de trafic entrant** écran, entrez les propriétés suivantes, puis cliquez sur **ajouter**:

        | Propriété | Valeur |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number> |
        | Name | Port_LDAP_636 |

    ![règle de sécurité de trafic entrant](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` est l’utilisateur affecté identité gérée, le rôle de contributeur de Services de domaine HDInsight est activé pour l’identité gérée qui permettra cette identité pour le lire, créer, modifier et supprimer des opérations de services de domaine.

    ![Créer utilisateur attribué l’identité gérée](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Création de Package de sécurité d’entreprise activé HDInsight cluster

Cette étape nécessite les prérequis suivants :

1. Créer un nouveau groupe de ressources `HDIFabrikam-WestUS` dans l’emplacement `West US`.
1. Créer une machine virtuelle réseau qui hébergera ESP activé HDInsight cluster.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Créer une relation homologue entre le réseau virtuel qui héberge AADDS (`HDIFabrikam-AADDSVNET`) et le réseau virtuel qui hébergera le ESP activé HDInsight cluster (`HDIFabrikam-HDIVNet `). Utilisez le code powershell suivant pour ces deux réseaux virtuels.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Créer un nouveau compte Azure Data Lake Storage Gen2, **Hdigen2store**, qui est configuré avec l’identité d’utilisateur géré **HDIFabrikamManagedIdentity**. Pour plus d’informations sur la création de comptes Data Lake Storage Gen2 activés avec l’utilisateur des identités gérées, consultez [utiliser Azure Data Lake Storage Gen2 avec Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configuration des DNS personnalisé sur le **HDIFabrikam-AADDSVNET** réseau virtuel.
    1. Accédez au portail Azure > **groupes de ressources** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET**  >   **Serveurs DNS**.
    1. Sélectionnez **personnalisé** et entrez `10.0.0.4` et `10.0.0.5`.
    1. Cliquez sur **Enregistrer**.

        ![enregistrement des paramètres dns personnalisés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Créer un nouveau cluster compatibles avec ESP des HDInsight Spark.
    1. Cliquez sur **personnalisé (taille, paramètres, applications)** .
    2. Entrez les détails de votre choisis de la section 1 **notions de base**. Vérifiez que le **type de Cluster** est **Spark 2.3 (HDI 3.6)** et **groupe de ressources** est **HDIFabrikam-CentralUS**

    1. Dans la section 2 **sécurité + mise en réseau**, procédez comme suit :
        1. Cliquez sur **activé** sous **Enterprise Security Package**.
        1. Cliquez sur **utilisateur d’administrateur de Cluster** et sélectionnez le **HDIAdmin** compte que vous avez créé précédemment en tant que l’utilisateur d’administrateur local. Cliquez sur **Sélectionner**.

        1. Cliquez sur **groupe d’accès de Cluster** , puis sélectionnez **HDIUserGroup**. Tout utilisateur que vous ajoutez à ce groupe à l’avenir seront en mesure d’accéder à des clusters HDInsight.

            ![Sélectionnez le groupe d’accès de cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Effectuer les autres étapes de la configuration du cluster et vérifiez les détails sur la **résumé du Cluster**. Cliquez sur **Créer**.

1. Connectez-vous à l’UI Ambari pour le cluster nouvellement créé au `https://CLUSTERNAME.azurehdinsight.net` à l’aide de votre nom d’utilisateur admin `hdiadmin@hdifabrikam.com` et mot de passe.

    ![Connectez-vous à Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Cliquez sur **rôles** à partir du tableau de bord du cluster.
1. Sur le **rôles** page, entrez le groupe **hdiusergroup** à affecter à la **administrateur de Cluster** rôle sous **attribuer des rôles à ces**.

    ![attribuer le rôle d’administrateur de cluster pour hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Ouvrez votre client SSH et la connexion au cluster en utilisant le **hdiuser** que vous avez créé précédemment dans Active Directory en local.

    ![connexion au cluster avec SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Si vous êtes en mesure de se connecter avec ce compte, puis vous avez configuré votre cluster ESP correctement pour la synchronisation avec active directory en local.

## <a name="next-steps"></a>Étapes suivantes

* [Introduction à la sécurité Apache Hadoop avec le Pack Sécurité Entreprise](apache-domain-joined-introduction.md)
