---
title: Présentation de l’analyse spatiale Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Ce document explique les concepts et fonctionnalités de base d’un conteneur d’analyse spatiale Vision par ordinateur.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f90e4e5e187977f0ee77a565ff9143902ea3a10d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736833"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Présentation de l’analyse spatiale Vision par ordinateur

L’analyse spatiale de Vision par ordinateur est une nouvelle fonctionnalité de l’API Vision par ordinateur d’Azure Cognitive Services qui permet aux organisations de maximiser la valeur de leurs espaces physiques en comprenant les déplacements et la présence des personnes dans une zone donnée. Elle vous permet d’ingérer le flux vidéo de caméras de télésurveillance, d’exploiter des opérations d’intelligence artificielle (IA) pour extraire des insights de flux vidéo, et de générer des événements que d’autres systèmes peuvent utiliser. À partir d’un flux de caméra, une opération d’IA peut effectuer des opérations telles que dénombrer les personnes entrant dans un espace ou mesurer le respect des consignes de port du masque ou de distanciation sociale.

## <a name="the-basics-of-spatial-analysis"></a>Concepts de base de l’analyse spatiale

Aujourd’hui, les principales opérations en lien avec l’analyse spatiale s’appuient sur un pipeline qui ingère un flux vidéo, détecte des personnes dans celui-ci, suit ces personnes dans leurs déplacements au fil du temps et génère des événements à mesure que les personnes interagissent avec des zones d’intérêt.

## <a name="spatial-analysis-terms"></a>Terminologie de l’analyse spatiale

| Terme | Définition |
|------|------------|
| Détection de personnes | Ce composant répond à la question « où sont les personnes dans cette image » ? Il recherche les êtres humains dans une image et transmet un rectangle englobant indiquant l’emplacement de chaque personne au composant de suivi des personnes. |
| Suivi des personnes | Ce composant connecte les détections de personnes au fil du temps à mesure que les personnes se déplacent devant une caméra. Pour ce faire, il utilise une logique temporelle relative à la manière dont les personnes se déplacent généralement, ainsi que des informations de base sur l’apparence globale des personnes. Il ne fait pas le suivi des personnes sur plusieurs caméras. Si une personne sort du champ d’une caméra pendant plus d’une minute environ, puis entre à nouveau dans le champ de la caméra, le système la considère comme une nouvelle personne. Le suivi des personnes n’identifie pas de manière unique les individus sur plusieurs caméras. Il n’utilise pas la reconnaissance faciale ni le suivi de la démarche. |
| Détection de masque | Ce composant détecte l’emplacement du visage d’une personne dans le champ de la caméra et identifie la présence d’un masque. Pour ce faire, l’opération d’IA analyse les images de la vidéo. Quand un visage est détecté, le service dessine un cadre englobant autour du visage. À l’aide des fonctionnalités de détection d’objets, il identifie la présence d’un masque dans le cadre englobant. La détection de masque ne nécessite pas de faire la distinction entre les visages, de prédire ou de classifier les attributs faciaux ni d’utiliser la reconnaissance faciale. |
| Zone d’intérêt | Il s’agit d’une zone ou d’une ligne définie dans la vidéo d’entrée lors de la configuration. Quand une personne interagit avec la zone de la vidéo, le système génère un événement. Par exemple, pour l’opération PersonCrossingLine, une ligne est définie dans la vidéo. Quand une personne franchit cette ligne, un événement est généré. |
| Événement | Un événement est la sortie principale de l’analyse spatiale. Chaque opération émet un événement spécifique soit périodiquement (par exemple, une fois par minute), soit quand un déclencheur spécifique s’active. L’événement inclut des informations sur ce qui s’est produit dans la vidéo d’entrée, mais n’inclut aucune image ou vidéo. Par exemple, l’opération PeopleCount peut émettre un événement contenant le nombre actualisé chaque fois que le nombre de personnes change (déclencheur) ou une fois par minute (périodiquement). |

## <a name="example-use-cases-for-spatial-analysis"></a>Exemples de cas d’usage de l’analyse spatiale

Voici quelques exemples de cas d’usage que nous avions à l’esprit quand nous avons conçu et testé l’analyse spatiale.

**Respect de la distanciation sociale** : un espace de bureau est équipé de plusieurs caméras qui utilisent l’analyse spatiale pour surveiller le respect de la distanciation sociale en mesurant la distance entre les personnes. Le gestionnaire des installations peut utiliser des cartes thermiques présentant des statistiques agrégées du respect de la distanciation sociale au fil du temps afin d’ajuster l’espace de travail et de faciliter le respect des consignes de distanciation sociale.

**Analyse de client** : un supermarché utilise des caméras pointées sur des présentoirs de produits pour mesurer l’impact d’un changement de marchandisage sur le trafic au sein du magasin. Le système permet au gérant du magasin d’identifier les nouveaux produits qui modifient le plus l’engagement des clients.

**Gestion de file d’attente** : des caméras pointées sur les files aux caisses alertent les responsables quand le délai d’attente est trop long, ce qui leur permet d’ouvrir davantage de caisses. Les données historiques sur l’abandon des files d’attente fournissent des informations sur le comportement des consommateurs.

