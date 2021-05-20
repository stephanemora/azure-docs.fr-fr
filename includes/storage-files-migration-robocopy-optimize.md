---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a3dc42ece6bbd05b61ef9a4f1a0f82e147b2a762
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108749287"
---
La vitesse et le taux de réussite d’une exécution de RoboCopy donnée dépendent de plusieurs facteurs :

* les IOPS sur le stockage source et le stockage cible ;
* la bande passante réseau disponible entre la source et la cible ;
* la capacité de traiter rapidement des fichiers et des dossiers dans un espace de noms ;
* le nombre de modifications entre les exécutions de RoboCopy.


### <a name="iops-and-bandwidth-considerations"></a>Remarques relatives à la bande passante et aux IOPS

Dans cette catégorie, vous devez prendre en compte les capacités du **stockage source**, du **stockage cible** et du **réseau** qui les connecte. Le débit maximal possible est déterminé par le plus lent de ces trois composants. Vérifiez que votre infrastructure réseau est configurée pour prendre en charge des vitesses de transfert optimales au mieux de ses possibilités.

> [!CAUTION]
> Si la copie la plus rapide est souvent la plus souhaitée, envisagez l’utilisation de votre réseau local et de votre appliance NAS pour d’autres tâches, généralement critiques pour l’entreprise.

Une copie aussi rapide que possible peut ne pas être souhaitable lorsqu’il existe un risque de monopolisation des ressources disponibles par la migration.

* Réfléchissez au moment qui sera le plus approprié dans votre environnement pour effectuer des migrations : dans la journée, pendant les heures creuses ou au cours des week-ends.
* Pensez également à la mise en réseau de la Qualité de service sur un serveur Windows pour limiter la vitesse de RoboCopy.
* Évitez les tâches inutiles pour les outils de migration.

RoboCopy peut insérer des délais inter-paquets, en spécifiant le commutateur `/IPG:n`, sachant que `n` est mesuré en millisecondes entre les paquets de Robocopy. L’utilisation de ce commutateur permet d’éviter la monopolisation des ressources à la fois sur les appareils limités en E/S et sur les liens réseau encombrés.

`/IPG:n` ne peut pas être utilisé pour limiter avec précision le réseau à un certain nombre de Mbits/s près. Utilisez plutôt la Qualité de service du réseau Windows Server. RoboCopy s’appuie entièrement sur le protocole SMB pour tous les besoins réseau. Cette utilisation de SMB est la raison pour laquelle RoboCopy ne peut pas influer sur le débit du réseau lui-même, par contre il peut ralentir son utilisation. 

Un raisonnement similaire s’applique aux E/S par seconde (IOPS) observées sur l’appliance NAS. La taille du cluster sur le volume NAS, les tailles de paquets et un ensemble d’autres facteurs affectent les IOPS observées. L’introduction d’un délai entre des paquets constitue souvent le moyen le plus simple de contrôler la charge sur le NAS. Testez plusieurs valeurs, par exemple, à partir d’environ 20 millisecondes (n=20) jusqu’aux multiples de ce nombre. Dès lors qu’un délai est intercalé, vous pouvez évaluer si vos autres applications peuvent désormais fonctionner comme prévu. Cette stratégie d’optimisation vous permettra de trouver la vitesse optimale de RoboCopy dans votre environnement.

### <a name="processing-speed"></a>Vitesse de traitement

RoboCopy parcourra l’espace de noms qui lui est désigné et évaluera tous les fichiers et les dossiers en vue de leur copie. Chaque fichier sera évalué lors d’une copie initiale et au cours des copies de rattrapage. Prenons l’exemple des exécutions répétées de RoboCopy/MIR sur les mêmes emplacements de stockage source et cible. Ces exécutions répétées sont utiles pour réduire au minimum le temps d’interruption des utilisateurs et des applications, et pour améliorer le taux de réussite global des fichiers migrés.

Nous avons souvent tendance à considérer la bande passante comme le facteur le plus limitatif dans une migration, et cela peut être vrai. Toutefois, la possibilité d’énumérer un espace de noms peut influencer encore plus la durée totale de la copie pour les espaces de noms plus grands avec des fichiers de plus petite taille. Considérez que la copie de 1 Tio comportant des petits fichiers prendra beaucoup plus de temps que la copie de 1 Tio contenant des fichiers moins nombreux, mais plus volumineux. En supposant que toutes les autres variables demeurent inchangées.

La cause de cette différence réside dans la puissance de traitement nécessaire pour parcourir un espace de noms. RoboCopy prend en charge les copies multithread par le biais du paramètre `/MT:n`, où n représente le nombre de threads de processeurs. Ainsi, lors du provisionnement d’une machine plus particulièrement destinée à RoboCopy, tenez compte du nombre de cœurs de processeur et de leur relation avec le nombre de threads qu’ils fournissent. Le plus souvent, il est question de deux threads par cœur. Le nombre de cœurs et de threads d’une machine constitue un point de données important pour déterminer les valeurs multithread `/MT:n` que vous devez spécifier. Tenez également compte du nombre de tâches de RoboCopy que vous prévoyez d’exécuter en parallèle sur une machine donnée.

