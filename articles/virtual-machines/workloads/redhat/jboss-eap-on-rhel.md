---
title: 'Démarrage rapide : Déployer JBoss EAP (Enterprise Application) on RHEL (Red Hat Enterprise Linux) sur un groupe de machines virtuelles identiques et une machine virtuelle Azure'
description: Découvrez comment déployer des applications Java d’entreprise à l’aide de Red Hat JBoss EAP sur un groupe de machines virtuelles identiques et une machine virtuelle RHEL Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134993"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Déployer des applications Java d’entreprise sur Azure avec JBoss EAP on Red Hat Enterprise Linux

Ces modèles de démarrage rapide vous expliquent comment déployer [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) avec [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) sur des groupes de machines virtuelles identiques et des machines virtuelles Azure. Vous disposez d’un exemple d’application Java dans le déploiement pour valider ce dernier. JBoss EAP est une plateforme de serveur d’applications open source. Il offre une sécurité, une scalabilité et des performances de qualité entreprise pour vos applications Java. RHEL est une plateforme de système d’exploitation open source. Il permet la mise à l’échelle des applications existantes et le déploiement des technologies émergentes dans tous les environnements. JBoss EAP et RHEL incluent tout ce qui est nécessaire pour générer, exécuter, déployer et gérer des applications Java d’entreprise dans n’importe quel environnement. Il s’agit d’une excellente solution open source pour les environnements virtuels et locaux ainsi que dans les clouds privés, publics ou hybrides.

## <a name="prerequisite"></a>Configuration requise 

