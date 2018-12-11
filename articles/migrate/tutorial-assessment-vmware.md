---
title: Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure avec Azure Migrate | Microsoft Docs
description: Décrit comment découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure, à l’aide du service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dddfbab1d40c03659ba346c9f0e898cfefc8d55e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847981"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure.

Le service [Azure Migrate](migrate-overview.md) évalue les charges de travail locales pour la migration vers Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte utilisé par Azure Migrate pour découvrir les machines virtuelles locales
> * Créer un projet Azure Migrate.
> * Configurer une machine virtuelle collector locale, pour découvrir les machines virtuelles VMware locales en vue de les évaluer.
> * Regrouper les machines virtuelles et créer une évaluation.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- **VMware** : les machines virtuelles à migrer doivent être gérées par un vCenter Server exécutant la version 5.5, 6.0 ou 6.5. De plus, vous avez besoin d'un hôte ESXi exécutant la version 5.0 ou ultérieure pour déployer la machine virtuelle du collecteur.
- **Compte de serveur vCenter** : vous avez besoin d'un compte en lecture seule pour accéder au serveur vCenter. Azure Migrate utilise ce compte pour découvrir les machines virtuelles sur site.
- **Autorisations** : sur le serveur vCenter, vous devez disposer des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA.

## <a name="create-an-account-for-vm-discovery"></a>Créer un compte pour la découverte de machine virtuelle

Azure Migrate doit accéder à des serveurs VMware pour découvrir automatiquement les machines virtuelles à évaluer. Créez un compte VMware avec les propriétés suivantes. Vous devez spécifier ce compte pendant la configuration d’Azure Migrate.

- Type d’utilisateur : au moins un utilisateur en lecture seule
- Autorisations : objet de centre de données > Propager vers l’objet enfant, rôle = lecture seule
- Détails : l’utilisateur est affecté au niveau du centre de données et a accès à tous les objets du centre de données.
- Pour restreindre l’accès, attribuez le rôle Aucun accès avec l’objet Propager vers l’objet enfant défini sur les objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).


## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Création d’un projet

1. Dans le portail Azure, cliquez sur **Créer une ressource**.
2. Recherchez **Azure Migrate**, puis sélectionnez le service **Azure Migrate** dans les résultats de la recherche. Cliquez ensuite sur **Créer**.
3. Spécifiez un nom de projet et l’abonnement Azure pour le projet.
4. Créez un groupe de ressources.
5. Spécifiez la zone géographique dans laquelle vous souhaitez créer le projet, puis cliquez sur **Créer**. Vous ne pouvez créer un projet Azure Migrate que dans la zone géographique États-Unis. Toutefois, vous pouvez toujours planifier votre migration pour n'importe quel emplacement Azure cible. La zone géographique spécifiée pour le projet est utilisée uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Azure Migrate crée une machine virtuelle locale connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware sur site et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, vous téléchargez un fichier .OVA, puis vous l’importez dans le serveur vCenter local pour créer la machine virtuelle.

1. Dans le projet Azure Migrate, cliquez sur **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Découvrir des machines**, cliquez sur **Télécharger** pour télécharger l’appliance.

    L’appliance Azure Migrate communique avec vCenter Server et profile en continu l’environnement local afin de recueillir des données d’utilisation en temps réel pour chaque machine virtuelle. Elle collecte les données des compteurs de valeurs maximales pour chaque métrique (utilisation du processeur, utilisation de la mémoire etc.). Ce modèle ne dépend pas des paramètres de statistiques de vCenter Server pour la collecte des données de performances. Vous pouvez arrêter à tout moment le profilage continu à partir de l’appliance.

    > [!NOTE]
    > L’appliance de découverte unique est désormais dépréciée, car son utilisation dépend des paramètres de statistiques vCenter Server concernant la disponibilité des points de données de performances, et nécessite la collecte des données de compteurs de performance moyenne, ce qui a comme résultat d’attribuer une taille insuffisante aux machines virtuelles pour la migration vers Azure.

    **Résultats instantanés :** avec l’application de découverte continue, une fois la découverte terminée (au bout de deux heures environ, selon le nombre de machines virtuelles), vous pouvez créer immédiatement des évaluations. Étant donné que la collecte des données de performances démarre lorsque vous lancez la découverte, si vous voulez obtenir des résultats instantanément, vous devez sélectionner le critère de dimensionnement *Localement* dans l’évaluation. Pour les évaluations de performances, il est conseillé d’attendre au moins un jour après le lancement de la découverte afin d’obtenir des recommandations de taille fiables.

    L’appliance collecte uniquement les données de performances en continu. Elle ne détecte pas les changements de configuration dans l’environnement local (par exemple, ajout ou suppression de machine virtuelle, ajout de disque etc.). En cas de modification de configuration de l’environnement local, vous pouvez procéder aux opérations suivantes pour refléter les modifications dans le portail :

    - Ajout d’éléments (machines virtuelles, disques, cœurs, etc.) : pour refléter ces modifications dans le portail Azure, vous pouvez arrêter la détection de l’appliance et puis la redémarrer. Cela garantit que les modifications sont mises à jour dans le projet Azure Migrate.

    - Suppression de machines virtuelles : en raison de la façon dont l’appliance est conçue, la suppression de machines virtuelles n’apparaît pas même si vous arrêtez et redémarrez la détection. Cela est dû au fait que les données de détections ultérieures sont ajoutées, et non pas remplacées, aux détections plus anciennes. Dans ce cas, vous pouvez simplement ignorer la machine virtuelle dans le portail en la supprimant de votre groupe et en recalculant l’évaluation.


