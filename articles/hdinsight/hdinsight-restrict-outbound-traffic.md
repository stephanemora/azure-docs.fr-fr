---
title: Configurer la restriction du trafic réseau sortant pour les clusters Azure HDInsight
description: Découvrez comment configurer la restriction du trafic réseau sortant pour les clusters Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 8bb077242c0a989e100c81d4dfefeb53f4bc90c4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620687"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Configurer le trafic réseau sortant pour les clusters Azure HDInsight à l’aide du pare-feu (préversion)

Cet article décrit les étapes de sécurisation du trafic sortant de votre cluster HDInsight à l’aide du pare-feu Azure. Les étapes ci-dessous partent de la supposition que vous configurez le pare-feu Azure d’un cluster existant. Si vous déployez un nouveau cluster derrière un pare-feu, créez d’abord votre cluster HDInsight et votre sous-réseau, puis suivez les étapes décrites dans ce guide.

## <a name="background"></a>Arrière-plan

Les clusters Azure HDInsight sont normalement déployés dans votre propre réseau virtuel. Le cluster a des dépendances sur des services hors de ce réseau virtuel qui nécessitent un accès réseau fonctionnel.

Plusieurs dépendances requièrent un trafic entrant. Le trafic de gestion entrant ne peut pas être envoyé via un dispositif de pare-feu. Les adresses sources de ce trafic sont connues et publiées [ici](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Vous pouvez également créer des règles de groupe de sécurité réseau (NSG) avec ces informations pour sécuriser le trafic entrant dans les clusters.

Les dépendances de trafic sortant HDInsight sont presque entièrement définies avec des noms FQDN, qui n’ont pas d’adresses IP statiques sous-jacentes. L’absence d’adresses statiques signifie que les groupes de sécurité réseau (NSG) ne peuvent pas être utilisés pour verrouiller le trafic sortant d’un cluster. Les adresses changent assez souvent et il n’est donc pas possible de définir des règles basées sur la résolution de noms actuelle et de les utiliser pour configurer des règles NSG.

La solution pour sécuriser les adresses sortantes réside dans l’utilisation d’un dispositif de pare-feu pouvant contrôler le trafic sortant en fonction des noms de domaine. Le pare-feu Azure peut restreindre le trafic HTTP et HTTPS sortant en fonction du nom de domaine complet de la destination ou des [étiquettes FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configuration du pare-feu Azure avec HDInsight

Voici le résumé des étapes pour verrouiller les sorties de votre cluster HDInsight existant avec le pare-feu Azure :
1. Créez un pare-feu.
1. Ajoutez des règles d’application au pare-feu.
1. Ajoutez des règles de réseau au pare-feu.
1. Créez une table de routage.

### <a name="create-a-new-firewall-for-your-cluster"></a>Créer un pare-feu pour votre cluster

1. Créez un sous-réseau nommé **AzureFirewallSubnet** dans le réseau virtuel où se trouve votre cluster. 
1. Créer un pare-feu **Test-FW01** à l’aide de la procédure décrite dans l’article [Didacticiel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)

### <a name="configure-the-firewall-with-application-rules"></a>Configurer le pare-feu avec des règles d’application

Créez un regroupement de règles d’application permettant au cluster d’envoyer et de recevoir les communications importantes.

Sélectionnez le nouveau pare-feu **Test-FW01** dans le Portail Azure. Cliquez sur **Règles** sous **Paramètres** > **Collection de règles d’application** > **Ajouter une collection de règles d’application**.

![Titre : Ajouter une collection de règles d’application](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Dans l’écran **Ajouter une collection de règles d’application**, procédez comme suit :

1. Indiquez un **Nom**, une **Priorité**, et cliquez sur **Autoriser** dans le menu déroulant **Action**, puis entrez les règles suivantes dans la **section Étiquettes FQDN** :

   | **Nom** | **Adresse source** | **Étiquette FQDN** | **Remarques** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight et Windows Update | Requis pour les services HDI |

1. Ajoutez les règles suivantes dans la **section Noms de domaine complets cibles** :

   | **Nom** | **Adresse source** | **Protocole:Port** | **Noms de domaine complets cibles** | **Remarques** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Autorise les activités de connexion Windows |
   | Rule_3 | * | https:443,http:80 | <nom_compte_stockage.blob.core.windows.net> | Si votre cluster s’appuie sur WASB, ajoutez une règle pour WASB. Pour utiliser UNIQUEMENT les connexions https, assurez-vous que l’option [« Transfert sécurisé requis »](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) est activée sur le compte de stockage. |

1. Cliquez sur **Add**.

   ![Titre : Entrer les détails de la collection de règles d’application](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurer le pare-feu avec des règles de réseau

Créez les règles de réseau pour configurer correctement votre cluster HDInsight.

1. Sélectionnez le nouveau pare-feu **Test-FW01** dans le Portail Azure.
1. Cliquez sur **Règles** sous **Paramètres** > **Collection de règles de réseau** > **Ajouter une collection de règles de réseau**.
1. Dans l’écran **Ajouter une collection de règles de réseau**, indiquez un **Nom**, une **Priorité**, puis cliquez sur **Autoriser** dans le menu déroulant **Action**.
1. Créez les règles suivantes dans la section **Adresses IP** :

   | **Nom** | **Protocole** | **Adresse source** | **Adresses de destination** | **Port de destination** | **Remarques** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Service de temps |
   | Rule_2 | Quelconque | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Si vous utilisez le Pack Sécurité Entreprise (ESP), ajoutez une règle de réseau dans la section Adresses IP permettant la communication avec AAD-DS pour les clusters ESP. Vous pouvez trouver les adresses IP des contrôleurs de domaine dans la section AAD-DS du portail. | 
   | Rule_3 | TCP | * | Adresse IP de votre compte Data Lake Storage | `*` | Si vous utilisez Azure Data Lake Storage, vous pouvez ajouter une règle de réseau dans la section Adresses IP pour résoudre un problème d’indication de nom de serveur avec ADLS Gen1 et Gen2. Cette option achemine le trafic vers le pare-feu, ce qui peut entraîner des coûts plus élevés pour les chargements de données volumineux, mais le trafic sera enregistré et pourra être contrôlé dans les journaux de pare-feu. Déterminez l’adresse IP de votre compte Data Lake Storage. Vous pouvez utiliser une commande powershell comme `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` pour convertir le nom de domaine complet en adresse IP.|
   | Rule_4 | TCP | * | * | `12000` | (Facultatif) Si vous utilisez Log Analytics, créez une règle de réseau dans la section Adresses IP pour permettre la communication avec votre espace de travail Log Analytics. |

1. Créez les règles suivantes dans la section **Étiquettes de service** :

   | **Nom** | **Protocole** | **Adresse source** | **Balises de service** | **Port de destination** | **Remarques** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Configurez une règle de réseau dans la section Étiquettes de service pour SQL qui vous permettra d’enregistrer et d’auditer le trafic SQL, sauf si vous avez configuré des points de terminaison de service pour SQL Server sur le sous-réseau HDInsight qui contournent le pare-feu. |

1. Cliquez sur **Ajouter** pour terminer la création de votre collection de règles de réseau.

   ![Titre : Entrer les détails de la collection de règles d’application](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Créer et configurer une table de routage

Créez une table de routage avec les entrées suivantes :

1. Six adresses de [cette liste d’adresses IP de gestion HDInsight requises](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) avec comme tronçon suivant **Internet** :
    1. Quatre adresses IP pour les clusters de toutes les régions
    1. Deux adresses IP propres à la région où le cluster a été créé
1. Une route d’appliance virtuelle pour l’adresse IP 0.0.0.0/0 avec comme tronçon suivant l’adresse IP privée de votre pare-feu Azure.

Par exemple, pour configurer la table de routage d’un cluster créé dans la région des États-Unis « USA Centre », procédez comme suit :

1. Connectez-vous au portail Azure.
1. Sélectionnez votre pare-feu Azure **Test-FW01**. Copiez l’**adresse IP privée** figurant dans la page **Vue d’ensemble**. Pour cet exemple, nous utiliserons **l’exemple d’adresse 10.1.1.4**
1. Créez une table de routage.
1. Sous **Paramètres**, cliquez sur **Itinéraires**.
1. Cliquez sur **Ajouter** afin de créer des itinéraires pour les adresses IP figurant dans le tableau ci-dessous.

| Nom de l’itinéraire | Préfixe de l’adresse | Type de tronçon suivant | adresse de tronçon suivant |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N/D |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N/D |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N/D |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N/D |
| 13.67.223.215 | 13.67.223.215/32 | Internet | N/D |
| 40.86.83.253 | 40.86.83.253/32 | Internet | N/D |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuelle | 10.1.1.4 |

Terminez la configuration de la table de routage :

1. Affectez la table de routage que vous avez créée à votre sous-réseau HDInsight en sélectionnant **Sous-réseaux** sous **Paramètres**, puis **Associer**.
1. Dans l’écran **Associer un sous-réseau**, sélectionnez le réseau virtuel dans lequel votre cluster a été créé et le **Sous-réseau HDInsight** que vous avez utilisé pour votre cluster HDInsight.
1. Cliquez sur **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nœud de périphérie ou trafic d’application personnalisée

Les étapes ci-dessus permettent à votre cluster de fonctionner sans problème. Vous devez encore configurer les dépendances pour prendre en charge vos applications personnalisées s’exécutant sur les nœuds de périphérie, le cas échéant.

Les dépendances des applications doivent être identifiées et ajoutées au pare-feu Azure ou à la table de routage.

Les itinéraires doivent être créés pour le trafic des applications afin d’éviter des problèmes de routage asymétrique.

Si vos applications ont d’autres dépendances, celles-ci doivent être ajoutées à votre pare-feu Azure. Créez des règles d’application pour autoriser le trafic HTTP/HTTPS et des règles de réseau pour tout le reste.

## <a name="logging"></a>Journalisation

Le pare-feu Azure peut envoyer des journaux à plusieurs systèmes de stockage différents. Pour obtenir des instructions sur la configuration de la journalisation de votre pare-feu, suivez les étapes décrites dans l’article [Didacticiel : superviser les journaux du Pare-feu Azure et les métriques](../firewall/tutorial-diagnostics.md).

Une fois que vous avez terminé la configuration de la journalisation, si vous enregistrez les données dans Log Analytics, vous pouvez afficher le trafic bloqué à l’aide d’une requête telle que la suivante :

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L’intégration de votre pare-feu Azure à des journaux d’activité Azure Monitor est utile quand vous préparez une application sans connaître toutes ses dépendances. Pour en savoir plus sur les journaux d’activité Azure Monitor, consultez [Analyser les données de journal d’activité dans Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Accès au cluster
Après avoir correctement configuré le pare-feu, vous pouvez utiliser le point de terminaison interne (`https://<clustername>-int.azurehdinsight.net`) pour accéder à l’API Ambari à partir du réseau virtuel. Pour utiliser le point de terminaison public (`https://<clustername>.azurehdinsight.net`) ou ssh (`<clustername>-ssh.azurehdinsight.net`), assurez-vous que vous avez les bons itinéraires dans la table de routage et que les règles NSG sont bien configurées pour éviter le problème de routage asymétrique expliqué [ici](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Configurer une autre appliance virtuelle réseau

>[!Important]
> Les informations suivantes sont requises **uniquement** si vous souhaitez configurer une appliance virtuelle réseau (NVA) autre que le pare-feu Azure.

Les instructions précédentes vous aideront à configurer le pare-feu Azure pour restreindre le trafic sortant de votre cluster HDInsight. Le pare-feu Azure est automatiquement configuré pour autoriser le trafic dans la plupart des scénarios importants courants. Si vous souhaitez utiliser une autre appliance virtuelle réseau, vous devrez configurer manuellement un certain nombre de fonctionnalités supplémentaires. Gardez les points suivants à l’esprit pendant la configuration de votre appliance virtuelle réseau :

* Les services compatibles avec les points de terminaison de service doivent être configurés avec des points de terminaison de service.
* Les dépendances d’adresses IP sont destinées au trafic non HTTP/S (à la fois le trafic TCP et UDP).
* Les points de terminaison HTTP/HTTPS avec des noms FQDN peuvent être placés dans votre dispositif NVA.
* Les points de terminaison HTTP/HTTPS avec des caractères génériques sont des dépendances qui peuvent varier selon le nombre de qualificateurs.
* Affectez la table de routage créée à votre sous-réseau HDInsight.

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
| Adresses IP publiées [ici](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Elles sont associées au service HDInsight |
| Adresses IP privées AAD-DS pour les clusters ESP |
| \*:16800 pour KMS Windows Activation |
| \*12000 pour Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dépendances HTTP/HTTPS FQDN

>[!Important]
> La liste ci-dessous contient seulement quelques-uns des noms FQDN les plus importants. Vous pouvez obtenir la liste complète des noms FQDN pour la configuration de votre NVA [dans ce fichier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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
