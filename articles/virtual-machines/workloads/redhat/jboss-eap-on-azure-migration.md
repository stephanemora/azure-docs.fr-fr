---
title: Guide de migration de JBoss EAP vers des groupes de machines virtuelles identiques et des machines virtuelles Azure
description: Ce guide fournit des informations sur la façon de migrer vos applications Java d’entreprise d’un autre serveur d’applications vers JBoss EAP et d’un serveur local traditionnel vers des groupes de machines virtuelles identiques et des machines virtuelles Azure RHEL.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 06/08/2021
ms.openlocfilehash: deded6927998daa5956cff2f63a0a53d3b973b6d
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111855082"
---
# <a name="how-to-migrate-java-applications-to-jboss-eap-on-azure-vms-and-virtual-machine-scale-sets"></a>Découvrez comment migrer des applications Java vers JBoss EAP sur des groupes de machines virtuelles identiques et des machines virtuelles Azure.

Ce guide fournit des informations sur la façon de migrer vos applications Java d’entreprise sur [Red Hat JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) à partir d’un serveur local traditionnel vers des groupes de machines virtuelles identiques et des machines virtuelles Azure Red Hat Enterprise Linux (RHEL) si votre stratégie cloud consiste à effectuer une opération « lift-and-shift » des applications Java en l’état. Toutefois, si vous souhaitez effectuer une opération « lift-and-optimize », vous pouvez également migrer vos applications conteneurisées vers [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/) avec des images JBoss EAP à partir de la Galerie Red Hat, ou déposer votre code d’application Java directement dans une instance JBoss EAP sur Azure App Service.

## <a name="best-practice-starting-with-azure-marketplace-offers-and-quickstarts"></a>Bonne pratique pour commencer avec les offres de la Place de marché Azure et les démarrages rapides