3. Dans **Copier les informations d’identification du projet**, copiez l’ID de projet et la clé. Vous en aurez besoin pour la configuration du collecteur.

    ![Télécharger le fichier .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Vérifiez que le fichier .OVA est sécurisé, avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Le code de hachage généré doit correspondre aux paramètres ci-après.

#### <a name="continuous-discovery"></a>Détection continue

  Pour OVA version 1.0.10.4

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Découverte unique (désormais dépréciée)

Ce modèle est désormais déprécié, cependant, les appliances existantes continuent d’être prises en charge.

  Pour OVA version 1.0.9.15

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Pour OVA version 1.0.9.14

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Pour OVA version 1.0.9.12

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

## <a name="create-the-collector-vm"></a>Créer la machine virtuelle collector

Importez le fichier téléchargé sur le serveur vCenter.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .ova.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle collector et l’objet d’inventaire destiné à héberger la machine virtuelle.
5. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle collector.
7. Dans Storage (Stockage), spécifiez la destination de stockage pour la machine virtuelle collector.
8. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
9. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle collector. Le réseau requiert une connexion à Internet pour envoyer des métadonnées vers Azure.
10. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).

## <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Indiquez les préférences de langue, de fuseau horaire et de mot de passe de l’appliance.
3. Sur le bureau, cliquez sur le raccourci **Run collector** (Exécuter le collecteur).
4. Cliquez sur **Vérifier les mises à jour** dans la barre supérieure de l’interface utilisateur du collecteur et vérifiez que le collecteur est exécuté sur la version la plus récente. Si ce n’est pas le cas, vous pouvez choisir de télécharger le dernier package de mise à niveau à partir du lien et mettre à jour le collecteur.
5. Dans Azure Migrate Collector, ouvrez **Set Up Prerequisites** (Configurer les prérequis).
    - Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - Le collecteur vérifie que la machine virtuelle a accès à Internet.
    - Si la machine virtuelle accède à internet via un proxy, cliquez sur **Proxy settings** (Paramètres du proxy) et spécifiez l’adresse du proxy et le port d’écoute. Spécifiez les informations d’identification si le proxy nécessite une authentification. [En savoir plus](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity) sur les exigences de connectivité Internet et la liste des URL auxquelles le collecteur accède.

    > [!NOTE]
    > L’adresse proxy doit être saisie dans le formulaire http://ProxyIPAddress ou http://ProxyFQDN. Seuls les proxys HTTP sont pris en charge. Si vous disposez d’un proxy d’interception, il est possible que la connexion Internet échoue à l’origine si vous n’avez pas importé le certificat de proxy ; [apprenez-en davantage](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity-with-intercepting-proxy) sur la manière dont vous pouvez résoudre ce problème en important le certificat de proxy en tant que certificat approuvé sur la machine virtuelle collector.

    - Le collecteur vérifie que le service Collector est en cours d’exécution. Le service est installé par défaut sur la machine virtuelle collector.
    - Téléchargez et installez VMware PowerCLI.

6. Dans **Specify vCenter Server details** (Spécifier les informations vCenter Server), procédez aux étapes suivantes :
    - Spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter.
    - Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte en lecture seule que le collecteur utilisera pour découvrir les machines virtuelles sur le serveur vCenter.
    - Dans **Étendue de la collecte**, sélectionnez une étendue pour la découverte des machines virtuelles. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. Elle ne doit pas contenir plus de 1 500 machines virtuelles. [En savoir plus](how-to-scale-assessment.md) sur la manière dont vous pouvez découvrir un environnement plus large.

