---
title: À propos de l’appliance Collector dans Azure Migrate | Microsoft Docs
description: Fournit des informations sur l’appliance Collector dans Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: b79045e54b9c2ee4846f2216704a419e0ff85501
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434430"
---
# <a name="about-the-collector-appliance"></a>À propos de l’appliance Collector

 Cet article fournit des informations sur Azure Migrate Collector.

Azure Migrate Collector est une appliance légère qui peut être utilisée pour découvrir un environnement vCenter local dans le cadre d’une évaluation avec le service [Azure Migrate](migrate-overview.md), avant la migration vers Azure.  


## <a name="deploying-the-collector"></a>Déployer le collecteur

Vous déployez l’appliance Collector à l’aide d’un modèle OVF :

- Vous téléchargez le modèle OVF à partir d’un projet Azure Migrate dans le portail Azure. Vous importez le fichier téléchargé dans vCenter Server, pour configurer la machine virtuelle de l’appliance Collector.
- À partir du modèle OVF, VMware configure une machine virtuelle dotée de 4 cœurs, de 8 Go de RAM et d’un disque de 80 Go. Le système d’exploitation est Windows Server 2012 R2 (64 bits).
- Quand vous exécutez le collecteur, une série de vérifications de prérequis s’exécute pour vérifier que le collecteur peut se connecter à Azure Migrate.

