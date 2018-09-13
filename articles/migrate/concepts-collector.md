---
title: Appliance Collecteur dans Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble de l’appliance Collecteur et indique comment la configurer.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 74caf0ab052e1f6558dc20d15d84c01177b3f9cb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665578"
---
# <a name="collector-appliance"></a>Appliance Collecteur

[Azure Migrate](migrate-overview.md) évalue les charges de travail sur site pour la migration vers Azure. Cet article contient des informations sur l’utilisation de l’appliance Collecteur.



## <a name="overview"></a>Vue d’ensemble

Azure Migrate Collector est une appliance légère qui peut être utilisée pour découvrir votre environnement vCenter local. Cette appliance découvre les machines VMware locales et envoie les métadonnées les concernant au service Azure Migrate.

L’appliance Collecteur est un OVF que vous pouvez télécharger à partir du projet Azure Migrate. Elle instancie une machine virtuelle VMware disposant de 4 cœurs, de 8 Go de RAM et d’un disque de 80 Go. Le système d’exploitation de l’appliance est Windows Server 2012 R2 (64 bits).

Vous pouvez créer le Collecteur en suivant les étapes décrites ici dans la section relative à la [création de la machine virtuelle Collecteur](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Schéma de communication de collecteur

![Schéma de communication de collecteur](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Composant      | Communication avec   | Port requis                            | Motif                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Collecteur      | Service Azure Migrate | TCP 443                                  | Le collecteur doit être en mesure de communiquer avec le service via le port SSL 443 |
| Collecteur      | Serveur vCenter        | Par défaut 443                             | Le collecteur doit être en mesure de communiquer avec le serveur vCenter. Il se connecte à vCenter sur le port 443 par défaut. Si le vCenter écoute sur un autre port, ce port doit être disponible en tant que port sortant sur le collecteur |
| Collecteur      | RDP|   | TCP 3389 | Pour être en mesure d’utiliser RDP sur la machine du collecteur |





## <a name="collector-pre-requisites"></a>Conditions requises pour le Collecteur

Vous devez effectuer quelques vérifications des conditions préalables pour vous assurer que le Collecteur peut se connecter au service Azure Migrate et charger les données découvertes. Cet article examine chacune des conditions requises et indique pourquoi elles sont obligatoires.

### <a name="internet-connectivity"></a>Connectivité Internet

L’appliance Collecteur doit être connectée à Internet pour pouvoir envoyer les informations sur les machines détectées. Vous pouvez connecter la machine à Internet de l’une des deux façons indiquées ci-dessous.

1. Vous pouvez configurer le Collecteur pour qu’il bénéficie d’une connectivité Internet directe.
2. Vous pouvez configurer le Collecteur via un serveur proxy.
    * Si le serveur proxy requiert une authentification, spécifiez le nom d’utilisateur et le mot de passe dans les paramètres de connexion.
    * L’adresse IP/le nom FQDN du serveur proxy doit être au format http://IPaddress ou http://FQDN. Seul le proxy HTTP est pris en charge.

> [!NOTE]
> Les serveurs proxy HTTPS ne sont pas pris en charge par le Collecteur.

#### <a name="internet-connectivity-with-intercepting-proxy"></a>Connectivité Internet avec proxy d’interception

Si le serveur proxy que vous utilisez pour vous connecter à Internet est un proxy d’interception, vous devez importer le certificat de proxy dans la machine virtuelle du collecteur. Voici les étapes d’importation du certificat dans la machine virtuelle du collecteur.

1. Dans la machine virtuelle du collecteur, accédez au **Menu Démarrer**, puis recherchez et ouvrez **Gérer les certificats d’ordinateur**.
2. Dans l’outil Certificats, dans le volet gauche, sous **Certificats - Ordinateur Local**, recherchez **Éditeurs approuvés**. Sous **Éditeurs approuvés**, cliquez sur **Certificats** pour voir la liste des certificats dans le volet de droite.

    ![Outil Certificats](./media/concepts-intercepting-proxy/certificates-tool.png)

3. Copiez votre certificat de proxy sur la machine virtuelle du collecteur. Vous devez peut-être contacter l’équipe d’administration du réseau de votre organisation pour obtenir ce certificat.
4. Double-cliquez sur le certificat pour l’ouvrir. Cliquez sur **Installer le certificat**. L’Assistant Importation de certificat démarre.
5. Dans l’Assistant Importation de certificat, pour l’emplacement du magasin, choisissez **Ordinateur local**. Cliquez sur **Suivant**.

    ![Emplacement du magasin de certificats](./media/concepts-intercepting-proxy/certificate-store-location.png)

6. Choisissez de **Placer tous les certificats dans le magasin suivant**. Cliquez sur **Parcourir** et sélectionnez **Éditeurs approuvés** dans la liste de certificats qui s’affiche. Cliquez sur **Suivant**.

    ![Magasin de certificats](./media/concepts-intercepting-proxy/certificate-store.png)
    
7. Cliquez sur **Terminer**. Le certificat est importé. 
8. Vous pouvez aussi vérifier que le certificat est importé en ouvrant l’outil Certificats comme dans les étapes 1 et 2 ci-dessus.
9. Dans l’application de collecteur Azure Migrate, vérifiez que la vérification du prérequis de connectivité internet a réussi.


#### <a name="whitelisting-urls-for-internet-connection"></a>Inscription dans la liste verte des URL de connexion Internet

La vérification des conditions requises est réussie si le Collecteur peut se connecter à Internet via les paramètres indiqués. La vérification de la connectivité est validée en vous connectant à une liste d’URL, comme indiqué dans le tableau ci-dessous. Si vous utilisez un proxy de pare-feu basé sur une URL pour contrôler la connectivité sortante, veillez à inclure dans la liste verte les URL nécessaires suivantes :

**URL** | **Objectif**  
--- | ---
*. portal.azure.com | Indispensable pour vérifier la connectivité avec le service Azure et valider les problèmes de synchronisation de l’heure.

En outre, la vérification tente également de valider la connectivité aux URL suivantes, mais elle n’est pas en échec en cas d’inaccessibilité. La configuration de la liste verte des URL suivantes est facultative, mais vous devez procéder manuellement pour limiter la vérification des conditions requises.

**URL** | **Objectif**  | **Que faire si vous n’établissez pas une liste verte ?**
--- | --- | ---
*.oneget.org:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.
*.windows.net:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.
*.windowsazure.com:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.
*.powershellgallery.com:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.
*.msecnd.net:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.
*.visualstudio.com:443 | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell. | L’installation de PowerCLI échoue. Installez le module manuellement.

### <a name="time-is-in-sync-with-the-internet-server"></a>L’heure est synchronisée avec le serveur Internet

Le Collecteur doit être synchronisé avec le serveur de temps Internet pour que les requêtes envoyées au service soient authentifiées. L’URL portal.azure.com doit être accessible à partir du Collecteur afin que l’heure puisse être validée. Si la machine est désynchronisée, vous devez modifier l’heure de l’horloge sur la machine virtuelle Collecteur afin qu’elle corresponde à l’heure actuelle, en procédant comme suit :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
1. Pour vérifier le fuseau horaire, exécutez w32tm /tz.
1. Pour synchroniser l'heure, exécutez w32tm /resync.

### <a name="collector-service-should-be-running"></a>Le service Collecteur doit être en cours d’exécution

Le service Azure Migrate Collector doit être en cours d’exécution sur la machine. Ce service est lancé automatiquement au démarrage de la machine. S’il ne s’exécute pas, vous pouvez démarrer le service *Azure Migrate Collector* dans le Panneau de configuration. Le service Collecteur est chargé de se connecter au serveur vCenter, de collecter les métadonnées et les données de performance des machines et de les envoyer au service.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

Le module PowerShell de VMware PowerCLI doit être installé pour que le Collecteur puisse communiquer avec le serveur vCenter et rechercher les détails des machines et leurs données de performances. Le module PowerShell est automatiquement téléchargé et installé dans le cadre de la vérification des conditions requises. Le téléchargement automatique nécessite que quelques URL figurent dans la liste verte, faute de quoi vous devez fournir cet accès en les inscrivant sur la liste verte ou en installant le module manuellement.

Pour installer le module manuellement, procédez comme suit :

1. Pour installer le module PowerCli sur le Collecteur sans connexion Internet, suivez les étapes décrites dans [ce lien](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html).
2. Une fois que vous avez installé le module PowerShell sur un autre ordinateur, qui dispose d’un accès Internet, copiez les fichiers VMware.* de cette machine sur la machine Collecteur.
3. Recommencez les vérifications des conditions requises et vérifiez que le module PowerCLI est installé.

## <a name="connecting-to-vcenter-server"></a>Connexion au serveur vCenter Server

Le Collecteur doit se connecter au serveur vCenter Server et être en mesure d’interroger les machines virtuelles, leurs métadonnées et leurs compteurs de performances. Ces données sont utilisées par le projet pour calculer une évaluation.

1. Pour la connexion au serveur vCenter Server, un compte en lecture seule disposant des autorisations indiquées dans le tableau suivant peut être utilisé pour exécuter la détection.

    |Tâche  |Rôle/compte requis  |Autorisations  |
    |---------|---------|---------|
    |Détection basée sur l’appliance Collecteur    | Vous devez disposer d’au moins un utilisateur en lecture seule        |Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule         |

2. Seuls les centres de données qui sont accessibles au compte vCenter spécifié sont accessibles à la détection.
3. Vous devez spécifier le nom FQDN/l’adresse IP du serveur vCenter pour vous y connecter. Par défaut, il se connecte via le port 443. Si vous avez configuré le serveur vCenter pour qu’il écoute sur un autre numéro de port, vous pouvez le spécifier dans l’adresse du serveur au format Adresse_IP:Numéro_Port ou FQDN:Numéro_Port.
4. Vous devez définir les paramètres de statistiques de vCenter Server sur le niveau 3 avant de démarrer le déploiement. Si le niveau appliqué est inférieur à 3, la détection est effectuée, mais les données de performances relatives au stockage et au réseau ne sont pas collectées. Dans ce cas, les recommandations de taille de l’évaluation sont effectuées selon les données de performances du processeur et de la mémoire, et selon les seules données de configuration des adaptateurs de disque dur et des adaptateurs réseau. [En savoir plus](./concepts-collector.md) sur les données qui sont collectées et sur leur impact sur l’évaluation.
5. Le Collecteur doit avoir le réseau en ligne de mire jusqu’au serveur vCenter.

> [!NOTE]
> Seules les versions vCenter Server 5.5, 6.0 et 6.5 sont officiellement prises en charge.

> [!IMPORTANT]
> Nous vous recommandons de définir le niveau commun le plus élevé (niveau 3) pour les statistiques, afin que les données de tous les compteurs soient correctement collectées. Si vCenter est défini sur un niveau inférieur, il se peut que seuls quelques compteurs voient l’intégralité de leurs données collectées, les autres étant définis sur 0. L’évaluation peut donc afficher des données incomplètes.

### <a name="selecting-the-scope-for-discovery"></a>Sélection de l’étendue de la détection

Une fois connecté au serveur vCenter, vous pouvez sélectionner une étendue de détection. La sélection d’une étendue permet de détecter toutes les machines virtuelles à partir du chemin d’inventaire vCenter spécifié.

1. L’étendue peut être un centre de données, un dossier ou un hôte ESXi.
2. Vous ne pouvez sélectionner qu’une étendue à la fois. Pour sélectionner plusieurs machines virtuelles, vous pouvez effectuer une détection, puis redémarrer le processus de détection avec une nouvelle étendue.
3. Vous ne pouvez sélectionner qu’une étendue ayant *moins de 1 500 machines virtuelles*.

## <a name="specify-migration-project"></a>Spécifier un projet de migration

Une fois que vous avez connecté le serveur VCenter local et spécifié une étendue, vous pouvez spécifier les détails du projet de migration qui doivent être utilisés pour la détection et l’évaluation. Spécifiez l’ID et la clé de projet et connectez-vous.

## <a name="start-discovery-and-view-collection-progress"></a>Démarrer la détection et afficher la progression de la collecte

Une fois que la détection est démarrée, les machines virtuelles vCenter sont détectées, et leurs métadonnées et données de performances sont envoyées au serveur. L’état d’avancement vous informe également des ID suivants :

1. ID du collecteur : ID unique attribué à votre machine Collecteur. Cet ID ne change pas entre les différentes détections pour une machine donnée. Vous pouvez l’utiliser en cas de défaillances lorsque vous signalez le problème au Support Microsoft.
2. ID de session : ID unique pour le travail de collecte en cours d’exécution. Vous pouvez désigner le même ID de session dans le portail à l’issue du travail de détection. Cet ID change pour chaque travail de collecte. En cas de défaillances, vous pouvez signaler cet ID au Support Microsoft.

### <a name="what-data-is-collected"></a>Quelles sont les données collectées ?

Le travail de collecte détecte les métadonnées statiques suivantes sur les machines virtuelles sélectionnées.

1. Nom d’affichage de machine virtuelle (sur vCenter)
2. Chemin d’accès de l’inventaire de machine virtuelle (hôte/dossier dans vCenter)
3. Adresse IP
4. Adresse MAC
5. Système d’exploitation
5. Nombre de cœurs, disques, cartes réseau
6. Taille de la mémoire, taille des disques
7. Compteurs de performances de la machine virtuelle, du disque et du réseau indiqués dans le tableau ci-dessous.

Le tableau ci-dessous répertorie les compteurs de performances qui sont collectés et répertorie également les résultats d’évaluation qui sont affectés si un compteur particulier n’est pas collecté.

|Compteur                                  |Level    |Niveau par appareil  |Évaluation de l'impact                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |N/D                |Taille de machine virtuelle recommandée et coût                    |
|mem.usage.average                        | 1       |N/D                |Taille de machine virtuelle recommandée et coût                    |
|virtualDisk.read.average                 | 2       |2                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.write.average                | 2       |2                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|net.received.average                     | 2       |3                 |Taille de la machine virtuelle et coût du réseau                        |
|net.transmitted.average                  | 2       |3                 |Taille de la machine virtuelle et coût du réseau                        |

> [!WARNING]
> Si vous venez de définir un niveau de statistiques plus élevé, la génération des compteurs de performances peut prendre jusqu’à 24 heures. Par conséquent, il est recommandé d’attendre 24 heures avant d’exécuter la découverte.

### <a name="time-required-to-complete-the-collection"></a>Temps nécessaire à la collecte

Le Collecteur détecte uniquement les données de la machine et les envoie au projet. Le projet peut nécessiter plus de temps avant que les données détectées ne soient affichées sur le portail et que vous ne puissiez commencer à créer une évaluation.

En fonction du nombre de machines virtuelles comprises dans l’étendue sélectionnée, l’envoi de métadonnées statiques au projet peut prendre jusqu’à 15 minutes. Une fois que les métadonnées statiques sont disponibles sur le portail, vous pouvez afficher la liste des machines dans le portail et commencer à créer des groupes. Aucune évaluation ne peut être créée tant que le travail de collecte n’est pas terminé et que le projet n’a pas traité les données. Une fois le travail de collecte terminé sur le Collecteur, une heure peut être nécessaire pour que les données de performance soient disponibles sur le portail, en fonction du nombre de machines virtuelles comprises dans l’étendue sélectionnée.

## <a name="locking-down-the-collector-appliance"></a>Verrouillage de l’appliance collecteur
Nous recommandons d’exécuter des mises à jour Windows continues sur l’appliance collecteur. Si un collecteur n’est pas mis à jour pendant 60 jours, il amorcera l’arrêt automatique de la machine. Si une détection est en cours d’exécution, la machine ne sera pas désactivée, même au-delà de la période de 60 jours. Une fois la publication du travail de détection terminée, la machine est désactivée. Si vous utilisez le collecteur pendant plus de 45 jours, nous vous recommandons de conserver la machine à jour à tout moment en exécutant la mise à jour de Windows.

Nous vous recommandons également de suivre les étapes ci-dessous pour sécuriser votre appliance
1. Ne partagez pas les mots de passe administrateur avec des tiers non autorisés et ne les égarez pas non plus.
2. Arrêtez l’appliance lorsqu’elle est inutilisée.
3. Placez l’appliance sur un réseau sécurisé.
4. Une fois le travail de migration terminé, supprimez l’instance de l’appliance. Veillez à supprimer également les fichiers de sauvegarde du disque (VMDK), puisqu’il peut se trouver des informations d’identification de vCenter mis en cache sur les disques.

## <a name="how-to-upgrade-collector"></a>Procédure de mise à niveau du collecteur

Vous pouvez mettre à niveau le collecteur vers la version la plus récente sans télécharger à nouveau le fichier OVA.

1. Téléchargez la dernière version du [package de mise à niveau](https://aka.ms/migrate/col/upgrade_9_14) (1.0.9.14).
2. Pour vous assurer que le correctif logiciel téléchargé est sécurisé, ouvrez la fenêtre de commande d’administrateur et exécutez la commande suivante pour générer le hachage du fichier ZIP. Le hachage généré doit correspondre au hachage mentionné pour la version spécifique :

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exemple d’utilisation C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copiez le fichier zip sur la machine virtuelle du collecteur Azure Migrate (appliance collecteur).
4. Cliquez avec le bouton droit sur le fichier zip et sélectionnez Tout extraire.
5. Cliquez avec le bouton droit sur Setup.ps1 et sélectionnez Exécuter avec PowerShell et suivez les instructions à l’écran pour installer la mise à jour.

### <a name="list-of-updates"></a>Liste des mises à jour

#### <a name="upgrade-to-version-10914"></a>Mise à niveau vers la version 1.0.9.14

Valeurs de hachage pour le [package de mise à niveau 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="upgrade-to-version-10913"></a>Mise à niveau vers la version 1.0.9.13

Valeurs de hachage pour le [package de mise à niveau 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Mise à niveau vers la version 1.0.9.11

Valeurs de hachage pour le [package de mise à niveau 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Mise à niveau vers la version 1.0.9.7

Valeurs de hachage pour le [package de mise à niveau 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Étapes suivantes

[Configurer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
