---
title: Pack Sécurité Entreprise avec Azure Active Directory dans HDInsight
description: Découvrez comment configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 448b2674aa6021107d138bc0d91f1bda399eb4a6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755896"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configuration d’un Pack Sécurité Entreprise avec Azure Active Directory Domain Services dans HDInsight

Les clusters Pack Sécurité Entreprise (ESP) offrent un accès multi-utilisateur sur les clusters Azure HDInsight. Comme les clusters HDInsight avec ESP sont connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data.

Dans cet article, vous allez apprendre à configurer un cluster HDInsight avec ESP en utilisant Azure Active Directory Domain Services (Azure AD-DS).

> [!NOTE]  
> La protection ESP est généralement disponible dans HDInsight 3.6 et 4.0 pour les types de clusters suivants : Apache Spark, Interactive, Hadoop et HBase. Le type de cluster ESP pour Apache Kafka est en préversion et ne bénéficie que d’une prise en charge au mieux. Les clusters ESP créés avant la date ESP GA (1er octobre 2018) ne sont pas pris en charge.

## <a name="enable-azure-ad-ds"></a>Activer Azure AD-DS

> [!NOTE]  
> Seuls les administrateurs des locataires disposent des privilèges pour activer Azure AD-DS. Si l’espace de stockage en cluster est Azure Data Lake Storage (ADLS) Gen1 ou Gen2, vous devez désactiver l’authentification multifacteur (MFA) uniquement pour les utilisateurs qui accèdent au cluster à l’aide d’une authentification Kerberos de base. Vous pouvez utiliser des [adresses IP approuvées](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [l’accès conditionnel](../../active-directory/conditional-access/overview.md) afin de désactiver l’authentification MFA pour des utilisateurs spécifiques UNIQUEMENT quand ils accèdent à la plage d’adresses IP du réseau virtuel du cluster HDInsight. Si vous utilisez l’accès conditionnel, vérifiez que ce point de terminaison de service AD est activé sur le réseau virtuel HDInsight.
>
> Si le stockage en cluster est Stockage Blob Azure (WASB), ne désactivez pas MFA.

L’activation d’Azure AD-DS est une condition préalable à la création d’un cluster HDInsight avec ESP. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quand Azure AD-DS est activé, tous les utilisateurs et les objets commencent la synchronisation d’Azure Active Directory (AAD) avec Azure AD-DS par défaut. La longueur de l’opération de synchronisation varie selon le nombre d’objets dans Azure AD. La synchronisation pourrait prendre quelques jours pour des centaines de milliers d’objets. 

Le nom de domaine que vous utilisez avec Azure AD-DS doit comporter 39 caractères au maximum pour fonctionner avec HDInsight.

Vous pouvez choisir de synchroniser uniquement les groupes qui ont besoin d’accéder aux clusters HDInsight. Cette option de synchroniser uniquement certains groupes est appelée *synchronisation délimitée*. Pour des instructions, consultez [Configurer une synchronisation délimitée entre Azure AD et votre domaine managé](../../active-directory-domain-services/scoped-synchronization.md).

Au moment d’activer le protocole LDAP sécurisé, placez le nom de domaine dans le nom de l’objet et dans l’autre nom de l’objet du certificat. Par exemple, si votre nom de domaine est *contoso100.onmicrosoft.com*, assurez-vous que ce nom exact existe dans le nom de l’objet et dans l’autre nom de l’objet de votre certificat. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé pour un domaine managé Azure AD-DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). Vous retrouverez ci-dessous un exemple de création d’un certificat autosigné et portant le nom de domaine (*contoso100.onmicrosoft.com*) dans le nom de l’objet et DnsName (autre nom de l’objet) :

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Vérifier l’état d’intégrité d’Azure AD-DS
Affichez l’état d’intégrité de vos services AAD-DS en sélectionnant **Intégrité** sous la catégorie **Gérer**. Assurez-vous que l’état d’Azure AD-DS est vert (en cours d’exécution) et que la synchronisation est terminée.

