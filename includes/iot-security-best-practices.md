---
title: Fichier Include
description: Fichier Include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146641"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Meilleures pratiques de sécurité pour l’Internet des objets (IoT)

Sécuriser une infrastructure IoT implique la mise en œuvre d’une stratégie complète et rigoureuse. Dans le cadre de cette stratégie, vous devez sécuriser les données dans le cloud, protéger l’intégrité des données transitant sur le réseau Internet public et configurer des appareils en toute sécurité. Chaque couche contribue à garantir la sécurité de l’infrastructure globale.

## <a name="secure-an-iot-infrastructure"></a>Sécuriser une infrastructure IoT

Cette stratégie de sécurité complète peut être développée et appliquée avec la participation active des différents acteurs impliqués dans la fabrication, le développement et le déploiement de l’infrastructure et des appareils IoT. Voici une description générale de ces acteurs.

* **Fabricant/intégrateur de matériel IoT**: En règle générale, ces lecteurs sont les fabricants de matériels IoT déployés, d’intégrateurs qui assemblent le matériel à partir de différents fabricants ou de fournisseurs de matériel pour un déploiement IoT réalisé ou intégré par d’autres fournisseurs.

* **Développeur de solutions IoT**: Le développement d’une solution IoT est généralement effectué par un développeur de solutions. Ce développeur peut faire partie d’une équipe interne ou être un intégrateur système spécialisé dans cette activité. Le développeur de solutions IoT peut développer plusieurs composants de la solution IoT en partant de zéro, intégrer de nombreux composants du commerce ou open source ou encore adopter des accélérateurs de solution moyennant une légère adaptation.

* **Responsable du déploiement de la solution IoT**: Une fois une solution IoT est développée, elle doit être déployé dans le champ. Ce processus implique le déploiement du matériel, l’interconnexion des appareils et le déploiement des solutions sur les appareils ou dans le cloud.

* **Opérateur de solutions IoT**: Une fois la solution IoT est déployée, elle nécessite des opérations à long terme, de surveillance, de mises à niveau et la maintenance. Ces tâches peuvent être assurées par une équipe interne comprenant des spécialistes en technologies de l’information, des équipes d’exploitation et de maintenance du matériel et des spécialistes du domaine qui contrôlent le comportement de l’infrastructure IoT globale.

Les sections qui suivent décrivent les bonnes pratiques associées à chacun de ces acteurs et facilitant le développement, le déploiement et l’exploitation d’une infrastructure IoT sécurisée.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricant/intégrateur de matériel IoT

Voici les bonnes pratiques pour les intégrateurs de matériel et les fabricants de matériel IoT.

* **Matériel aux exigences minimales d’étendue**: La conception du matériel doit inclure les fonctionnalités minimales requises pour le fonctionnement du matériel et rien de plus. Par exemple, des ports USB sont inclus uniquement s’ils sont nécessaires au fonctionnement de l’appareil. Les fonctionnalités supplémentaires exposent l’appareil à des vecteurs d’attaque indésirables qui doivent être évités.

* **Falsifications**: Intégrez un mécanisme pour détecter les altérations physiques, telles que l’ouverture du capot ou la suppression d’une partie de l’appareil. Les signaux de falsification peuvent être inclus dans le flux de données chargé sur le cloud, permettant d’informer les opérateurs de ces événements.

* **Intégrer la sécurité au matériel**: Si le coût des marchandises vendues le permet, créer des fonctionnalités de sécurité tel qu’un stockage sécurisé et chiffré, ou les fonctionnalités de démarrage en fonction de Module de plateforme sécurisée (TPM). Ces fonctionnalités renforcent la sécurité des appareils et facilitent la protection de l’infrastructure IoT globale.

* **Renforcer la sécurité des mises à niveau**: Mises à jour de microprogramme durant la durée de vie de l’appareil sont inévitables. Prévoyez des chemins d’accès sécurisés pour les mises à jour et un chiffrement garanti des versions du microprogramme. L’appareil sera ainsi sécurisé pendant et après les mises à jour.

## <a name="iot-solution-developer"></a>Développeur de solutions IoT

Voici les bonnes pratiques pour les développeurs de solutions IoT :

* **Respecter la méthodologie de développement de logiciels sécurisés**: Développement de logiciels sécurisés requiert la réflexion d’a à z sur la sécurité, depuis le lancement du projet jusqu'à son implémentation, le test et le déploiement. Les choix des plateformes, des langages et des outils sont influencés par cette méthodologie. Microsoft Security Development Lifecycle offre une approche pas à pas de la création de logiciels sécurisés.

* **Choisir des logiciels open source avec précaution**: Logiciels Open source permet de développer des solutions rapidement. Quand vous choisissez des logiciels open source, tenez compte du niveau d’activité de la communauté vis-à-vis de chaque composant open source. Avec une communauté active, les logiciels bénéficient d’un vaste soutien, et les problèmes sont identifiés et traités. Ce n’est nécessairement pas le cas d’un projet logiciel open source obscur et inactif : les problèmes ne sont probablement pas identifiés.

