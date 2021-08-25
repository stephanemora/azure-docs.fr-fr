---
title: Offre de la Place de marché Azure pour Red Hat JBoss EAP sur machine virtuelle Azure Red Hat Enterprise Linux et groupes de machines virtuelles identiques
description: Comment déployer Red Hat JBoss EAP sur une machine virtuelle Azure Red Hat Enterprise Linux (RHEL) et des groupes de machines virtuelles identiques à l’aide de l’offre de la Place de marché Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 3c819367f35cb4a8174abaac1380eb439ace206a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769704"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>Déployer Red Hat JBoss Enterprise Application Platform (EAP) sur des machines virtuelles Azure et des groupes de machines virtuelles identiques à l’aide de l’offre de la Place de marché Azure

Les offres de la Place de marché Azure pour [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) sur Azure [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) constituent une solution conjointe de [Red Hat](https://www.redhat.com/) et Microsoft. Red Hat est l’un des principaux fournisseurs et contributeurs de solutions open source, y compris les normes [Java](https://www.java.com/), [OpenJDK](https://openjdk.java.net/), [MicroProfile](https://microprofile.io/), [Jakarta EE](https://jakarta.ee/) et [Quarkus](https://quarkus.io/). JBoss EAP est une plateforme de serveur d’applications Java leader, certifiée conforme aux normes Java EE et Jakarta EE, à la fois dans le profil web et la plateforme complète. Chaque version de JBoss EAP est testée et prise en charge sur divers systèmes d’exploitation, machines virtuelles Java (JVM) et combinaisons de bases de données, leaders du marché.  JBoss EAP et RHEL incluent tout ce dont vous avez besoin pour générer, exécuter, déployer et gérer des applications Java d’entreprise dans n’importe quel environnement.  Cela inclut des environnements virtuels et locaux, ainsi que des clouds privés, publics ou hybrides. La solution conjointe de Red Hat et Microsoft prend en charge la flexibilité des licences logicielles et de support intégrées.

## <a name="jboss-eap-on-azure-integrated-support"></a>Prise en charge intégrée de JBoss EAP sur Azure

L’offre de la place de marché JBoss EAP sur Azure est une solution conjointe de Red Hat et Microsoft qui inclut la flexibilité des licences logicielles et de support intégrées. Vous pouvez contacter Microsoft ou Red Hat pour soumettre vos tickets de support.  Nous partagerons et résoudrons les problèmes conjointement pour que vous n’ayez pas à soumettre plusieurs tickets pour chaque fournisseur. Le support intégré couvre l’ensemble des problèmes de prise en charge de l’infrastructure Azure et du serveur d’applications Red Hat.    

## <a name="prerequisites"></a>Prérequis

* Un compte Azure avec un abonnement actif – Si vous n’avez pas d’abonnement Azure, vous pouvez activer vos [avantages d’abonné Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (anciennement MSDN) ou [créer un compte gratuitement](https://azure.microsoft.com/pricing/free-trial).

* Installation de JBoss EAP : vous devez disposer d’un compte Red Hat avec le droit RHSM (Red Hat Subscription Management) pour JBoss EAP. Ce droit vous permet de télécharger la version de JBoss EAP testée et certifiée par Red Hat.  Si vous ne disposez pas du droit EAP, inscrivez-vous pour bénéficier d’un abonnement développeur gratuit via l’[abonnement Red Hat Developer pour les particuliers](https://developers.redhat.com/register). Une fois inscrit, vous pouvez trouver les informations d’identification nécessaires (ID de pool) sur le [portail client Red Hat](https://access.redhat.com/management/).

* Options RHEL : choisissez entre le mode Paiement à l’utilisation (PAYG) ou Apportez votre propre abonnement (BYOS). Avec BYOS, vous devez activer votre [image RHEL Gold](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) de [Red Hat Cloud Access](https://access.redhat.com/) avant de déployer l’offre de la place de marché avec un modèle de solutions. Suivez [ces instructions](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) pour activer les images RHEL Gold à utiliser dans Microsoft Azure.

* Interface de ligne de commande Azure (CLI)

## <a name="azure-marketplace-offer-subscription-options"></a>Options d’abonnement à l’offre de la Place de marché Azure

L’offre de la Place de marché Azure concernant JBoss EAP sur RHEL installe et provisionne un déploiement de machines virtuelles Azure ou de groupes de machines virtuelles identiques en moins de 10 minutes. Vous pouvez accéder à ces offres sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/).

Cette offre de la Place de marché comprend différentes combinaisons de versions EAP et RHEL pour répondre à vos exigences. JBoss EAP est toujours disponible en mode BYOS, mais pour le système d’exploitation RHEL, vous pouvez choisir entre les modes BYOS et PAYG. L’offre comprend des options de configuration de plan pour JBoss EAP sur RHEL en tant que machines virtuelles autonomes, machines virtuelles clusterisées ou groupes de machines virtuelles identiques clusterisés. 

Les six plans disponibles sont les suivants : 

- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Machine virtuelle en cluster JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle clusterisée JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Groupe de machines virtuelles identiques clusterisé JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Groupe de machines virtuelles identiques clusterisé JBoss EAP 7.3 sur RHEL 8.3 (BYOS)

## <a name="using-rhel-os-with-payg-model"></a>Utilisation du système d’exploitation RHEL avec le modèle PAYG

L’offre de la Place de marché Azure vous permet de déployer RHEL en tant que groupes de machines virtuelles identiques/machines virtuelles PAYG à la demande. Les plans PAYG engendrent des frais d’abonnement RHEL horaires qui s’ajoutent aux coûts habituels de calcul, de réseau et de stockage de l’infrastructure Azure.

Pour plus d’informations sur le modèle PAYG, consultez [Tarification de Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Pour utiliser RHEL dans le modèle PAYG, vous avez besoin d’un abonnement Azure. (Les offres [RHEL de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605) nécessitent la spécification d’un mode de paiement dans l’abonnement Azure).

## <a name="using-rhel-os-with-byos-model"></a>Utilisation du modèle Système d’exploitation RHEL avec BYOS

Pour utiliser des machines virtuelles/groupes de machines virtuelles identiques RHEL en tant que BYOS, vous devez disposer d’un abonnement Red Hat valide avec les droits d’utilisation de RHEL dans Azure. Ces plans BYOS JBoss EAP sur RHEL sont disponibles sous forme d’[offres privées Azure](../../../marketplace/private-offers.md). Vous DEVEZ remplir les conditions préalables suivantes pour déployer un plan d’offre RHEL BYOS à partir de la Place de marché Azure. 

1. Vérifiez que les droits JBoss EAP et de système d’exploitation RHEL sont associés à votre abonnement Red Hat.
2. Autorisez votre ID d’abonnement Azure à utiliser des images BYOS RHEL. Suivez la [documentation RHSM (Red Hat Subscription Management)](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) pour effectuer ce processus, qui comprend les étapes suivantes :
    1. Activez Microsoft Azure en tant que fournisseur dans votre tableau de bord Red Hat Cloud Access.
    2. Ajoutez vos ID d’abonnement Azure.
    3. Activez de nouveaux produits pour Cloud Access sur Microsoft Azure.
    4. Activez les images Red Hat Gold pour votre abonnement Azure. Pour plus d’informations, lisez le chapitre sur [l’activation et la maintenance des abonnements pour Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access). 
    5. Attendez que les images Red Hat Gold soient disponibles dans votre abonnement Azure. Ces images Gold sont généralement disponibles dans un délai de 3 heures après l’envoi ou moins en tant qu’offres privées Azure.

3. Acceptez les conditions générales de la Place de marché Azure pour les images RHEL BYOS. Pour accepter, exécutez les commandes de l’[interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli), comme indiqué ci-dessous. Pour plus d’informations, consultez la documentation sur les [images RHEL BYOS Gold dans Azure](./byos.md). Il est important que vous exécutiez la dernière version d’Azure CLI.
    1. Lancez une session Azure CLI et authentifiez-vous avec votre compte Azure. Pour obtenir de l’aide, reportez-vous à [Connexion avec Azure CLI](/cli/azure/authenticate-azure-cli). Vérifiez que vous exécutez la dernière version d’Azure CLI avant de poursuivre.
    2. Vérifiez que les plans RHEL BYOS sont disponibles dans votre abonnement en exécutant la commande CLI suivante. Si vous n’y trouvez pas de résultats, reportez-vous à l’étape no 2. Assurez-vous que votre abonnement Azure est activé avec le droit d’utiliser les plans JBoss EAP sur RHEL BYOS.

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. Exécutez la commande suivante pour accepter les conditions générales de la Place de marché Azure comme cela est nécessaire pour le plan JBoss EAP sur RHEL BYOS.

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. Où `$PLANID` est l’un des éléments suivants (répétez l’étape no 3 pour chaque plan d’offre de la place de marché que vous souhaitez utiliser) :

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. Votre abonnement est maintenant prêt pour déployer les plans EAP sur RHEL BYOS. Au cours du déploiement, votre ou vos abonnements sont automatiquement attachés à l’aide de `subscription-manager` avec les informations d’identification fournies pendant le déploiement.

## <a name="using-jboss-eap-with-byos-model"></a>Utilisation JBoss EAP avec le modèle BYOS

JBoss EAP est disponible sur Azure via le modèle BYOS uniquement. Lors du déploiement de votre plan JBoss EAP sur RHEL, vous devez fournir vos informations d’identification RHSM ainsi que l’ID de pool RHSM avec des droits EAP valides. Si vous ne disposez pas de droits EAP, procurez-vous un [abonnement Red Hat Developer pour les particuliers](https://developers.redhat.com/register). Une fois inscrit, vous pouvez trouver les informations d’identification nécessaires (ID de pool) dans le menu de navigation Subscription (Abonnement). Au cours du déploiement, votre ou vos abonnements sont automatiquement attachés à l’aide de `subscription-manager` avec les informations d’identification fournies pendant le déploiement.

## <a name="template-solution-architectures"></a>Architectures de la solution de modèle

Ces plans d’offre créent toutes les ressources de calcul Azure requises pour exécuter l’installation JBoss EAP sur RHEL. Les ressources suivantes sont créées par la solution de modèle :

- Machine virtuelle RHEL
- Équilibreur de charge unique (pour la configuration clusterisée)
- Réseau virtuel avec un seul sous-réseau
- Installation JBoss EAP sur une machine virtuelle RHEL/des groupes de machines virtuelles identiques (autonomes ou clusterisés)
- Exemple d’application Java
- Compte de stockage

## <a name="after-a-successful-deployment"></a>Après un déploiement réussi

1. [Créez une machine virtuelle Jump avec peering de réseaux virtuels](../../windows/quick-create-portal.md#create-virtual-machine) dans un autre réseau virtuel, accédez au serveur et exposez l’application à l’aide du [peering de réseaux virtuels](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks).
2. [Créez une IP publique](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) pour accéder au serveur et à l’application.
3. [Créez une machine virtuelle Jump dans le même réseau virtuel (VNet)](../../windows/quick-create-portal.md#create-virtual-machine) dans un sous-réseau différent (nouveau sous-réseau) dans le même réseau virtuel, et accédez au serveur via une machine virtuelle Jump. La machine virtuelle Jump peut être utilisée pour exposer l’application.
4. Exposez l’application à l’aide d’une [passerelle applicative](../../../application-gateway/quick-create-portal.md#create-an-application-gateway).
5. Exposez l’application à l’aide d’un équilibreur de charge externe (ELB).
6. [Utilisez Azure Bastion](../../../bastion/bastion-overview.md) pour accéder à vos machines virtuelles RHEL à l’aide de votre navigateur et du portail Azure. 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. Créer une machine virtuelle Jump dans un autre réseau virtuel et accéder à la machine virtuelle RHEL à l’aide du peering de réseaux virtuels (méthode recommandée)

1. [Créez une machine virtuelle Windows](../../windows/quick-create-portal.md#create-virtual-machine) – Dans un nouveau groupe de ressources Azure, créez une machine virtuelle Windows, qui DOIT figurer dans la même région que la machine virtuelle RHEL. Fournissez les détails requis et laissez les autres configurations par défaut, car cela créera la machine virtuelle Jump dans un nouveau réseau virtuel.
2. [Appairez les réseaux virtuels](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) –: Le peering correspond à la manière dont vous associez la machine virtuelle RHEL à la machine virtuelle Jump. Une fois le peering des réseaux virtuels réussi, les deux machines virtuelles peuvent communiquer entre elles.
3. Accédez à la page de détails de la machine virtuelle Jump et copiez l’IP publique. Connectez-vous à la machine virtuelle Jump à l’aide de l’IP publique.
4. Copiez l’IP privée de la machine virtuelle RHEL à partir de la page de sortie et utilisez-la pour vous connecter à la machine virtuelle RHEL à partir de la machine virtuelle Jump.
5. Collez l’URL de l’application que vous avez copiée à partir de la page de sortie dans un navigateur au sein de la machine virtuelle Jump. Affichez la page web JBoss EAP sur Azure à partir de ce navigateur.
6. Accédez à la console d’administration JBoss EAP – Collez l’URL de la console d’administration copiée dans la page de sortie dans un navigateur sur la machine virtuelle Jump, puis entrez le nom d’utilisateur et le mot de passe JBoss EAP pour vous connecter.

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2. Créer une IP publique pour accéder à la machine virtuelle RHEL et à la console d’administration JBoss EAP

1. Aucune IP publique n’est associée à la machine virtuelle RHEL que vous avez créée. Vous pouvez [créer une IP publique](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) pour accéder à la machine virtuelle et [associer l’IP publique à la machine virtuelle](../../../virtual-network/associate-public-ip-address-vm.md). La création d’une IP publique peut être effectuée à l’aide des commandes du portail Azure ou d’[Azure PowerShell](/powershell/) ou des commandes [Azure CLI](/cli/azure/install-azure-cli).
2. Obtenez l’IP publique d’une machine virtuelle – Accédez à la page de détails de la machine virtuelle et copiez l’IP publique. Utilisez l’IP publique pour accéder à la machine virtuelle et à la console d’administration JBoss EAP.
3. Consultez la page web JBoss EAP sur Azure – Ouvrez un navigateur web et accédez à *http://<PUBLIC_HOSTNAME>:8080/* pour voir la page d’accueil EAP par défaut.
4. Connectez-vous à la console d’administration JBoss EAP – Ouvrez un navigateur web et accédez à *http://<PUBLIC_HOSTNAME>:9990*. Entrez le nom d’utilisateur et le mot de passe JBoss EAP pour vous connecter.

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. Créer une machine virtuelle Jump dans un sous-réseau différent (nouveau sous-réseau) dans le même réseau virtuel et accéder à la machine virtuelle RHEL via une machine virtuelle Jump

1. [Ajoutez un nouveau sous-réseau](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) dans le réseau virtuel existant, qui contient la machine virtuelle RHEL.
2. [Créez une machine virtuelle Windows](../../windows/quick-create-portal.md#create-virtual-machine) dans Azure dans le même groupe de ressources que la machine virtuelle RHEL. Fournissez les informations requises et laissez les autres configurations par défaut, à l’exception du réseau virtuel et du sous-réseau. Veillez à sélectionner le réseau virtuel existant dans le groupe de ressources et sélectionnez le sous-réseau que vous avez créé à l’étape ci-dessus, car ce sera votre machine virtuelle Jump.
3. Accédez à l’IP publique de la machine virtuelle Jump – Une fois le déploiement réussi, accédez à la page de détails de la machine virtuelle et copiez l’IP publique. Connectez-vous à la machine virtuelle Jump à l’aide de l’IP publique.
4. Connectez-vous à la machine virtuelle RHEL – Copiez l’IP privée de la machine virtuelle RHEL dans la page de sortie et utilisez-la pour vous connecter à la machine virtuelle RHEL à partir de la machine virtuelle Jump.
5. Accédez à la page d’accueil de JBoss EAP – Dans votre machine virtuelle Jump, ouvrez un navigateur et collez l’URL de l’application que vous avez copiée dans la page de sortie du déploiement.
6. Accédez à la console d’administration JBoss EAP – Collez l’URL de la console d’administration que vous avez copiée dans la page de sortie dans un navigateur sur la machine virtuelle Jump pour accéder à la console d’administration JBoss EAP, puis entrez le nom d’utilisateur et le mot de passe JBoss EAP pour vous connecter.

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. Exposer l’application à l’aide d’un équilibreur de charge externe

1. [Créez une passerelle applicative](../../../application-gateway/quick-create-portal.md#create-an-application-gateway) – Pour accéder aux ports de la machine virtuelle RHEL, créez une passerelle applicative dans un sous-réseau différent. Ce sous-réseau doit contenir uniquement la passerelle applicative.
2. Définissez les paramètres des *front-ends* – Veillez à sélectionner l’IP publique ou les deux et fournissez les détails requis. Sous la section *Backends*, sélectionnez **Ajouter un pool de backends** et ajoutez la machine virtuelle RHEL au pool de backends de la passerelle applicative.
3. Définissez les ports d’accès – Sous la section *Configuration*, ajoutez des règles de routage pour accéder aux ports 8080 et 9990 de la machine virtuelle RHEL.
4. Copiez l’IP publique de la passerelle applicative – Une fois la passerelle applicative créée avec les configurations requises, accédez à la page de présentation et copiez l’IP publique de la passerelle applicative.
5. Consultez la page web JBoss EAP sur Azure – Ouvrez un navigateur web et accédez à *http://<PUBLIC_IP_AppGateway>:8080* pour voir la page d’accueil EAP par défaut.
6. Connectez-vous à la console d’administration JBoss EAP – Ouvrez un navigateur web et accédez à *http://<PUBLIC_IP_AppGateway>:9990*. Entrez le nom d’utilisateur et le mot de passe JBoss EAP pour vous connecter.

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. Utiliser un équilibreur de charge externe (ELB) pour accéder à votre machine virtuelle RHEL/vos groupes de machines virtuelles identiques

1. [Créez un équilibreur de charge](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) pour accéder aux ports de la machine virtuelle RHEL. Fournissez les détails requis pour déployer l’équilibreur de charge externe et laissez les autres configurations par défaut. Laissez la référence SKU de base pour la configuration ELB.
2. Ajoutez des règles d’équilibreur de charge – Une fois l’équilibreur de charge créé avec succès, [créez des ressources d’équilibreur de charge](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources), puis ajoutez des règles d’équilibreur de charge pour accéder aux ports 8080 et 9990 de la machine virtuelle RHEL.
3. Ajoutez la machine virtuelle RHEL au pool de backends de l’équilibreur de charge – Cliquez sur *Pools de backends* sous la section des paramètres, puis sélectionnez le pool de backends que vous avez créé à l’étape ci-dessus. Sélectionnez la machine virtuelle correspondant à l’option *Associé à*, puis ajoutez la machine virtuelle RHEL.
4. Obtenez l’IP publique de l’équilibreur de charge – Accédez à la page de présentation de l’équilibreur de charge et copiez l’IP publique de l’équilibreur de charge.
5. Consultez la page web JBoss EAP sur Azure – Ouvrez un navigateur web et accédez à *http://<PUBLIC_IP_LoadBalancer>:8080/* pour voir la page d’accueil EAP par défaut.
6. Connectez-vous à la console d’administration JBoss EAP – Ouvrez un navigateur web et accédez à *http://<PUBLIC_IP_LoadBalancer>:9990*. Entrez le nom d’utilisateur et le mot de passe JBoss EAP pour vous connecter.

### <a name="6-use-azure-bastion-to-connect"></a>6. Utiliser Azure Bastion pour vous connecter 

1. Créez un hôte Azure Bastion pour votre réseau virtuel où figure votre machine virtuelle RHEL. Le service Bastion se connecte automatiquement à votre machine virtuelle RHEL à l’aide de SSH.
2. Créez vos rôles de lecteur sur la machine virtuelle, la carte réseau avec IP privée de la machine virtuelle et la ressource Azure Bastion.
3. Le port entrant RHEL est ouvert pour SSH (22). 
4. Connectez-vous à l’aide de vos nom d’utilisateur et mot de passe dans le portail Azure. Cliquez sur « Se connecter » et sélectionnez « Bastion » dans la liste déroulante. Sélectionnez ensuite « Se connecter » pour vous connecter à votre machine virtuelle RHEL. Vous pouvez vous connecter à l’aide d’un fichier de clé privée ou d’une clé privée stockée dans Azure Key Vault.

## <a name="azure-platform"></a>Plateforme Azure

- **Échec de validation** – Votre déploiement ne démarrera pas si les critères de paramètre ne sont pas remplis (par exemple, les critères de mot de passe d’administrateur ne sont pas satisfaits) ou si des paramètres obligatoires ne sont pas fournis dans la section des paramètres. Vous pouvez examiner les détails des paramètres avant de cliquer sur *Créer*.
- **Échec de provisionnement des ressources** – Une fois le déploiement démarré, les ressources déployées sont visibles dans la page de déploiement. En cas d’échec du déploiement après le processus de validation des paramètres, un message d’échec plus détaillé est disponible. 
- **Échec de provisionnement de machine virtuelle** – Si votre déploiement échoue au point de ressource de l’**extension de script personnalisé de machine virtuelle**, un message d’erreur plus détaillé est disponible dans le fichier journal de la machine virtuelle. Consultez la section suivante pour poursuivre la résolution des problèmes.

## <a name="troubleshooting-eap-deployment-extension"></a>Dépannage de l’extension de déploiement EAP

Ces offres utilisent des extensions de script personnalisé de machine virtuelle pour déployer JBoss EAP et configurer l’utilisateur de gestion JBoss EAP. Votre déploiement peut échouer ici pour plusieurs raisons, par exemple :

- Droit RHSM ou EAP non valide
- ID de pool des droits JBoss EAP ou du système d’exploitation RHEL non valide
- Conditions générales de la Place de marché Azure non acceptées pour les machines virtuelles RHEL

Suivez les étapes ci-dessous pour poursuivre la résolution du problème :

1. Connectez-vous à la machine virtuelle provisionnée via SSH. Vous pouvez récupérer l’IP publique de la machine virtuelle à partir de la page de présentation de la machine virtuelle dans le portail Azure.
1. Basculez vers l’utilisateur racine.

    ```cli
    sudo su -
    ```

1. Entrez le mot de passe d’administrateur de la machine virtuelle si vous y êtes invité.
1. Accédez au répertoire de journalisation.

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. Examinez les journaux dans le fichier journal eap.log.

    ```cli
    more eap.log
    ```

Le fichier journal contient des détails indiquant la raison de l’échec du déploiement et les solutions possibles. Si votre déploiement a échoué en raison d’un compte ou de droits RHSM, reportez-vous à la section « Remarques sur le support et les abonnements » pour vérifier les conditions préalables, puis réessayez. Lors du déploiement d’un plan clusterisé EAP sur RHEL, assurez-vous que le déploiement ne dépasse pas la limite de quota. Il est important de vérifier vos quotas de processeurs virtuels régionaux et de processeurs virtuels de la série de machines virtuelles avant de fournir le nombre d’instances pour le déploiement. Si votre abonnement ou votre région n’a pas une limite de quota suffisante, [demandez un quota](../../../azure-portal/supportability/regional-quota-requests.md) à partir de votre portail Azure.

Pour plus d’informations sur la résolution des problèmes liés aux extensions de script personnalisé de machine virtuelle, consultez [Utilisation de l’extension de script personnalisé Azure version 2 avec des machines virtuelles Linux](../../extensions/custom-script-linux.md).

## <a name="resource-links-and-support"></a>Liens vers les ressources et support

Pour toute question relative au support technique, aux problèmes ou aux exigences de personnalisation, contactez le [support technique Red Hat](https://access.redhat.com/support) ou le [support Microsoft Azure](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* En savoir plus sur [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/)
* [JBoss EAP sur Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP dans Azure App Service](/azure/developer/java/ee/jboss-on-azure) 
* [Avantages Azure Hybrid](../../windows/hybrid-use-benefit-licensing.md)
* [Gestion des abonnements Red Hat](https://access.redhat.com/products/red-hat-subscription-management)
* [Documentation Microsoft pour Red Hat sur Azure](./overview.md)

## <a name="next-steps"></a>Étapes suivantes

* Déployer une machine virtuelle ou des groupes de machines virtuelles identiques JBoss EAP sur RHEL à partir de la [Place de marché Azure](https://aka.ms/AMP-JBoss-EAP)
* Déployer une machine virtuelle ou des groupes de machines virtuelles identiques JBoss EAP sur RHEL à partir d’un [modèle de démarrage rapide Azure](https://aka.ms/Quickstart-JBoss-EAP)
* Configuration d’une application Java pour [Azure App Service](../../../app-service/configure-language-java.md)
* Tutoriel [Comment déployer JBoss EAP sur Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)
* Utiliser l’[Assistant Migration d’Azure App Service](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Utiliser Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
