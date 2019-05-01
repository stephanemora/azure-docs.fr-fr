---
title: À propos de l’appliance Collector dans Azure Migrate | Microsoft Docs
description: Fournit des informations sur l’appliance Collector dans Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: d00899e0ca358b4e2970caa8c63c98e375ea970c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728024"
---
# <a name="about-the-collector-appliance"></a>À propos de l’appliance Collector

 Cet article fournit des informations sur Azure Migrate Collector.

Azure Migrate Collector est une appliance légère qui peut être utilisée pour découvrir un environnement vCenter local dans le cadre d’une évaluation avec le service [Azure Migrate](migrate-overview.md), avant la migration vers Azure.  

## <a name="discovery-method"></a>Méthode de détection

Auparavant, deux options étaient disponibles pour l’appliance collecteur : la détection unique et la détection en continu. Le modèle de détection unique est maintenant déconseillé, car il s’appuyait sur les paramètres de statistiques vCenter Server pour la collecte de données de performance (ce qui nécessitait des paramètres de statistiques de niveau 3) et collectait également des compteurs de moyenne (au lieu de compteurs de pointe), ce qui entraînait un sous-dimensionnement. Le modèle de détection en continu garantit la collecte de données granulaires et entraîne un dimensionnement précis en raison de la collecte de compteurs de pointe. Voici comment cela fonctionne :

L’appliance collecteur est connectée en continu au projet Azure Migrate et elle collecte en continu les données de performances des machines virtuelles.

- Le collecteur profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
- L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes.
- Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes et l’envoie à Azure.
- Ce modèle ne dépend pas des paramètres de statistiques vCenter Server pour collecter les données de performances.
- Vous pouvez arrêter à tout moment le profilage continu à partir du Collecteur.

**Évaluations rapides** : avec l’application de détection en continu, une fois la détection terminée (au bout de deux heures environ, en fonction du nombre de machines virtuelles), vous pouvez créer immédiatement des évaluations. Étant donné que la collecte des données de performances démarre lorsque vous lancez la détection, si vous voulez obtenir des évaluations rapides, vous devez sélectionner le critère de dimensionnement *Localement* dans l’évaluation. Pour les évaluations de performances, il est conseillé d’attendre au moins un jour après le lancement de la découverte afin d’obtenir des recommandations de taille fiables.

L’appliance collecte uniquement les données de performances en continu. Elle ne détecte pas les changements de configuration dans l’environnement local (par exemple, ajout ou suppression de machine virtuelle, ajout de disque, etc.). En cas de modification de configuration de l’environnement local, vous pouvez procéder aux opérations suivantes pour refléter les modifications dans le portail :

- Ajout d’éléments (machines virtuelles, disques, cœurs, etc.) : pour refléter ces modifications dans le portail Azure, vous pouvez arrêter la détection de l’appliance, puis la redémarrer. Cela garantit que les modifications sont mises à jour dans le projet Azure Migrate.

- Suppression de machines virtuelles : en raison de la façon dont l’appliance est conçue, la suppression de machines virtuelles n’apparaît pas, même si vous arrêtez et redémarrez la détection. Cela est dû au fait que les données de détections ultérieures sont ajoutées, et non pas remplacées, aux détections plus anciennes. Dans ce cas, vous pouvez simplement ignorer la machine virtuelle dans le portail en la supprimant de votre groupe et en recalculant l’évaluation.

> [!NOTE]
> L’appliance de découverte unique est désormais dépréciée, car son utilisation dépend des paramètres de statistiques vCenter Server concernant la disponibilité des points de données de performances, et nécessite la collecte des données de compteurs de performance moyenne, ce qui a comme résultat d’attribuer une taille insuffisante aux machines virtuelles pour la migration vers Azure.

## <a name="deploying-the-collector"></a>Déployer le collecteur

Vous déployez l’appliance Collector à l’aide d’un modèle OVF :