Red Hat et Microsoft travaillent en partenariat pour proposer un ensemble d’[offres JBoss EAP sur la Place de marché Azure](https://aka.ms/AMP-JBoss-EAP) afin de fournir un point de départ solide pour migrer vers Azure. Consultez la documentation pour obtenir la liste des offres et des plans et sélectionner l’option qui correspond le mieux à votre déploiement existant. Consultez l’article sur les [bonnes pratiques pour JBoss EAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/jboss-eap-azure-best-practices/).

Si aucune des offres existantes ne constitue un bon point de départ, vous pouvez reproduire manuellement le déploiement à l’aide de la machine virtuelle Azure et d’autres ressources disponibles. Pour plus d’informations, consultez [Qu’est-ce que l’IaaS ?](https://azure.microsoft.com/overview/what-is-iaas/)

### <a name="azure-marketplace-offers"></a>Offres de la Place de marché Azure

En partenariat avec Microsoft, Red Hat a publié les offres suivantes sur la Place de marché Azure. Vous pouvez accéder à ces offres depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/) ou à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour plus d’informations, consultez l’article sur la façon de [déployer Red Hat JBoss EAP sur une machine virtuelle Azure et des groupes de machines virtuelles identiques à l’aide de l’offre de la Place de marché Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/jboss-eap-azure-marketplace/).

Cette offre de la place de marché comprend diverses combinaisons des versions de JBoss EAP et de RHEL avec des modèles d’abonnement de support flexibles. JBoss EAP est disponible en mode BYOS (apportez votre propre abonnement), mais pour RHEL, vous pouvez choisir entre les modes BYOS et PAYG (paiement à l’utilisation). L’offre de la Place de marché Azure comprend des options de plan pour JBoss EAP sur RHEL en tant que machines virtuelles autonomes, machines virtuelles clusterisées et groupes de machines virtuelles identiques clusterisés. Ces 6 plans sont les suivants :

- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Machine virtuelle clusterisée JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle clusterisée JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Groupes de machines virtuelles identiques clusterisés JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Groupes de machines virtuelles identiques clusterisés JBoss EAP 7.3 sur RHEL 8.3 (BYOS)

### <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Azure

En plus des offres de la Place de marché Azure, des modèles de démarrage rapide vous sont proposés comme version d’évaluation d’EAP sur Azure. Ces modèles de démarrage rapide incluent des modèles ARM et un script prédéfinis pour le déploiement de JBoss EAP sur Azure dans différentes configurations et combinaisons de versions. L’architecture de solution comprend :

- Une machine virtuelle autonome JBoss EAP sur RHEL
- Des machines virtuelles clusterisées JBoss EAP sur RHEL
- Des groupes de machines virtuelles identiques clusterisés JBoss EAP sur RHEL

Pour commencer rapidement, sélectionnez l’un des modèles de démarrage rapide qui correspond étroitement à votre combinaison de versions de JBoss EAP sur RHEL. Pour en apprendre plus, consultez la documentation [Démarrage rapide de JBoss EAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/jboss-eap-on-rhel). 

## <a name="prerequisites"></a>Prérequis

* **Un compte Azure avec un abonnement actif** – Si vous n’avez pas d’abonnement Azure, vous pouvez activer vos [avantages d’abonné Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (anciennement MSDN) ou [créer un compte gratuitement](https://azure.microsoft.com/pricing/free-trial).

- **Installation de JBoss EAP** – Vous devez disposer d’un compte Red Hat avec le droit RHSM (Red Hat Subscription Management) pour JBoss EAP. Ce droit vous permet de télécharger la version de JBoss EAP testée et certifiée pour Red Hat.  Si vous ne disposez pas du droit EAP, vous pouvez vous inscrire pour bénéficier d’un abonnement développeur gratuit via l’[abonnement Red Hat Developer pour les particuliers](https://developers.redhat.com/register). Une fois inscrit, vous pouvez trouver les informations d’identification nécessaires (ID de pool) sur le [portail client Red Hat](https://access.redhat.com/management/).

- **Options RHEL** – Choisissez entre le mode PAYG (Paiement à l’utilisation) ou BYOS (Apportez votre propre abonnement). Avec BYOS, vous devez activer votre [image RHEL Gold](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) de [Red Hat Cloud Access](https://access.redhat.com/) avant de déployer l’offre de la place de marché avec un modèle de solutions. Suivez [ces instructions](https://access.redhat.com/documentation/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access) pour activer les images RHEL Gold à utiliser sur Microsoft Azure.

- **[Interface de ligne de commande Azure (CLI)](https://docs.microsoft.com/cli/azure/overview)**

- **Code source Java et [version du kit de développement Java (JDK)](https://www.oracle.com/java/technologies/javase-downloads.html)**

- **[Application Java basée sur JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/development_guide/index#become_familiar_with_java_enterprise_edition_8)** ou **[application Java basée sur JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/development_guide/index#get_started_developing_applications)**

**Options RHEL** – Choisissez entre PAYG et BYOS. Pour BYOS, vous devez activer votre image RHEL Gold de [Red Hat Cloud Access](https://access.redhat.com/documentation/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index) pour utiliser l’offre de la Place de marché Azure. Les offres BYOS s’affichent dans la section Offre privée du portail Azure. 

**Versions du produit**

* [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* [RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM)
* [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)

## <a name="migration-flow-and-architecture"></a>Architecture et flux de migration

Cette section spécifie les outils gratuits pour la migration d’applications JBoss EAP à partir d’un autre serveur d’applications afin de les exécuter sur JBoss EAP et à partir des serveurs locaux traditionnels vers l’environnement cloud Microsoft Azure. 

### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Kit de ressources de migration Red Hat pour applications (MTA)

Il est recommandé d’utiliser le kit de ressources de migration Red Hat pour applications, pour la migration des applications Java, au début de votre cycle de planification, avant d’exécuter tout projet de migration lié à EAP. Le kit MTA est un ensemble d’outils prenant en charge des projets de modernisation et de migration d’applications Java à grande échelle dans une [large gamme de transformations et de cas d’usage](https://developers.redhat.com/products/mta/use-cases). Il accélère l’analyse du code d’application, prend en charge l’estimation des efforts, accélère la migration du code et vous aide à déplacer les applications vers le cloud et les conteneurs.

:::image type="content" source="./media/migration-toolkit.png" alt-text="Image montrant la page de tableau de bord de l’application de kit de ressources de migration":::

Le kit Red Hat MTA vous permet de migrer des applications à partir d’autres serveurs d’applications vers Red Hat JBoss EAP.

## <a name="pre-migration"></a>Prémigration

Pour garantir la réussite de la migration, avant de commencer, effectuez les étapes d’évaluation et d’inventaire décrites dans les sections suivantes.

### <a name="validate-the-compatibility"></a>Valider la compatibilité

Il est recommandé de valider votre modèle de déploiement actuel et sa version avant de planifier la migration. Vous devrez peut-être apporter des modifications importantes à votre application si votre version actuelle n’est pas prise en charge.

Le kit MTA prend en charge les migrations à partir de serveurs d’applications d’entreprise tiers, tels qu’Oracle WebLogic Server, vers JBoss EAP et les mises à niveau vers la dernière version de JBoss EAP.

Le tableau suivant décrit les chemins de migration pris en charge les plus courants.

**Tableau – Chemins de migration pris en charge : source vers cible**

|Plateforme source ⇒ | JBoss EAP 6 | JBoss EAP 7 | Red Hat OpenShift | OpenJDK 8 et 11 | Apache Camel 3 | Exécutions Spring Boot sur RH | Quarkus
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Oracle WebLogic Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| IBM WebSphere Application Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 4 | &#x2714; | &#x2714; | X<sup>1</sup> | &#x2714; | - | - | - |
| JBoss EAP 5 | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 7 | N/A | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| Oracle JDK | - | - | &#x2714; | &#x2714; | - | - | - |
| Apache Camel 2 | - | - | &#x2714; | &#x2714; | &#x2714; | - | - |
| SpringBoot | - | - | &#x2714; | &#x2714; | - | &#x2714; | &#x2714; |
| Application Java | - | - | &#x2714; | &#x2714; | - | - | - |

<sup>1</sup> Même si le kit MTA ne fournit actuellement aucune règle pour ce chemin de migration, Red Hat Consulting peut faciliter la migration à partir de n’importe quelle plateforme source vers JBoss EAP 7.

:::image type="content" source="./media/jboss-cli-image.png" alt-text="Image montrant la sortie dans la fenêtre de l’interface CLI":::

Vous pouvez également vérifier la [configuration système requise](https://access.redhat.com/documentation/en/migration_toolkit_for_applications/5.0/html-single/introduction_to_the_migration_toolkit_for_applications/index#system_requirements_getting-started-guide) pour le kit MTA.

Vérifiez les [configurations prises en charge par JBoss EAP 7.3](https://access.redhat.com/articles/2026253#EAP_73) et les [configurations prises en charge par JBoss EAP 7.2](https://access.redhat.com/articles/2026253#EAP_72) avant de planifier la migration.

Pour obtenir votre version actuelle de Java, connectez-vous à votre serveur et exécutez la commande suivante :

```
java -version
```

### <a name="validate-operating-mode"></a>Valider le mode d’opération

JBoss EAP est pris en charge sur RHEL, Windows Server et Oracle Solaris. JBoss EAP s’exécute en mode de fonctionnement de serveur autonome pour la gestion des instances discrètes ou du mode de fonctionnement du domaine managé pour gérer des groupes d’instances à partir d’un point de contrôle unique.

Les domaines managés JBoss EAP ne sont pas pris en charge dans Microsoft Azure. Seules les instances de serveur JBoss EAP autonomes sont prises en charge. Notez que la configuration de clusters JBoss EAP avec des serveurs JBoss EAP autonomes est prise en charge dans Azure et c’est ainsi que l’offre de la Place de marché Azure crée vos machines virtuelles ou groupes de machines virtuelles identiques clusterisés.

### <a name="inventory-server-capacity"></a>Inventorier la capacité des serveurs

Documentez le matériel (mémoire, processeur, disque, etc.) du ou des serveurs de production actuels, ainsi que les nombres moyen et maximal de requêtes et l’utilisation des ressources. Vous aurez besoin de ces informations, quel que soit le chemin de migration que vous choisissez. Pour plus d’informations sur les tailles, consultez [Tailles pour Azure Cloud Services](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs).

### <a name="inventory-all-secrets"></a>Inventorier tous les secrets

Avant l’avènement des technologies de « configuration en tant que service » comme [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) et [Azure App Configuration](https://azure.microsoft.com/services/app-configuration/), le concept de « secrets » n’était pas bien défini. Vous aviez plutôt un ensemble disparate de paramètres de configuration qui fonctionnaient en réalité comme ce que nous appelons maintenant des « secrets ». Avec les serveurs d’applications comme JBoss EAP, ces secrets se trouvent dans de nombreux fichiers de configuration et magasins de configurations différents. Recherchez les secrets et mots de passe dans l’ensemble des propriétés et fichiers de configuration présents sur le ou les serveurs de production. Veillez à vérifier *jboss-web.xml* dans vos fichiers WAR. Vous pouvez également trouver des fichiers de configuration contenant des mots de passe ou des informations d’identification au sein de votre application. Pour plus d’informations sur Azure Key Vault, consultez [Concepts de base d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/basic-concepts).

### <a name="inventory-all-certificates"></a>Inventorier tous les certificats

Documentez tous les certificats utilisés pour les points de terminaison SSL publics. Vous pouvez voir tous les certificats présents sur les serveurs de production en exécutant la commande suivante :

```cli
keytool -list -v -keystore <path to keystore>
```

### <a name="inventory-jndi-resources"></a>Inventorier les ressources JNDI

- Inventoriez toutes les ressources JNDI (Java Naming and Directory Interface). Certaines, comme les répartiteurs JMS (Java Message Service), peuvent nécessiter une migration ou une reconfiguration.

### <a name="insideyyour-application"></a>Application InsideyYour 

Inspectez les fichiers WEB-INF/jboss-web.xml et/ou WEB-INF/web.xml.

### <a name="document-data-sources"></a>Documentation de sources de données

Si votre application utilise des bases de données, vous devez capturer les informations suivantes :

* Quel est le nom de la source de données ?
* Quelle est la configuration du pool de connexions ?
* Où se trouve le fichier JAR du pilote JDBC (Java Database Connectivity) ?

Pour plus d’informations, consultez [About JBoss EAP DataSources](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/datasource_management) (À propos des sources de données JBoss EAP) dans la documentation de JBoss EAP.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Déterminer si le système de fichiers est utilisé et de quelle manière

Toute utilisation du système de fichiers sur le serveur d’applications nécessite une reconfiguration ou, dans de rares cas, des modifications architecturales. Le système de fichiers peut être utilisé par les modules JBoss EAP ou par votre code d’application. Vous pouvez identifier une partie ou l’ensemble des scénarios décrits dans les sections suivantes.

**Contenu statique en lecture seule**

Si votre application sert actuellement du contenu statique, vous aurez besoin d’un autre emplacement pour lui. Vous pouvez envisager de déplacer du contenu statique vers le Stockage Blob Azure et d’ajouter [Azure Content Delivery Network (CDN)](https://docs.microsoft.com/azure/cdn/) pour accélérer globalement les téléchargements. Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website) et [Démarrage rapide : Intégrer un compte de stockage Azure à Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-create-a-storage-account-with-cdn).

**Contenu statique publié dynamiquement**

Si votre application autorise le contenu statique chargé/produit par votre application mais immuable après sa création, vous pouvez utiliser le [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/) et Azure CDN comme décrit ci-dessus, avec une [fonction Azure](https://docs.microsoft.com/azure/azure-functions/) pour gérer les chargements et l’actualisation du réseau CDN. Nous avons mis à votre disposition un exemple d’implémentation dans [Chargement et préchargement CDN de contenu statique avec Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

**Contenu dynamique ou interne**

Pour les fichiers fréquemment écrits et lus par votre application (comme les fichiers de données temporaires) ou les fichiers statiques uniquement visibles pour votre application, vous pouvez monter des partages de [stockage Azure](https://docs.microsoft.com/azure/storage/) en tant que volumes persistants. Pour plus d’informations, consultez [Créer et utiliser un volume persistant de manière dynamique avec Azure Files dans Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv).

### <a name="determine-whether-a-connection-to-on-premises-is-needed"></a>Déterminer si une connexion à un service local est nécessaire

Si votre application doit accéder à vos services locaux, vous devez provisionner l’un des services de connectivité d’Azure. Pour plus d’informations, consultez [Connecter un réseau local à Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/). Vous avez également besoin de refactoriser votre application pour qu’elle utilise les API publiques disponibles que vos ressources locales exposent.

### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Déterminer si les files d’attente ou les rubriques JMS sont en cours d’utilisation

Si votre application utilise des files d’attente ou des rubriques JMS, vous devez les migrer vers un serveur JMS hébergé en externe. Azure Service Bus et le protocole AMQP (Advanced Message Queuing Protocol) peuvent être une excellente stratégie de migration pour ceux qui utilisent JMS. Pour plus d’informations, reportez-vous à [Utiliser JMS avec Azure Service Bus et AMQP 1.0](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp) ou à [Envoyer et recevoir des messages à partir de files d’attente Azure Service Bus (Java)](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-queues).

Si des magasins persistants JMS ont été configurés, vous devez capturer leur configuration et les appliquer après la migration.

### <a name="determine-whether-your-application-is-composed-of-multiple-wars"></a>Déterminer si votre application est composée de plusieurs WAR

Si votre application est composée de plusieurs WAR, vous devez traiter chacun de ces WAR comme des applications distinctes et suivre ce guide pour chacun d’entre eux.

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Déterminer si votre application est empaquetée en tant que fichier EAR

Si votre application est empaquetée en tant que fichier EAR, veillez à examiner le fichier application.xml et à capturer la configuration.

### <a name="identify-all-outside-processes-and-daemons-running-on-the-production-servers"></a>Identifier tous les processus et démons extérieurs exécutés sur les serveurs de production

Si vous avez des processus qui s’exécutent en dehors du serveur d’applications, comme les démons de supervision, vous devez les éliminer ou les migrer ailleurs.


## <a name="migration"></a>Migration

### <a name="provision-the-target-infrastructure"></a>Provisionner l’infrastructure cible

Pour démarrer la migration, vous devez d’abord déployer l’infrastructure JBoss EAP. Plusieurs options de déploiement s’offrent à vous :

- [**Machine virtuelle Azure**](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
- [**Groupe de machines virtuelles identiques Azure**](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
- [**Azure App Service**](https://aka.ms/jboss-app-service-overview)
- [**Azure Red Hat OpenShift (ARO) pour conteneurs**](https://azure.microsoft.com/services/openshift)
- [**Azure Container Service**](https://azure.microsoft.com/product-categories/containers/)

Reportez-vous à la section Bien démarrer avec la Place de marché Azure pour évaluer votre infrastructure de déploiement avant de créer l’environnement.

### <a name="perform-the-migration"></a>Effectuer la migration

Il existe des outils qui peuvent vous aider lors de la migration :

* [Kit de ressources de migration d’applications Red Hat pour analyser les applications pour la migration](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/migration_guide/index#use_windup_to_analyze_applications_for_migration)
* [Outil de migration de serveur JBoss pour migrer des configurations de serveur](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/migration_guide/index#migration_tool_server_migration_tool)

Pour migrer votre configuration de serveur à partir de la version antérieure de JBoss EAP vers la version plus récente de JBoss EAP, vous pouvez utiliser l’[outil de migration de serveur JBoss](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/migration_guide/index#migrate_server_migration_tool_option) ou vous pouvez effectuer une migration manuelle en vous aidant de l’[opération de migration de l’interface CLI de gestion](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/migration_guide/index#migrate__migrate_operation_option).

### <a name="run-red-hat-application-migration-toolkit"></a>Exécuter le kit de ressources de migration d’applications Red Hat

Vous pouvez [exécuter l’outil de migration de serveur JBoss en mode interactif](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_run_interactive_mode). Par défaut, l’outil de migration de serveur JBoss s’exécute de façon interactive. Ce mode vous permet de choisir exactement les configurations de serveur que vous voulez migrer.

Vous pouvez également [exécuter l’outil de migration de serveur JBoss en mode non interactif](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_run_noninteractive_mode). Ce mode permet à l’outil de s’exécuter sans invite.

### <a name="review-the-result-of-jboss-server-migration-toolkit-execution"></a>Examiner le résultat de l’exécution du kit de ressources de migration de serveur JBoss

Une fois la migration terminée, examinez les fichiers de configuration de serveur migrés dans les répertoires *EAP_HOME/standalone/configuration/* et *EAP_HOME/domain/configuration/* . Pour plus d’informations, consultez [Examen des résultats de l’exécution de l’outil de migration de serveur JBoss](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_results).

### <a name="expose-the-application"></a>Exposer l’application

Vous pouvez exposer l’application à l’aide des méthodes suivantes, qui sont adaptées à votre environnement.

* [Créez une IP publique](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address) pour accéder au serveur et à l’application.
* [Créez une machine virtuelle Jump dans le même réseau virtuel (VNet)](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine) dans un sous-réseau différent (nouveau sous-réseau) dans le même réseau virtuel, et accédez au serveur via une machine virtuelle Jump. Cette machine virtuelle Jump peut être utilisée pour exposer l’application.
* [Créez une machine virtuelle Jump avec peering de réseaux virtuels](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine) dans un autre réseau virtuel, accédez au serveur et exposez l’application à l’aide du [peering de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal#peer-virtual-networks).
* Exposez l’application à l’aide d’une [passerelle applicative](https://docs.microsoft.com/azure/application-gateway/quick-create-portal#create-an-application-gateway).
* Exposez l’application à l’aide d’un [équilibreur de charge externe](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal#create-a-standard-load-balancer) (ELB).

## <a name="post-migration"></a>Postmigration

Une fois que vous avez atteint les objectifs de migration que vous aviez définis dans l’étape de prémigration, effectuez des tests d’acceptation de bout en bout pour vérifier que tout fonctionne comme prévu. Vous trouverez ci-dessous des rubriques sur les améliorations de la postmigration, mais ce ne sont certainement pas les seules :

* Utilisation du stockage Azure pour traiter le contenu statique monté sur les machines virtuelles. Pour plus d’informations, consultez [Attacher ou détacher un disque de données sur une machine virtuelle](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-attach-detach-data-disk).
* Déployez vos applications sur votre cluster JBoss migré avec Azure DevOps. Pour plus d’informations, reportez-vous à la [documentation Bien démarrer avec Azure DevOps](https://docs.microsoft.com/azure/devops/get-started/?view=azure-devops).
* Envisagez d’utiliser [Application Gateway](https://docs.microsoft.com/azure/application-gateway/).
* Améliorer votre topologie réseau avec des services d’équilibrage de charge avancés. Pour plus d’informations, reportez-vous à [Utilisation des services d’équilibrage de charge dans Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure).
* Tirez parti des identités managées Azure pour gérer les secrets et attribuer un contrôle d’accès en fonction du rôle (RBAC) aux ressources Azure. Pour plus d’informations, reportez-vous à [Que sont les identités managées pour les ressources Azure ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
* Utiliser Azure Key Vault pour stocker toutes les informations qui servent de « secrets ». Pour plus d’informations, reportez-vous à [Concepts de base d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/basic-concepts).

## <a name="resource-links-and-support"></a>Liens vers les ressources et support

Pour toute question relative au support technique, aux problèmes ou aux exigences de personnalisation, contactez le [support technique Red Hat](https://access.redhat.com/support) ou le [support Microsoft Azure](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* En savoir plus sur [JBoss EAP](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/getting_started_with_jboss_eap_for_openshift_online/introduction)
* En savoir plus sur [Red Hat Subscription Manager (Cloud Access)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* En savoir plus sur [Machines virtuelles Azure](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
* En savoir plus sur les [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* En savoir plus sur [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* En savoir plus sur [Azure App Service sur Linux](https://docs.microsoft.com/azure/app-service/overview#app-service-on-linux)
* En savoir plus sur le [stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction)
* En savoir plus sur la [mise en réseau Azure](https://docs.microsoft.com/azure/networking/networking-overview)

## <a name="next-steps"></a>Étapes suivantes
* [Déployer JBoss EAP sur une machine virtuelle ou un groupe de machines virtuelles identiques RHEL à partir de la Place de marché Azure](https://aka.ms/AMP-JBoss-EAP)
* [Configuration d’une application Java pour Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* Tutoriel [Comment déployer JBoss EAP sur Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)
* [Utiliser l’Assistant Migration d’Azure App Service](https://azure.microsoft.com/services/app-service/migration-assistant/)
* [Utiliser Red Hat Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
