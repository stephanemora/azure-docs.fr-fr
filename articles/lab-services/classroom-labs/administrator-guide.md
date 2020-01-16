---
title: Azure Lab Services – Guide de l’administrateur | Microsoft Docs
description: Ce guide aide les administrateurs qui créent et gèrent des comptes Lab à l’aide d’Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 1c26e5c6b4bd484b643182e9e4ade3d71929fdba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476899"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – Guide de l’administrateur
Les administrateurs des technologies de l’information qui gèrent les ressources cloud d’une organisation sont aussi généralement responsables de la configuration du compte Lab de leur organisation. Les administrateurs ou les formateurs créent les labos de classe dans le compte Lab. Cet article offre une vue d’ensemble globale des ressources Azure en question ainsi que des conseils pour les créer.

![Vue globale des ressources Azure d’un compte Lab](../media/administrator-guide/high-level-view.png)

- Les labos de classe sont hébergés dans un abonnement Azure dont Azure Lab Services est propriétaire
- Les comptes Lab, la galerie d’images partagées et les versions d’images sont hébergés dans votre abonnement
- Vous pouvez placer le compte Lab et la galerie d’images partagées dans le même groupe de ressources. Dans ce schéma, ils se trouvent dans des groupes de ressources différents. 

## <a name="subscription"></a>Subscription
Votre organisation dispose d’un ou plusieurs abonnements Azure. Un abonnement permet de gérer la facturation et la sécurité de l’ensemble des ressources/services Azure qui y sont utilisés, notamment les comptes Lab.

La relation entre un compte Lab et son abonnement est importante pour les raisons suivantes :

- La facturation est établie via l’abonnement qui contient le compte Lab.
- Vous pouvez accorder aux utilisateurs du locataire Azure Active Directory (AD) associé à l’abonnement un accès à Azure Lab Services. Vous pouvez ajouter l’utilisateur en tant que propriétaire/contributeur d’un compte Lab ou en tant que créateur d’un labo de classe.

Les labos de classe et leurs machines virtuelles sont entièrement gérés automatiquement. Plus précisément, ils sont hébergés dans un abonnement dédié dont Azure Lab Services est propriétaire.

## <a name="resource-group"></a>Resource group
Un abonnement contient un ou plusieurs groupes de ressources. Les groupes de ressources servent à créer des regroupements logiques des ressources Azure utilisées ensemble dans une même solution.  

Quand vous créez un compte Lab, vous devez configurer le groupe de ressources qui le contient. 

