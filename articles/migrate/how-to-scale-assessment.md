---
title: Mettre à l’échelle la découverte et l’évaluation avec Azure Migrate | Microsoft Docs
description: Décrit comment évaluer un grand nombre de machines locales avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: d7814b976529bf7032edd54e4afd574ce766e5dd
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919860"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Découvrir et évaluer un environnement VMware de grande taille

Une limite de 1 500 machines par projet s’applique pour Azure Migrate. Cet article décrit comment évaluer un grand nombre de machines virtuelles locales avec [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Prérequis

- **VMware** : les machines virtuelles à migrer doivent être gérées par vCenter Server version 5.5, 6.0 ou 6.5. De plus, vous avez besoin d’un hôte ESXi exécutant la version 5.0 ou ultérieure pour déployer la machine virtuelle du collecteur.
- **Compte vCenter** : vous avez besoin d’un compte en lecture seule pour accéder à vCenter Server. Azure Migrate utilise ce compte pour découvrir les machines virtuelles sur site.
- **Autorisations** : dans vCenter Server, vous devez disposer des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA.
- **Paramètres de statistiques** : vous devez définir les paramètres de statistiques de vCenter Server sur le niveau 3 avant de démarrer le déploiement. Si le niveau appliqué est inférieur à 3, l’évaluation est effectuée, mais les données de performances relatives au stockage et au réseau ne sont pas collectées. Dans ce cas, les recommandations de taille sont effectuées selon les données de performances du processeur et de la mémoire, et selon les données de configuration des adaptateurs de disque dur et des adaptateurs réseau.


### <a name="set-up-permissions"></a>Définir des autorisations

Azure Migrate doit accéder à des serveurs VMware pour découvrir automatiquement les machines virtuelles à évaluer. Le compte VMware nécessite les autorisations suivantes :

- Type d’utilisateur : au moins un utilisateur en lecture seule
- Autorisations : objet de centre de données > Propager vers l’objet enfant, rôle = lecture seule
- Détails : l’utilisateur est affecté au niveau du centre de données et a accès à tous les objets du centre de données.
- Pour restreindre l’accès, attribuez le rôle Aucun accès avec l’autorisation Propager vers l’objet enfant aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

Si vous effectuez un déploiement dans un environnement de locataire, voici une façon de procéder :

1.  Créez un utilisateur par locataire et à l’aide de [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), assignez des autorisations en lecture seule à toutes les machines virtuelles appartenant à un locataire particulier. Utilisez ensuite ces informations d’identification pour la découverte. RBAC garantit que l’utilisateur vCenter correspondant aura accès uniquement aux machines virtuelles spécifique à un locataire.
2. Pour configurer RBAC pour des utilisateurs de locataires différents, procédez comme indiqué dans l’exemple suivant pour Utilisateur#1 et Utilisateur#2 :

    - Dans **User name** (Nom d’utilisateur) et **Password** (Mot de passe), spécifiez les informations d’identification du compte en lecture seule que le collecteur doit utiliser pour découvrir les machines virtuelles dans
    - Datacenter1 - donnez des autorisations en lecture seule pour Utilisateur#1 et Utilisateur#2. Ne propagez pas ces autorisations à tous les objets enfants, car vous allez définir des autorisations sur des machines virtuelles individuelles.

      - MV1 (Locataire#1) (Autorisation en lecture seule pour Utilisateur#1)
      - MV2 (Locataire#1) (Autorisation en lecture seule pour Utilisateur#1)
      - MV3 (Locataire#2) (Autorisation en lecture seule pour Utilisateur#2)
      - MV4 (Locataire#2) (Autorisation en lecture seule pour Utilisateur#2)

   - Si vous effectuez la découverte à l’aide des informations d’identification d’Utilisateur#1, seules MV1 et MV2 seront détectées.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planifier vos projets de migration et détections

Un collecteur Azure Migrate unique prend en charge la détection depuis plusieurs serveurs vCenter Servers (l’un après l’autre), ainsi que la détection vers des projets de migration multiples (l’un après l’autre). Le collecteur fonctionne selon un modèle sans réponse : une fois une détection terminée, vous pouvez utiliser le même collecteur pour récupérer des données à partir d’un autre serveur vCenter Server ou les envoyer vers un projet de migration différent.

Planifiez vos découvertes et vos évaluations en fonction des contraintes suivantes :

| **Entité** | **Limite de la machine** |
| ---------- | ----------------- |
| projet    | 1 500             |
| Découverte  | 1 500             |
| Évaluation | 1 500             |

Pour la planification, gardez à l’esprit les éléments suivants :

- Lorsque vous effectuez une découverte à l’aide d’Azure Migrate Collector, vous pouvez définir l’étendue de la découverte sur un dossier vCenter Server, un centre de données, un cluster ou un hôte.
- Pour effectuer plusieurs découvertes, vérifiez dans vCenter Server que les machines virtuelles que vous souhaitez découvrir se trouvent dans des dossiers, des centres de données, des clusters ou des hôtes qui prennent en charge la limite de 1 500 machines.
- À des fins d’évaluation, nous vous recommandons de conserver les machines ayant des interdépendances au sein d’un même projet et d’une même évaluation. Dans vCenter Server, vérifiez que les machines dépendantes se trouvent dans le même dossier, centre de données ou cluster, pour l’évaluation.

Selon votre scénario, vous pouvez fractionner vos détections comme indiqué ci-dessous :

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Plusieurs serveurs vCenter Servers avec moins de 1 500 machines virtuelles

Si vous avez plusieurs serveurs vCenter Servers dans votre environnement et moins de 1 500 machines virtuelles, vous pouvez utiliser un seul collecteur et un seul projet de migration pour détecter toutes les machines virtuelles sur l’ensemble des serveurs vCenter Servers. Étant donné que le collecteur détecte un serveur vCenter Server à la fois, vous pouvez exécuter le même collecteur sur tous les serveurs vCenter Servers, l’un après l’autre, et pointer le collecteur vers le même projet de migration. Une fois toutes les détections terminées, vous pouvez créer des évaluations pour les machines.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Plusieurs serveurs vCenter Servers avec plus de 1 500 machines virtuelles

Si vous avez plusieurs serveurs vCenter Servers avec mois de 1 500 machines virtuelles par serveur vCenter Server, mais plus de 1 500 machines virtuelles sur tous les serveurs vCenter Servers, vous devez créer plusieurs projets de migration (un projet de migration peut accueillir 1 500 machines virtuelles uniquement). Pour ce faire, il vous suffit de créer un projet de migration par serveur vCenter Server et de fractionner les détections. Vous pouvez utiliser un seul collecteur pour détecter chaque serveur vCenter Server (l’un après l’autre). Si vous souhaitez que les détections commencent simultanément, vous pouvez également déployer plusieurs appliances et exécuter les détections en parallèle.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Plus de 1 500 machines dans un seul serveur vCenter Server

Si vous avez plus de 1 500 machines virtuelles dans un seul serveur vCenter Server, vous devez fractionner la détection en plusieurs projets de migration. Pour fractionner les détections, vous pouvez utiliser le champ Étendue de l’appliance et spécifier l’hôte, le cluster ou le centre de données à détecter. Par exemple, si vous avez deux dossiers dans le serveur vCenter Server, un avec 1 000 machines virtuelles (Folder1) et l’autre avec 800 machines virtuelles (Folder2), vous pouvez utiliser un seul collecteur et effectuer deux détections. Dans la première détection, vous pouvez spécifier Folder1 en tant qu’étendue et la pointer vers le premier projet de migration. Une fois que la détection est terminée, vous pouvez utiliser le même collecteur, modifier son étendue en la remplaçant par Folder2, ainsi que les informations associées au projet de migration pour qu’elles correspondent au deuxième projet de migration et effectuer la deuxième détection.

### <a name="multi-tenant-environment"></a>Environnement multilocataire

Si vous avez un environnement qui est partagé entre des locataires et que vous ne voulez pas détecter les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, vous pouvez utiliser le champ Étendue dans l’appliance du collecteur pour limiter la détection. Si les locataires ont des hôtes en commun, créez des informations d’identification qui ont un accès en lecture seule uniquement aux machines virtuelles appartenant au locataire spécifique, puis utilisez ces informations d’identification dans l’appliance du collecteur et spécifiez comme étendue l’hôte où effectuer la détection. Vous pouvez aussi créer des dossiers dans le serveur vCenter Server (par exemple folder1 pour tenant1 et folder2 pour tenant2) sous l’hôte partagé, déplacer les machines virtuelles pour tenant1 dans folder1 et pour tenant2 dans folder2, puis limiter en conséquence les détections dans le collecteur en spécifiant le dossier approprié.

## <a name="discover-on-premises-environment"></a>Détecter un environnement local

Une fois votre plan défini, vous pouvez commencer le processus de détection des machines virtuelles locales :

### <a name="create-a-project"></a>Création d’un projet

Créez un projet Azure Migrate en fonction de vos besoins :

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Recherchez **Azure Migrate**, puis sélectionnez le service **Azure Migrate (préversion)** dans les résultats de la recherche. Sélectionnez ensuite **Créer**.
3. Spécifiez un nom pour le projet, ainsi que l’abonnement Azure associé.
4. Créez un groupe de ressources.
5. Spécifiez l’emplacement auquel vous souhaitez créer le projet, puis sélectionnez **Créer**. Notez que vous pouvez toujours évaluer les machines virtuelles si elles sont situées dans un autre emplacement cible. L’emplacement spécifié pour le projet est utilisé pour stocker les métadonnées collectées à partir des machines virtuelles locales.

### <a name="set-up-the-collector-appliance"></a>Configuration de l’appliance collecteur

Azure Migrate crée une machine virtuelle sur site connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware locales et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, vous téléchargez un fichier .OVA, puis vous l’importez dans l’instance locale de vCenter Server.

#### <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Si vous avez plusieurs projets, vous n’avez besoin de télécharger l’appliance collecteur qu’une seule fois dans vCenter Server. Une fois l’appliance téléchargée et configurée, vous l’exécutez pour chaque projet et spécifiez l’ID et la clé uniques du projet.

1. Dans le projet Azure Migrate, sélectionnez **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Découvrir des machines**, sélectionnez **Télécharger** pour télécharger le fichier .OVA.
3. Dans **Copier les informations d’identification du projet**, copiez l’ID et la clé du projet. Vous en aurez besoin pour la configuration du collecteur.


#### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Vérifiez que le fichier .OVA est sécurisé avant de le déployer :

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.

2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Vérifiez que le hachage généré correspond aux paramètres suivants.

    Pour OVA version 1.0.9.12

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

    Pour OVA version 1.0.9.8

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Pour OVA version 1.0.9.7

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Pour OVA version 1.0.9.5

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Pour OVA version 1.0.9.2

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

### <a name="create-the-collector-vm"></a>Créer la machine virtuelle collector

Importez le fichier téléchargé dans vCenter Server :

1. Dans la console du client vSphere, sélectionnez **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle collector et l’objet d’inventaire destiné à héberger la machine virtuelle.
4. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle collector.
5. Dans Storage (Stockage), spécifiez la destination de stockage pour la machine virtuelle collector.
6. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
7. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle collector. Le réseau nécessite une connexion à Internet pour envoyer des métadonnées vers Azure.
8. Validez les paramètres, puis sélectionnez **Finish (Terminer)**.

### <a name="identify-the-id-and-key-for-each-project"></a>Identifier l’ID et la clé de chaque projet

Si vous avez plusieurs projets, identifiez l’ID et la clé de chacun d’eux. Vous avez besoin de la clé lorsque vous exécutez le collecteur pour découvrir les machines virtuelles.

1. Dans le projet, sélectionnez **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Copier les informations d’identification du projet**, copiez l’ID et la clé du projet.
    ![Copier les informations d’identification du projet](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Définir le niveau de statistiques de vCenter
Voici la liste des compteurs de performances dont les données ont été collectées lors de la découverte. Par défaut, les compteurs sont disponibles à différents niveaux dans vCenter Server.

Nous vous recommandons de définir le niveau commun le plus élevé (niveau 3) pour les statistiques, afin que les données de tous les compteurs soient correctement collectées. Si vCenter est défini sur un niveau inférieur, il se peut que seuls quelques compteurs voient l’intégralité de leurs données collectées, les autres étant définis sur 0. L’évaluation peut donc afficher des données incomplètes.

Le tableau ci-dessous répertorie également les résultats d’évaluation qui sont impactés si les données d’un compteur particulier ne sont pas collectées.

| Compteur                                 | Level | Niveau par appareil | Évaluation de l'impact                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| cpu.usage.average                       | 1     | N/D               | Taille de machine virtuelle recommandée et coût         |
| mem.usage.average                       | 1     | N/D               | Taille de machine virtuelle recommandée et coût         |
| virtualDisk.read.average                | 2     | 2                | Taille du disque, coût de stockage et taille de la machine virtuelle |
| virtualDisk.write.average               | 2     | 2                | Taille du disque, coût de stockage et taille de la machine virtuelle |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Taille du disque, coût de stockage et taille de la machine virtuelle |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Taille du disque, coût de stockage et taille de la machine virtuelle |
| net.received.average                    | 2     | 3                | Taille de la machine virtuelle et coût du réseau             |
| net.transmitted.average                 | 2     | 3                | Taille de la machine virtuelle et coût du réseau             |

> [!WARNING]
> Si vous venez de définir un niveau de statistiques plus élevé, la génération des compteurs de performances peut prendre jusqu’à 24 heures. Par conséquent, il est recommandé d’attendre 24 heures avant d’exécuter la découverte.

### <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

Pour chaque découverte à effectuer, vous devez exécuter le collecteur afin de découvrir les machines virtuelles de l’étendue spécifiée. Exécutez les découvertes les unes après les autres. Les découvertes simultanées ne sont pas prises en charge, et chaque découverte doit avoir une portée différente.

1.  Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2.  Indiquez les préférences de langue, de fuseau horaire et de mot de passe de l’appliance.
3.  Sur le Bureau, sélectionnez le raccourci **Run collector** (Exécuter le collecteur).
4.  Dans Azure Migrate Collector, ouvrez **Set Up Prerequisites** (Configurer les prérequis).

    a. Acceptez les termes de licence et lisez les informations relatives aux tiers.

    Le collecteur vérifie que la machine virtuelle a accès à Internet.

    b. Si la machine virtuelle accède à Internet via un proxy, sélectionnez **Proxy settings** (Paramètres du proxy), puis spécifiez l’adresse du proxy et le port d’écoute. Spécifiez les informations d’identification si le proxy nécessite une authentification.

    Le collecteur vérifie que le service Collector est en cours d’exécution. Le service est installé par défaut sur la machine virtuelle collector.

    c. Téléchargez et installez VMware PowerCLI.

5.  Dans **Specify vCenter Server details** (Spécifier les informations vCenter Server), procédez aux étapes suivantes :
    - Spécifiez le nom (FQDN) ou l’adresse IP de l’instance vCenter Server.
    - Dans **User name** (Nom d’utilisateur) et **Password** (Mot de passe), spécifiez les informations d’identification du compte en lecture seule que le collecteur doit utiliser pour découvrir les machines virtuelles dans vCenter Server.
    - Dans **Select scope** (Sélectionner une étendue), sélectionnez une étendue pour la découverte des machines virtuelles. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. L’étendue ne doit pas contenir plus de 1 000 machines virtuelles.

6.  Dans **Specify migration project** (Spécifier un projet de migration), spécifiez l’ID et la clé du projet. Si vous ne les avez pas copiés, ouvrez le portail Azure à partir de la machine virtuelle collecteur. Dans la page **Vue d’ensemble** du projet, sélectionnez **Découvrir des machines**, puis copiez les valeurs.  
7.  Dans **View collection progress** (Afficher la progression de la collecte), surveillez le processus de découverte et vérifiez que les métadonnées collectées à partir des machines virtuelles appartiennent à l’étendue spécifiée. Le collecteur fournit une durée approximative de la découverte.


#### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

La durée de la découverte varie selon le nombre de machines virtuelles découvertes. En règle générale, pour 100 machines virtuelles, la découverte prend environ une heure après exécution du collecteur.

1. Dans le projet Azure Migrate, sélectionnez **Gérer** > **Machines**.
2. Vérifiez que les machines virtuelles que vous souhaitez découvrir apparaissent dans le portail.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un groupe](how-to-create-a-group.md) pour l'évaluation.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
