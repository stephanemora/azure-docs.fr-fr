---
title: Azure Lab Services – Guide de l’administrateur | Microsoft Docs
description: Ce guide aide les administrateurs qui créent et gèrent des comptes Lab à l’aide d’Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad3bc110d93efb5b735f77fb8a0b2af9e4f9a7cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444146"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – Guide de l’administrateur
Les administrateurs des technologies de l’information qui gèrent les ressources cloud d’une université sont généralement responsables de la configuration du compte lab de leur établissement. Une fois un compte lab configuré, les administrateurs ou enseignants créent des laboratoires de salle de classe dans ce compte. Cet article offre une vue d’ensemble globale des ressources Azure en question ainsi que des conseils pour les créer.

![Vue globale des ressources Azure d’un compte Lab](./media/administrator-guide/high-level-view.png)

- Les laboratoires de salle de classe sont hébergés dans un abonnement Azure dont le service Azure Lab Services est propriétaire.
- Les comptes lab, la galerie d’images partagées et les versions d’images sont hébergés dans votre abonnement.
- Le compte lab et la galerie d’images partagées peuvent se trouver dans le même groupe de ressources. Dans ce schéma, ils se trouvent dans des groupes de ressources différents. 

## <a name="subscription"></a>Abonnement
Votre université dispose d’un ou plusieurs abonnements Azure. Un abonnement permet de gérer la facturation et la sécurité de l’ensemble des ressources/services Azure qui y sont utilisés, notamment les comptes Lab.

La relation entre un compte Lab et son abonnement est importante pour les raisons suivantes :

- La facturation est établie via l’abonnement qui contient le compte Lab.
- Vous pouvez accorder aux utilisateurs du locataire Azure Active Directory (AD) de l’abonnement un accès au service Azure Lab Services. Vous pouvez ajouter un utilisateur en tant que propriétaire/contributeur de compte lab, créateur de laboratoire de salle de classe ou propriétaire de laboratoire de salle de classe.

Les laboratoires de salle de classe et leurs machines virtuelles sont gérés et hébergés pour vous dans un abonnement dont le service Azure Lab Services est propriétaire.

## <a name="resource-group"></a>Resource group
Un abonnement contient un ou plusieurs groupes de ressources. Les groupes de ressources servent à créer des regroupements logiques des ressources Azure utilisées ensemble dans une même solution.  

Quand vous créez un compte Lab, vous devez configurer le groupe de ressources qui le contient. 

