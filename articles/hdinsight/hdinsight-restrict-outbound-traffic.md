---
title: Configurer la restriction du trafic réseau sortant pour les clusters Azure HDInsight
description: Découvrez comment configurer la restriction du trafic réseau sortant pour les clusters Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 4ce3ca31163c286f54b9630e5d4779e2e47a032f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754589"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Configurer le trafic réseau sortant pour les clusters Azure HDInsight à l’aide de pare-feu (version préliminaire)

Cet article fournit les étapes pour vous permet de sécuriser le trafic sortant à partir de votre cluster HDInsight à l’aide de pare-feu Azure. Les étapes ci-dessous supposent que vous configurez un pare-feu d’Azure pour un cluster existant. Si vous déployez un nouveau cluster et derrière un pare-feu, créez d’abord votre cluster de HDInsight et le sous-réseau, puis suivez les étapes décrites dans ce guide.

## <a name="background"></a>Arrière-plan

Les clusters Azure HDInsight sont normalement déployées dans votre propre réseau virtuel. Le cluster a des dépendances sur les services en dehors de ce réseau virtuel qui nécessitent un accès réseau fonctionne correctement.

Il existe plusieurs dépendances qui requièrent le trafic entrant. Le trafic de gestion entrant ne peut pas être envoyé via un dispositif de pare-feu. Les adresses de source pour ce trafic sont connues et sont publiées [ici](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Vous pouvez également créer des règles de groupe de sécurité réseau (NSG) avec ces informations pour sécuriser le trafic entrant vers les clusters.

Les dépendances de trafic sortant de HDInsight sont presque entièrement définis avec des noms de domaine complets, ce qui n’ont pas des adresses IP statiques derrière eux. L’absence d’adresses statiques signifie que les groupes de sécurité réseau (NSG) ne peut pas être utilisés pour verrouiller le trafic sortant à partir d’un cluster. Les adresses changent assez souvent qu’un ne peut pas configurer des règles basées sur la résolution de noms actuel et qui permet de définir des règles de groupe de sécurité réseau.

La solution de sécurisation des adresses sortantes consiste à utiliser un dispositif de pare-feu que vous pouvez contrôler le trafic sortant en fonction des noms de domaine. Pare-feu Azure peut restreindre le trafic HTTP et HTTPS sortant en fonction du nom de domaine complet de la destination ou [balises de nom de domaine complet](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configuration du pare-feu Azure avec HDInsight

Un résumé des étapes pour verrouiller la sortie à partir de votre HDInsight existant avec le pare-feu Azure sont :
1. Créer un pare-feu.
1. Ajouter des règles d’application vers le pare-feu
1. Ajouter des règles de réseau vers le pare-feu.
1. Créez une table de routage.

### <a name="create-a-new-firewall-for-your-cluster"></a>Créer un nouveau pare-feu de votre cluster

1. Créez un sous-réseau nommé **AzureFirewallSubnet** dans le réseau virtuel où se trouve de votre cluster. 
1. Créer un nouveau pare-feu **Test-FW01** à l’aide de la procédure décrite dans [didacticiel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)

### <a name="configure-the-firewall-with-application-rules"></a>Configurer le pare-feu avec des règles d’application

Créer un regroupement de règles d’application qui permet au cluster envoyer et recevoir les communications importantes.

Sélectionnez le nouveau pare-feu **Test-FW01** à partir du portail Azure. Cliquez sur **règles** sous **paramètres** > **regroupement de règles d’Application** > **ajouter le regroupement de règles d’application**.

![Titre : Ajouter le regroupement de règles d’application](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Sur le **ajouter le regroupement de règles d’application** écran, procédez comme suit :

1. Entrez un **nom**, **priorité**, puis cliquez sur **autoriser** à partir de la **Action** menu déroulant.
1. Ajoutez les règles suivantes :
    1. Une règle pour autoriser le trafic de HDInsight et de mise à jour de Windows :
        1. Dans le **balises de nom de domaine complet** section, fournissez un **nom**et définissez **adresses sources** à `*`.
        1. Sélectionnez **HDInsight** et **WindowsUpdate** à partir de la **balises de nom de domaine complet** menu déroulant.
    1. Une règle pour autoriser l’activité de connexion de Windows :
        1. Dans le **cible FQDN** section, fournissez un **nom**et définissez **adresses sources** à `*`.
        1. Entrez `https:443` sous **: Port de protocole** et `login.windows.net` sous **cibler les noms de domaine complets**.
    1. Si votre cluster est soutenu par WASB, puis ajoutez une règle pour WASB :
        1. Dans le **cible FQDN** section, fournissez un **nom**et définissez **adresses sources** à `*`.
        1. Entrez `http:80,https:443` sous **: Port de protocole** et l’url de compte de stockage sous **noms de domaine complets cible**. Le format sera similaire à < storage_account_name.blob.core.windows.net >. Pour utiliser le protocole https uniquement, assurez-vous connexions [« transfert sécurisé requis »](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) est activé sur le compte de stockage.
1. Cliquez sur **Add**.

![Titre : Entrez les détails de la collection application règle](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurer le pare-feu avec les règles de réseau

Créer les règles de réseau pour configurer correctement votre cluster HDInsight.

1. Sélectionnez le nouveau pare-feu **Test-FW01** à partir du portail Azure.
1. Cliquez sur **règles** sous **paramètres** > **regroupement de règles de réseau** > **ajouter le regroupement de règles de réseau**.
1. Sur le **ajouter le regroupement de règles de réseau** écran, entrez un **nom**, **priorité**, puis cliquez sur **autoriser** à partir de la **Action** menu déroulant.
1. Créer les règles suivantes :
    1. Une règle de réseau dans la section adresses IP qui permet au cluster effectuer la synchronisation de l’horloge à l’aide de NTP.
        1. Dans le **règles** section, fournissez un **nom** et sélectionnez **UDP** à partir de la **protocole** liste déroulante.
        1. Définissez **adresses Source** et **adresses de Destination** à `*`.
        1. Définissez **Ports de Destination** à 123.
    1. Si vous utilisez Enterprise Security Package (ESP), puis ajoutez une règle de réseau dans la section adresses IP qui permet la communication avec AAD-DS pour les clusters de ESP.
        1. Déterminer les deux adresses IP pour vos contrôleurs de domaine.
        1. Dans la ligne suivante dans le **règles** section, fournissez un **nom** et sélectionnez **n’importe quel** à partir de la **protocole** liste déroulante.
        1. Définissez **adresses sources** `*`.
        1. Entrez toutes les adresses IP pour vos contrôleurs de domaine dans **adresses de Destination** séparés par des virgules.
        1. Définissez **Ports de Destination** à `*`.
    1. Si vous utilisez Azure Data Lake Storage, vous pouvez ajouter une règle de réseau dans la section adresses IP à résoudre un problème SNI avec ADLS Gen1 et Gen2. Cette option acheminera le trafic au pare-feu, ce qui peut entraîner des coûts plus élevés pour les chargements de données volumineux, mais le trafic sera enregistrée et contrôlable dans les journaux de pare-feu.
        1. Déterminer l’adresse IP de votre compte Data Lake Storage. Vous pouvez utiliser une commande powershell comme `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` pour résoudre le nom de domaine complet pour une adresse IP.
        1. Dans la ligne suivante dans le **règles** section, fournissez un **nom** et sélectionnez **TCP** à partir de la **protocole** liste déroulante.
        1. Définissez **adresses sources** `*`.
        1. Entrez l’adresse IP de votre compte de stockage dans **adresses de Destination**.
        1. Définissez **Ports de Destination** à `*`.
    1. (Facultatif) Si vous utilisez l’Analytique de journal, vous devez ensuite créer une règle de réseau dans la section adresses IP pour permettre la communication avec votre espace de travail Analytique de journal.
        1. Dans la ligne suivante dans le **règles** section, fournissez un **nom** et sélectionnez **TCP** à partir de la **protocole** liste déroulante.
        1. Définissez **adresses sources** `*`.
        1. Définissez **adresses de Destination** à `*`.
        1. Définissez **Ports de Destination** à `12000`.
    1. Configurer une règle de réseau dans la section balises de Service pour SQL qui vous permettra de se connecter et à auditer le trafic SQL, sauf si vous avez configuré des points de terminaison de Service pour SQL Server sur le sous-réseau HDInsight qui contournent le pare-feu.
        1. Dans la ligne suivante dans le **règles** section, fournissez un **nom** et sélectionnez **TCP** à partir de la **protocole** liste déroulante.
        1. Définissez **adresses sources** `*`.
        1. Définissez **adresses de Destination** à `*`.
        1. Sélectionnez **Sql** à partir de la **balises de Service** liste déroulante.
        1. Définissez **Ports de Destination** à `1433,11000-11999,14000-14999`.
1. Cliquez sur **ajouter** pour terminer la création de votre regroupement de règles de réseau.

![Titre : Entrez les détails de la collection application règle](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Créer et configurer une table de routage

Créer une table de routage avec les entrées suivantes :

1. Six des adresses [cette liste d’adresses IP de gestion nécessaires HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) avec un tronçon suivant de **Internet**:
    1. Quatre adresses IP pour tous les clusters dans toutes les régions
    1. Deux adresses IP qui sont spécifiques à la région où le cluster est créé.
1. Un itinéraire d’Appliance virtuelle pour 0.0.0.0/0 des adresses IP avec le tronçon suivant qui est votre adresse IP privée de pare-feu Azure.

Par exemple, pour configurer la table de routage d’un cluster créé dans la région États-Unis de « États-Unis », utilisez comme suit :

1. Connectez-vous au portail Azure.
1. Sélectionnez votre pare-feu Azure **Test-FW01**. Copie le **adresse IP privée** répertoriés sur le **vue d’ensemble** page. Pour cet exemple, nous utiliserons un **exemple d’adresse de 10.1.1.4**
1. Créer une nouvelle table de routage.
1. Cliquez sur **itinéraires** sous **paramètres**.
1. Cliquez sur **ajouter** pour créer des itinéraires pour les adresses IP dans le tableau ci-dessous.

| Nom de l’itinéraire | Préfixe de l’adresse | Type de tronçon suivant | adresse de tronçon suivant |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N/D |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N/D |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N/D |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N/D |
| 13.67.223.215 | 13.67.223.215/32 | Internet | N/D |
| 40.86.83.253 | 40.86.83.253/32 | Internet | N/D |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuelle | 10.1.1.4 |

Terminer la configuration de table de routage :

1. Affectez la table de routage que vous avez créé à votre sous-réseau HDInsight en cliquant sur **sous-réseaux** sous **paramètres** , puis **associer**.
1. Sur le **associer un sous-réseau** , sélectionnez le réseau virtuel créé dans votre cluster et le **HDInsight sous-réseau** vous avez utilisé pour votre cluster HDInsight.
1. Cliquez sur **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nœud de périmètre ou le trafic d’application personnalisée

Les étapes ci-dessus vous permettent du cluster de fonctionner sans problème. Vous devez toujours configurer les dépendances pour s’adapter à vos applications personnalisées s’exécutant sur les nœuds de périphérie, le cas échéant.

Dépendances d’application doivent être identifiés et ajoutés pour le pare-feu Azure ou de la table de routage.

Itinéraires doivent être créés pour le trafic d’application afin d’éviter les problèmes de routage asymétriques.

Si vos applications possèdent d’autres dépendances, elles doivent être ajoutées à votre pare-feu d’Azure. Créez des règles d’application pour autoriser le trafic HTTP/HTTPS et des règles de réseau pour tout le reste.

## <a name="logging"></a>Journalisation

Pare-feu Azure peut envoyer des journaux à quelques différents systèmes de stockage. Pour obtenir des instructions sur la configuration de journalisation de votre pare-feu, suivent les étapes de [didacticiel : Surveiller les journaux de pare-feu d’Azure et les mesures](../firewall/tutorial-diagnostics.md).

Une fois que vous avez terminé la configuration de journalisation, si vous êtes à l’Analytique de journal, les données de journalisation, vous pouvez afficher le trafic bloqué avec une requête telle que la suivante :

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Il est utile de l’intégration de votre pare-feu d’Azure avec Azure Monitor journaux lorsque tout d’abord obtenir un travail de l’application lorsque vous n’avez pas connaissance de toutes les dépendances d’application. Pour en savoir plus sur les journaux d’activité Azure Monitor, consultez [Analyser les données de journal d’activité dans Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Accès au cluster
Après avoir correctement la configuration du pare-feu, vous pouvez utiliser le point de terminaison interne (`https://<clustername>-int.azurehdinsight.net`) pour accéder à la Ambari à partir du réseau virtuel. Pour utiliser le point de terminaison public (`https://<clustername>.azurehdinsight.net`) ou ssh point de terminaison (`<clustername>-ssh.azurehdinsight.net`), assurez-vous que vous avez les itinéraires de droite dans la table de routage et de paramétrage des règles de groupe de sécurité réseau pour éviter le problème de routage asymétrique expliqué [ici](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Configurer une autre appliance virtuelle réseau

>[!Important]
> Les informations suivantes sont **uniquement** requis si vous souhaitez configurer une appliance virtuelle réseau (NVA) autre que le pare-feu d’Azure.

Les instructions précédentes vous aider à configurer le pare-feu Azure pour restreindre le trafic sortant à partir de votre cluster HDInsight. Pare-feu Azure est automatiquement configuré pour autoriser le trafic pour la plupart des scénarios courants importants. Si vous souhaitez utiliser une autre appliance virtuelle réseau, vous devrez configurer manuellement un nombre de fonctionnalités supplémentaires. Gardez les points suivants à l’esprit que votre configuration de votre appliance virtuelle réseau :

* Les services compatibles avec les points de terminaison de service doivent être configurés avec des points de terminaison de service.
* Dépendances de l’adresse IP sont pour le trafic HTTP/S (le trafic TCP et UDP).
* Points de terminaison HTTP/HTTPS de nom de domaine complet peuvent être placés dans votre appareil de l’appliance virtuelle réseau.
* Points de terminaison HTTP/HTTPS de caractère générique sont des dépendances qui peuvent varier selon un nombre de qualificateurs.
* Affectez la table de routage que vous créez à votre sous-réseau HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dépendances compatibles avec les points de terminaison de service

| **Point de terminaison** |
|---|
| Azure SQL |
| Stockage Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dépendances des adresses IP

| **Point de terminaison** | **Détails** |
|---|---|
| \*:123 | Vérification de l’horloge NTP. Le trafic est vérifié à plusieurs points de terminaison sur le port 123 |
| Adresses IP publiée [ici](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Il s’agit de service de HDInsight |
| AAD-DS des adresses IP privées pour ESP de clusters |
| \*: 16800 service d’activation de Windows |
| \*12000 pour l’Analytique de journal |

#### <a name="fqdn-httphttps-dependencies"></a>Dépendances HTTP/HTTPS FQDN

>[!Important]
> La liste ci-dessous donne uniquement parmi les noms de domaine complets plus importantes. Vous pouvez obtenir la liste complète des noms de domaine complets pour la configuration de votre appliance virtuelle réseau [dans ce fichier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Point de terminaison**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Étapes suivantes

* [Architecture de réseau virtuel Azure HDInsight](hdinsight-virtual-network-architecture.md)