* **Intégrer avec précaution**: Nombreuses failles de sécurité logicielle existent à la limite des bibliothèques et API. Des fonctionnalités qui ne sont pas requises pour le déploiement concerné peuvent rester disponibles par le biais d’une couche API. Pour garantir la sécurité globale, vérifiez que toutes les interfaces des composants intégrés sont exemptes de failles de sécurité.

## <a name="iot-solution-deployer"></a>Responsable du déploiement de solutions IoT

Voici les bonnes pratiques pour les responsables du déploiement de solutions IoT :

* **Déployer du matériel de façon sécurisée** : les déploiements IoT peuvent nécessiter un déploiement du matériel dans des lieux non sécurisés, par exemple dans des espaces publics ou des régions non supervisées. Dans ce cas, assurez-vous que le déploiement matériel est protégé contre les falsifications, autant que faire se peut. Si le matériel est doté de ports, notamment de ports USB, vérifiez qu’ils sont correctement protégés. De nombreux vecteurs d’attaque peuvent les utiliser comme point d’entrée.

* **Protéger les clés d’authentification**: Au cours du déploiement, chaque appareil nécessite l’ID de périphérique et les clés d’authentification associées générées par le service cloud. Protégez physiquement ces clés, même après le déploiement. Une clé compromise pourrait être utilisée par un appareil malveillant pour usurper l’identité d’un appareil existant.

## <a name="iot-solution-operator"></a>Opérateur de solutions IoT

Voici les bonnes pratiques pour les opérateurs de solutions IoT :

* **Garder le système à jour**: Assurez-vous que les systèmes d’exploitation et tous les pilotes de périphérique sont mis à niveau vers les dernières versions. Si vous activez les mises à jour automatiques dans Windows 10 (IoT ou autres références), Microsoft le met constamment à jour. Les appareils IoT bénéficient ainsi d’un système d’exploitation sécurisé. Mettre régulièrement à jour les autres systèmes d’exploitation (tels que Linux) permet également de les protéger contre les attaques malveillantes.

* **Protéger contre les activités malveillantes**: Si le système d’exploitation le permet, installez les dernières fonctionnalités antivirus et anti-programme malveillant sur chaque système d’exploitation. Cette pratique peut contribuer à atténuer la plupart des menaces externes. Vous pouvez protéger la plupart des systèmes d’exploitation contre les menaces en prenant les mesures appropriées.

* **Effectuer des audits fréquemment**: L’infrastructure IoT pour les problèmes de sécurité d’est la clé lors de la réponse aux incidents de sécurité. La plupart des systèmes d’exploitation offrent une journalisation des événements intégrée. Examinez les journaux fréquemment pour vous assurer qu’aucune violation de sécurité n’a été détectée. Les informations d’audit peuvent être envoyées sous la forme d’un flux de télémétrie au service cloud, où elles peuvent être analysées.

* **Protéger physiquement l’infrastructure IoT**: Les attaques de sécurité pires pour l’infrastructure IoT sont lancées à l’aide d’accès physique aux appareils. Une pratique de sécurité essentielle consiste à protéger les appareils contre l’utilisation malveillante des ports USB et d’autres modes d’accès physiques. Pour détecter toute violation, vous devez notamment journaliser l’accès physique, comme l’utilisation des ports USB. Là encore, Windows 10 (IoT et autres références) peut assurer une journalisation détaillée de ces événements.

* **Protéger les informations d’identification cloud**: Informations d’identification cloud utilisées pour configurer et exploiter un déploiement IoT sont probablement le moyen le plus simple d’accéder à et compromettre un système IoT. Protégez les informations d’identification en modifiant fréquemment le mot de passe et en évitant de les utiliser sur des machines publiques.

Les fonctionnalités des différents appareils IoT peuvent varier. Certains appareils peuvent être des ordinateurs exécutant des systèmes d’exploitation courants, tandis que d’autres peuvent exécuter des systèmes d’exploitation très légers. Les bonnes pratiques en matière de sécurité décrites précédemment peuvent s’appliquer à ces appareils selon différents degrés. Il se peut que les fabricants de ces appareils établissent leurs propres bonnes pratiques en matière de sécurité et de déploiement. Le cas échéant, elles doivent être observées également.

Certains appareils existants et limités n’ont peut-être pas été conçus spécifiquement pour un déploiement IoT. Il se peut qu’ils ne soient pas en mesure de chiffrer les données, de se connecter à Internet ou d’assurer un audit avancé. Dans ce cas, une passerelle de champ moderne et sécurisée peut agréger les données des appareils existants et offrir la sécurité requise pour connecter ces appareils à Internet. Les passerelles de champ peuvent assurer l’authentification sécurisée, la négociation de sessions chiffrées, la réception des commandes du cloud et beaucoup d’autres fonctionnalités de sécurité.