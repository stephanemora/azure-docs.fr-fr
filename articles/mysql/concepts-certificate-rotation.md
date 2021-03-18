---
title: Rotation de certificat pour Azure Database pour MySQL
description: En savoir plus sur les modifications à venir dues aux changements de certificat racine qui affectent Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 23fa3e93565066ce4b897bffe63164486efc179e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449881"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Comprendre les modifications liées au changement d’autorité de certification racine pour Azure Database pour MySQL - Serveur unique

Azure Database pour MySQL - Serveur unique a fait l’objet d’un changement d’autorité de certification racine le **15 février 2021 (15/02/2021)** dans le cadre de la maintenance standard et des bonnes pratiques en matière de sécurité. Cet article fournit davantage de détails sur les changements, les ressources affectées et les étapes nécessaires pour garantir que votre application maintient sa connectivité à votre serveur de base de données.

> [!NOTE]
> Cet article s’applique UNIQUEMENT à [Azure Database pour MySQL - Serveur unique](single-server-overview.md). Pour [Azure Database pour MySQL - Serveur flexible](flexible-server/overview.md), le certificat nécessaire pour communiquer par le biais de SSL est [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem).
> 
> Cet article contient des références au terme _esclave_, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.
>

## <a name="why-root-certificate-update-is-required"></a>Pourquoi la mise à jour du certificat racine est obligatoire ?

