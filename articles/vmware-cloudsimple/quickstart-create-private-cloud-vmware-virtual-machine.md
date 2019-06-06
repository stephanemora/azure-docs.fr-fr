---
title: Guide de démarrage rapide - créer une machine virtuelle VMware sur un Cloud privé
description: Décrit comment créer et une VM VMware sur le Cloud privé de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5837fd615b8dbf32ff289a2e0d09da9db51a908d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481503"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Créer des machines virtuelles VMware sur votre Cloud privé

Pour créer des machines virtuelles sur votre Cloud privé, commencez par accéder au portail CloudSimple à partir du portail Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accéder au portail CloudSimple

1. Sélectionnez **Tous les services**.
2. Recherchez **CloudSimple Services**.
3. Sélectionnez le service CloudSimple sur lequel vous souhaitez créer votre Cloud privé.
4. À partir de la **vue d’ensemble** , cliquez sur **accéder au portail CloudSimple** pour ouvrir un nouvel onglet de navigateur pour CloudSimple portail.  Si vous y êtes invité, connectez-vous avec votre Azure connectez-vous aux informations d’identification.  

    ![Lancez CloudSimple portail](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Lancer l’interface utilisateur web de vCenter

Vous pouvez maintenant lancer vCenter pour configurer des stratégies et des machines virtuelles.

Pour accéder à vCenter, démarrez à partir du portail CloudSimple. Sur la page d’accueil, sous **tâches courantes**, cliquez sur **lancer le Client vSphere**.  Sélectionnez le Cloud privé, puis cliquez **lancer le Client vSphere** sur le Cloud privé.

   ![Lancer le Client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Télécharger un modèle ISO ou vSphere

> [!WARNING]
> Pour les téléchargements ISO, utilisez le client vSphere HTML5.  L’utilisation de Flash client peut entraîner une erreur.

1. Obtenir le modèle ISO ou vSphere que vous souhaitez charger dans vCenter pour créer une machine virtuelle et qu’il disponibles sur votre système local.

2. Dans vCenter, cliquez sur le **disque** icône, sélectionnez **vsanDatastore**. Cliquez sur **fichiers** puis cliquez sur **nouveau dossier**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Créez un dossier pour stocker les fichiers ISO.

4. Accédez au nouveau dossier créé, puis cliquez sur **charger des fichiers**. Suivez l’à l’écran instructions pour télécharger l’image ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Créer une Machine virtuelle dans vCenter

1. Dans vCenter, cliquez sur le **hôtes et Clusters** icône.

2. Avec le bouton droit **charge de travail** et sélectionnez **nouvelle Machine virtuelle**.
    
    ![Créer une machine virtuelle](media/create-vcenter-virtual-machine-01.png)

3. Sélectionnez **créer machine virtuelle** et cliquez sur **suivant**.

    ![Assistant Nouvel ordinateur virtuel](media/create-vcenter-virtual-machine-02.png)

4. Nom de la machine, sélectionnez le **de machine virtuelle de la charge de travail** dossier, puis cliquez sur **suivant**.

    ![Sélectionnez le nom et le dossier](media/create-vcenter-virtual-machine-03.png)

5. Sélectionnez le **charge de travail** ressource de calcul et cliquez sur **suivant**.

    ![Sélectionnez la ressource de calcul](media/create-vcenter-virtual-machine-04.png)

6. Sélectionnez **vsanDatastore** et cliquez sur **suivant**.

    ![Sélectionner Stockage](media/create-vcenter-virtual-machine-05.png)

7. Conservez la sélection de compatibilité par défaut ESXi 6.5 et cliquez sur **suivant**.

    ![Sélectionnez la compatibilité](media/create-vcenter-virtual-machine-06.png)

8. Sélectionnez le système d’exploitation invité de l’image ISO pour la machine virtuelle et cliquez sur **suivant**.

    ![Personnaliser le système d’exploitation invité](media/create-vcenter-virtual-machine-07.png)

9. Sélectionnez les options de réseau et de disque dur. Pour le lecteur de CD/DVD de nouveau, sélectionnez **fichier ISO de Datastore**.  Si vous souhaitez autoriser le trafic à partir de l’adresse IP publique à cette machine virtuelle, sélectionnez le réseau en tant que **vm-1**.

    ![Personnalisation de sélectionner du matériel](media/create-vcenter-virtual-machine-08.png)

10. Une fenêtre de sélection s’ouvre. Sélectionnez le fichier que vous avez téléchargé précédemment dans le dossier de fichiers ISO et les modèles, cliquez sur **OK**.

    ![Sélectionnez ISO](media/create-vcenter-virtual-machine-10.png)

11. Passez en revue les paramètres et cliquez sur **OK** pour créer la machine virtuelle.

    ![Passez en revue les options](media/create-vcenter-virtual-machine-11.png)

La machine virtuelle est désormais ajoutée pour les ressources de calcul de la charge de travail et est prêt à être utilisé. 

![Nouvelle machine virtuelle dans vCenter](media/create-vcenter-virtual-machine-12.png)

La configuration de base est maintenant terminée. Vous pouvez commencer à l’aide de votre Cloud privé similaire à l’utilisation de votre infrastructure de machine virtuelle locale.

Sections suivantes contiennent des informations facultatives sur la configuration DNS et DHCP serveurs Cloud privé pour les charges de travail et modification de la configuration de mise en réseau par défaut.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Ajouter des utilisateurs et des Sources d’identité à vCenter (facultatif)

CloudSimple attribue un compte d’utilisateur vCenter par défaut avec le nom d’utilisateur **cloudowner@cloudsimple.local** . Aucune configuration de compte supplémentaire n’est nécessaire pour vous familiariser.  CloudSimple attribue normalement les administrateurs les privilèges que dont ils ont besoin pour effectuer des opérations normales.  Configurer votre active directory sur site ou d’un Azure AD comme un [source d’identité supplémentaires](https://docs.azure.cloudsimple.com/set-vcenter-identity/) sur votre Cloud privé.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Créer un serveur DHCP et DNS (facultatif)

Applications et charges de travail en cours d’exécution dans un environnement de Cloud privé nécessitent la résolution de noms et des services DHCP pour la recherche et l’attribution d’adresses IP. Une infrastructure DHCP et DNS appropriée est nécessaire pour fournir ces services. Vous pouvez configurer une machine virtuelle dans vCenter pour fournir ces services dans votre environnement de Cloud privé.

### <a name="prerequisites"></a>Conditions préalables

* Un groupe de ports distribué avec le réseau local virtuel configuré

* Configurer sur site ou serveurs DNS Internet de routage

* Modèle d’ordinateur virtuel ou ISO pour créer une machine virtuelle

Les liens suivants fournissent des conseils sur la configuration des serveurs DHCP et DNS sur Linux et Windows.

### <a name="linux-based-dns-server-setup"></a>Configuration du serveur DNS basé sur Linux

Linux offre différents packages de configuration des serveurs DNS.  Voici un lien vers des instructions pour configurer un serveur DNS BIND d’open source.

[Exemple de configuration](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Programme d’installation basé sur Windows

Ces articles Microsoft décrivent comment configurer un serveur Windows comme un serveur DNS et comme un serveur DHCP.
<br>
[Windows Server comme serveur DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server comme serveur DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personnaliser la configuration de mise en réseau (facultative)

Les pages de réseau dans le portail CloudSimple permettent de spécifier la configuration pour les tables de pare-feu et des adresses IP publiques pour les machines virtuelles.

### <a name="allocate-public-ips"></a>Allouer des adresses IP publiques

1. Accédez à **réseau > adresse IP publique** dans le portail CloudSimple.
2. Cliquez sur **allouer une adresse IP publique**.
3. Entrez un nom pour identifier l’entrée d’adresse IP.
4. Sélectionnez l’emplacement de votre nuage privé.
5. Utilisez le curseur pour modifier le délai d’inactivité Si vous le souhaitez.
6. Entrez l’adresse IP locale pour laquelle vous souhaitez affecter une adresse IP publique.
7. Entrez un nom DNS associé, le cas échéant.
8. Cliquez sur **Done**.

    ![Adresse IP publique](media/quick-create-pc-public-ip.png)

La tâche d’allouer l’adresse IP publique commence. Vous pouvez vérifier l’état de la tâche sur le **activité > tâches** page. Lors de l’allocation est terminée, la nouvelle entrée est indiquée dans la page adresses IP publiques.

La machine virtuelle à laquelle cette adresse IP doit être mappée doit être configuré avec l’adresse locale spécifiée ci-dessus. La procédure pour configurer une adresse IP est spécifique au système d’exploitation de machine virtuelle. Consultez la documentation de votre système d’exploitation de machine virtuelle pour la procédure correcte.

#### <a name="example"></a>Exemples

Par exemple, voici les détails pour Ubuntu 16.04.

Ajoutez la méthode statique à la configuration d’adresse famille inet dans le fichier ```/etc/network/interfaces```. Modifier les valeurs d’adresse, masque de sous-réseau et passerelle. Pour cet exemple, nous utilisons l’interface eth0, adresse IP interne 192.168.24.10, adresse de la passerelle 192.168.24.1 et masque de sous-réseau 255.255.255.0. 

Modifiez le fichier ```interfaces``` .

```
sudo vi /etc/network/interfaces
```

Mettre à jour de la section suivante dans ```interfaces``` fichier.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Désactiver l’interface.

```
sudo ifdown eth0
```

Réactiver l’interface.

```
sudo ifup eth0
```

Par défaut, tout le trafic entrant à partir d’Internet est **refusé**. Si vous souhaitez ouvrir un autre port, créez un [tableau pare-feu](https://docs.azure.cloudsimple.com/firewall/).

Après avoir configuré une adresse IP interne en tant que l’adresse IP statique, vérifiez que vous pouvez accéder à Internet à partir de la machine virtuelle.

```
ping 8.8.8.8
```

Vérifiez que vous pouvez atteindre la machine virtuelle à partir d’Internet à l’aide de l’adresse IP publique.

Assurez-vous que les règles de pare-feu (iptable) sur la machine virtuelle ne bloquent pas le port 80 entrant.

```
netstat -an | grep 80
```

Démarrage d’un serveur http qui écoute sur le port 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

or

```
python3 -m http.server 80
```

Ouvrez un navigateur sur votre bureau et pointez-le vers le port 80 pour l’adresse IP publique parcourir les fichiers sur votre machine virtuelle. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Règles de pare-feu CloudSimple par défaut pour l’adresse IP publique

* Trafic VPN : Tout le trafic entre (depuis/vers) la connexion VPN et tous les réseaux de la charge de travail et réseau de gestion est autorisé.
* Trafic interne de cloud privé : Tout est-ouest le trafic entre (depuis/vers) des réseaux de charge de travail et le réseau de gestion (illustré ci-dessus) est autorisé.
* Trafic Internet :
    * Tout le trafic entrant à partir d’Internet est refusé à des réseaux de la charge de travail et le réseau de gestion.
    * Tout le trafic sortant à Internet depuis des réseaux de charge de travail ou le réseau de gestion est autorisé.

Vous pouvez également modifier la façon de que votre trafic est sécurisé, à l’aide de la fonctionnalité de règles de pare-feu. Pour plus d’informations, consultez [définir des tables de pare-feu et des règles](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installer des solutions (facultatives)
Vous pouvez installer des solutions sur votre Cloud privé de CloudSimple pour tirer pleinement parti de votre nuage privé environnement vCenter. Vous pouvez configurer de sauvegarde, de récupération d’urgence, de réplication et d’autres fonctions pour protéger vos machines virtuelles. Exemples VMware Site Recovery Manager (VMware SRM) et Veeam Backup & Replication.

Pour installer une solution, vous devez demander des privilèges supplémentaires pour une période limitée. Consultez [élever les privilèges](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* [Se connecter au réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Configurer des passerelles VPN sur réseau de CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
