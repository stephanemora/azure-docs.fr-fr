---
title: Versions Microsoft Security Code Analysis
description: Cet article décrit les prochaines versions de l’extension Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146122"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Versions Microsoft Security Code Analysis et feuille de route

En partenariat avec Developer Support, l’équipe d’analyse Microsoft Security Code Analysis est fière d’annoncer les améliorations récentes et à venir de notre extension MSCA. Veuillez consulter la feuille de route ci-dessous.

![Publications](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v2.0 : Publié en avril 2020

### <a name="innovations--improvements"></a>Innovations et améliorations

- **Moteur principal**

   - Mise à niveau des performances moyennes de 25 % avec des délais d’exécution quasi linéaires
   - Recherche et classement en fonction du contexte et des preuves pour une plus grande précision
   - Améliorations apportées aux détections de mot de passe générales et à la logique de correspondance pour les espaces réservés évidents (par exemple, fakePassword)

- **Couverture** : prise en charge de plus de 25 types de secrets, y compris les éléments suivants les plus demandés :

   - Phrase secrète du certificat du compte Service Fabric
   - Secret/clé API du client
   - En-tête d'autorisation HTTP
   - Clé d’accès à la clé secrète du client Amazon S3
   - Jeton d’accès au client Azure Active Directory
   - Clé principale/API Azure Function
   - Clé d’accès Power BI
   - Modèle de mot de passe Azure Resource Manager

- **Sorties**

   - Prise en charge des formats de fichier de sortie SARIF 2.1 et CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0 : Publié en avril 2020

### <a name="improvements"></a>Améliorations

- FONCTIONNALITÉ : Mise à jour vers la version finale SARIF v2 (version 2.1.16). Permet la mise en cache des résultats lors du passage des hachages sur la ligne de commande, ce qui améliore considérablement les performances lors de l’analyse récursive des répertoires avec plusieurs copies des cibles d’analyse.
- RÉSOLUTION DE BOGUE : Correction d’une faute de frappe dans la sortie BA2021.DoNotMarkWritableSectionsAsExecutable.
- PERFORMANCES : Éliminez le chargement PDB pour tous les assemblys managés qui ne sont pas en mode mixte, y compris les binaires de la bibliothèque IL (par anticipation).
- CORRECTIF DE FAUX NÉGATIF : Vérifier qu’un fichier PDB placé avec un binaire correspond réellement au binaire en cours d’analyse
- FONCTIONNALITÉ : Fournit un argument --local-symbol-directories pour spécifier des emplacements de recherche PDB supplémentaires (locaux, serveur sans symbole)
- CORRECTIF DE FAUX POSITIF : Ignorez l’analyse pilotée par PDB pour le fichier exécutable natif .NET Core généré (code non contrôlable par l’utilisateur).

## <a name="whats-next-in-fy20"></a>Quelles sont les prochaines étapes pour 2020 ?

- Outil d’analyse de la sécurité Java
- Outil d’analyse de la sécurité Python
- ES Lint remplacera TS Lint pour TypeScript et JavaScript

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions sur l'intégration et l'installation de Microsoft Security Code Analysis, consultez notre [Guide d'intégration et d'installation](security-code-analysis-onboard.md).

Si vous avez d’autres questions sur l’extension et les outils proposés, voir notre [page FAS](security-code-analysis-faq.md).