**Respect du port du masque** : dans un magasin, vous pouvez diriger des caméras vers l’entrée pour vérifier si les clients qui entrent portent un masque. Vous pouvez ainsi veiller au respect des règles de sécurité et analyser des statistiques d’agrégation pour obtenir des insights sur les tendances liées au port du masque. 

**Occupation et analyse de bâtiment** : un immeuble de bureaux utilise des caméras pointées sur les accès à des espaces clés pour mesurer la fréquentation de ceux-ci et la façon dont les personnes utilisent l’espace de travail. Les insights permettent au gestionnaire du bâtiment d’ajuster le service et l’agencement pour mieux servir les occupants.

**Détection du personnel minimal** : dans un centre de données, des caméras surveillent l’activité autour des serveurs. Quand des employés interviennent physiquement sur des équipements sensibles, deux personnes doivent toujours être présentes pendant la réparation pour des raisons de sécurité. Les caméras sont utilisées pour vérifier le respect de cette consigne.

**Optimisation de l’espace de travail** : dans un établissement de restauration rapide, des caméras en cuisine sont utilisées pour générer des informations agrégées sur le flux de travail des employés. Cela permet aux responsables d’améliorer les processus et la formation de l’équipe.

## <a name="considerations-when-choosing-a-use-case"></a>Aspects à prendre en considération lors du choix d’un cas d’usage de l’analyse spatiale

**Éviter les alertes de sécurité critiques** : l’analyse spatiale n’est pas conçue pour générer des alertes de sécurité critiques en temps réel. Elle ne doit pas être à la base de scénarios où des alertes en temps réel sont nécessaires pour déclencher une intervention afin d’éviter tout dommage corporel, comme la désactivation d’une grosse machine en présence d’une personne. Elle peut être utilisée à des fins de réduction des risques sur la base de statistiques, et d’intervention pour réduire des comportements à risque, comme des personnes qui pénètrent dans une zone restreinte ou interdite.

**Éviter d’utiliser l’analyse spatiale pour la prise de décisions relatives à l’emploi** : l’analyse spatiale fournit des mesures probabilistes concernant l’emplacement et le déplacement des personnes au sein d’un espace. Si ces données peuvent être utiles pour agréger l’amélioration de processus, elles ne constituent pas un bon indicateur des performances individuelles des travailleurs et ne doivent pas être utilisées pour prendre des décisions relatives à l’emploi.

**Éviter d’utiliser l’analyse spatiale pour la prise de décisions relatives à l’intégrité** : l’analyse spatiale fournit des données probabilistes et partielles relatives aux déplacements des personnes. Ces données ne sont pas appropriées pour prendre des décisions liées à l’intégrité des personnes.

**Éviter d’utiliser l’analyse spatiale dans des espaces protégés** : il convient de préserver l’intimité des personnes en évaluant les emplacements et les positions des caméras, en ajustant les angles et les zones d’intérêt de celles-ci afin qu’elles ne pointent pas sur des zones protégées telles que des toilettes.

**Considérer avec prudence l’utilisation de l’analyse spatiale dans des écoles ou des établissements de soins pour personnes âgées** : l’analyse spatiale n’a pas été testée de manière approfondie avec des données relatives à des mineurs de moins 18 ans ou des adultes de plus de 65 ans. Nous recommandons aux clients d’évaluer minutieusement les taux d’erreur de leur scénario dans des environnements où ces catégories d’âge sont prédominantes.

**Considérer avec prudence l’utilisation de l’analyse spatiale dans des espaces publics** : il convient d’évaluer les emplacements et positions des caméras, en ajustant les angles et les zones d’intérêt de façon à minimiser la collecte d’images d’espaces publics. Les conditions lumineuses et météorologiques dans des espaces publics tels que des rues et des parcs ont une incidence significative sur les performances du système d’analyse spatiale, et il est extrêmement difficile de fournir une divulgation efficace dans des espaces publics.

## <a name="spatial-analysis-gating-for-public-preview"></a>Contrôle d’accès à l’analyse spatiale pour la préversion publique

Pour nous assurer que l’analyse spatiale soit utilisée pour les scénarios pour lesquels elle a été conçue, nous mettons cette technologie à la disposition des clients par le biais d’un processus de demande. Pour accéder à l’analyse spatiale, vous devez commencer par remplir notre formulaire de demande en ligne. [Commencez votre demande ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

L’accès à la préversion publique de l’analyse spatiale est à l’entière discrétion de Microsoft selon nos critères d’admissibilité, notre processus de vérification et notre capacité à prendre en charge un nombre limité de clients pendant cette période de préversion contrôlée. Dans le cadre de la préversion publique, nous recherchons des clients qui ont un lien significatif avec Microsoft, sont désireux de collaborer avec nous sur les cas d’usage recommandés, ainsi que d’autres scénarios en accord avec nos engagements en matière d’IA responsable.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Caractéristiques et limitations de l’analyse spatiale](/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)