7. Dans **Specify migration project** (Spécifier un projet de migration), spécifiez l’ID et la clé du projet Azure Migrate que vous avez copiés à partir du portail. Si vous ne les avez pas copiés, ouvrez le portail Azure à partir de la machine virtuelle collector. Dans la page **Vue d’ensemble** du projet, cliquez sur **Découvrir des machines**, puis copiez les valeurs.  
8. Dans **Afficher la progression de la collecte**, surveillez l’état de la détection. [En savoir plus](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected) sur les données collectées par le collecteur Azure Migrate.

> [!NOTE]
> Le collecteur prend uniquement en charge « Anglais (États-Unis) » comme langue du système d’exploitation et langue de l’interface du collecteur.
> Si vous modifiez les paramètres d’un ordinateur que vous souhaitez évaluer, relancez une détection avant d’exécuter l’évaluation. Dans le collecteur, utilisez l’option **Redémarrer la collecte** pour ce faire. Une fois la collection terminée, sélectionnez l’option **Recalculer** pour effectuer l’évaluation dans le portail, pour obtenir des résultats d’évaluation de mise à jour.


### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

L’appliance du collecteur va profiler en continu l’environnement local et va envoyer en continu les données de performances, heure par heure. Vous pouvez afficher les machines dans le portail une heure après le lancement de la découverte.

1. Dans le projet de migration, cliquez sur **Gérer** > **Machines**.
2. Vérifiez que les machines virtuelles que vous souhaitez découvrir apparaissent dans le portail.


## <a name="create-and-view-an-assessment"></a>Créer et afficher une évaluation

Une fois les machines virtuelles découvertes dans le portail, vous pouvez les regrouper pour créer une évaluation. Vous pouvez créer des évaluations locales dès que les machines virtuelles sont découvertes. Il est recommandé d’attendre au moins un jour avant de créer des évaluations basées sur les performances pour obtenir des recommandations de taille fiables.

1. Dans la page **Vue d’ensemble** du projet, cliquez sur **Créer une évaluation**.
2. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.
3. Créez le groupe et spécifiez un nom de groupe.
4. Sélectionnez les machines que vous souhaitez ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe et l’évaluation.
6. Une fois l’évaluation créée, affichez-la dans **Vue d’ensemble** > **Tableau de bord**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.

> [!NOTE]
> Il est fortement recommandé d’attendre au moins un jour, après le lancement de la découverte, avant de créer une évaluation. Si vous souhaitez mettre à jour une évaluation existante avec les dernières données de performances, vous pouvez utiliser la commande **Recalculer** sur l’évaluation afin de la mettre à jour.

### <a name="assessment-details"></a>Détails de l’évaluation

Une évaluation inclut des informations complémentaires sur la compatibilité des machines virtuelles locales pour Azure, la taille idéale de machine virtuelle pour l’exécution de la machine virtuelle dans Azure, et l’estimation des coûts Azure mensuels.