Des threads plus nombreux copieront notre exemple de 1 Tio de petits fichiers beaucoup plus rapidement que des threads moins nombreux. En même temps, l’investissement en ressources supplémentaires sur notre 1 Tio de fichiers volumineux peut ne pas produire d’avantages, en proportion. Un nombre élevé de threads tentera de copier simultanément un plus grand nombre de fichiers volumineux sur le réseau. Cette activité réseau supplémentaire augmente la probabilité d’être limité par les IOPS de débit ou de stockage.

Pendant une première RoboCopy dans une cible vide ou une exécution différentielle avec un grand nombre de fichiers modifiés, vous êtes probablement limité par le débit de votre réseau. Démarrez avec un nombre élevé de threads pour une série initiale. Un nombre élevé de threads, même au-delà des threads actuellement disponibles sur votre machine, permet de saturer la bande passante réseau disponible. Les exécutions suivantes de /MIR sont affectées progressivement par les éléments traités. Un nombre moindre de modifications dans une exécution différentielle signifie moins de transport des données sur le réseau. Votre vitesse est désormais plus dépendante de votre capacité à traiter les éléments d’espace de noms que de leur déplacement sur la liaison réseau. Pour les exécutions suivantes, associez votre valeur de nombre de threads au nombre de cœurs du processeur et au nombre de threads par cœur. Déterminez si les cœurs doivent être réservés pour les autres tâches qu’un serveur de production peut prendre en charge.

### <a name="avoid-unnecessary-work"></a>Éviter les tâches inutiles

Évitez les modifications à grande échelle dans votre espace de noms. Par exemple, le déplacement de fichiers entre des répertoires, la modification de propriétés à grande échelle ou la modification des autorisations (ACL NTFS). En particulier, les modifications de liste de contrôle d’accès (ACL, access-control list) peuvent avoir un impact important, car elles ont souvent un effet de modification en cascade sur les fichiers situés plus bas dans l’arborescence des dossiers. Les conséquences peuvent être les suivantes :

* Un temps d’exécution de la tâche RoboCopy prolongé, car chaque fichier et dossier concerné par une modification ACL doit être mis à jour
* La réutilisation de données déplacées auparavant demandera peut-être que celles-ci soient recopiées. Par exemple, une plus grande quantité de données devra être copiée lorsque des structures de dossiers seront modifiées après une copie de fichiers déjà effectuée antérieurement. Une tâche RoboCopy ne peut pas « lire » une modification d’espace de noms. La tâche suivante doit donc purger les fichiers précédemment transportés vers l’ancienne structure de dossiers, et charger de nouveau les fichiers dans la nouvelle structure de dossiers.

Un autre aspect important consiste à utiliser efficacement l’outil RoboCopy. Avec le script RoboCopy recommandé, vous allez créer et enregistrer un fichier journal d’erreurs. Des erreurs de copie peuvent se produire, cela est normal. Ces erreurs rendent souvent nécessaire l’exécution de plusieurs séquences d’un outil de copie tel que RoboCopy. Une exécution initiale, par exemple d’un appareil NAS vers DataBox ou d’un serveur vers un partage de fichiers Azure. À cela, ajouter une ou plusieurs exécutions supplémentaires avec le commutateur/MIR pour intercepter et refaire une tentative sur les fichiers qui n’ont pas été copiés.

Vous devez être prêt à exécuter plusieurs séquences de RoboCopy sur une étendue d’espace de noms déterminée. Les exécutions successives se terminent plus rapidement, car elles ont moins à copier, mais elles sont progressivement limitées par la vitesse de traitement de l’espace de noms. Lorsque vous exécutez plusieurs séquences, vous pouvez accélérer chacune d’elles en évitant que RoboCopy n’essaie exagérément de tout copier dans une exécution donnée. Ces commutateurs RoboCopy peuvent faire une grande différence :

* `/R:n` n = fréquence à laquelle vous réessayez de copier un fichier ayant échoué et 
* `/W:n` n = fréquence d’attente, en secondes, entre les tentatives

`/R:5 /W:5` est un paramètre raisonnable que vous pouvez adapter à votre convenance. Dans cet exemple, un fichier ayant échoué sera retenté cinq fois, avec un délai d’attente de cinq secondes entre chaque tentative. Si la copie du fichier échoue toujours, la tâche RoboCopy suivante fera une nouvelle tentative. Souvent les fichiers en échec, du fait de leur utilisation en cours ou de problèmes de délai d’attente, peuvent au final être correctement copiés de cette façon.
   