Un groupe de ressources est également nécessaire pour créer une [galerie d’images partagées](#shared-image-gallery). Vous pouvez choisir de placer votre compte Lab et votre galerie d’images partagées dans deux groupes de ressources distincts, ce qui est normal si vous envisagez de partager la galerie d’images entre différentes solutions. Vous pouvez aussi choisir de les placer dans le même groupe de ressources.

Quand vous créez un compte Lab et qu’en même temps vous créez et attachez automatiquement une galerie d’images partagées, le compte Lab et la galerie d’images partagées sont créés par défaut dans des groupes de ressources distincts. Vous pourrez observer ce comportement en suivant les étapes décrites dans ce tutoriel : [Configurer une galerie d’images partagées pendant la création d’un compte Lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). L’image située dans la partie supérieure de cet article utilise également cette configuration. 

Nous vous recommandons de prendre suffisamment de temps pour réfléchir à la façon de structurer vos groupes de ressources, car une fois que le groupe de ressources d’un compte Lab ou d’une galerie d’images partagées est créé, il ne peut plus être modifié. Si vous avez besoin de modifier le groupe de ressources pour ces ressources, vous devrez supprimer et recréer votre compte Lab et/ou la galerie d’images partagées.

## <a name="lab-account"></a>Compte Lab
Un compte Lab sert de conteneur à un ou plusieurs labos de classe. Lors des premiers pas avec Azure Lab Services, il est courant de n’avoir qu’un seul compte lab. À mesure que l’utilisation du labo évolue, vous pouvez décider de créer des comptes Lab supplémentaires.

La liste suivante met en avant les scénarios dans lesquels l’existence de plusieurs comptes Lab peut s’avérer bénéfique :

- **Gérer des besoins de stratégies différents entre les labos de classe** 

    Quand vous configurez un compte Lab, vous définissez des stratégies qui s’appliquent à tous les labos de classe relevant du compte Lab, par exemple :
    - Le réseau virtuel Azure avec des ressources partagées auxquelles le labo de classe peut accéder. Par exemple, vous pouvez disposez d’un ensemble de labos de classe qui doit pouvoir accéder à un jeu de données partagées au sein d’un réseau virtuel.
    - Les images de machine virtuelle qui permettent aux labos de classe de créer des machines virtuelles. Par exemple, vous pouvez disposer d’un ensemble de labos de classe qui doivent pouvoir accéder à l’image de la Place de marché [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu).  

    Si vous avez des labos de classe qui ont chacun des besoins en stratégies uniques, il peut être avantageux de créer des comptes Lab distincts pour gérer séparément ces labos de classe.
- **Restreindre l’accès des créateurs de labos à des labos de classe spécifiques**  

    Quand un utilisateur est ajouté en tant que créateur de labo, il peut accéder à tous les labos de classe relevant du compte Lab, y compris à ceux créés par d’autres créateurs de labos. Pour limiter les créateurs de laboratoire à la gestion de labos spécifiques, vous pouvez créer des comptes lab distincts de façon à limiter l’étendue de leur accès. Par exemple, vous pouvez créer un compte Lab distinct pour chaque département d’une université. Par exemple : un compte Lab pour le département Sciences, un autre pour le département Mathématiques, etc.   
- **Budget séparé par compte Lab**

    Pour éviter que les coûts de tous les labos de classe soient imputés à un seul compte Lab, il peut être judicieux de séparer le budget plus distinctement. Pour reprendre l’exemple précédent, vous pourriez créer un compte Lab pour chaque département de l’université de façon à séparer le budget en conséquence. En utilisant Azure Cost Management, vous pourriez alors voir les coûts pour chaque compte Lab.
•   **Isoler les labos pilotes des labos actifs**

    Dans certains cas, vous souhaiterez peut-être piloter des changements de stratégies dans un compte Lab sans risquer d’affecter les labos actifs. Dans ce type de scénario, la création d’un compte Lab distinct à des fins de pilotage permet d’isoler les modifications. 

## <a name="classroom-lab"></a>Laboratoire de classe
Un labo de classe contient une ou plusieurs machines virtuelles, chacune attribuée à un étudiant déterminé. En règle générale, vous pouvez vous attendre à ce que :

- Un labo de classe existe pour chaque classe.
- Un nouvel ensemble de labos de classe soit créé à chaque semestre (ou à chaque période correspondant à votre classe). En général, pour les classes qui ont les mêmes besoins en images, vous pouvez utiliser une [galerie d’images partagées](#shared-image-gallery) de façon à partager les images entre les labos et les semestres.

Pour savoir comment structurer vos labos de classe, tenez compte des points suivants :

- **Toutes les machines virtuelles d’un labo de classe sont déployées avec la même image publiée**. De ce fait, si vous devez publier simultanément plusieurs images de labo pour une classe, des labos de classe distincts devront être créés pour chacune d’elles.
- **Un quota d’utilisation est défini au niveau du labo et s’applique à tous ses utilisateurs**. Par exemple, certains formateurs peuvent avoir besoin d’accéder aux machines virtuelles d’une classe pour préparer leurs cours. Or, quand les formateurs ont besoin d’un quota de 10 heures, les étudiants participant à la classe ont besoin d’un quota de 40 heures. Pour définir des quotas différents pour les utilisateurs, vous devez créer des labos de classe distincts. Cependant, avant de pouvoir ajouter des heures supplémentaires à un utilisateur déterminé, vous devez avoir défini le quota.
- **La planification de démarrage ou d’arrêt est définie au niveau du labo et s’applique à toutes ses machines virtuelles**. Comme au point précédent, si vous avez besoin de définir des planifications différentes pour les utilisateurs, vous devez créer des labos de classe distincts. 

## <a name="shared-image-gallery"></a>Galerie d’images partagées
Une galerie d’images partagées est attachée à un compte Lab et sert de référentiel central pour le stockage d’images. Une image est enregistrée dans la galerie dès lors qu’un formateur choisit de l’enregistrer à partir du modèle de machine virtuelle d’un labo de classe. Chaque fois qu’un formateur apporte des modifications au modèle de machine virtuelle et qu’il l’enregistre, la nouvelle version de l’image est enregistrée et les versions précédentes sont conservées.

Les formateurs peuvent publier une version d’image à partir de la galerie d’images partagées au moment de créer un labo de classe. Même si la galerie peut stocker plusieurs versions d’une image, les formateurs ne peuvent sélectionner que la dernière version pendant la création du labo.

La galerie d’images partagées est une ressource facultative dont vous n’aurez peut-être pas besoin tout de suite compte tenu du nombre limité de labos de classe que vous aurez au début. Cependant, l’utilisation de la galerie d’images partagées présente de nombreux avantages à mesure que vos besoins évoluent vers davantage de labos de classe :

- **Elle vous permet d’enregistrer et de gérer différentes versions d’une image de modèle de machine virtuelle**.

    Cela est utile quand il s’agit de créer une image personnalisée ou d’apporter des modifications (logiciels, configuration, etc.) à une image de la galerie de la Place de marché publique.  Par exemple, il est courant que les formateurs réclament l’installation de différents logiciels/outils. Au lieu de demander aux étudiants d’installer eux-mêmes ces composants manuellement, il est possible d’enregistrer différentes versions de l’image du modèle de machine virtuelle dans une galerie d’images partagées. Ces versions d’images peuvent ensuite être utilisées pendant la création de nouveaux labos de classe.
- **Elle permet de partager/réutiliser les images de modèle de machine virtuelle entre les labos de classe**.

    Cela vous évite d’avoir à configurer entièrement une image chaque fois que vous créez un labo de classe. Par exemple, si parmi les cours proposés, plusieurs d’entre eux nécessitent la même image, il suffit de créer cette image une fois pour toutes et de l’enregistrer dans la galerie d’images partagées pour que les labos de classe puissent la partager.
- **Elle garantit la disponibilité des images via la réplication**.

    Du moment que vous enregistrez une image dans la galerie d’images partagées à partir d’un labo de classe, elle est automatiquement répliquée dans les autres régions d’une même zone géographique. Si une défaillance se produit dans une région, la publication du modèle de machine virtuelle dans votre labo de classe n’est pas affectée par l’utilisation d’un réplica d’image dans les autres régions. De plus, cela favorise les performances dans les scénarios de publication de plusieurs machines virtuelles en autorisant l’utilisation de différents réplicas.

Pour regrouper logiquement des images partagées, plusieurs options s’offrent à vous :

- Créez plusieurs galeries d’images partagées. Sachant que chaque compte Lab ne peut se connecter qu’à une seule galerie d’images partagées, cette option vous engage aussi à créer plusieurs comptes Lab.
- Vous pouvez aussi utiliser une seule galerie d’images partagées commune à plusieurs comptes Lab. Dans ce cas, chaque compte Lab peut activer uniquement les images applicables aux labos de classe qu’il contient.

## <a name="naming"></a>Dénomination
Durant vos premiers pas avec Azure Lab Services, nous vous recommandons d’établir des conventions d’affectation de noms pour les groupes de ressources, les comptes Lab, les labos de classe et la galerie d’images partagées. Même si les conventions d’affectation de noms que vous établissez sont destinées à répondre aux besoins uniques de votre organisation, le tableau suivant en décrit les principes généraux.

| Type de ressource | Role | Modèle suggéré | Exemples |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contient un ou plusieurs comptes Lab et une ou plusieurs galeries d’images partagées | \<nom abrégé de l’organisation\>-environnement \<\>-rg<ul><li>**Nom abrégé de l’organisation** identifie l’organisation dans laquelle le groupe de ressources est utilisé</li><li>**Environnement** identifie l’environnement de la ressource, par exemple Test ou Production</li><li>**Rg** est l’abréviation du type de ressource : resource group (groupe de ressources).</li></ul> | labosuniversitecontoso-rg<br/>labosuniversitecontoso-test-rg<br/>labosuniversitecontoso-prod-rg |
| Compte Lab | Contient un ou plusieurs labos | \<nom abrégé de l’organisation\>-environnement \<\>-la<ul><li>**Nom abrégé de l’organisation** identifie l’organisation dans laquelle le groupe de ressources est utilisé</li><li>**Environnement** identifie l’environnement de la ressource, par exemple Test ou Production</li><li>**La** est l’abréviation du type de ressource : lab account (compte Lab).</li></ul> | labosuniversitecontoso-la<br/>labosdeptmaths-la<br/>labosdeptsciences-test-la<br/>labosdeptsciences-prod-la |
| Laboratoire de classe | Contient une ou plusieurs machines virtuelles |\<nom de la classe\>-\<période\>-\<identificateur du formateur\><ul><li>**Nom de la classe** identifie la classe rattachée au labo.</li><li>**Période** identifie la période au cours de laquelle la classe est assurée.</li>**Identificateur du formateur** identifie le formateur qui est propriétaire du labo.</li></ul> | CS1234-automne2019-jeandupont<br/>CS1234-printemps2019-jeandupont | 
| Galerie d’images partagées | Contient une ou plusieurs versions d’image de machine virtuelle | galerie\<nom abrégé de l’organisation\> | galerielabosuniversitecontoso |

Pour plus d’informations sur le nommage des autres ressources Azure, consultez [Conventions d’affectation de noms pour les ressources Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Régions ou localisations
Au moment de configurer vos ressources Azure Lab Services, vous devez indiquer la région (ou localisation) du centre de données appelé à héberger ces ressources. Vous trouverez ci-dessous des informations complémentaires sur la façon dont la région/localisation affecte chacune des ressources suivantes utilisées dans votre déploiement Lab Services :

- **Groupe de ressources**

    La région désigne le centre de données où sont stockées les informations sur le groupe de ressources. Les ressources Azure contenues dans le groupe de ressources peuvent se trouver dans des régions différentes de celle de leur parent.
- **Compte Lab ou labo de classe**

    La localisation du compte Lab désigne la région de cette ressource. Les labos de classe créés dans le compte Lab peuvent être déployés dans n’importe quelle région au sein d’une zone géographique. La région dans laquelle les machines virtuelles du labo sont déployées est sélectionnée automatiquement en fonction de la capacité disponible dans la région à ce moment-là.  
    Si un administrateur autorise les créateurs de labos à choisir la localisation de leur labo de classe, les localisations proposées varient en fonction de la capacité régionale disponible pendant la création du labo.

    La localisation du labo de classe détermine aussi les tailles de calcul de machine virtuelle pouvant être sélectionnées. Certaines tailles de calcul sont disponibles uniquement dans certaines localisations.
- **Galerie d’images partagées**

    La région désigne la région source dans laquelle la première version d’image est stockée avant d’être automatiquement répliquée dans les régions cibles.
    
Une règle générale consiste à attribuer à une ressource la région/localisation la plus proche de ses utilisateurs. Pour les labos de classe, cela signifie que le labo de classe doit être créé au plus près de vos étudiants. Pour les cours en ligne où les étudiants sont situés dans le monde entier, faites appel à votre meilleur jugement pour créer un labo de classe central. Sinon, scindez une classe en plusieurs labos de classe en fonction de la région de vos étudiants.

## <a name="vm-sizing"></a>Dimensionnement des machines virtuelles
Au moment de créer un labo de classe, les administrateurs ou les créateurs de labos ont le choix entre les tailles de machine virtuelle suivantes, en fonction des besoins de leur classe. N’oubliez pas que les tailles de calcul disponibles dépendent de la région dans laquelle se trouve votre compte Lab :

| Size | Spécifications | Utilisation suggérée |
| ---- | ----- | ------------- |
| Petite| <ul><li>2 cœurs</li><li>3,5 Go de RAM</li></ul> | Cette taille est idéale pour la ligne de commande, l’ouverture d’un navigateur web, les serveurs web à faible trafic et les bases de données de petite et moyenne taille. |
| Moyenne | <ul><li>4 cœurs</li><li>7 Go de RAM</li></ul> | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse. |
| Moyenne (virtualisation imbriquée) | <ul><li>4 cœurs</li><li>16 Go de RAM</li></ul> | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse.  Cette taille prend également en charge la virtualisation imbriquée. |
| grand | <ul><li>8 cœurs</li><li>32 Go de RAM</li></ul> | Cette taille est idéale pour les applications nécessitant des UC plus rapides, de meilleures performances du disque local, des bases de données volumineuses et des caches mémoire volumineux.  Cette taille prend également en charge la virtualisation imbriquée. |
| GPU de petite taille (visualisation) | <ul><li>6 cœurs</li><li>56 Go de RAM</li> | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. |
| GPU de petite taille (calcul) | <ul><li>6 cœurs</li><li>56 Go de RAM</li></ul> |Cette taille convient tout particulièrement aux applications qui nécessitent beaucoup de ressources informatiques comme l’intelligence artificielle et le Deep Learning. |
| GPU de taille moyenne (visualisation) | <ul><li>12 cœurs</li><li>112 Go de RAM</li></ul> | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. |

## <a name="manage-identity"></a>Gérer l’identité
Un administrateur de compte Lab peut avoir l’un des deux types de rôle suivants :

- **Propriétaire**

    Un administrateur qui a le rôle **Propriétaire** dispose d’un accès complet au compte Lab et peut notamment en accorder l’accès à d’autres utilisateurs et ajouter des créateurs de labos. L’administrateur qui crée le compte Lab est ajouté par défaut en tant que propriétaire.
- **Contributeur**

    Un administrateur qui a le rôle Contributeur peut modifier les paramètres du compte Lab, mais il ne peut pas accorder d’accès à d’autres utilisateurs ni ajouter des créateurs de labos.

Quand vous attachez une galerie d’images partagées à un compte Lab, l’accès est automatiquement accordé à l’administrateur et aux créateurs de labos, ce qui leur permet de voir et d’enregistrer les images dans la galerie. 

## <a name="pricing"></a>Tarifs

### <a name="azure-lab-services"></a>Azure Lab Services
Les tarifs d’Azure Lab Services sont décrits dans l’article suivant : [Tarifs d’Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Vous devez aussi tenir compte des tarifs de la galerie d’images partagées si vous prévoyez de vous en servir pour le stockage et la gestion des versions d’images. 

### <a name="shared-image-gallery"></a>Galerie d’images partagées
Créer une galerie d’images partagées et l’attacher à votre compte Lab sont des opérations gratuites. Aucun coût n’est induit tant que vous n’enregistrez pas de version d’image dans la galerie. En général, le coût d’utilisation d’une galerie d’images partagées est assez négligeable, mais il est important de comprendre la façon dont il est calculé, car il n’est pas inclus dans le tarif d’Azure Lab Services.  

### <a name="storage-charges"></a>Frais de stockage
Pour stocker des versions d’images, une galerie d’images partagées utilise des disques managés HDD standard. La taille du disque managé HDD utilisé dépend de la taille de la version d’image stockée. Pour connaître les tarifs, consultez l’article suivant : [Tarifs des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Frais de réplication et de sortie réseau
Quand vous enregistrez une version d’image en utilisant le modèle de machine virtuelle d’un labo de classe, Lab Services la stocke d’abord dans une région source avant de la répliquer automatiquement dans une ou plusieurs régions cibles. Il est important de noter qu’Azure Lab Services réplique automatiquement la version d’image source dans toutes les régions cibles de la géolocalisation dans laquelle se trouve le labo de classe. Par exemple, si votre labo de classe se trouve dans la géolocalisation États-Unis, une version d’image est répliquée dans chacune des huit régions qui existent aux États-Unis.

La sortie réseau est facturée du moment qu’une version d’image est répliquée de la région source vers d’autres régions cibles. Le montant facturé dépend de la taille de la version d’image au moment où les données de l’image sont initialement transférées en sortie depuis la région source.  Pour obtenir des détails sur les tarifs, consultez l’article suivant : [Détails sur les tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Les clients de [solutions Éducation](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) peuvent être exonérés de frais de sortie. Pour en savoir plus, contactez votre responsable de compte.  Pour plus d’informations, consultez la section **Questions fréquentes (FAQ)** dans le document lié, en particulier la question relative aux programmes de transfert de données destinés aux clients de l’enseignement et aux critères d’éligibilité.

### <a name="pricing-example"></a>Exemple de tarif
Pour récapituler les tarifs décrits ci-dessus, penchons-nous sur un exemple d’enregistrement d’une image de modèle de machine virtuelle dans la galerie d’images partagées. Les scénarios sont les suivants :

- Vous disposez d’une seule image de machine virtuelle personnalisée.
- Vous enregistrez deux versions de l’image.
- Votre labo se trouve aux États-Unis, où il existe au total huit régions.
- La taille de chaque version d’image est de 32 Go ; par conséquent, le coût du disque managé HDD est de 1,54 USD par mois.

Le coût total estimé est calculé comme suit :

Nombre d’images × nombre de versions × nombre de réplicas × prix du disque managé

Dans cet exemple, le coût est le suivant :

1 image personnalisée (32 Go) x 2 versions x 8 régions des États-Unis x 1,54 USD = 24,64 USD par mois

### <a name="cost-management"></a>la gestion des coûts ;
Il est important pour l’administrateur de compte Lab de contenir les coûts en supprimant régulièrement les versions d’images inutiles de la galerie. 

Veillez à ne pas supprimer la réplication vers des régions spécifiques dans le but de réduire les coûts (cette option existe dans la galerie d’images partagées). Le fait de modifier la réplication peut avoir des conséquences négatives sur la capacité d’Azure Lab Services à publier des machines virtuelles à partir d’images enregistrées dans une galerie d’images partagées.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment créer un compte Lab et un labo, suivez les instructions pas à pas de ce tutoriel : [Tutoriel : Configurer un compte Lab](tutorial-setup-lab-account.md)