![Rapport d’évaluation](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Préparé pour Azure

La vue Préparé pour Azure de l’évaluation indique l’état de préparation de chaque machine virtuelle. En fonction des propriétés de la machine virtuelle, chaque machine virtuelle peut être marquée comme suit :
- Préparé pour Azure
- Préparé pour Azure sous condition
- Non préparé pour Azure
- Préparation inconnue

Pour les machines virtuelles qui sont prêtes, Azure Migrate recommande une taille de machine virtuelle dans Azure. La recommandation de taille effectuée par Azure Migrate varie selon le critère de dimensionnement spécifié dans les propriétés de l’évaluation. Si le critère de dimensionnement est le dimensionnement basé sur les performances, la recommandation de taille tient compte de l’historique des performances des machines virtuelles (UC et mémoire) et des disques (IOPS et débit). Si le critère de dimensionnement est « local », Azure Migrate ne considère pas les données de performances pour la machine virtuelle et les disques. La recommandation pour la taille de la machine virtuelle dans Azure est effectuée en examinant la taille de la machine virtuelle locale et le dimensionnement du disque est effectué en fonction du type de stockage spécifié dans les propriétés de l’évaluation (disques premium est la valeur par défaut). [En savoir plus](concepts-assessment-calculation.md) sur le déroulement du dimensionnement dans Azure Migrate.

Pour les machines virtuelles qui ne sont pas préparées ou qui sont préparées sous condition pour Azure, Azure Migrate explique les problèmes de préparation et fournit la procédure de correction.

Les machines virtuelles pour lesquelles Azure Migrate ne peut pas identifier la préparation pour Azure (en raison de l’indisponibilité des données) sont marquées de la valeur Préparation inconnue.

En plus de la préparation Azure et du dimensionnement, Azure Migrate suggère des outils que vous pouvez utiliser pour migrer la machine virtuelle. Cela requiert une découverte plus approfondie que dans l’environnement local. [En savoir plus](how-to-get-migration-tool.md) sur la procédure à suivre pour effectuer une découverte plus approfondie en installant des agents sur les machines locales. Si les agents ne sont pas installés sur les machines locales, la migration lift-and-shift est suggérée à l’aide [d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Si les agents sont installés sur la machine locale, Azure Migrate examine les processus exécutés à l’intérieur de la machine et indique si celle-ci est une machine de base de données ou non. Si la machine est une machine de base de données, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) est suggéré ; autrement Azure Site Recovery est suggéré en tant qu’outil de migration.

  ![Préparation de l’évaluation](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimation des coûts mensuels

Cette vue affiche le coût total du calcul et du stockage de l'exécution des machines virtuelles dans Azure avec les détails pour chaque machine. Les estimations des coûts sont calculées en tenant compte des recommandations de taille d’Azure Migrate pour une machine, ses disques et les propriétés de l’évaluation.

> [!NOTE]
> L'estimation des coûts fournie par Azure Migrate s'applique à l'exécution des machines virtuelles sur site en tant que machines virtuelles Azure Infrastructure as a service (IaaS). Azure Migrate ne tient pas compte des coûts Platform as a service (PaaS) ou Software as a service (SaaS).

L’estimation des coûts mensuels de calcul et de stockage est agrégée pour toutes les machines virtuelles dans le groupe.

![Évaluation des coûts pour les machines virtuelles](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Niveau de confiance

Chaque évaluation reposant sur des performances dans Azure Migrate est associée à un niveau de confiance allant de 1 étoile à 5 étoiles (1 étoile constituant la valeur la plus faible, et 5 étoiles la valeur la plus élevée). Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation. Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate. Le niveau de confiance n’est pas applicable aux évaluations locales.

Pour le dimensionnement basé sur les performances, Azure Migrate a besoin des données d’utilisation relatives au processeur et à la mémoire de la machine virtuelle. Par ailleurs, pour chaque disque attaché à la machine virtuelle, les E/S par seconde du disque et les données de débit sont nécessaires. De même, pour chaque carte réseau jointe à une machine virtuelle, Azure Migrate doit permettre au réseau entrant/sortant de respecter un dimensionnement basé sur les performances. Si aucun des chiffres d’utilisation ci-dessus n’est disponible dans vCenter Server, la recommandation de dimensionnement effectuée par Azure Migrate peut ne pas être fiable. Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni comme suit :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

Tous les points de données d’une évaluation peuvent ne pas être disponibles pour l’une des raisons suivantes :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur 1 jour, vous devez attendre au moins un jour après le démarrage de la découverte pour collecter tous les points de données.

- Plusieurs machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles ont été mises hors tension pendant un certain temps, vous ne pourrez pas collecter les données de performances pour cette période.

- Quelques machines virtuelles ont été créées pendant la période de calcul de l’évaluation. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques machines virtuelles dans l’environnement ne remonte qu’à une semaine. Dans ce cas, l’historique des performances des nouvelles machines virtuelles ne sera pas disponible pour toute la durée définie.

> [!NOTE]
> Si l’indice de confiance d’une évaluation est inférieur à 5 étoiles, attendez au moins un jour que l’appliance profile l’environnement, puis *recalculez* l’évaluation. Si vous ne pouvez pas effectuer ce qui précède, le dimensionnement basé sur les performances est susceptible de manquer de fiabilité ; nous vous recommandons alors de basculer vers le *dimensionnement local* en changeant les propriétés de l’évaluation.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](how-to-modify-assessment.md) sur la manière de personnaliser une évaluation en fonction de vos besoins.
- Découvrez comment créer des groupes d’évaluation à fiabilité élevée à l’aide d’un [mappage de dépendances des machines](how-to-create-group-machine-dependencies.md).
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
- [Découvrez comment détecter et évaluer](how-to-scale-assessment.md) un environnement VMware de grande taille.
- [En savoir plus](resources-faq.md) sur les FAQ sur Azure Migrate