- [Familiarisez-vous](tutorial-assessment-vmware.md#create-the-collector-vm) avec la création du collecteur.


## <a name="collector-prerequisites"></a>Prérequis pour le collecteur

Vous devez effectuer quelques vérifications de prérequis pour vous assurer que le Collecteur peut se connecter au service Azure Migrate via Internet et charger des données découvertes.

- **Vérifier la connexion Internet** : le collecteur peut se connecter à Internet directement ou via un proxy.
    - La vérification de prérequis vérifie la connectivité aux [URL requises et facultatives](#connect-to-urls).
    - Si vous avez une connexion directe à Internet, aucune action spécifique n’est nécessaire à part vérifier que le collecteur peut atteindre les URL requises.
    - Si vous vous connectez via un proxy, notez les [exigences ci-dessous](#connect-via-a-proxy).
- **Vérifier la synchronisation de l’heure** : le Collecteur doit être synchronisé avec le serveur de temps Internet pour que les requêtes envoyées au service soient authentifiées.
    - L’URL portal.azure.com doit être accessible à partir du Collecteur afin que l’heure puisse être validée.
    - Si la machine n’est pas synchronisée, vous devez changer l’heure de l’horloge sur la machine virtuelle Collecteur afin qu’elle corresponde à l’heure actuelle. Pour ce faire, ouvrez une invite d’administrateur sur la machine virtuelle, puis exécutez **w32tm /tz** pour vérifier le fuseau horaire. Exécutez **w32tm /resync** pour synchroniser l’heure.
- **Vérifier que le service Collecteur est en cours d’exécution** : le service Azure Migrate Collector doit être en cours d’exécution sur la machine virtuelle Collecteur.
    - Ce service est lancé automatiquement au démarrage de la machine.
    - Si le service n’est pas en cours d’exécution, démarrez-le à partir du Panneau de configuration.
    - Le service Collecteur se connecte à vCenter Server, collecte les données de performances et les métadonnées des machines virtuelles, puis les envoie au service Azure Migrate.
- **Vérifier que VMware PowerCLI 6.5 est installé** : le module PowerShell VMware PowerCLI 6.5 doit être installé sur la machine virtuelle Collecteur, afin qu’elle puisse communiquer avec vCenter Server.
    - Si le collecteur peut accéder aux URL requises pour installer le module, celui-ci est installé automatiquement pendant le déploiement du collecteur.
    - Si le collecteur ne peut pas installer le module pendant le déploiement, vous devez [l’installer manuellement](#install-vwware-powercli-module-manually).
- **Vérifier la connexion à vCenter Server** : le collecteur doit être en mesure de se connecter à vCenter Server et d’exécuter des requêtes portant sur les machines virtuelles, leurs métadonnées et leurs compteurs de performances. [Vérifiez les prérequis](#connect-to-vcenter-server) pour la connexion.


### <a name="connect-to-the-internet-via-a-proxy"></a>Se connecter à Internet via un proxy

- Si le serveur proxy requiert une authentification, vous pouvez spécifier le nom d’utilisateur et le mot de passe quand vous configurez le collecteur.
- L’adresse IP/le nom FQDN du serveur proxy doit être spécifié sous la forme *http://IPaddress* ou *http://FQDN*.
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




### <a name="connect-to-urls"></a>Se connecter aux URL

Vous validez la vérification de la connectivité en vous connectant à une liste d’URL.

**URL** | **Détails**  | **Vérification du prérequis**
--- | --- | ---
*. portal.azure.com | Vérifie la connectivité avec le service Azure et la synchronisation de l’heure. | Accès à l’URL requise.<br/><br/> La vérification du prérequis échoue s’il n’y a pas de connectivité.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Permet de télécharger le module PowerShell vCenter PowerCLI. | Accès aux URL facultatives.<br/><br/> La vérification du prérequis n’échoue pas.<br/><br/> L’installation automatique du module sur la machine virtuelle Collecteur échoue. Vous devez installer le module manuellement.


### <a name="install-vmware-powercli-module-manually"></a>Installer le module VMware PowerCLI manuellement

1. Installez le module en suivant [ces étapes](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Ces dernières décrivent l’installation en ligne et hors connexion.
2. Si la machine virtuelle Collecteur est hors connexion et que vous installez le module sur une autre machine connectée à Internet, vous devez copier les fichiers VMware.* à partir de cette machine vers la machine virtuelle Collecteur.
3. Après l’installation, vous pouvez redémarrer les vérifications des prérequis pour vérifier que PowerCLI est installé.

### <a name="connect-to-vcenter-server"></a>Se connecter à vCenter Server

Le Collecteur se connecte à vCenter Server et exécute des requêtes portant sur les métadonnées et les compteurs de performances des machines virtuelles. Voici les éléments à prendre en compte pour la connexion.

- Seules les versions vCenter Server 5.5, 6.0 et 6.5 sont prises en charge.
- Vous avez besoin d’un compte en lecture seule avec les autorisations résumées ci-après pour la découverte. Seuls les centres de données accessibles à l’aide du compte sont accessibles pour la découverte.
- Par défaut, vous vous connectez à vCenter Server avec une adresse IP ou un nom de domaine complet. Si vCenter Server écoute sur un port différent, vous vous connectez sous la forme *adresse_IP:numéro_port* ou *FQDN:numéro_port*.
- Pour que les données de performances du stockage et du réseau puissent être collectées, les paramètres de statistiques de vCenter Server doivent être configurés sur le niveau 3.
- Si le niveau est inférieur à 3, la découverte fonctionne, mais les données de performances ne sont pas collectées. Certains compteurs peuvent être collectées, mais d’autres peuvent être définis sur zéro.
- Si les données de performances du stockage et du réseau ne sont pas collectées, les recommandations de taille de l’évaluation sont effectuées selon les données de performances du processeur et de la mémoire, et selon les données de configuration des adaptateurs de disque et des cartes réseau.
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


## <a name="securing-the-collector-appliance"></a>Sécurisation de l’appliance Collector

Nous vous recommandons de suivre les étapes ci-dessous pour sécuriser l’appliance Collector :

- Ne partagez pas les mots de passe administrateur avec des tiers non autorisés et ne les égarez pas non plus.
- Arrêtez l’appliance lorsqu’elle est inutilisée.
- Placez l’appliance sur un réseau sécurisé.
- Une fois la migration terminée, supprimez l’instance de l’appliance.
- En outre, après la migration, supprimez les fichiers de sauvegarde du disque (VMDK), puisqu’il peut se trouver des informations d’identification de vCenter mises en cache sur les disques.

## <a name="os-license-in-the-collector-vm"></a>Licence de système d’exploitation sur la machine virtuelle du collecteur

Le collecteur est fourni avec une licence d’évaluation Windows Server 2012 R2 qui est valide pendant 180 jours. Si la période d’évaluation arrive à expiration pour la machine virtuelle du collecteur, nous vous recommandons de télécharger un nouveau fichier OVA et de créer une appliance.

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


## <a name="discovery-methods"></a>Méthodes de découverte

L’appliance Collector peut utiliser deux méthodes de découverte : découverte unique ou découverte en continue.


### <a name="one-time-discovery"></a>Découverte unique

Le Collecteur communique une fois avec vCenter Server pour rassembler les métadonnées sur les machines virtuelles. À l’aide de cette méthode :

- L’appliance n’est pas connectée en permanence au projet Azure Migrate.
- Les modifications effectuées dans l’environnement local ne sont pas répercutées dans Azure Migrate au terme de la découverte. Pour répercuter ces modifications, vous devez redécouvrir le même environnement dans le même projet.
- Pour cette méthode de découverte, vous devez définir les paramètres de statistiques dans vCenter Server sur le niveau 3.
- Une fois que vous avez défini le niveau sur 3, la génération des compteurs de performances peut prendre un jour. Ainsi, nous vous recommandons d’attendre 24 heures avant d’exécuter la découverte.
- Quand elle collecte les données de performances relatives à une machine virtuelle, l’appliance s’appuie sur les données de performances historiques stockées dans vCenter Server. Elle collecte l’historique des performances du mois passé.
- Azure Migrate collecte une valeur moyenne (plutôt qu’une valeur maximale) pour chaque métrique.

### <a name="continuous-discovery"></a>Découverte en continu

L’appliance Collector est connectée en permanence au projet Azure Migrate.

- Le Collecteur profile en permanence l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
- Ce modèle ne dépend pas des paramètres de statistiques vCenter Server pour collecter les données de performances.
- L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes.
- Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes et l’envoie à Azure.
- Vous pouvez arrêter à tout moment le profilage continu à partir du Collecteur.

> [!NOTE]
> La fonctionnalité de découverte en continu est en préversion. Si les paramètres de statistiques vCenter Server ne sont pas définis sur le niveau 3, nous vous recommandons d’utiliser cette méthode.


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

### <a name="collected-metadata"></a>Métadonnées collectées

L’appliance Collector découvre les métadonnées statiques suivantes pour les machines virtuelles :

- Nom d’affichage de la machine virtuelle (sur vCenter Server)
- Chemin d’inventaire de la machine virtuelle (hôte/dossier sur vCenter Server)
- Adresse IP
- Adresse MAC
- Système d’exploitation
- Nombre de cœurs, disques, cartes réseau
- Taille de la mémoire, taille des disques
- Compteurs de performance de la machine virtuelle, du disque et du réseau.

#### <a name="performance-counters"></a>Compteurs de performances

- **Découverte unique** : quand des compteurs sont collectés pour une découverte unique, notez les points suivants :

    - La collecte des métadonnées de configuration et leur envoi au projet peuvent prendre jusqu’à 15 minutes.
    - Une fois les données de configuration collectées, il peut s’écouler une heure avant que les données de performances ne soient disponibles dans le portail.
    - Une fois les métadonnées disponibles dans le portail, la liste des machines virtuelles s’affiche ; vous pouvez alors commencer à créer des groupes pour l’évaluation.
- **Découverte en continu** : pour la découverte en continu, notez les points suivants :
    - Les données de configuration pour la machine virtuelle sont disponibles une heure après le démarrage de la découverte.
    - Les données de performances commencent à être disponibles au bout de 2 heures.
    - Après avoir démarré la découverte, attendez au moins un jour que l’appliance profile l’environnement ; vous pourrez ensuite créer des évaluations.

**Compteur** | **Niveau** | **Niveau par appareil** | **Impact sur l’évaluation**
--- | --- | --- | ---
cpu.usage.average | 1 | N/D | Taille de machine virtuelle recommandée et coût  
mem.usage.average | 1 | N/D | Taille de machine virtuelle recommandée et coût  
virtualDisk.read.average | 2 | 2 | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.write.average | 2 | 2  | Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.numberReadAveraged.average | 1 | 3 |  Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
virtualDisk.numberWriteAveraged.average | 1 | 3 |   Calcule la taille du disque, le coût de stockage et la taille de la machine virtuelle
net.received.average | 2 | 3 |  Calcule la taille de la machine virtuelle et le coût du réseau                        |
net.transmitted.average | 2 | 3 | Calcule la taille de la machine virtuelle et le coût du réseau    

## <a name="next-steps"></a>Étapes suivantes

[Configurer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