![Intégrité d’Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Créer et autoriser une identité managée

Une **identité managée affectée à l’utilisateur** est utilisée pour simplifier et sécuriser les opérations de services de domaine. Lorsque vous affectez le rôle Contributeur des services de domaine HDInsight à l’identité managée, il peut lire, créer, modifier et supprimer des opérations de services de domaine. Certaines opérations telles que la création d’unités d’organisation ou de principaux de service sont nécessaires pour le Pack Sécurité Entreprise HDInsight. Les identités managées peuvent être créées dans n’importe quel abonnement. Pour plus d’informations sur les identités managées en général, consultez l’article [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](../hdinsight-managed-identities.md).

Pour configurer des clusters ESP, créez une identité managée affectée à l’utilisateur, si vous n’en possédez pas déjà une. Pour obtenir des instructions, voir [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Ensuite, affectez le rôle **Contributeur de services de domaine HDInsight** à l’identité managée dans le contrôle d’accès Azure AD-DS (des privilèges d’administrateur AAD-DS sont requis pour affecter ce rôle).

![Contrôle d’accès Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L’affectation du rôle **Contributeur des services de domaine HDInsight** permet de s’assurer que l’identité a un accès approprié (de la part de) pour effectuer les opérations de services de domaine sur le domaine AAD-DS, comme la création ou la suppression d’unités organisationnelles.

Une fois l’identité managée créée et le rôle qui convient donné, l’administrateur AAD-DS peut définir qui peut utiliser cette identité managée. Pour configurer des utilisateurs pour l’identité managée, l’administrateur doit sélectionner l’identité managée dans le portail, puis cliquer sur **Contrôle d’accès (IAM)** sous **Vue d’ensemble**. Puis, sur la droite, l’administrateur doit attribuer le rôle **Opérateur d’identité managée** aux utilisateurs ou groupes qui souhaitent créer des clusters HDInsight ESP. Par exemple, l’administrateur AAD-DS peut attribuer ce rôle au groupe **MarketingTeam** pour l’identité managée **sjmsi** comme illustré sur l’image ci-dessous. Cela garantit que les bonnes personnes dans l’organisation ont accès et peuvent utiliser cette identité managée pour la création de clusters ESP.

![Attribution du rôle Opérateur d'identité managée HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Mise en réseau - Éléments à prendre en compte

> [!NOTE]  
> Azure AD-DS doit être déployé sur un réseau virtuel Azure Resource Manager. Les réseaux virtuels classiques ne sont pas pris en charge pour Azure AD-DS. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Après avoir activé Azure AD-DS, un serveur DNS (Domain Name System) local s’exécute sur les machines virtuelles AD. Configurez votre réseau virtuel (VNET) Azure AD DS pour utiliser des serveurs DNS personnalisés. Pour rechercher les adresses IP qui conviennent, sélectionnez **Propriétés** sous la catégorie **Gérer** et examinez les adresses IP répertoriées sous **Adresse IP sur le réseau virtuel**.

![Rechercher les adresses IP des serveurs DNS locaux](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Modifiez la configuration des serveurs DNS dans le réseau virtuel Azure AD DS pour utiliser ces adresses IP personnalisées en sélectionnant **Serveurs DNS** sous la catégorie **Paramètres**. Ensuite, cliquez sur la case d'option située en regard de **Personnalisé**, entrez la première adresse IP dans la zone de texte ci-dessous, puis cliquez sur **Enregistrer**. Ajoutez des adresses IP supplémentaires en suivant la même procédure.

![Mise à jour de la configuration DNS de réseau virtuel](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Il est plus facile de placer l’instance Azure AD-DS et le cluster HDInsight dans le même réseau virtuel Azure. Si vous envisagez d’utiliser différents réseaux virtuels, vous devez les appairer de manière à ce que le contrôleur de domaine soit visible pour les machines virtuelles HDI. Pour en savoir plus, consultez [Peering de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md). 

Une fois les réseaux virtuels appairés, configurez le réseau virtuel HDInsight pour utiliser un serveur DNS personnalisé et entrez les adresses IP privées Azure AD-DS en tant qu'adresses de serveur DNS. Lorsque les deux réseaux virtuels utilisent les mêmes serveurs DNS, votre nom de domaine personnalisé est résolu vers l'adresse IP qui convient et accessible à partir de HDInsight. Par exemple, si votre nom de domaine est `contoso.com`, après cette étape, `ping contoso.com` doit être résolu à la bonne adresse IP Azure AD-DS.

![Configuration des serveurs DNS personnalisés pour le réseau virtuel appairé](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Si vous utilisez des règles de groupes de sécurité réseau (NSG) dans votre sous-réseau HDInsight, vous devez autoriser les [adresses IP nécessaires ](../hdinsight-management-ip-addresses.md) pour le trafic entrant et le trafic sortant.

**Pour vérifier** si votre mise en réseau est correctement configurée, joignez une machine virtuelle au réseau virtuel/sous-réseau HDInsight et effectuez un test ping sur le nom de domaine (il doit être résolu en adresse IP), puis exécutez **ldp.exe** pour accéder au domaine Azure AD-DS. **Joignez ensuite cette machine virtuelle Windows au domaine pour vérifier** que tous les appels RPC requis ont abouti entre le client et le serveur. Vous pouvez également utiliser **nslookup** pour vérifier l'accès de la mise en réseau à votre compte de stockage ou à n'importe quelle base de données externe que vous pourriez utiliser (par exemple, metastore Hive ou base de données Ranger externe).
Assurez-vous que tous les [ports requis](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) figurent dans la liste verte des règles de groupe de sécurité réseau du sous-réseau AAD-DS, si AAD-DS est sécurisé par un groupe NSG. Si la jonction de domaine de cette machine virtuelle Windows est réussie, vous pouvez passer à l’étape suivante et créer des clusters ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Créer un cluster HDInsight avec ESP

Après avoir correctement configuré les étapes précédentes, l’étape suivante consiste à créer le cluster HDInsight avec ESP activé. Au moment de créer un cluster HDInsight, vous pouvez activer le pack Sécurité Entreprise sous l’onglet **Sécurité + réseau**. Si vous préférez utiliser un modèle Azure Resource Manager pour le déploiement, accédez au portail pour télécharger le modèle pré-rempli de la page **Vérifier et créer** en vue de le réutiliser plus tard.

> [!NOTE]  
> Les six premiers caractères des noms de cluster ESP doivent être uniques dans votre environnement. Par exemple, si vous disposez de plusieurs clusters ESP situés sur différents réseaux virtuels, vous devez choisir une convention d'affectation de noms garantissant que les six premiers caractères des noms de cluster sont uniques.

![Pack Sécurité Entreprise Azure HDInsight - Validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Dès lors que vous activez ESP, les erreurs de configuration courantes en rapport avec Azure AD-DS sont automatiquement détectées et validées. Après avoir corrigé ces erreurs, vous pouvez passer à l’étape suivante :

![Pack Sécurité Entreprise Azure HDInsight - Échec de validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quand vous créez un cluster HDInsight avec ESP, vous devez fournir les paramètres suivants :

- **Utilisateur administrateur de cluster** : Choisissez un administrateur pour votre cluster à partir de votre instance Azure AD-DS synchronisée. Ce compte de domaine doit être déjà synchronisé et disponible dans Azure AD-DS.

- **Groupe d’accès au cluster** : Les groupes de sécurité dont vous voulez que les utilisateurs puissent avoir accès au cluster et le synchroniser doivent être disponibles dans Azure AD-DS. Par exemple, le groupe HiveUsers. Pour plus d’informations, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS** : Par exemple `ldaps://contoso.com:636`.

Vous pouvez choisir l’identité managée que vous avez créée dans la liste déroulante des identités managées affectées par l’utilisateur lors de la création d’un cluster.

![Identité managée Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Apache Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md).
* Pour vous connecter à des clusters HDInsight avec ESP à l’aide de SSH, consultez [Utiliser SSH avec Apache Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