* Compte Azure avec un abonnement actif. Pour obtenir un abonnement Azure, activez vos [crédits Azure pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [créez un compte gratuit](https://azure.microsoft.com/pricing/free-trial).

* Installation de JBoss EAP : vous devez disposer d’un compte Red Hat avec le droit RHSM (Red Hat Subscription Management) pour JBoss EAP. Ce droit vous permet de télécharger la version de JBoss EAP testée et certifiée pour Red Hat.  Si vous n’avez pas de droit EAP, obtenez un [abonnement d’évaluation JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) avant de commencer. Pour créer un abonnement Red Hat, accédez au [portail client Red Hat](https://access.redhat.com/) et configurez un compte.
F
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/overview).

* Options RHEL : choisissez entre le mode Paiement à l’utilisation (PAYG) ou Apportez votre propre abonnement (BYOS). Avec BYOS, vous devez activer votre image RHEL Gold [Red Hat Cloud Access](https://access.redhat.com/) avant de déployer le modèle de guide de démarrage rapide.

## <a name="java-ee--jakarata-ee-application-migration"></a>Migration d’applications Java EE/Jakarata EE

### <a name="migrate-to-jboss-eap"></a>Migrer vers JBoss EAP
JBoss EAP 7.2 et 7.3 sont des implémentations certifiées des spécifications de Java Enterprise Edition (Java EE) 8 et Jakarta EE 8. JBoss EAP fournit des options préconfigurées pour les fonctionnalités telles que le clustering haute disponibilité (HA), la messagerie et la mise en cache distribuée. Il permet également aux utilisateurs d’écrire, de déployer et d’exécuter des applications à l’aide des différents API et services fournis par JBoss EAP.  Pour plus d’informations sur JBoss EAP, consultez [Présentation de JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) ou [Présentation de JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Applications sur JBoss EAP

* Applications de services web : les services web offrent un moyen standard d’interopérer entre différentes applications logicielles. Chaque application peut s’exécuter sur différentes plateformes et frameworks. Ces services web facilitent la communication des sous-systèmes internes et hétérogènes. Pour plus d’informations, consultez [Développement d’applications de services web sur EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) ou [Développement d’applications de services web sur EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Applications Enterprise Java Beans (EJB) : EJB 3.2 est une API pour le développement d’applications Java EE et Jakarta EE distribuées, transactionnelles, sécurisées et portables. EJB utilise des composants côté serveur appelés beans d’entreprise pour implémenter la logique métier d’une application d’une manière découplée qui encourage la réutilisation. Pour plus d’informations, consultez [Développement d’applications EJB sur EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) ou [Développement d’applications EJB sur EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Applications Hibernate : les développeurs et les administrateurs peuvent développer et déployer des applications JPA (Java Persistence API)/Hibernate avec JBoss EAP. Hibernate Core est un framework de mappage objet-relationnel pour le langage Java. Il fournit un framework pour le mappage d’un modèle de domaine orienté objet à une base de données relationnelle, ce qui permet aux applications d’éviter une interaction directe avec la base de données. Le gestionnaire d’entités de Hibernate implémente les interfaces de programmation et les règles de cycle de vie définies par la [spécification JPA 2.1](https://www.jcp.org/en/jsr/overview). Avec Hibernate Annotations, ce wrapper implémente une solution JPA complète (et autonome) qui vient s’ajouter au composant Hibernate éprouvé. Pour en savoir plus sur Hibernate, consultez [JPA sur EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) ou [Jakarta Persistence sur EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Outil Red Hat MTA (Migration Toolkit for Applications)
[Red Hat MTA (Migration Toolkit for Applications)](https://developers.redhat.com/products/mta/overview) est un outil de migration pour les serveurs d’applications Java. Utilisez cet outil pour effectuer une migration depuis un autre serveur d’applications vers JBoss EAP. Il fonctionne avec les plug-ins IDE pour [IDE Eclipse](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) et [Visual Studio Code (VS Code)](https://code.visualstudio.com/docs/languages/java) pour Java.  MTA est un outil gratuit et open source qui effectue les opérations suivantes :
* Automatiser l’analyse des applications
* Prendre en charge l’estimation de l’effort
* Accélérer la migration de code
* Prendre en charge la conteneurisation
* S’intégrer au générateur de charge de travail Azure

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrer JBoss EAP depuis un site local vers Azure
L’offre de JBoss EAP on RHEL sur la Place de marché Azure s’installe et se provisionne sur des machines virtuelles Azure en moins de 20 minutes. Vous pouvez accéder à ces offres depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/).

Cette offre de la Place de marché comprend différentes combinaisons de versions EAP et RHEL pour répondre à vos exigences. JBoss EAP est toujours disponible en mode BYOS, mais pour le système d’exploitation RHEL, vous pouvez choisir entre les modes BYOS et PAYG. L’offre de la Place de marché Azure comprend des options de plan pour JBoss EAP on RHEL en tant que machines virtuelles autonomes ou mises en cluster :

* Machine virtuelle JBoss EAP 7.2 sur RHEL 7.7 (PAYG)
* Machine virtuelle JBoss EAP 7.2 sur RHEL 8.0 (PAYG)
* Machine virtuelle JBoss EAP 7.3 sur RHEL 8.0 (PAYG)
* Machine virtuelle JBoss EAP 7.2 sur RHEL 7.7 (BYOS)
* Machine virtuelle JBoss EAP 7.2 sur RHEL 8.0 (BYOS)
* Machine virtuelle JBoss EAP 7.3 sur RHEL 8.0 (BYOS)

Outre les offres de la Place de marché Azure, des modèles de guide de démarrage rapide sont mis à votre disposition pour vous aider à démarrer votre migration Azure. Ces guides de démarrage rapide incluent des modèles Azure Resource Manager (ARM) et un script prédéfinis qui facilitent le déploiement de JBoss EAP on RHEL dans différentes configurations et combinaisons de versions. Vous disposez des éléments suivants :

* Équilibreur de charge
* Adresse IP privée pour l’équilibrage de charge et les machines virtuelles
* Réseau virtuel (VNET) avec un seul sous-réseau
* Configuration des machines virtuelles (mode cluster ou autonome)
* Exemple d’application Java

L’architecture de la solution pour ces modèles comprend les éléments suivants :

* JBoss EAP sur machine virtuelle RHEL autonome
* JBoss EAP mis en cluster sur plusieurs machines virtuelles RHEL
* JBoss EAP mis en cluster à l’aide d’un groupe de machines virtuelles identiques Azure

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migration des charges de travail Linux pour JBoss EAP
Le générateur de charge de travail Azure simplifie le processus de preuve de concept (POC), d’évaluation et de migration pour les applications Java locales vers Azure. Le générateur de charge de travail s’intègre à l’outil de découverte Azure Migrate pour identifier les serveurs JBoss. Ensuite, il génère dynamiquement un playbook Ansible pour le déploiement des serveurs JBoss EAP. Il exploite l’outil Red Hat MTA pour migrer des serveurs depuis d’autres serveurs d’applications vers JBoss EAP. Les étapes de la simplification de la migration sont les suivantes :
* Évaluation : clusters JBoss EAP avec un groupe de machines virtuelles identiques ou une machine virtuelle Azure
* Examen : analyse les applications et l’infrastructure
* Configuration de l’infrastructure : crée un profil de charge de travail
* Déploiement et test : déployer, migrer et tester la charge de travail
* Configuration après le déploiement : s’intègre aux données, à la supervision, à la sécurité, à la sauvegarde, etc.

## <a name="server-configuration-choice"></a>Choix de la configuration du serveur

Pour le déploiement d’une machine virtuelle RHEL, vous pouvez choisir entre les modes PAYG et BYOS. Les images de la [Place de marché Azure](https://azuremarketplace.microsoft.com) utilisent par défaut le mode PAYG. Déployez une machine virtuelle RHEL de type BYOS si vous disposez de votre propre image de système d’exploitation RHEL. Avant de déployer les machines virtuelles ou le groupe de machines virtuelles identiques, assurez-vous que votre compte RHSM dispose d’un droit BYOS via Cloud Access.

JBoss EAP offre des fonctionnalités de gestion puissantes ainsi que des fonctionnalités et des API pour ses applications. Ces fonctionnalités de gestion varient selon le mode de fonctionnement utilisé pour démarrer JBoss EAP. Il est pris en charge sur RHEL et Windows Server. JBoss EAP offre un mode de fonctionnement de serveur autonome pour la gestion des instances discrètes. Il offre également un mode de fonctionnement de domaine managé pour gérer des groupes d’instances à partir d’un point de contrôle unique. Remarque : Les domaines managés par JBoss EAP ne sont pas pris en charge dans Microsoft Azure dans la mesure où la fonctionnalité de haute disponibilité (HA) est managée par les services d’infrastructure Azure. La variable d’environnement nommée *EAP_HOME* est utilisée pour indiquer le chemin de l’installation de JBoss EAP.

**Démarrer JBoss EAP en tant que serveur autonome**  : la commande suivante montre comment démarrer le service EAP en mode autonome.

```
$EAP_HOME/bin/standalone.sh
```
    
Ce script de démarrage utilise le fichier EAP_HOME/bin/standalone.conf pour définir certaines préférences par défaut, telles que les options JVM. Les paramètres peuvent être personnalisés dans ce fichier. JBoss EAP utilise le fichier de configuration standalone.xml pour démarrer en mode autonome par défaut, mais il peut être démarré à l’aide d’un autre mode. Pour plus d’informations sur les fichiers de configuration autonome disponibles et leur utilisation, consultez la section [Fichiers de configuration de serveur autonome pour EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) ou [Fichiers de configuration de serveur autonome pour EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). Pour démarrer JBoss EAP avec une configuration différente, utilisez l’argument --server-config. Par exemple :
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Pour obtenir la liste complète de tous les arguments de script de démarrage disponibles et leurs objectifs, utilisez l’argument --help ou consultez la section [Arguments d’exécution du serveur sur EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) ou [Arguments d’exécution du serveur sur EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP peut également fonctionner en mode cluster. Pour plus d’informations, consultez [Vue d’ensemble des clusters sur EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) ou [Vue d’ensemble des clusters sur EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). La messagerie de cluster JBoss EAP permet de regrouper les serveurs de messagerie JBoss EAP pour partager la charge de traitement des messages. Chaque nœud actif du cluster est un serveur de messagerie JBoss EAP actif qui gère ses propres messages et connexions.

## <a name="support-and-subscription-notes"></a>Remarques sur le support et les abonnements
Ces modèles de démarrage rapide sont proposés dans les conditions suivantes : 

- Le système d’exploitation RHEL est proposé en mode PAYG ou BYOS via le modèle d’image Red Hat Gold
- JBoss EAP est proposé en mode BYOS uniquement

#### <a name="using-rhel-os-with-payg-model"></a>Utilisation du système d’exploitation RHEL avec le modèle PAYG

Par défaut, ces modèles de démarrage rapide utilisent l’image PAYG RHEL 7.7 ou 8.0 à la demande à partir de la galerie Azure. Les images PAYG engendrent des frais d’abonnements RHEL horaires qui s’ajoutent aux coûts de calcul, de réseau et de stockage habituels. En même temps, l’instance est inscrite auprès de votre abonnement Red Hat. Cela signifie que vous utilisez un de vos droits. Cette image PAYG entraîne une « double facturation ». Vous pouvez éviter ce problème en générant votre propre image RHEL. Pour plus d’informations, consultez l’article de la base de connaissances Red Hat indiquant [comment provisionner une machine virtuelle RHEL pour Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Ou activez votre image RHEL Gold [Red Hat Cloud Access](https://access.redhat.com/).

Pour plus d’informations sur les tarifs des machines virtuelles en mode PAYG, consultez les prix de [Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Pour utiliser RHEL dans le modèle PAYG, vous avez besoin d’un abonnement Azure avec le mode de paiement spécifié pour l’[offre de la Place de marché Azure RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) ou l’[offre de la Place de marché Azure RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Ces offres nécessitent la spécification d’un mode de paiement dans l’abonnement Azure.

#### <a name="using-rhel-os-with-byos-model"></a>Utilisation du système d’exploitation RHEL avec le modèle BYOS

Afin d’utiliser le modèle BYOS pour le système d’exploitation RHEL, vous devez disposer d’un abonnement Red Hat valide avec les droits d’utilisation du système d’exploitation RHEL dans Azure. Avant de déployer ce modèle de démarrage rapide, effectuez les prérequis suivants :

1. Vérifiez que les droits JBoss EAP et de système d’exploitation RHEL sont associés à votre abonnement Red Hat.
2. Autorisez votre ID d’abonnement Azure à utiliser des images BYOS RHEL. Suivez la [documentation RHSM (Red Hat Subscription Management)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) pour effectuer ce processus, qui comprend les étapes suivantes :

    2.1 Activez Microsoft Azure en tant que fournisseur dans votre tableau de bord Red Hat Cloud Access.

    2.2 Ajoutez vos ID d’abonnement Azure.

    2.3 Activez de nouveaux produits pour Cloud Access sur Microsoft Azure.
    
    2.4 Activez les images Red Hat Gold pour votre abonnement Azure. Pour plus d’informations, lisez le chapitre sur [l’activation et la maintenance des abonnements pour Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

    2.5 Attendez que les images Red Hat Gold soient disponibles dans votre abonnement Azure. Ces images Gold sont généralement disponibles dans un délai de 3 heures.
    
3. Acceptez les conditions de la Place de marché Azure pour les images RHEL BYOS. Vous pouvez effectuer ce processus en exécutant des commandes depuis l’interface de ligne de commande Azure (CLI), comme indiqué plus bas. Pour plus d’informations, consultez la documentation sur les [images RHEL BYOS Gold dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos). Il est important que vous exécutiez la dernière version d’Azure CLI.

    3.1 Lancez une session Azure CLI et authentifiez-vous avec votre compte Azure. Pour obtenir de l’aide, reportez-vous à [Connexion avec Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Vérifiez que vous exécutez la dernière version d’Azure CLI avant de poursuivre.

    3.2 Vérifiez que les images RHEL BYOS sont disponibles dans votre abonnement en exécutant la commande CLI suivante. Si vous n’obtenez pas de résultats ici, reportez-vous au point 2 et vérifiez que votre abonnement Azure est activé pour les images RHEL BYOS.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 Exécutez la commande suivante afin d’accepter les conditions de la Place de marché pour l’offre RHEL 7.7 BYOS et l’offre RHEL 8.0 BYOS, respectivement.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Votre abonnement est maintenant prêt à déployer RHEL 7.7 ou 8.0 BYOS sur les machines virtuelles Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Utilisation de JBoss EAP avec le modèle BYOS

JBoss EAP est disponible sur Azure par le biais du modèle BYOS uniquement. Lors du déploiement de ce modèle, vous devez fournir vos informations d’identification RHSM ainsi que l’ID du pool RHSM avec des droits EAP valides. Si vous n’avez pas de droit EAP, obtenez un [abonnement d’évaluation JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) avant de commencer.

## <a name="how-to-consume"></a>Mode d’utilisation

Vous pouvez déployer le modèle des trois façons suivantes :

- Utiliser PowerShell - Déployez le modèle en exécutant les commandes suivantes : (Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).)

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Utiliser Azure CLI - Déployez le modèle en exécutant les commandes suivantes : (Pour plus d’informations sur l’installation et la configuration d’Azure CLI, consultez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Utiliser le portail Azure : vous pouvez effectuer le déploiement sur le portail Azure en accédant aux *modèles de démarrage rapide Azure* comme indiqué dans la section ci-dessous. Une fois que vous êtes dans le guide de démarrage rapide, cliquez sur le bouton **Déployer dans Azure** ou **Rechercher sur GitHub**.

## <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Azure

Vous pouvez commencer avec l’un des modèles de démarrage rapide de combinaison de JBoss EAP on RHEL qui répond à votre objectif de déploiement. Voici la liste des modèles de démarrage rapide disponibles.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (machine virtuelle autonome)</a> : ce modèle déploie une application web nommée JBoss-EAP sur Azure sur JBoss EAP 7.2 ou 7.3 exécuté sur une machine virtuelle RHEL 7.7 ou 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (plusieurs machines virtuelles mises en cluster)</a> : ce modèle déploie une application web appelée eap-session-replication sur un cluster JBoss EAP 7.2 ou 7.3 exécuté sur « n » machines virtuelles RHEL 7.7 ou 8.0. La valeur « n » est déterminée par l’utilisateur. Toutes les machines virtuelles sont ajoutées au pool de back-ends d’un équilibreur de charge.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (groupe de machines virtuelles identiques mis en cluster)</a> : ce modèle déploie une application web appelée eap-session-replication sur un cluster JBoss EAP 7.2 ou 7.3 exécuté sur des instances de groupe de machines virtuelles identiques RHEL 7.7 ou 8.0.

## <a name="resource-links"></a>Liens de ressources :

* [Avantages Azure Hybrid](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Configuration d’une application Java pour Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP sur Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* Démarrage rapide [JBoss EAP sur Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java)
* Tutoriel [Comment déployer JBoss EAP sur Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Étapes suivantes

* En découvrir plus [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* En découvrir plus [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* En découvrir plus la [gestion des abonnements Red Hat](https://access.redhat.com/products/red-hat-subscription-management)
* Documentation Microsoft pour [Red Hat sur Azure](https://aka.ms/rhel-docs)
* Déployer [JBoss EAP sur une machine virtuelle ou un groupe de machines virtuelles identiques RHEL à partir de la Place de marché Azure](https://aka.ms/AMP-JBoss-EAP)
* Déployer [JBoss EAP sur une machine virtuelle ou un groupe de machines virtuelles identiques RHEL à partir d’un guide de démarrage rapide Azure](https://aka.ms/Quickstart-JBoss-EAP)
