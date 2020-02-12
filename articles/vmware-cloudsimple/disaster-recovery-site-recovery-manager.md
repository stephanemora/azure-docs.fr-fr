---
title: Azure VMware Solutions (AVS) - Configurer le cloud privé AVS en tant que site de reprise d’activité avec VMware Site Recovery Manager
description: Explique comment configurer votre cloud privé AVS en tant que site de reprise d’activité pour les charges de travail VMware locales
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bbd8a12820432ce2f131dda29af6740a2f04e18
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024719"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurer le cloud privé AVS en tant que cible de reprise d’activité avec VMware Site Recovery Manager

Vous pouvez utiliser votre cloud privé AVS comme site de reprise d’activité pour les charges de travail VMware locales.

La solution de récupération d'urgence repose sur vSphere Replication et VMware Site Recovery Manager (SRM). Vous pouvez adopter une approche similaire pour activer votre cloud privé AVS en tant que site principal protégé par votre site de récupération local.

La solution AVS :

* Vous évite d’avoir à configurer un centre de données spécifiquement dédié à la reprise d’activité après sinistre.
* Vous permet de tirer parti des emplacements Azure où AVS est déployé pour une résilience géographique à l’échelle mondiale.
* Vous permet de réduire les coûts de déploiement et le coût total de possession avec la mise en place de la reprise d’activité après sinistre.

Pour tirer parti de la solution AVS, vous devez :

* Installer, configurer et gérer vSphere Replication et SRM dans votre cloud privé AVS.
* Fournir vos propres licences pour SRM lorsque le cloud privé AVS est le site protégé. Aucune licence SRM supplémentaire n’est requise pour le site AVS quand il est utilisé comme site de récupération.

Avec cette solution, vous disposez d'un contrôle total sur vSphere Replication et SRM. Les interfaces UI, API et CLI vous permettent d'utiliser vos scripts et outils existants.

![Déploiement de Site Recovery Manager](media/srm-deployment.png)

Toutes les versions de vRA et SRM compatibles avec votre cloud privé AVS et votre environnement local peuvent être utilisées. Les exemples fournis dans ce guide utilisent vRA 6.5 et SRM 6.5. Ces versions sont compatibles avec vSphere 6.5, qui est pris en charge par AVS.

## <a name="deploy-the-solution"></a>Déployer la solution

Les sections suivantes expliquent comment déployer une solution de reprise d’activité à l’aide de SRM dans votre cloud privé AVS.

