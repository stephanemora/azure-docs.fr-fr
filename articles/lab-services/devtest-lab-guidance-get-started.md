---
title: Prise en main d’Azure DevTest Labs
description: Cet article fournit les principaux scénarios d’emplois d’Azure DevTest Labs et deux chemins d’accès généraux pour commencer à utiliser le service dans votre organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 87baef8ddb5b5d8fc979ba5afb9f9b13cb4fc2ef
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876534"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Prise en main d’Azure DevTest Labs
Pour explorer DevTest Labs, il existe deux chemins généraux : déploiement à l’échelle ou déploiement de preuve de concept. 

Un **déploiement à l’échelle** consiste en des semaines/mois d’examen et de planification dans le but de déployer DevTest Labs dans toute l’entreprise pour des centaines voire des milliers de développeurs. 

Une **déploiement de preuve de concept** consiste en un effort concentré sur une seule équipe pour établir la valeur d’organisation. Si un déploiement à l’échelle semble tentant, l’approche a tendance à échouer plus souvent que l’option de preuve de concept. Par conséquent, nous vous recommandons de commencer doucement. Apprenez de la première équipe, répétez la même approche avec deux ou trois équipes supplémentaires, puis planifiez un déploiement à l’échelle en vous appuyant sur les connaissances acquises. Pour que l’option de preuve de concept réussisse, nous vous recommandons de choisir une ou deux équipes et d’identifier leurs scénarios (environnement de test ou environnement de développement), de documenter leurs cas d’utilisation actuels et de déployer DevTest Labs. 

## <a name="scenarios"></a>Scénarios
Il existe trois principaux scénarios pour une implémentation de DevTest Labs : 

- Postes de travail de développeur
- Environnements de test
- Labos/Formation/Hackathon

## <a name="developer-desktops"></a>Postes de travail de développeur
Les développeurs ont souvent des exigences distinctes concernant les ordinateurs de développement pour différents projets. Avec DevTest Labs, les développeurs peuvent accéder à des machines virtuelles à la demande préconfigurées pour s’adapter à leurs scénarios les plus courants. DevTest Labs permet de bénéficier des avantages suivants :

- Les organisations peuvent fournir des environnements de développement et de test garantissant la cohérence entre les équipes.
- Les développeurs peuvent rapidement approvisionner leurs ordinateurs de développement à la demande.
- Les développeurs peuvent approvisionner les ressources en libre-service sans avoir besoin d’autorisations de niveau abonnement.
- Les informaticiens ou les administrateurs peuvent définir préalablement la topologie de réseau, et les développeurs peuvent l’exploiter directement, manière simple et intuitive, sans accès spécial.
- Si besoin, les développeurs peuvent facilement personnaliser leurs ordinateurs de développement.
- Les administrateurs peuvent contrôler les coûts en s’assurant que :
    - Les développeurs ne peuvent pas obtenir plus de machines virtuelles que nécessaire pour le développement.
    - Les machines virtuelles sont éteintes lorsqu’elles ne sont pas utilisées.
    - Seul un sous-ensemble des tailles d’instance de machine virtuelle est autorisé pour les charges de travail spécifiques.

## <a name="test-environments"></a>Environnements de test
La création et la gestion des environnements de test dans une entreprise peuvent nécessiter un effort considérable. Avec DevTest Labs, il est facile de créer, mettre à jour ou dupliquer des environnements de test, ce qui permet aux équipes pour accéder à un environnement entièrement configuré lorsque cela est nécessaire. Dans ce scénario, DevTest Labs offre les avantages suivants :

- Les testeurs peuvent tester la dernière version de l’application en approvisionnant rapidement des environnements Windows et Linux à l’aide d’artefacts et de modèles réutilisables.
- Ils peuvent faire monter en puissance (scale up) leur test de charge en approvisionnant plusieurs agents de test.
- Les administrateurs peuvent connecter le laboratoire à Azure DevOps pour activer des scénarios DevOps
- Les administrateurs peuvent contrôler les coûts en s’assurant que :
    - Les testeurs ne peuvent pas obtenir plus de machines virtuelles qu’il n’est nécessaire.
    - Les machines virtuelles sont éteintes lorsqu’elles ne sont pas utilisées.
    - Seul un sous-ensemble des tailles d’instance de machine virtuelle est autorisé pour les charges de travail spécifiques.

## <a name="labs-workshops-trainings-and-hackathons"></a>Laboratoires, ateliers, formations et hackathons  
Un laboratoire dans Azure DevTest Labs constitue un excellent conteneur pour les activités temporaires telles que les ateliers, les laboratoires pratiques, les formations ou les hackathons. Ce service vous permet de créer un laboratoire où vous pouvez fournir des modèles personnalisés utilisables par chaque participant pour créer des environnements de formation identiques et isolés. Vous pouvez appliquer des stratégies pour vous assurer que les environnements de formation sont à la disposition des participants uniquement lorsque ces derniers en ont besoin et qu’ils contiennent suffisamment de ressources (telles que des machines virtuelles) pour la formation. Enfin, vous pouvez facilement partager le laboratoire avec les participants, qui peuvent y accéder en un seul clic. Une fois le cours terminé, il est facile de supprimer le laboratoire et toutes les ressources associées.


## <a name="next-steps"></a>Étapes suivantes
Voir l’article suivant de cette série : [Monter votre déploiement DevTest Labs](devtest-lab-guidance-scale.md)