Les clients Azure Database pour MySQL peuvent uniquement utiliser le certificat prédéfini pour se connecter à leur serveur MySQL, situé [ici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Toutefois, le [forum Certificate Authority (CA) Browser](https://cabforum.org/)  a récemment publié des rapports indiquant que plusieurs certificats émis par les fournisseurs d’autorité de certification n’étaient pas conformes.

Conformément aux exigences de conformité du secteur, les fournisseurs d’autorité de certification ont commencé à révoquer les certificats d’autorité de certification non conformes, en demandant aux serveurs d’utiliser des certificats émis par des autorités de certification conformes et signés par des certificats d’autorité de certification provenant de ces dernières. Étant donné qu’Azure Database pour MySQL utilisait l’un de ces certificats non conformes, nous avions besoin de procéder à la permutation du certificat vers la version conforme afin de réduire la menace potentielle envers vos serveurs MySQL.

Le nouveau certificat est déployé et prend effet à compter du 15 février 2021 (15/02/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Quel changement a été apporté le 15 février 2021 (15/02/2021) ?

Le 15 février 2021, le [certificat racine BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) a été remplacé par une **version conforme** du même [certificat racine BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) afin de s’assurer que les clients existants n’aient pas besoin de modifier quoi que ce soit et qu’il n’y ait aucun impact sur leurs connexions au serveur. Lors de ce changement, le [certificat racine BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) n’a **pas été remplacé** par [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem), et ce changement est différé afin d’accorder aux clients plus de temps pour effectuer le changement.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Est-ce que je dois apporter des modifications à mon client pour maintenir la connectivité ?

Aucune modification n’est nécessaire du côté client. Si vous avez suivi notre précédente recommandation ci-dessous, vous pourrez toujours continuer à vous connecter tant que le **certificat BaltimoreCyberTrustRoot n’est pas supprimé** du certificat d’autorité de certification combiné. **Nous vous recommandons de ne pas supprimer BaltimoreCyberTrustRoot de votre certificat d’autorité de certification combiné tant que vous n’y aurez pas été invité, afin de maintenir la connectivité.**

### <a name="previous-recommendation"></a>Recommandation précédente

Pour éviter toute interruption de la disponibilité de votre application en raison de la révocation inattendue de certificats, ou pour mettre à jour un certificat qui a été révoqué, effectuez les étapes suivantes. L’idée est de créer un fichier *.pem*, qui combine le certificat actuel et le nouveau, et pendant la validation du certificat SSL, une des valeurs autorisées sera utilisée. Reportez-vous aux étapes suivantes :

* Téléchargez l’autorité de certification racine BaltimoreCyberTrustRootDigiCertGlobalRootG2 à partir des liens suivants :

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* Générez un magasin de certificats d’autorité de certification combiné où les certificats **BaltimoreCyberTrustRoot** et **DigiCertGlobalRootG2** sont inclus.

  * Pour les utilisateurs Java (MySQL Connector/J), exécutez :

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    Remplacez ensuite le fichier de magasin de clés d’origine par le nouveau fichier généré :

    * System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
    * System.setProperty("javax.net.ssl.trustStorePassword","password");

  * Si vous utilisez .NET (MySQL Connector/NET, MySQLConnector), assurez-vous que **BaltimoreCyberTrustRoot** et **DigiCertGlobalRootG2** existent dans le magasin de certificats Windows et dans les autorités de certification racines de confiance. S’il n’existe aucun certificat, importez le certificat manquant.

    :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Diagramme de certificat .NET Azure Database pour MySQL":::

  * Si vous utilisez .NET sur Linux en utilisant SSL_CERT_DIR, assurez-vous que **BaltimoreCyberTrustRoot** et **DigiCertGlobalRootG2** existent tous deux dans le répertoire indiqué par SSL_CERT_DIR. S’il n’existe aucun certificat, créez le fichier de certificat manquant.

  * Si vous utilisez d’autres solutions (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), vous pouvez fusionner deux fichiers de certificat d’autorité de certification dans le format suivant :

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

* Remplacez le fichier .pem d’origine de l’autorité de certification racine par le fichier d’autorité de certification racine combiné et redémarrez votre application/client.
* Ensuite, après le déploiement du nouveau certificat côté serveur, vous pouvez remplacer votre fichier .pem d’autorité de certification par DigiCertGlobalRootG2.crt.pem.

> [!NOTE]
> Ne supprimez pas ou ne modifiez pas **certificat Baltimore** tant que le changement de certificat n’est pas effectué. Nous enverrons une communication une fois la modification effectuée, après quoi il est possible de supprimer le certificat Baltimore. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Pourquoi le certificat BaltimoreCyberTrustRoot n’a pas été remplacé par DigiCertGlobalRootG2 lors de ce changement le 15 février 2021 ?

Nous avons évalué le degré de préparation des clients à ce changement et nous sommes rendus compte que nombre d’entre eux souhaitaient disposer d’un délai supplémentaire pour gérer ce changement. Afin de fournir aux clients davantage de temps pour se préparer, nous avons décidé de reporter le passage au certificat DigiCertGlobalRootG2 pendant au moins un an, offrant ainsi un délai suffisant aux clients et aux utilisateurs finaux. 

Nos recommandations aux utilisateurs sont les suivantes : suivez les étapes ci-dessus pour créer un certificat combiné et se connecter à votre serveur, mais ne supprimez pas le certificat BaltimoreCyberTrustRoot tant que nous ne vous y avons pas invité. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Que se passe-t-il si nous avons supprimé le certificat BaltimoreCyberTrustRoot ?

Vous commencerez à observer des erreurs lors de la connexion à votre serveur Azure Database pour MySQL. Vous devrez [reconfigurer SSL](howto-configure-ssl.md) avec le certificat [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) pour maintenir la connectivité.


## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Si je n’utilise pas SSL/TLS, dois-je quand même mettre à jour l’autorité de certification racine ?

  Aucune action n’est requise si vous n’utilisez pas SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Si j’utilise SSL/TLS, dois-je redémarrer mon serveur de base de données pour mettre à jour l’autorité de certification racine ?

Non, vous n’avez pas besoin de redémarrer le serveur de base de données pour commencer à utiliser le nouveau certificat. Ce certificat racine est une modification côté client et les connexions clientes entrantes doivent utiliser le nouveau certificat pour s’assurer qu’elles peuvent se connecter au serveur de base de données.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Comment savoir si j’utilise SSL/TLS avec la vérification du certificat racine ?

Vous pouvez déterminer si vos connexions vérifient le certificat racine en examinant votre chaîne de connexion.

- Si votre chaîne de connexion inclut `sslmode=verify-ca` ou `sslmode=verify-identity`, vous devez mettre à jour le certificat.
- Si votre chaîne de connexion inclut `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` ou `sslmode=require`, vous n’avez pas besoin de mettre à jour les certificats.
- Si votre chaîne de connexion ne spécifie pas sslmode, vous n’avez pas besoin de mettre à jour les certificats.

Si vous utilisez un client qui abstrait la chaîne de connexion, consultez la documentation du client pour savoir s’il vérifie les certificats.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. Quel est l’impact de l’utilisation d’App Service avec Azure Database for MySQL ?

Pour les services Azure App Service qui se connectent à Azure Database pour MySQL, deux scénarios sont possibles et dépendent de la façon dont vous utilisez SSL avec votre application.

* Ce nouveau certificat a été ajouté à App Service au niveau de la plateforme. Si vous utilisez les certificats SSL inclus sur la plateforme App Service dans votre application, aucune action n’est nécessaire. Il s’agit du scénario le plus courant. 
* Si vous incluez explicitement le chemin du fichier de certificat SSL dans votre code, vous devez télécharger le nouveau certificat, générer un certificat combiné comme indiqué ci-dessus, et utiliser le fichier de certificat. Un bon exemple de ce scénario est lorsque vous utilisez des conteneurs personnalisés dans App Service de la manière décrite dans la [documentation d’App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Il s’agit d’un scénario rare, mais nous l’avons déjà observé chez certains utilisateurs.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. Quel est l’impact de l’utilisation d’Azure Kubernetes Services (AKS) avec Azure Database for MySQL ?

Si vous essayez de vous connecter au serveur Azure Database pour MySQL à l’aide d’Azure Kubernetes Services (AKS), cela est similaire à un accès à partir d’un environnement d’hôte de clients dédié. Reportez-vous aux étapes indiquées [ici](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. Quel est l’impact de l’utilisation d’Azure Data Factory pour se connecter à Azure Database for MySQL ?

Le connecteur qui utilise Azure Integration Runtime exploite les certificats du magasin de certificats Windows dans l’environnement hébergé par Azure. Comme ces certificats sont déjà compatibles avec les certificats récemment appliqués, aucune action n’est nécessaire.

Pour le connecteur utilisant le runtime d’intégration auto-hébergé où vous incluez explicitement le chemin d’accès au fichier de certificat SSL dans votre chaîne de connexion, vous devez télécharger le [nouveau certificat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) et mettre à jour la chaîne de connexion de façon à ce qu’elle l’utilise.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Dois-je prévoir un temps d’arrêt lié à la maintenance du serveur de base de données pour effectuer cette modification ?

Non. Étant donné que la modification est uniquement côté client pour la connexion au serveur de base de données, aucun temps d’arrêt lié à la maintenance n’est nécessaire pour le serveur de base de données lors de cette modification.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Si je crée un serveur après le 15 février 2021 (15/02/2021), serai-je concerné ?

Pour les serveurs créés après le 15 février 2021 (15/02/2021), vous continuerez à utiliser [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) pour que vos applications se connectent à l’aide de SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Quelle est la fréquence à laquelle Microsoft met à jour ses certificats ou quelle est la stratégie d’expiration ?

Les certificats utilisés par Azure Database for MySQL sont fournis par des autorités de certification approuvées. Par conséquent, la prise en charge de ces certificats est liée à la prise en charge de ces certificats par l’autorité de certification. L’expiration du certificat [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) est prévue pour 2025. Microsoft devra donc procéder à un changement de certificat avant l’expiration. De plus, en cas de bogues imprévus dans ces certificats prédéfinis, Microsoft devra procéder à la permutation de certificat au plus tôt, d’une manière similaire au changement effectué le 15 février 2021, afin de garantir que le service demeure sécurisé et conforme à tout moment.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Si j’utilise des réplicas en lecture, dois-je effectuer cette mise à jour uniquement sur le serveur source ou sur les réplicas de lecture ?

Étant donné que cette mise à jour est une modification côté client, si le client avait l’habitude de lire les données du serveur de réplica, vous devez également appliquer les modifications pour ces clients.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Si j’utilise la réplication des données entrantes, dois-je effectuer une action particulière ?

Si vous utilisez la [réplication des données entrantes](concepts-data-in-replication.md) pour vous connecter à Azure Database pour MySQL, deux éléments sont à prendre en compte :

* Si la réplication des données s'effectue entre une machine virtuelle (locale ou Azure) et Azure Database pour MySQL, vous devez vérifier si SSL est utilisé pour créer le réplica. Exécutez **SHOW SLAVE STATUS** et vérifiez le paramètre suivant.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Si vous constatez que le certificat est fourni pour CA_file, SSL_Cert et SSL_Key, vous devez mettre à jour le fichier en ajoutant le [nouveau certificat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem), et créer un fichier de certificat combiné.

* Si la réplication des données s’effectue entre deux instances d’Azure Database pour MySQL, vous devez réinitialiser le réplica en exécutant **CALL mysql.az_replication_change_master** et en fournissant le nouveau certificat racine double en tant que dernier paramètre [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Existe-t-il une requête côté serveur pour vérifier si SSL est utilisé ?

Pour vérifier si vous utilisez une connexion SSL pour vous connecter au serveur, consultez [Vérification SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. Une action est-elle nécessaire si je dispose déjà de DigiCertGlobalRootG2 dans mon fichier de certificat ?

Non. Aucune action n’est nécessaire si votre fichier de certificat contient déjà **DigiCertGlobalRootG2**.

### <a name="14-what-if-i-have-further-questions"></a>14. Que se passe-t-il si j’ai d’autres questions ?

Si vous avez des questions, posez-les aux experts de la communauté dans [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Si vous disposez d'un plan de support et que vous avez besoin d'une aide technique, [contactez-nous](mailto:AzureDatabaseforMySQL@service.microsoft.com).