- Vous téléchargez le modèle OVF à partir d’un projet Azure Migrate dans le portail Azure. Vous importez le fichier téléchargé dans vCenter Server, pour configurer la machine virtuelle de l’appliance Collector.
- À partir du modèle OVF, VMware configure une machine virtuelle dotée de 8 cœurs, de 16 Go de RAM et d’un disque de 80 Go. Le système d’exploitation est Windows Server 2016 (64 bits).
- Quand vous exécutez le collecteur, une série de vérifications de prérequis s’exécute pour vérifier que le collecteur peut se connecter à Azure Migrate.

- [Familiarisez-vous](tutorial-assessment-vmware.md#create-the-collector-vm) avec la création du collecteur.


## <a name="collector-prerequisites"></a>Prérequis pour le collecteur

Vous devez effectuer quelques vérifications de prérequis pour vous assurer que le Collecteur peut se connecter au service Azure Migrate via Internet et charger des données découvertes.

- **Vérifier le cloud Azure** : Le collecteur doit connaître le cloud Azure vers lequel vous envisagez d’effectuer la migration.
    - Sélectionnez Azure Government si vous envisagez d’effectuer une migration vers le cloud Azure Government.
    - Sélectionnez Azure Global si vous envisagez d’effectuer une migration vers le cloud Azure commercial.
    - En fonction du cloud spécifié ici, l’appliance envoie les métadonnées détectées aux points de terminaison respectifs.
- **Vérifier la connexion Internet** : le collecteur peut se connecter à Internet directement ou par le biais d’un proxy.
    - La vérification de prérequis vérifie la connectivité aux [URL requises et facultatives](#urls-for-connectivity).
    - Si vous avez une connexion directe à Internet, aucune action spécifique n’est nécessaire à part vérifier que le collecteur peut atteindre les URL requises.
    - Si vous vous connectez via un proxy, notez les exigences ci-dessous.
- **Vérifier la synchronisation de l’heure** : le collecteur doit être synchronisé avec le serveur de temps Internet pour que les requêtes envoyées au service soient authentifiées.
    - L’URL portal.azure.com doit être accessible à partir du Collecteur afin que l’heure puisse être validée.
    - Si la machine n’est pas synchronisée, vous devez changer l’heure de l’horloge sur la machine virtuelle Collecteur afin qu’elle corresponde à l’heure actuelle. Pour ce faire, ouvrez une invite d’administrateur sur la machine virtuelle, puis exécutez **w32tm /tz** pour vérifier le fuseau horaire. Exécutez **w32tm /resync** pour synchroniser l’heure.
- **Vérifier le service collecteur en cours d’exécution** :  le service Azure Migrate Collector doit être en cours d’exécution sur la machine Collector.
    - Ce service est lancé automatiquement au démarrage de la machine.
    - Si le service n’est pas en cours d’exécution, démarrez-le à partir du Panneau de configuration.
    - Le service Collecteur se connecte à vCenter Server, collecte les données de performances et les métadonnées des machines virtuelles, puis les envoie au service Azure Migrate.
- **Vérifier que VMware PowerCLI 6.5 est installé** : le module PowerShell VMware PowerCLI 6.5 doit être installé sur la machine virtuelle Collector, afin qu’elle puisse communiquer avec vCenter Server.
    - Si le collecteur peut accéder aux URL requises pour installer le module, celui-ci est installé automatiquement pendant le déploiement du collecteur.
    - Si le collecteur ne peut pas installer le module pendant le déploiement, vous devez l’installer manuellement.
- **Vérifier la connexion à vCenter Server** : le collecteur doit être en mesure de se connecter à vCenter Server et d’exécuter des requêtes portant sur les machines virtuelles, leurs métadonnées et leurs compteurs de performances. [Vérifiez les prérequis](#connect-to-vcenter-server) pour la connexion.


### <a name="connect-to-the-internet-via-a-proxy"></a>Se connecter à Internet via un proxy

- Si le serveur proxy requiert une authentification, vous pouvez spécifier le nom d’utilisateur et le mot de passe quand vous configurez le collecteur.
- L’adresse IP ou le FQDN du serveur Proxy doit être spécifié en tant que *http :\//IPaddress* ou *http :\//FQDN*.
- Seuls les proxys HTTP sont pris en charge. Les serveurs proxy HTTPS ne sont pas pris en charge par le Collecteur.
- Si le serveur proxy est un proxy d’interception, vous devez importer le certificat de proxy sur la machine virtuelle Collecteur.
  1. Dans la machine virtuelle du collecteur, accédez au **Menu Démarrer** > **Gérer les certificats d’ordinateur**.
  2. Dans l’outil Certificats, sous **Certificats - Ordinateur Local**, recherchez **Éditeurs approuvés** > **Certificats**.

      ![Outil Certificats](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Copiez le certificat de proxy sur la machine virtuelle du collecteur. Vous devrez peut-être l’obtenir auprès de votre administrateur réseau.
  4. Double-cliquez pour ouvrir le certificat, puis cliquez sur **Installer le certificat**.
  5. Dans l’Assistant Importation de certificat > Emplacement du magasin, choisissez **Ordinateur local**.

     ![Emplacement du magasin de certificats](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. Sélectionnez **Placer tous les certificats dans le magasin suivant** > **Parcourir** > **Éditeurs approuvés**. Cliquez sur **Terminer** pour importer le certificat.

     ![Magasin de certificats](./media/concepts-intercepting-proxy/certificate-store.png)

  7. Vérifiez que le certificat a été importé normalement, et que la vérification du prérequis relatif à la connectivité Internet fonctionne comme prévu.


### <a name="urls-for-connectivity"></a>Adresses URL de connectivité

Vous validez la vérification de la connectivité en vous connectant à une liste d’URL.

**URL** | **Détails**  | **Vérification du prérequis**
--- | --- | ---
*. portal.azure.com | S’applique à Azure Global. Vérifie la connectivité avec le service Azure et la synchronisation de l’heure. | Accès à l’URL requise.<br/><br/> La vérification du prérequis échoue s’il n’y a pas de connectivité.
*.portal.azure.us | S’applique uniquement à Azure Government. Vérifie la connectivité avec le service Azure et la synchronisation de l’heure. | Accès à l’URL requise.<br/><br/> La vérification du prérequis échoue s’il n’y a pas de connectivité.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Permet de télécharger le module PowerShell vCenter PowerCLI. | Accès aux URL est obligatoire.<br/><br/> La vérification du prérequis n’échoue pas.<br/><br/> L’installation automatique du module sur la machine virtuelle Collecteur échoue. Vous devez installer le module manuellement sur un ordinateur qui possède une connexion internet, puis copier les modules dans l’appliance. [En savoir plus en accédant à l’étape 4 dans ce guide de dépannage](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Installer le module VMware PowerCLI manuellement

1. Installez le module en suivant [ces étapes](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Ces dernières décrivent l’installation en ligne et hors connexion.
2. Si la machine virtuelle Collecteur est hors connexion et que vous installez le module sur une autre machine connectée à Internet, vous devez copier les fichiers VMware.* à partir de cette machine vers la machine virtuelle Collecteur.
3. Après l’installation, vous pouvez redémarrer les vérifications des prérequis pour vérifier que PowerCLI est installé.

### <a name="connect-to-vcenter-server"></a>Se connecter à vCenter Server

Le Collecteur se connecte à vCenter Server et exécute des requêtes portant sur les métadonnées et les compteurs de performances des machines virtuelles. Voici les éléments à prendre en compte pour la connexion.

- Seules les versions vCenter Server 5.5, 6.0, 6.5 et 6.7 sont prises en charge.
- Vous avez besoin d’un compte en lecture seule avec les autorisations résumées ci-après pour la découverte. Seuls les centres de données accessibles à l’aide du compte sont accessibles pour la découverte.
- Par défaut, vous vous connectez à vCenter Server avec une adresse IP ou un nom de domaine complet. Si vCenter Server écoute sur un port différent, vous vous connectez sous la forme *adresse_IP:numéro_port* ou *FQDN:numéro_port*.
- Le Collecteur doit avoir le réseau en ligne de mire jusqu’au serveur vCenter.

#### <a name="account-permissions"></a>Autorisations du compte

**Compte** | **autorisations**
--- | ---
Au moins un compte d’utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule   


## <a name="collector-communications"></a>Communications du collecteur

Le collecteur communique comme résumé dans le schéma et le tableau suivants.

![Schéma de communication de collecteur](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Le collecteur communique avec** | **Port** | **Détails**
--- | --- | ---
Service Azure Migrate | TCP 443 | Le Collecteur communique avec le service Azure Migrate sur le port SSL 443.
Serveur vCenter | TCP 443 | Le collecteur doit être en mesure de communiquer avec vCenter Server.<br/><br/> Par défaut, Il se connecte à vCenter sur le port 443.<br/><br/> Si vCenter Server écoute sur un autre port, ce port doit être disponible en tant que port sortant sur le Collecteur.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Métadonnées collectées

> [!NOTE]
> Métadonnées détectées par les Azure Migrate appliance collecteur est utilisé pour vous aider à dimensionner correctement vos applications lorsque vous les migrez vers Azure, effectuer l’analyse d’adéquation Azure, l’analyse de dépendance application et planification du coût. Microsoft n’utilise pas ces données par rapport à un audit de conformité de licence.

L’appliance collecteur détecte les métadonnées de configuration suivantes pour chaque machine virtuelle. Les données de configuration relatives à la machine virtuelle sont disponibles une heure après le démarrage de la détection.

- Nom d’affichage de la machine virtuelle (sur vCenter Server)
- Chemin d’inventaire de la machine virtuelle (hôte/dossier sur vCenter Server)
- Adresse IP
- Adresse MAC
- Système d’exploitation
- Nombre de cœurs, disques, cartes réseau
- Taille de la mémoire, taille des disques
- Compteurs de performance de la machine virtuelle, du disque et du réseau.

### <a name="performance-counters"></a>Compteurs de performances

 L’appliance collecteur collecte les compteurs de performances suivants pour chaque machine virtuelle à partir de l’hôte ESXi, et ce, à des intervalles de 20 secondes. Ce sont des compteurs vCenter et, bien que la terminologie indique le terme « moyenne », les exemples de 20 secondes sont des compteurs en temps réel. Les données de performances pour les machines virtuelles commencent à être disponibles sur le portail deux heures après le lancement de la détection. Il est vivement recommandé d’attendre au moins un jour avant de créer des évaluations basées sur les performances pour obtenir des recommandations de right-sizing fiables. Si vous souhaitez des résultats instantanés, vous pouvez créer des évaluations avec un critère de dimensionnement, tel que *localement*, qui ne prend pas en compte les données de performances pour le right-sizing.

**Compteur** |  **Impact sur l’évaluation**
--- | ---
cpu.usage.average | Taille de machine virtuelle recommandée et coût  
mem.usage.average | Taille de machine virtuelle recommandée et coût  
virtualDisk.read.average | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.write.average | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.numberReadAveraged.average | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.numberWriteAveraged.average | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
net.received.average | Calcule la taille de machine virtuelle                          
net.transmitted.average | Calcule la taille de machine virtuelle     

La liste complète des compteurs VMware collectés par Azure Migrate est disponible ci-dessous :

**Catégorie** |  **Métadonnées** | **Point de données vCenter**
--- | --- | ---
Détails de la machine | ID de l'ordinateur virtuel | vm.Config.InstanceUuid
Détails de la machine | nom de la machine virtuelle | vm.Config.Name
Détails de la machine | ID du vCenter Server | VMwareClient.InstanceUuid
Détails de la machine |  Description de la machine virtuelle |  vm.Summary.Config.Annotation
Détails de la machine | Nom de produit de la licence | vm.Client.ServiceContent.About.LicenseProductName
Détails de la machine | Type de système d'exploitation | vm.Summary.Config.GuestFullName
Détails de la machine | Version de système d’exploitation | vm.Summary.Config.GuestFullName
Détails de la machine | Type de démarrage | vm.Config.Firmware
Détails de la machine | Nombre de mémoires à tores magnétiques | vm.Config.Hardware.NumCPU
Détails de la machine | Mégaoctets de mémoire | vm.Config.Hardware.MemoryMB
Détails de la machine | Nombre de disques | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
Détails de la machine | Liste des tailles de disque | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
Détails de la machine | Liste des adaptateurs réseau | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
Détails de la machine | Utilisation du processeur | cpu.usage.average
Détails de la machine | Utilisation de la mémoire | mem.usage.average
Détails du disque (par disque) | Valeur de la clé du disque | disk.Key
Détails du disque (par disque) | Nombre d’unités de disque | disk.UnitNumber
Détails du disque (par disque) | Valeur de clé de contrôleur de disque | disk.ControllerKey.Value
Détails du disque (par disque) | Gigaoctets approvisionnés | virtualDisk.DeviceInfo.Summary
Détails du disque (par disque) | Nom du disque | Cette valeur est générée à l’aide de disk.UnitNumber, de disk.Key et de disk.ControllerKey.Value
Détails du disque (par disque) | Nombre d’opérations de lecture par seconde | virtualDisk.numberReadAveraged.average
Détails du disque (par disque) | Nombre d’opérations d’écriture par seconde | virtualDisk.numberWriteAveraged.average
Détails du disque (par disque) | Débit de lecture en mégaoctets par seconde | virtualDisk.read.average
Détails du disque (par disque) | Débit d’écriture en mégaoctets par seconde | virtualDisk.write.average
Détails de la carte réseau (par carte réseau) | Nom de la carte réseau | nic.Key
Détails de la carte réseau (par carte réseau) | Adresse MAC | ((VirtualEthernetCard)nic).MacAddress
Détails de la carte réseau (par carte réseau) | Adresses IPv4 | vm.Guest.Net
Détails de la carte réseau (par carte réseau) | Adresses IPv6 | vm.Guest.Net
Détails de la carte réseau (par carte réseau) | Débit de lecture en mégaoctets par seconde | net.received.average
Détails de la carte réseau (par carte réseau) | Débit d’écriture en mégaoctets par seconde | net.transmitted.average
Détails de chemin d’accès d’inventaire | Nom | container.GetType().Name
Détails de chemin d’accès d’inventaire | Type d’objet enfant | container.ChildType
Détails de chemin d’accès d’inventaire | Détails de référence | container.MoRef
Détails de chemin d’accès d’inventaire | Chemin d’accès de l’inventaire complet | container.Name with complete path
Détails de chemin d’accès d’inventaire | Détails du parent | Container.Parent
Détails de chemin d’accès d’inventaire | Détails de dossier pour chaque machine virtuelle | ((Folder)container).ChildEntity.Type
Détails de chemin d’accès d’inventaire | Détails de centre de données pour chaque dossier de la machine virtuelle | ((Datacenter)container).VmFolder
Détails de chemin d’accès d’inventaire | Détails de centre de données pour chaque dossier hôte | ((Datacenter)container).HostFolder
Détails de chemin d’accès d’inventaire | Détails du cluster pour chaque hôte | ((ClusterComputeResource)container).Host)
Détails de chemin d’accès d’inventaire | Détails de l’hôte pour chaque machine virtuelle | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Sécurisation de l’appliance Collector

Nous vous recommandons de suivre les étapes ci-dessous pour sécuriser l’appliance Collector :

- Ne partagez pas les mots de passe administrateur avec des tiers non autorisés et ne les égarez pas non plus.
- Arrêtez l’appliance lorsqu’elle est inutilisée.
- Placez l’appliance sur un réseau sécurisé.
- Une fois la migration terminée, supprimez l’instance de l’appliance.
- En outre, après la migration, supprimez les fichiers de sauvegarde du disque (VMDK), puisqu’il peut se trouver des informations d’identification de vCenter mises en cache sur les disques.

## <a name="os-license-in-the-collector-vm"></a>Licence de système d’exploitation sur la machine virtuelle du collecteur

Le collecteur est fourni avec une licence d’évaluation de Windows Server 2016 qui n’est valide pendant 180 jours. Si la période d’évaluation arrive à expiration pour la machine virtuelle du collecteur, nous vous recommandons de télécharger un nouveau fichier OVA et de créer une appliance.

## <a name="updating-the-os-of-the-collector-vm"></a>Mise à jour du système d’exploitation de la machine virtuelle Collecteur

Bien que l’appliance Collector dispose d’une licence d’évaluation de 180 jours, vous devez mettre à jour en permanence le système d’exploitation sur l’appliance pour éviter les arrêts automatiques de celle-ci.

- Si le Collecteur n’est pas mis à jour pendant 60 jours, il commence à arrêter la machine automatiquement.
- Si une découverte est en cours d’exécution, la machine n’est pas mise hors tension, même si 60 jours se sont écoulés. La machine est désactivée au terme de la découverte.
- Si vous avez utilisé le Collecteur pendant plus de 60 jours, nous vous recommandons de conserver la machine à jour à tout moment en exécutant la mise à jour de Windows.

## <a name="upgrading-the-collector-appliance-version"></a>Mise à niveau de la version de l’appliance Collector

Vous pouvez mettre à niveau le collecteur vers la version la plus récente sans retélécharger le fichier OVA.

1. Téléchargez la [dernière version répertoriée du package de mise à niveau](concepts-collector-upgrade.md).
2. Pour vous assurer que le correctif logiciel téléchargé est sécurisé, ouvrez la fenêtre de commande d’administrateur et exécutez la commande suivante pour générer le hachage du fichier ZIP. Le hachage généré doit correspondre au hachage mentionné pour la version spécifique :

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exemple d’utilisation C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copiez le fichier zip sur la machine virtuelle du collecteur Azure Migrate (appliance collecteur).
4. Cliquez avec le bouton droit sur le fichier zip et sélectionnez Tout extraire.
5. Cliquez avec le bouton droit sur Setup.ps1 et sélectionnez Exécuter avec PowerShell et suivez les instructions à l’écran pour installer la mise à jour.

## <a name="discovery-process"></a>Processus de découverte

Une fois l’appliance configurée, vous pouvez exécuter la découverte. Voici comment cela fonctionne :

- Vous exécutez une découverte par étendue. Toutes les machines virtuelles dans le chemin d’inventaire vCenter spécifié sont découvertes.
    - Vous définissez une étendue à la fois.
    - Cette étendue peut comporter jusqu’à 1 500 machines virtuelles.
    - L’étendue peut être un centre de données, un dossier ou un hôte ESXi.
- Une fois connecté à vCenter Server, vous vous connectez en spécifiant un projet de migration pour la collection.
- Les machines virtuelles sont découvertes et leurs métadonnées et données de performances sont envoyées à Azure. Ces actions font partie d’un travail de collecte.
    - L’appliance Collector reçoit un ID de collecteur spécifique qui persiste pour une machine donnée d’une découverte à l’autre.
    - Un travail de collecte en cours d’exécution reçoit un ID de session spécifique. L’ID change pour chaque travail de collecte et peut être utilisé à des fins le dépannage.

## <a name="next-steps"></a>Étapes suivantes

[Configurer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
