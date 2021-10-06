---
title: Déployer des appliances virtuelles Bitnami
description: Découvrez les appliances virtuelles empaquetées par Bitnami pour le déploiement dans votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700511"
---
# <a name="bitnami-appliance-deployment"></a>Déploiement d’appliance Bitnami

Bitnami de VMware fournit un riche catalogue d’appliances virtuelles clés en main. Vous pouvez déployer n’importe quelle appliance compatible vSphere de Bitnami disponible sur [VMware Marketplace](https://marketplace.cloud.vmware.com/), y compris un grand nombre des projets logiciels open source les plus courants.

Dans cet article, vous allez découvrir comment installer et configurer les appliances virtuelles suivantes empaquetées par Bitnami sur votre cloud privé Azure VMware Solution :

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>Prérequis

- Cloud privé Azure VMware Solution [déployé avec un minimum de trois nœuds](deploy-azure-vmware-solution.md)

- Réseau configuré comme décrit dans [Liste de vérification pour la planification réseau](tutorial-network-checklist.md)



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>Étape 1. Télécharger le fichier OVA/OVF d’appliance virtuelle Bitnami


1. Accédez à [VMware Marketplace](https://marketplace.cloud.vmware.com/) et téléchargez l’appliance virtuelle que vous souhaitez installer sur votre cloud privé Azure VMware Solution :

   - [Appliance virtuelle LAMP empaquetée par Bitnami](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. Sélectionnez la version, sélectionnez **Download**, puis acceptez la licence du CLUF. 

   >[!NOTE]
   >Vérifiez que le fichier est accessible à partir de la machine virtuelle.



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>Étape 2. Accéder au vCenter local de votre cloud privé

1. Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez votre cloud privé, puis **Gérer** > **Identité**.

1. Copiez l’URL, le nom d’utilisateur et le mot de passe du vCenter. Vous les utiliserez pour accéder à votre machine virtuelle. 

1. Sélectionnez **Vue d’ensemble**, sélectionnez la machine virtuelle, puis connectez-vous à elle par le biais du protocole RDP. Si vous avez besoin d’aide pour la connexion, consultez [Connectez-vous à la machine virtuelle](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) pour plus d’informations.

1. Dans la machine virtuelle, ouvrez un navigateur et accédez à l’URL du vCenter. 

1. Connectez-vous avec les informations d’identification utilisateur `cloudadmin@vsphere.local` que vous avez copiées.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Capture d’écran montrant la page de connexion VMware vSphere." border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>Étape 3. Installer le fichier OVA/OVF Bitnami dans le vCenter

1. Cliquez avec le bouton droit sur le cluster sur lequel vous souhaitez installer l’appliance virtuelle LAMP, et sélectionnez **Déployer le modèle OVF**.

1. Sélectionnez **Fichier local** et accédez au fichier OVF que vous avez téléchargé. Sélectionnez ensuite **Suivant**.

1. Sélectionnez votre centre de données et fournissez un nom pour votre machine virtuelle d’appliance virtuelle, par exemple **bitnami-lampstack**. Sélectionnez ensuite **Suivant**.

1. Sélectionnez l’hôte ESXi comme ressource de calcul pour exécuter votre machine virtuelle, puis sélectionnez **Suivant**.

1. Passez en revue les détails, puis sélectionnez **Suivant**.

1. Acceptez le contrat de licence, puis sélectionnez **Suivant**.

1. Sélectionnez le stockage de votre machine virtuelle, puis sélectionnez **Suivant**.

1. Sélectionnez le réseau de destination de votre machine virtuelle, puis sélectionnez **Suivant**.

1. Fournissez les informations requises pour personnaliser le modèle, telles que les propriétés réseau et de machine virtuelle. Sélectionnez ensuite **Suivant**.  

1. Passez en revue les paramètres de configuration, puis sélectionnez **Terminer**.

1. À partir de la **Console de tâches**, vérifiez que le déploiement du modèle OVF s’est correctement déroulé.

1. Une fois l’installation terminée, sous **Actions**, sélectionnez **Mise sous tension** pour activer l’appliance. 

1. À partir de la console vCenter, sélectionnez **Lancer la console web** et connectez-vous à l’appliance virtuelle Bitnami. Consultez la [documentation de support de l’appliance virtuelle Bitnami](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/) pour connaître le nom d’utilisateur et le mot de passe par défaut.

   >[!NOTE]
   >Vous pouvez remplacer le mot de passe par défaut par un mot de passe plus sécurisé. Pour plus d’informations, consultez ...



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>Étape 4. Affecter une adresse IP statique à l’appliance virtuelle

Lors de cette étape, vous allez modifier les paramètres *bootproto* et *onboot*, et affecter une adresse IP statique à l’appliance virtuelle Bitnami. 

1. Recherchez le fichier de configuration réseau. 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. Ouvrez le fichier *\/etc\/sysconfig\/network-scripts\/ifcfg-ens160* et modifiez les paramètres de démarrage. Ajoutez ensuite l’adresse IP statique, le masque réseau et l’adresse de passerelle.

   - bootproto=static

   - onboot=yes


1. Affichez et confirmez les modifications apportées au fichier **ifcfg-ens160**.

   ```bash
   cat ifcfg-ens160  
   ```

1. Redémarrez le service réseau. Cela arrête d’abord les services réseau, puis applique la configuration IP. 

   ```bash
   sudo systemctl restart network
   ```

1. Exécutez une commande ping sur l’adresse IP de la passerelle pour vérifier la configuration et la connectivité de la machine virtuelle au réseau.

1. Confirmez que la route par défaut 0.0.0.0 est listée.

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>Étape 5. Activer l’accès SSH à l’appliance virtuelle

Lors de cette étape, vous allez activer le protocole SSH sur votre appliance virtuelle pour le contrôle d’accès à distance. Le service SSH est désactivé par défaut. Vous allez également utiliser PuTTy pour vous connecter à la console hôte.

1. Activez et démarrez le service SSH.

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. Modifiez le fichier *\/etc\/ssh\/sshd_config* pour changer l’authentification par mot de passe.

   ```bash
   PasswordAuthentication yes
   ```

1. Affichez et confirmez les modifications apportées au fichier **sshd_config**.

   ```bash
   sudo cat sshd_config
   ```

1. Rechargez les modifications apportées au fichier. 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. Ouvrez PuTTy, sélectionnez l’option **SSH**, et indiquez le nom d’hôte et le port **22*. Sélectionnez ensuite **Ouvrir**. 

1. À l’invite de la console de l’appliance virtuelle, entrez le nom d’utilisateur et le mot de passe Bitnami pour vous connecter à l’hôte. 