Un groupe de ressources est également nécessaire pour créer une [galerie d’images partagées](#shared-image-gallery). Vous pouvez choisir de placer votre compte Lab et votre galerie d’images partagées dans deux groupes de ressources distincts, ce qui est normal si vous envisagez de partager la galerie d’images entre différentes solutions. Vous pouvez aussi choisir de les placer dans le même groupe de ressources.

Lorsque vous créez un compte lab, vous pouvez créer et attacher automatiquement une galerie d’images partagées au même moment.  Cette option permet de créer le compte lab et la galerie d’images partagées dans des groupes de ressources distincts. Vous pourrez observer ce comportement en suivant les étapes décrites dans ce tutoriel : [Configurer une galerie d’images partagées pendant la création d’un compte Lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). L’image située dans la partie supérieure de cet article utilise également cette configuration. 

Nous vous recommandons de prendre le temps de réfléchir à la façon de structurer vos groupes de ressources car, une fois le groupe de ressources d’un compte lab ou d’une galerie d’images partagées créé, il n’est *plus* possible de le modifier. Si vous avez besoin de modifier le groupe de ressources pour ces ressources, vous devrez supprimer et recréer votre compte Lab et/ou la galerie d’images partagées.

## <a name="lab-account"></a>Compte Lab

Un compte lab sert de conteneur pour un ou plusieurs laboratoires de salle de classe. Lors des premiers pas avec Azure Lab Services, il est courant de n’avoir qu’un seul compte lab. À mesure que l’utilisation du labo évolue, vous pouvez décider de créer des comptes Lab supplémentaires.

La liste suivante met en avant les scénarios dans lesquels l’existence de plusieurs comptes Lab peut s’avérer bénéfique :

- **Gérer des besoins de stratégies différents entre les labos de classe**

    Quand vous configurez un compte lab, vous définissez des stratégies qui s’appliquent à *tousl* les laboratoires de salle de classe relevant du compte lab, par exemple :
    - Le réseau virtuel Azure avec des ressources partagées auxquelles le labo de classe peut accéder. Par exemple, vous pouvez disposez d’un ensemble de labos de classe qui doit pouvoir accéder à un jeu de données partagées au sein d’un réseau virtuel.
    - Les images de machine virtuelle qui permettent aux labos de classe de créer des machines virtuelles. Par exemple, vous pouvez disposer d’un ensemble de labos de classe qui doivent pouvoir accéder à l’image de la Place de marché [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

    Si vous avez des labos de classe qui ont chacun des besoins en stratégies uniques, il peut être avantageux de créer des comptes Lab distincts pour gérer séparément ces labos de classe.

- **Budget séparé par compte Lab**
  
    Au lieu de signaler les coûts de tous les laboratoires de salle de classe via un seul compte lab, il se peut que vous deviez ventiler le budget plus clairement. Par exemple, vous pouvez créer des comptes lab pour le département mathématiques, le département informatique, ainsi que d’autres départements de votre université, afin de ventiler le budget entre les différents départements.  Vous pouvez alors voir le coût de chaque compte lab en utilisant [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isoler les laboratoires pilotes des laboratoires actifs/de production**
  
    Dans certains cas, vous pouvez piloter des changements de stratégies pour un compte lab sans risquer d’affecter des laboratoires actifs/de production. Dans ce type de scénario, la création d’un compte Lab distinct à des fins de pilotage permet d’isoler les modifications. 

## <a name="classroom-lab"></a>Laboratoire de classe

Un laboratoire de salle de classe contient des machines virtuelles qui sont chacune attribuée à un étudiant déterminé.  En règle générale, vous pouvez vous attendre à ce que :

- Un labo de classe existe pour chaque classe.
- Créez un ensemble de laboratoires de salle de classe chaque semestre (ou pour chaque période pendant laquelle votre classe est proposée). En général, pour des classes qui ont les mêmes besoins en images, vous devez utiliser une [galerie d’images partagées](#shared-image-gallery) afin de réutiliser des images pour divers laboratoires et semestres.

Pour savoir comment structurer vos labos de classe, tenez compte des points suivants :

- **Toutes les machines virtuelles d’un labo de classe sont déployées avec la même image publiée**

    De ce fait, si vous devez publier simultanément plusieurs images de labo pour une classe, des labos de classe distincts devront être créés pour chacune d’elles.
  
- **Un quota d’utilisation est défini au niveau du labo et s’applique à tous ses utilisateurs**

    Pour définir des quotas différents pour les utilisateurs, vous devez créer des labos de classe distincts. Cependant, avant de pouvoir ajouter des heures supplémentaires à un utilisateur déterminé, vous devez avoir défini le quota.
  
- **La planification de démarrage ou d’arrêt est définie au niveau du labo et s’applique à toutes ses machines virtuelles**

    Comme au point précédent, si vous avez besoin de définir des planifications différentes pour les utilisateurs, vous devez créer des labos de classe distincts.

Par défaut, chaque laboratoire de classe aura son propre réseau virtuel.  Si l’homologation de réseaux virtuels est activée, chaque laboratoire de classe disposera de son propre sous-réseau, associé au réseau virtuel spécifié.

## <a name="shared-image-gallery"></a>Galerie d’images partagées

Une galerie d’images partagées est attachée à un compte Lab et sert de référentiel central pour le stockage d’images. Une image est enregistrée dans la galerie dès qu’un enseignant choisit de l’exporter à partir d’un modèle de machine virtuelle de laboratoire de salle de classe. Chaque fois qu’un enseignant apporte des modifications au modèle de machine virtuelle et l’enregistre, une nouvelle version de l’image est enregistrée, tandis que les versions précédentes sont conservées.

Les formateurs peuvent publier une version d’image à partir de la galerie d’images partagées au moment de créer un labo de classe. Même si la galerie stocke plusieurs versions d’une image, les formateurs ne peuvent sélectionner que la dernière version pendant la création du labo.

La galerie d’images partagées est une ressource facultative dont vous n’aurez peut-être pas besoin tout de suite compte tenu du nombre limité de labos de classe que vous aurez au début. Cependant, l’utilisation de la galerie d’images partagées présente de nombreux avantages à mesure que vos besoins évoluent vers davantage de labos de classe :

- **Elle vous permet d’enregistrer et de gérer différentes versions d’une image de modèle de machine virtuelle**

    Cela est utile pour créer une image personnalisée ou apporter des modifications (de logiciels, de configuration, etc.) à une image de la galerie de la Place de marché publique.  Par exemple, il est courant que les formateurs réclament l’installation de différents logiciels/outils. Au lieu de demander aux étudiants d’installer manuellement ces composants requis, il est possible d’exporter différentes versions de l’image du modèle de machine virtuelle dans une galerie d’images partagées. Ces versions d’images peuvent ensuite être utilisées pendant la création de nouveaux labos de classe.
- **Elle permet de partager/réutiliser les images de modèle de machine virtuelle entre les labos de classe**

    Vous pouvez enregistrer et réutiliser une image afin de ne pas avoir à la configurer à partir de rien chaque fois que vous créez un laboratoire de salle de classe. Par exemple, si plusieurs classes proposés ont besoin de la même image, il suffit de créer celle-ci, puis de l’exporter dans la galerie d’images partagées pour que les laboratoires de salle de classe puissent la partager.
- **Elle garantit la disponibilité des images via la réplication**

    Lorsque vous enregistrez une image dans la galerie d’images partagées à partir d’un laboratoire de salle de classe, elle est automatiquement répliquée dans les autres [régions au sein de la même zone géographique](https://azure.microsoft.com/global-infrastructure/regions/). En cas de panne pour une région, la publication de l’image dans votre laboratoire de salle de classe n’est pas affectée, car un réplica d’image d’une autre région peut être utilisé.  La publication de machines virtuelles à partir de plusieurs réplicas peut également contribuer à améliorer les performances.

Pour regrouper logiquement des images partagées, plusieurs options s’offrent à vous :

- Créez plusieurs galeries d’images partagées. Sachant que chaque compte Lab ne peut se connecter qu’à une seule galerie d’images partagées, cette option vous engage aussi à créer plusieurs comptes Lab.
- Vous pouvez aussi utiliser une seule galerie d’images partagées commune à plusieurs comptes Lab. Dans ce cas, chaque compte Lab peut activer uniquement les images applicables aux labos de classe qu’il contient.

## <a name="naming"></a>Dénomination

Durant vos premiers pas avec Azure Lab Services, nous vous recommandons d’établir des conventions d’affectation de noms pour les groupes de ressources, les comptes Lab, les labos de classe et la galerie d’images partagées. Même si les conventions d’affectation de noms que vous établissez sont destinées à répondre aux besoins uniques de votre organisation, le tableau suivant en décrit les principes généraux.

| Type de ressource | Role | Modèle suggéré | Exemples |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contient un ou plusieurs comptes Lab et une ou plusieurs galeries d’images partagées | \<organization short name\>-\<environment\>-rg<ul><li>**Nom abrégé de l’organisation** identifie l’organisation dans laquelle le groupe de ressources est utilisé</li><li>**Environnement** identifie l’environnement de la ressource, par exemple, Pilote ou Production</li><li>**Rg** est l’abréviation du type de ressource : resource group (groupe de ressources).</li></ul> | labosuniversitecontoso-rg<br/>contosouniversitylabs-pilot-rg<br/>labosuniversitecontoso-prod-rg |
| Compte Lab | Contient un ou plusieurs labos | \<organization short name\>-\<environment\>-la<ul><li>**Nom abrégé de l’organisation** identifie l’organisation dans laquelle le groupe de ressources est utilisé</li><li>**Environnement** identifie l’environnement de la ressource, par exemple, Pilote ou Production</li><li>**La** est l’abréviation du type de ressource : lab account (compte Lab).</li></ul> | labosuniversitecontoso-la<br/>labosdeptmaths-la<br/>sciencedeptlabs-pilot-la<br/>labosdeptsciences-prod-la |
| Laboratoire de classe | Contient une ou plusieurs machines virtuelles |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nom de la classe** identifie la classe rattachée au labo.</li><li>**Période** identifie la période au cours de laquelle la classe est assurée.</li>**Identificateur du formateur** identifie le formateur qui est propriétaire du labo.</li></ul> | CS1234-automne2019-jeandupont<br/>CS1234-printemps2019-jeandupont |
| Galerie d’images partagées | Contient une ou plusieurs versions d’image de machine virtuelle | galerie\<organization short name\> | galerielabosuniversitecontoso |

Pour plus d’informations sur le nommage des autres ressources Azure, consultez [Conventions d’affectation de noms pour les ressources Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Régions ou localisations

Lors de la configuration de vos ressources du service Azure Lab Services, vous devez indiquer la région (ou localisation) du centre de données appelé à héberger la ressource. Voici plus d’informations sur la façon dont la région impacte chaque ressource impliquée dans la configuration d’un labo.

### <a name="resource-group"></a>Resource group

La région désigne le centre de données où sont stockées les informations sur le groupe de ressources. Les ressources Azure contenues dans le groupe de ressources peuvent se trouver dans des régions différentes de celle de leur parent.

### <a name="lab-account"></a>Compte Lab

La localisation d’un compte Lab désigne la région où se trouve cette ressource.  

### <a name="classroom-lab"></a>Laboratoire de classe

La localisation d’un labo de classe varie en fonction des facteurs suivants :

  - **Le compte Lab est appairé à un réseau virtuel**
  
    Un compte Lab peut être [appairé à un réseau virtuel](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quand il se trouve dans la même région.  Quand un compte Lab est appairé à un réseau virtuel, les labos de classe sont automatiquement créés dans la même région que le compte Lab et le réseau virtuel.

    > [!NOTE]
    > Quand un compte Lab est appairé à un réseau virtuel, le paramètre **Autoriser le créateur du lab à choisir l’emplacement du lab** est désactivé. Pour plus d’informations sur ce paramètre, consultez l’article suivant : [Autoriser le créateur du lab à choisir l’emplacement du lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Aucun réseau virtuel n’est appairé ***et***les créateurs de labo ne sont pas autorisés à choisir la localisation du labo**
  
    Quand **aucun** réseau virtuel n’est appairé au compte Lab *et* que les [créateurs de labo ne sont **pas** autorisés à choisir la localisation du labo](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), les labos de classe sont automatiquement créés dans une région disposant d’une capacité de machine virtuelle.  Plus précisément, le service Azure Lab Services recherche la disponibilité dans des [régions qui se trouvent dans la même zone géographique que le compte lab](https://azure.microsoft.com/global-infrastructure/regions).

  - **Aucun réseau virtuel n’est appairé ***et***les créateurs de labo sont autorisés à choisir la localisation du labo**
       
    Quand il n’y a **aucun** réseau virtuel appairé et que [les créateurs de labo sont autorisés à choisir la localisation du labo](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), les localisations qui peuvent être sélectionnées par le créateur de labo sont basées sur la capacité disponible.

> [!NOTE]
> Pour s’assurer qu’il y a suffisamment de capacité de machines virtuelles pour une région, il est important de commencer par demander de la capacité par le biais du compte Lab ou lors de la création du lab.

Une règle générale consiste à attribuer à une ressource la région la plus proche de ses utilisateurs. Pour les labos de classe, cela signifie que le labo de classe doit être créé au plus près de vos étudiants. Pour les cours en ligne où les étudiants sont situés dans le monde entier, faites appel à votre meilleur jugement pour créer un labo de classe central. Sinon, scindez une classe en plusieurs labos de classe en fonction de la région de vos étudiants.

### <a name="shared-image-gallery"></a>Galerie d’images partagées

La région désigne la région source dans laquelle la première version d’image est stockée avant d’être automatiquement répliquée dans les régions cibles.

## <a name="vm-sizing"></a>Dimensionnement des machines virtuelles

Au moment de créer un labo de classe, les administrateurs ou les créateurs de labos ont le choix entre les tailles de machine virtuelle suivantes, en fonction des besoins de leur classe. N’oubliez pas que les tailles de calcul disponibles dépendent de la région dans laquelle se trouve votre compte Lab :

| Taille | Spécifications | Série | Utilisation suggérée |
| ---- | ----- | ------ | ------------- |
| Petite| <ul><li>2 cœurs</li><li>3,5 Go de RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Cette taille est idéale pour la ligne de commande, l’ouverture d’un navigateur web, les serveurs web à faible trafic et les bases de données de petite et moyenne taille. |
| Moyenne | <ul><li>4 cœurs</li><li>7 Go de RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse. |
| Moyenne (virtualisation imbriquée) | <ul><li>4 cœurs</li><li>16 Go de RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse.
| grand | <ul><li>8 cœurs</li><li>16 Go de RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Cette taille est idéale pour les applications nécessitant des UC plus rapides, de meilleures performances du disque local, des bases de données volumineuses et des caches mémoire volumineux.  Cette taille prend également en charge la virtualisation imbriquée. |
| Grande (virtualisation imbriquée) | <ul><li>8 cœurs</li><li>16 Go de RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Cette taille est idéale pour les applications nécessitant des UC plus rapides, de meilleures performances du disque local, des bases de données volumineuses et des caches mémoire volumineux. |
| GPU de petite taille (visualisation) | <ul><li>6 cœurs</li><li>56 Go de RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. |
| GPU de petite taille (calcul) | <ul><li>6 cœurs</li><li>56 Go de RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Cette taille convient tout particulièrement aux applications qui nécessitent beaucoup de ressources informatiques comme l’intelligence artificielle et le Deep Learning. |
| GPU de taille moyenne (visualisation) | <ul><li>12 cœurs</li><li>112 Go de RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. |

## <a name="manage-identity"></a>Gérer l’identité

Le [contrôle d’accès en fonction du rôle d’Azure](https://docs.microsoft.com/azure/role-based-access-control/overview), permet d’attribuer les rôles suivants pour accorder l’accès à des comptes lab et à des laboratoires de salle de classe :

- **Propriétaire de compte de laboratoire**

    L’administrateur qui crée le compte lab est automatiquement ajouté au rôle **Propriétaire** de ce compte.  Un administrateur auquel le rôle **Propriétaire** est attribué peut effectuer les opérations suivantes :
     - Modifier les paramètres du compte lab.
     - Donner à d’autres administrateurs l’accès au compte lab en tant que propriétaires ou contributeurs.
     - Donner à des enseignants l’accès à des laboratoires de salle de classe en tant que créateurs, propriétaires ou contributeurs.
     - Créer et gérer tous les laboratoires de salle de classe au sein du compte lab.

- **Contributeur de compte lab**

    Un administrateur auquel le rôle **contributeur** est attribué peut effectuer les opérations suivantes :
    - Modifier les paramètres du compte lab.
    - Créer et gérer tous les laboratoires de salle de classe au sein du compte lab.

    Toutefois, il *ne peut pas* accorder à d’autres utilisateurs l’accès à des comptes lab ou à des laboratoires de salle de classe.

- **Créateur de laboratoire de salle de classe**

    Pour créer des laboratoires de salle de classe au sein d’un compte lab, un enseignant doit être membre du rôle **Créateur de laboratoire**.  Quand un enseignant crée un laboratoire de salle de classe, il est automatiquement ajouté en tant que propriétaire du laboratoire.  Reportez-vous au tutoriel décrivant la façon d’[ajouter un utilisateur au rôle **Créateur de laboratoire**](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Propriétaire\contributeur de laboratoire de salle de classe**
  
    Un enseignant peut afficher et modifier les paramètres d’un laboratoire de salle de classe quand il a le rôle **Propriétaire** ou **Contributeur** d’un laboratoire. Il doit également avoir le rôle **Lecteur** du compte lab.

    L’une des principales différences entre les rôles **Propriétaire** et **Contributeur** d’un laboratoire est qu’un contributeur *ne peut pas* accorder à d’autres utilisateurs l’accès nécessaire pour gérer le laboratoire. Seul un propriétaire le peut.

    En outre, un enseignant *ne peut* créer de laboratoires de salle de classe que s’il a également le rôle **Créateur de laboratoire**.

- **Galerie d’images partagées**

    Quand vous attachez une galerie d’images partagées à un compte lab, l’accès est automatiquement accordé aux propriétaires\contributeurs du compte lab, ainsi qu’aux créateurs\propriétaires\contributeurs du laboratoire qui peuvent ensuite voir et enregistrer des images dans la galerie.

Voici quelques conseils pour vous aider à attribuer des rôles :
   - En règle générale, seuls les administrateurs doivent avoir le rôle **Propriétaire** ou **Contributeur** d’un compte lab, et il peut y avoir plusieurs propriétaires\contributeurs.
   - Pour permettre à un enseignant de créer des laboratoires de salle de classe et gérer ceux-ci, il suffit de lui accorder l’accès au rôle **Créateur de laboratoire**.
   - Pour permettre à un enseignant de gérer des laboratoires de salle de classe spécifiques, mais *pas* de créer des laboratoires, vous devez accorder l’accès au rôle **Propriétaire** ou **Contributeur** pour chacun des laboratoires de salle de classe qu’ils gèrent.  Par exemple, vous pouvez permettre qu’un professeur et un assistant soient copropriétaires d’un laboratoire de salle de classe.  Reportez-vous au guide décrivant comment [ajouter un utilisateur en tant que propriétaire à un laboratoire de salle de classe](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Tarifs

### <a name="azure-lab-services"></a>Azure Lab Services

Les tarifs d’Azure Lab Services sont décrits dans l’article suivant : [Tarifs d’Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Vous devez aussi tenir compte des tarifs de la galerie d’images partagées si vous prévoyez de vous en servir pour le stockage et la gestion des versions d’images. 

### <a name="shared-image-gallery"></a>Galerie d’images partagées

Créer une galerie d’images partagées et l’attacher à votre compte Lab sont des opérations gratuites. Aucun coût n’est induit tant que vous n’enregistrez pas de version d’image dans la galerie. En général, le coût d’utilisation d’une galerie d’images partagées est assez négligeable, mais il est important de comprendre la façon dont il est calculé, car il n’est pas inclus dans le tarif d’Azure Lab Services.  

#### <a name="storage-charges"></a>Frais de stockage

Pour stocker des versions d’images, une galerie d’images partagées utilise des disques managés HDD standard. La taille du disque managé HDD utilisé dépend de la taille de la version d’image stockée. Pour connaître les tarifs, consultez l’article suivant : [Tarifs des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Frais de réplication et de sortie réseau

Quand vous enregistrez une version d’image en utilisant le modèle de machine virtuelle d’un labo de classe, le service Azure Lab Services la stocke d’abord dans une région source avant de la répliquer automatiquement dans une ou plusieurs régions cibles. Il est important de noter qu’Azure Lab Services réplique automatiquement la version d’image source dans toutes les [régions cibles au sein de la zone géographique](https://azure.microsoft.com/global-infrastructure/regions/) dans laquelle se trouve le labo de classe. Par exemple, si votre laboratoire de salle de classe se trouve dans la zone géographique États-Unis, une version d’image est répliquée dans chacune des huit régions des États-Unis.

La sortie réseau est facturée du moment qu’une version d’image est répliquée de la région source vers d’autres régions cibles. Le montant facturé dépend de la taille de la version d’image au moment où les données de l’image sont initialement transférées en sortie depuis la région source.  Pour obtenir des détails sur les tarifs, consultez l’article suivant : [Détails sur les tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Les clients de [solutions Éducation](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) peuvent être exonérés de frais de sortie. Pour en savoir plus, contactez votre responsable de compte.  Pour plus d’informations, consultez la section **FAQ** dans le document lié, en particulier la question relative aux programmes de transfert de données destinés aux clients du secteur de l’enseignement et aux critères d’éligibilité.

#### <a name="pricing-example"></a>Exemple de tarif

Pour récapituler les tarifs décrits ci-dessus, penchons-nous sur un exemple d’enregistrement d’une image de modèle de machine virtuelle dans la galerie d’images partagées. Les scénarios sont les suivants :

- Vous disposez d’une seule image de machine virtuelle personnalisée.
- Vous enregistrez deux versions de l’image.
- Votre labo se trouve aux États-Unis, où il existe au total huit régions.
- La taille de chaque version d’image est de 32 Go ; par conséquent, le coût du disque managé HDD est de 1,54 USD par mois.

Le coût total estimé est calculé comme suit :

Nombre d’images × nombre de versions × nombre de réplicas × prix du disque managé

Dans cet exemple, le coût est le suivant :

1 image personnalisée (32 Go) x 2 versions x 8 régions des États-Unis x 1,54 USD = 24,64 USD par mois

#### <a name="cost-management"></a>la gestion des coûts ;

Il est important pour l’administrateur de compte Lab de contenir les coûts en supprimant régulièrement les versions d’images inutiles de la galerie. 

Veillez à ne pas supprimer la réplication vers des régions spécifiques dans le but de réduire les coûts (cette option existe dans la galerie d’images partagées). Le fait de modifier la réplication peut avoir des conséquences négatives sur la capacité d’Azure Lab Services à publier des machines virtuelles à partir d’images enregistrées dans une galerie d’images partagées.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer un compte Lab et un labo, suivez les instructions pas à pas de ce tutoriel : [Guide de configuration](tutorial-setup-lab-account.md)
