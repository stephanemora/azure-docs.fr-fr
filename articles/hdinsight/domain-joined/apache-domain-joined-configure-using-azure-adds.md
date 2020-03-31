---
title: Sécurité Entreprise avec Azure AD DS – Azure HDInsight
description: Découvrez comment configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79233733"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configuration d’un Pack Sécurité Entreprise avec Azure Active Directory Domain Services dans HDInsight

Les clusters Pack Sécurité Entreprise (ESP) offrent un accès multi-utilisateur sur les clusters Azure HDInsight. Comme les clusters HDInsight avec ESP sont connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data.

Dans cet article, vous allez apprendre à configurer un cluster HDInsight avec ESP en utilisant Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> La protection ESP est généralement disponible dans HDInsight 3.6 et 4.0 pour les types de clusters suivants : Apache Spark, Interactive, Hadoop et HBase. Le type de cluster ESP pour Apache Kafka est en préversion et ne bénéficie que d’une prise en charge au mieux. Les clusters ESP créés avant la date de disponibilité générale ESP (1er octobre 2018) ne sont pas pris en charge.

## <a name="enable-azure-ad-ds"></a>Activer Azure AD DS

> [!NOTE]  
> Seuls les administrateurs de locataires disposent des privilèges pour activer Azure AD DS. Si l’espace de stockage en cluster correspond à Azure Data Lake Storage Gen1 ou Gen2, vous ne devez désactiver Azure Multi-Factor Authentication que pour les utilisateurs qui accèdent au cluster à l’aide d’une authentification Kerberos de base. 
>
> Vous pouvez utiliser des [adresses IP approuvées](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou l’[accès conditionnel](../../active-directory/conditional-access/overview.md) afin de désactiver l’authentification multifacteur pour des utilisateurs spécifiques, *seulement* quand ils accèdent à la plage d’adresses IP du réseau virtuel du cluster HDInsight. Si vous utilisez l’accès conditionnel, vérifiez que le point de terminaison de service Active Directory est activé sur le réseau virtuel HDInsight.
>
> Si l’espace de stockage en cluster correspond au stockage Blob Azure, ne désactivez pas l’authentification multifacteur.

L’activation d’Azure AD DS est une condition préalable à la création d’un cluster HDInsight avec ESP. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du Portail Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quand Azure AD DS est activé, tous les utilisateurs et les objets commencent la synchronisation d’Azure Active Directory (Azure AD) vers Azure AD DS par défaut. La longueur de l’opération de synchronisation varie selon le nombre d’objets dans Azure AD. La synchronisation peut prendre plusieurs jours pour des centaines de milliers d’objets. 

Le nom de domaine que vous utilisez avec Azure AD DS doit comporter 39 caractères au maximum pour fonctionner avec HDInsight.

Vous pouvez choisir de synchroniser uniquement les groupes qui ont besoin d’accéder aux clusters HDInsight. Cette option de synchroniser uniquement certains groupes est appelée *synchronisation délimitée*. Pour obtenir des instructions, consultez [Configurer la synchronisation étendue depuis Azure AD vers votre domaine managé](../../active-directory-domain-services/scoped-synchronization.md).

Lorsque vous activez le protocole LDAP sécurisé, insérez le nom de domaine dans le nom de l’objet et dans l’autre nom de l’objet du certificat. Par exemple, si votre nom de domaine est *contoso100.onmicrosoft.com*, assurez-vous que ce nom exact existe dans le nom de l’objet et dans l’autre nom de l’objet de votre certificat. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé pour un domaine managé Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

L’exemple suivant crée un certificat auto-signé. Le nom de domaine *contoso100.onmicrosoft.com* figure à la fois dans `Subject` (nom de l’objet) et `DnsName` (autre nom de l’objet).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Vérifier l’état d’intégrité d’Azure AD DS
Affichez l’état d’intégrité d’Azure Active Directory Domain Services en sélectionnant **Intégrité** dans la catégorie **Gérer**. Assurez-vous que l’état d’Azure AD DS est vert (en cours d’exécution) et que la synchronisation est terminée.

![Intégrité d’Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Créer et autoriser une identité managée

Vous pouvez utiliser une *identité managée affectée à l’utilisateur* pour simplifier et sécuriser les opérations de services de domaine. Lorsque vous affectez le rôle **Contributeur des services de domaine HDInsight** à l’identité managée, il peut lire, créer, modifier et supprimer des opérations de services de domaine. 

Certaines opérations de services de domaine, telles que la création d’unités d’organisation ou de principaux de service, sont nécessaires pour le Pack Sécurité Entreprise HDInsight. Vous pouvez créer des identités managées dans n’importe quel abonnement. Pour plus d’informations sur les identités managées en général, consultez l’article [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](../hdinsight-managed-identities.md).

Pour configurer des clusters ESP, créez une identité managée affectée à l’utilisateur, si vous n’en possédez pas déjà une. Pour obtenir des instructions, consultez [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du Portail Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Ensuite, affectez le rôle **Contributeur HDInsight Domain Services** à l’identité managée dans **Contrôle d’accès** pour Azure AD DS. Vous avez besoin de privilèges d’administrateur Azure AD DS pour effectuer cette attribution de rôle.

![Contrôle d’accès Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L’affectation du rôle **Contributeur HDInsight Domain Services** permet de s’assurer que l’identité a un accès approprié (de la part de) pour effectuer les opérations de services de domaine sur le domaine Azure AD DS. Ces opérations incluent la création et la suppression d’unités d’organisation.

Une fois que l’identité managée a été créée et que le rôle approprié lui a été donné, l’administrateur Azure AD DS peut configurer qui peut utiliser cette identité managée. Tout d’abord, l’administrateur sélectionne l’identité managée dans le portail, puis sélectionne **Contrôle d’accès (IAM)** sous **Vue d’ensemble**. Ensuite, sur la droite, l’administrateur attribue le rôle **Opérateur d’identité managée** aux utilisateurs et groupes qui souhaitent créer des clusters ESP HDInsight. 

Par exemple, l’administrateur Azure AD DS peut attribuer ce rôle au groupe **MarketingTeam** pour l’identité managée **sjmsi**, comme illustré sur l’image ci-dessous. Cette attribution garantit que les bonnes personnes de l’organisation peuvent utiliser l’identité managée pour créer des clusters ESP.

![Attribution du rôle Opérateur d'identité managée HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considérations relatives au réseau

> [!NOTE]  
> Azure AD DS doit être déployé dans un réseau virtuel basé sur Azure Resource Manager. Les réseaux virtuels classiques ne sont pas pris en charge pour Azure AD DS. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du Portail Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Après avoir activé Azure AD DS, un serveur DNS (Domain Name System) local s’exécute sur les machines virtuelles Active Directory. Configurez votre réseau virtuel Azure AD DS pour utiliser ces serveurs DNS personnalisés. Pour rechercher les adresses IP qui conviennent, sélectionnez **Propriétés** dans la catégorie **Gérer** et examinez ce qui est répertorié sous **ADRESSE IP SUR LE RÉSEAU VIRTUEL**.

![Rechercher les adresses IP des serveurs DNS locaux](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Modifiez la configuration des serveurs DNS dans le réseau virtuel Azure AD DS pour utiliser ces adresses IP personnalisées en sélectionnant **Serveurs DNS** dans la catégorie **Paramètres**. Sélectionnez ensuite l’option **Personnalisé**, entrez la première adresse IP dans la zone de texte, puis sélectionnez **Enregistrer**. Ajoutez des adresses IP supplémentaires en suivant la même procédure.

![Mise à jour de la configuration DNS du réseau virtuel](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Il est plus facile de placer l’instance Azure AD DS et le cluster HDInsight dans le même réseau virtuel Azure. Si vous envisagez d’utiliser différents réseaux virtuels, vous devez les appairer de manière à ce que le contrôleur de domaine soit visible pour les machines virtuelles HDInsight. Pour en savoir plus, consultez [Peering de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md). 

Une fois les réseaux virtuels appairés, configurez le réseau virtuel HDInsight pour utiliser un serveur DNS personnalisé et entrez les adresses IP privées Azure AD DS en tant qu’adresses de serveur DNS. Lorsque les deux réseaux virtuels utilisent les mêmes serveurs DNS, votre nom de domaine personnalisé est résolu vers l’adresse IP qui convient et accessible à partir de HDInsight. Par exemple, si votre nom de domaine est `contoso.com`, après cette étape, `ping contoso.com` doit être résolu à la bonne adresse IP Azure AD DS.

![Configuration des serveurs DNS personnalisés pour un réseau virtuel appairé](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Si vous utilisez des règles de groupe de sécurité réseau (NSG) dans votre sous-réseau HDInsight, vous devez autoriser les [adresses IP nécessaires](../hdinsight-management-ip-addresses.md) pour le trafic entrant et le trafic sortant.

Pour tester si votre réseau est correctement configuré, joignez une machine virtuelle Windows au réseau virtuel/sous-réseau HDInsight et effectuez un test ping sur le nom de domaine. (Il doit être résolu en une adresse IP.) Exécutez **ldp.exe** pour accéder au domaine Azure AD DS. Joignez ensuite cette machine virtuelle Windows au domaine pour confirmer que tous les appels RPC requis aboutissent entre le client et le serveur. 

Vous pouvez également utiliser **nslookup** pour confirmer l’accès réseau à votre compte de stockage ou à n’importe quelle base de données externe que vous pouvez utiliser (par exemple, base de données Ranger ou metastore Hive externe). Assurez-vous que tous les [ports requis](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) sont autorisés dans les règles de groupe de sécurité réseau du sous-réseau Azure AD DS, si un groupe de sécurité réseau permet de sécuriser Azure AD DS. Si la jonction de domaine de cette machine virtuelle Windows est réussie, vous pouvez passer à l’étape suivante et créer des clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Créer un cluster HDInsight avec ESP

Après avoir correctement configuré les étapes précédentes, l’étape suivante consiste à créer le cluster HDInsight avec ESP activé. Lorsque vous créez un cluster HDInsight, vous pouvez activer le pack Sécurité Entreprise sous l’onglet **Sécurité + réseau**. Si vous préférez utiliser un modèle Azure Resource Manager pour le déploiement, accédez au portail pour télécharger le modèle prérempli de la page **Vérifier + créer** en vue de le réutiliser plus tard. 

Vous pouvez également activer la fonctionnalité [Broker d’ID HDInsight](identity-broker.md) lors de la création du cluster. La fonctionnalité Broker d’ID vous permet de vous connecter à Ambari à l’aide de l’authentification multifacteur et d’obtenir les tickets Kerberos requis sans avoir besoin de hachages de mot de passe dans Azure AD DS.

> [!NOTE]  
> Les six premiers caractères des noms de cluster ESP doivent être uniques dans votre environnement. Par exemple, si vous disposez de plusieurs clusters ESP situés dans différents réseaux virtuels, choisissez une convention d’affectation de noms garantissant que les six premiers caractères des noms de cluster sont uniques.

![Validation de domaine pour le pack Sécurité Entreprise Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Après l’activation d’ESP, les erreurs de configuration courantes associées à Azure AD DS sont automatiquement détectées et validées. Après avoir corrigé ces erreurs, vous pouvez passer à l’étape suivante.

![Pack Sécurité Entreprise Azure HDInsight – Échec de validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quand vous créez un cluster HDInsight avec ESP, vous devez fournir les paramètres suivants :

- **Utilisateur administrateur de cluster** : Choisissez un administrateur pour votre cluster à partir de votre instance Azure AD DS synchronisée. Ce compte de domaine doit être déjà synchronisé et disponible dans Azure AD DS.

- **Groupe d’accès au cluster** : Les groupes de sécurité dont vous voulez que les utilisateurs puissent avoir accès au cluster et le synchroniser doivent être disponibles dans Azure AD DS. Le groupe HiveUsers en est un exemple. Pour plus d’informations, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS** : par exemple `ldaps://contoso.com:636`.

Vous pouvez choisir l’identité managée que vous avez créée dans la liste déroulante **Identité managée affectée par l’utilisateur** lors de la création d’un nouveau cluster.

![Identité managée Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Apache Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md).
* Pour vous connecter à des clusters HDInsight avec ESP à l’aide de SSH, consultez [Utiliser SSH avec Apache Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