1. [Vérifier que les versions des produits VMware sont compatibles](#verify-that-vmware-product-versions-are-compatible)
2. [Estimer la taille de votre environnement de récupération d'urgence](#estimate-the-size-of-your-dr-environment)
3. [Créer un cloud privé AVS pour votre environnement](#create-an-avs-private-cloud-for-your-environment)
4. [Configurer le réseau du cloud privé AVS pour la solution SRM](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Configurer une connexion VPN de site à site entre votre réseau local et le cloud privé AVS, et ouvrir les ports nécessaires](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Configurer des services d’infrastructure dans votre cloud privé AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Installer vSphere Replication Appliance dans votre environnement local](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installer vSphere Replication Appliance dans votre environnement de cloud privé AVS](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Installer un serveur SRM dans votre environnement local](#install-srm-server-in-your-on-premises-environment)
10. [Installer un serveur SRM dans votre cloud privé AVS](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Vérifier que les versions des produits VMware sont compatibles

Les configurations présentées dans ce guide sont soumises aux exigences de compatibilité suivantes :

* La même version de SRM doit être déployée sur votre cloud privé AVS et dans votre environnement local.
* La même version de vSphere Replication doit être déployée sur votre cloud privé AVS et dans votre environnement local.
* Les versions de Platform Services Controller (PSC) utilisées dans votre cloud privé AVS et dans votre environnement local doivent être compatibles.
* Les versions de vCenter utilisées votre cloud privé AVS et dans votre environnement local doivent être compatibles.
* Les versions de SRM et de vSphere Replication doivent être compatibles entre elles ainsi qu'avec les versions de PSC et de vCenter.

Pour obtenir des liens vers la documentation VMware et les informations de compatibilité pertinentes, accédez à la documentation de [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html).

Pour connaître les versions de vCenter et de PSC utilisées dans votre cloud privé AVS, ouvrez le portail AVS. Accédez à **Ressources**, sélectionnez votre cloud privé AVS et cliquez sur l’onglet **Réseau de gestion vSphere**.

![Versions de vCenter et de PSC dans le cloud privé AVS](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Estimer la taille de votre environnement de récupération d'urgence

1. Vérifiez que votre configuration locale se situe dans les limites prises en charge. Pour SRM 6.5, les limites sont décrites dans l'article [Limites opérationnelles pour Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110) de la base de connaissances VMware.
2. Vérifiez que vous disposez de la bande passante réseau suffisante pour satisfaire la taille de votre charge de travail et les exigences de votre objectif de point de récupération. L'article [Calcul des besoins en bande passante pour vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) de la base de connaissances VMware fournit des conseils sur les limites de bande passante.
3. Utilisez l’outil Sizer d’AVS pour estimer les ressources dont votre site de reprise d’activité a besoin pour protéger votre environnement local.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Créer un cloud privé AVS pour votre environnement

Créez un cloud privé AVS à partir du portail AVS en suivant les instructions et les recommandations de dimensionnement présentées dans [Créer un cloud privé AVS](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Configurer le réseau du cloud privé AVS pour la solution SRM

Accédez au portail AVS afin de configurer le réseau du cloud privé AVS pour la solution SRM.

Créez un réseau local virtuel (VLAN) pour le réseau de la solution SRM et attribuez-lui un CIDR de sous-réseau. Pour plus d'informations, consultez [Créer et gérer des réseaux VLAN/sous-réseaux](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Configurer une connexion VPN de site à site entre votre réseau local et le cloud privé AVS, et ouvrir les ports requis

Vous pouvez configurer la connectivité de site à site entre votre réseau local et votre cloud privé AVS. Pour obtenir des instructions, consultez [Configurer une connexion VPN à votre cloud privé AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Configurer des services d’infrastructure dans votre cloud privé AVS

Configurez des services d’infrastructure dans le cloud privé AVS afin de faciliter la gestion de vos charges de travail et outils.

Vous pouvez ajouter un fournisseur d’identité externe, comme décrit dans [Utiliser Azure AD comme fournisseur d’identité pour vCenter sur un cloud privé AVS](azure-ad.md), si vous souhaitez effectuer l’une des opérations suivantes :

* Identifier les utilisateurs de votre service Active Directory (AD) local dans votre cloud privé AVS.
* Configurer une instance AD dans votre cloud privé AVS pour tous les utilisateurs.
* Utiliser Azure AD

Pour fournir des services de recherche d’adresses IP, de gestion des adresses IP et de résolution de noms pour vos charges de travail dans le cloud privé AVS, configurez un serveur DNS et DHCP comme décrit dans [Configurer des applications et des charges de travail DNS et DHCP dans votre cloud privé AVS](dns-dhcp-setup.md).

Le domaine *.avs.io est utilisé par les machines virtuelles de gestion et les hôtes de votre cloud privé AVS. Pour résoudre les requêtes adressées à ce domaine, configurez le transfert DNS sur le serveur DNS, comme décrit à la section [Créer un redirecteur conditionnel](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installer vSphere Replication Appliance dans votre environnement local

Installez vSphere Replication Appliance (vRA) dans votre environnement local en suivant la documentation VMware. L'installation comprend les étapes suivantes :

1. Préparez votre environnement local pour l'installation de vRA.

2. Déployez vRA dans votre environnement local à l'aide de l'OVF de l'ISO VR de vmware.com. Pour vRA 6.5, [ce blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contient les informations pertinentes.

3. Inscrivez votre vRA local avec la fonctionnalité d'authentification unique de vCenter sur le site local. Pour obtenir des instructions détaillées sur vSphere Replication 6.5, consultez le document VMware [Installation et configuration de VMware vSphere Replication 6.5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Installer vSphere Replication Appliance dans votre environnement de cloud privé AVS

Avant de commencer, vérifiez que les conditions suivantes sont remplies :

* Accessibilité IP depuis les sous-réseaux de votre environnement local vers le sous-réseau de gestion de votre cloud privé AVS
* Accessibilité IP depuis le sous-réseau de réplication de votre environnement vSphere local vers le sous-réseau de la solution SRM de votre cloud privé AVS

Pour obtenir des instructions, consultez [Configurer une connexion VPN à votre cloud privé AVS](set-up-vpn.md). Les étapes sont semblables à celles de l'installation locale.

AVS recommande d’utiliser les noms de domaine complets au lieu des adresses IP lors de l’installation de vRA et de SRM. Pour connaître les noms de domaine complets de vCenter et de PSC utilisés dans votre cloud privé AVS, ouvrez le portail AVS. Accédez à **Ressources**, sélectionnez votre cloud privé AVS et cliquez sur l’onglet **Réseau de gestion vSphere**.

![Recherche du nom de domaine complet de vCenter/PSC dans un cloud privé AVS](media/srm-resources.png)

AVS vous demande de ne pas installer vRA et SRM avec l’utilisateur « cloudowner » par défaut. Vous devez donc créer un utilisateur. Ceci permet d’assurer une durée de fonctionnement et une disponibilité optimales pour votre environnement vCenter de cloud privé AVS. Or, l’utilisateur cloudowner par défaut de l’environnement vCenter de cloud privé AVS ne dispose pas de privilèges suffisants pour créer un utilisateur doté de privilèges administratifs.

Avant d'installer vRA et SRM, vous devez élever les privilèges vCenter de l'utilisateur cloudowner, puis créer un utilisateur doté de privilèges administratifs dans le domaine SSO de vCenter. Pour plus d’informations sur l’utilisateur et le modèle d’autorisation de cloud privé AVS par défaut, consultez [Découvrir le modèle d’autorisation de cloud privé AVS](learn-private-cloud-permissions.md).

L'installation comprend les étapes suivantes :

1. [Élevez les privilèges](escalate-private-cloud-privileges.md).
2. Créez un utilisateur dans votre cloud privé AVS pour l’installation de vSphere Replication et SRM. Cette procédure est décrite ci-dessous à la section [Interface utilisateur de vCenter : Créer un utilisateur dans le cloud privé AVS pour l’installation de vRA et de SRM](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Préparez votre environnement de cloud privé AVS pour l’installation de vRA.
4. Déployez vRA sur votre cloud privé AVS à l’aide de l’OVF de l’ISO VR de vmware.com. Pour vRA 6.5, [ce blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contient les informations pertinentes.
5. Configurez les règles de pare-feu pour vRA. Cette procédure est décrite ci-dessous à la section [Portail AVS : Configurer les règles de pare-feu pour vRA](#avs-portal-configure-firewall-rules-for-vra).
6. Inscrivez l’instance vRA du cloud privé AVS avec la fonctionnalité d’authentification unique de vCenter sur le site du cloud privé AVS.
7. Configurez les connexions vSphere Replication entre les deux appliances. Vérifiez que les ports requis sont ouverts sur les pare-feu. Reportez-vous à [cet article de la base de connaissances VMware](https://kb.vmware.com/s/article/2087769) afin d'obtenir la liste des numéros de port qui doivent être ouverts pour vSphere Replication 6.5.

Pour obtenir des instructions d'installation détaillées sur vSphere Replication 6.5, reportez-vous au document VMware [Installation et configuration de VMware vSphere Replication 6.5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>Interface utilisateur de vCenter : Créer un utilisateur dans le cloud privé AVS pour l’installation de vRA et de SRM

Connectez-vous à vCenter à l’aide des informations d’identification de l’utilisateur cloudowner après avoir réaffecté les privilèges à partir du portail AVS.

Créez un nouvel utilisateur, `srm-soln-admin`, dans vCenter et ajoutez-le au groupe Administrateurs dans vCenter.
Déconnectez-vous de vCenter en tant qu'utilisateur cloudowner et reconnectez-vous en tant qu'utilisateur *srm-soln-admin*.

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Portail AVS : Configurer les règles de pare-feu pour vRA

Configurez les règles de pare-feu comme décrit dans [Configurer des règles et tables de pare-feu](firewall.md) pour ouvrir les ports et permettre la communication entre :

* vRA sur le réseau de la solution SRM et les hôtes vCenter et ESXi sur le réseau de gestion
* les appliances vRA des deux sites

Reportez-vous à [cet article de la base de connaissances VMware](https://kb.vmware.com/s/article/2087769) afin d'obtenir la liste des numéros de port qui doivent être ouverts pour vSphere Replication 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installer un serveur SRM dans votre environnement local

Avant de commencer, vérifiez que les conditions suivantes sont remplies :

* vSphere Replication Appliance est installé dans vos environnements de cloud privé AVS et local.
* Les instances de vSphere Replication Appliance des deux sites sont connectées l'une à l'autre.
* Vous avez pris connaissance des conditions préalables et des meilleures pratiques relatives à VMware. Pour SRM 6.5, vous pouvez vous reporter au document VMware [Conditions préalables et meilleures pratiques pour SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Suivez la documentation VMware pour procéder à l'installation du serveur SRM dans le modèle de déploiement « Topologie à deux sites avec une instance de vCenter par instance de Platform Services Controller », comme décrit dans ce [document VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Les instructions d'installation de SRM 6.5 sont disponibles dans le document VMware [Installation de Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Installer un serveur SRM dans votre cloud privé AVS

Avant de commencer, vérifiez que les conditions suivantes sont remplies :

* vSphere Replication Appliance est installé dans vos environnements de cloud privé AVS et local.
* Les instances de vSphere Replication Appliance des deux sites sont connectées l'une à l'autre.
* Vous avez pris connaissance des conditions préalables et des meilleures pratiques relatives à VMware. Pour SRM 6.5, vous pouvez vous reporter à [Conditions préalables et meilleures pratiques pour l'installation du serveur Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Les étapes suivantes décrivent l’installation de SRM sur le cloud privé AVS.

1. [Interface utilisateur de vCenter : Installer SRM](#vcenter-ui-install-srm)
2. [Portail AVS : Configurer les règles de pare-feu pour SRM](#avs-portal-configure-firewall-rules-for-srm)
3. [Interface utilisateur de vCenter : Configurer SRM](#vcenter-ui-configure-srm)
4. [Portail AVS : Annuler la réaffectation des privilèges](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Interface utilisateur de vCenter : Installer SRM

Après vous être connecté à vCenter à l'aide des informations d'identification srm-soln-admin, suivez la documentation VMware pour procéder à l'installation du serveur SRM dans le modèle de déploiement « Topologie à deux sites avec une instance de vCenter par instance de Platform Services Controller », comme décrit dans ce [document VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Les instructions d'installation de SRM 6.5 sont disponibles dans le document VMware [Installation de Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Portail AVS : Configurer les règles de pare-feu pour SRM

Configurez les règles de pare-feu comme décrit dans [Configurer des règles et tables de pare-feu](firewall.md) afin de permettre la communication entre :

le serveur SRM et vCenter/PSC dans le cloud privé AVS
les serveurs SRM des deux sites

Reportez-vous à [cet article de la base de connaissances VMware](https://kb.vmware.com/s/article/2087769) afin d'obtenir la liste des numéros de port qui doivent être ouverts pour vSphere Replication 6.5.

#### <a name="vcenter-ui-configure-srm"></a>Interface utilisateur de vCenter : Configurer SRM

Une fois SRM installé dans le cloud privé AVS, effectuez les tâches suivantes, comme décrit dans les sections du Guide d’installation et de configuration de VMware Site Recovery Manager. Pour SRM 6.5, les instructions sont disponibles dans le document VMware [Installation de Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Connectez les instances du serveur Site Recovery Manager du site protégé et du site de récupération.
2. Établissez une connexion client avec l'instance distante du serveur Site Recovery Manager.
3. Installez la clé de licence de Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>Portail AVS : Annuler l’élévation des privilèges

Pour annuler l'élévation des privilèges, consultez [Annuler l'élévation des privilèges](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Gestion continue de votre solution SRM

Vous disposez d’un contrôle total sur les logiciels vSphere Replication et SRM de votre environnement de cloud privé AVS, et vous êtes tenu de procéder à la gestion nécessaire du cycle de vie de ceux-ci. Assurez-vous que toute nouvelle version des logiciels est compatible avec les instances vCenter et PSC du cloud privé AVS avant de mettre à jour ou à niveau vSphere Replication ou SRM.

> [!NOTE]
> AVS explore actuellement différentes options pour offrir un service de reprise d’activité géré. 

## <a name="multiple-replication-configuration"></a>Configuration de réplications multiples

 [Les technologies de réplication basée sur un groupe et de réplication vSphere peuvent toutes deux être utilisées simultanément avec SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication). En revanche, elles doivent être appliqués à un ensemble distinct de machines virtuelles (une machine virtuelle donnée peut être protégée soit par une réplication basée sur un groupe, soit par une réplication vSphere, mais pas les deux). De plus, le site AVS peut être configuré en tant que site de récupération pour plusieurs sites protégés. Pour plus d'informations sur les configurations multi-sites, consultez [Options multi-sites de SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/).

## <a name="references"></a>References

* [Documentation VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limites opérationnelles pour Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110)
* [Calcul des besoins en bande passante pour vSphere Replication](https://kb.vmware.com/s/article/2037268)
* [Options OVF lors du déploiement de vSphere Replication 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Installation et configuration de VMware vSphere Replication 6.5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Conditions préalables et meilleures pratiques pour SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager dans une topologie à deux sites avec une instance de vCenter par instance de Platform Services Controller](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Guide d'installation et de configuration de VMware Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware sur SRM avec réplication basée sur un groupe et réplication vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware sur les options multi-sites de SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Numéros de port qui doivent être ouverts pour vSphere Replication 5.8.x, 6.x et 8](https://kb.vmware.com/s/article/2147112)
