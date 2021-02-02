---
title: Notes de publication pour Azure SQL Edge
description: Notes de publication détaillant les nouveautés ou les changements apportés aux images Azure SQL Edge.
keywords: notes de publication SQL Server
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696377"
---
# <a name="azure-sql-edge-release-notes"></a>Notes de publication Azure SQL Edge 

Cet article décrit les nouveautés et les changements apportés à chaque nouvelle build d’Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

Moteur SQL build 15.0.2000.1554

### <a name="fixes"></a>Correctifs

- Diffusion en continu T-SQL  
   - Correctif dans les propriétés et autorisations pour les objets de diffusion en continu
   - Améliorations de la journalisation avec la rotation des journaux et l’attribution d’un préfixe aux journaux
   - Azure Stream Analytics : Améliorations de la journalisation, code d’erreur/messages d’erreur améliorés dans les adaptateurs 

- ONNX
    - Correctifs de bogues pour scénario de requête parallèle et échecs de nettoyage du modèle
    - Mise à niveau du runtime ONNX vers 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

Moteur SQL build 15.0.2000.1553

### <a name="whats-new"></a>Nouveautés

- Autorisation des expressions Date_Bucket définies dans les colonnes calculées.

### <a name="fixes"></a>Correctifs

- Correctif de stratégie de conservation pour supprimer une table dont la stratégie de conservation est activée avec un délai d’expiration infini
- Prise en charge du déploiement DacFx pour les fonctionnalités de streaming et les fonctionnalités de stratégie de conservation 
- Correctif de déploiement DacFx pour activer le déploiement à partir d’un dossier imbriqué dans une URL SAS 
- Correction PREDICT pour prendre en charge les noms de colonnes longs dans les messages d’erreur

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

Moteur SQL build 15.0.2000.1552

### <a name="whats-new"></a>Nouveautés
- Images conteneur basées sur Ubuntu 18.04 
- Prise en charge de la syntaxe de `IGNORE NULL` et `RESPECT NULL` avec les fonctions `LAST_VALUE()` et `FIRST_VALUE()` 
- Améliorations de la fiabilité pour PREDICT avec ONNX
- Prise en charge du nettoyage basé sur la stratégie de conservation des données :
   - Prise en charge de la mémoire tampon en anneau pour une tâche de nettoyage de la conservation des données à des fins de résolution des problèmes
- Prise en charge de nouvelles fonctionnalités : 
   - Récupération rapide
   - Paramétrage automatique des requêtes
   - Scénarios d’exécution parallèle
- Améliorations de l’économie d’énergie pour le mode basse consommation
- Prise en charge de nouvelles fonctionnalités de streaming : 
   - [Fenêtres d’instantané](/stream-analytics-query/snapshot-window-azure-stream-analytics) : Un nouveau type de fenêtre vous permet de regrouper les événements qui se produisent en même temps.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) et [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) peuvent être activés en tant que fonctions analytiques. Vous pouvez retourner des enregistrements classés selon la colonne de votre choix. Ils n’ont pas besoin de faire partie d’une fenêtre. 
   - Améliorations apportées à [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Correctifs
- Messages d’erreur supplémentaires et détails pour la résolution des problèmes liés aux opérations de streaming T-SQL 
- Améliorations visant à préserver l’autonomie de la batterie en mode inactif 
- Correctifs du moteur de streaming T-SQL : 
   - Nettoyage des travaux de streaming arrêtés 
   - Correctifs pour la localisation 
   - Prise en charge améliorée d’Unicode 
   - Amélioration du débogage pour le streaming T-SQL de SQL Edge, ce qui permet aux utilisateurs d’obtenir des informations sur les erreurs liées aux échecs de travaux de get_streaming_job
- Nettoyage basé sur la stratégie de conservation des données : 
    - Correctifs pour les scénarios de création et de nettoyage des stratégies de conservation
- Correctifs pour les tâches de minuteur en arrière-plan visant à améliorer l’économie d’énergie en mode basse consommation

### <a name="known-issues"></a>Problèmes connus 
- La fonction T-SQL Date_Bucket ne peut pas être utilisée dans une colonne calculée.


## <a name="ctp-23"></a>CTP 2.3
Moteur SQL build 15.0.2000.1549
### <a name="whats-new"></a>Nouveautés
- Prise en charge des origines personnalisées dans la fonction Date_Bucket() 
- Prise en charge des fichiers BACPAC dans le cadre du déploiement SQL
- Prise en charge du nettoyage basé sur la stratégie de conservation des données :      
   - Prise en charge de DDL pour l’activation de la stratégie de conservation 
   - Nettoyage des procédures stockées et tâche de nettoyage en arrière-plan
   - Événements étendus pour superviser les tâches de nettoyage

### <a name="fixes"></a>Correctifs
- Messages d’erreur supplémentaires et détails pour la résolution des problèmes liés aux opérations de streaming T-SQL 
- Améliorations visant à préserver l’autonomie de la batterie en mode inactif 
- Moteur de streaming T-SQL : 
   - Correctif pour le problème de filigrane bloqué dans la fenêtre récurrente de sous-streaming 
   - Correctif de gestion des exceptions de framework visant à garantir leur collecte en tant qu’erreurs actionnables par l’utilisateur


## <a name="ctp-22"></a>CTP 2.2
Moteur SQL build 15.0.2000.1546
### <a name="whats-new"></a>Nouveautés
- Prise en charge des conteneurs non racine 
- Prise en charge de l’utilisation et de la collecte de données de diagnostic 
- Mises à jour du streaming T-SQL :
   - Prise en charge des caractères Unicode pour les noms d’objets de flux

### <a name="fixes"></a>Correctifs
- Mises à jour du streaming T-SQL :
   - Améliorations du nettoyage de processus
   - Améliorations de la journalisation et des diagnostics
- Amélioration des performances de l’ingestion de données

## <a name="ctp-21"></a>CTP 2.1 
Moteur SQL build 15.0.2000.1545
### <a name="fixes"></a>Correctifs
- Les modèles PREDICT avec ONNX sont autorisés à prendre en charge un problème de CPUID dans ARM 
- Amélioration de la gestion du chemin de la défaillance au démarrage du streaming T-SQL
- Correction de la valeur du retard du filigrane dans les métriques de travail en l’absence de données.
- Correction d’un problème lié à l’adaptateur de sortie quand l’adaptateur a un schéma variable entre les lots  

## <a name="ctp-20"></a>CTP 2.0 
Moteur SQL build 15.0.2000.1401
### <a name="whats-new"></a>Nouveautés
-   Nom du produit mis à jour vers *Azure SQL Edge*
-  Fonction Date_Bucket :
    - Prise en charge des types Date, Time et DateTime
- PREDICT avec ONNX :
    - Obligations relatives à ONNX pour le paramètre RUNTIME  
- Prise en charge du streaming T-SQL (préversion limitée) 
 
### <a name="known-issues"></a>Problèmes connus

- Problème : Défaillances potentielles liées à l’application d’un DACPAC au démarrage en raison d’un problème de synchronisation.
- Solution de contournement : Redémarrez SQL Server. Sinon, le conteneur réessaie d’appliquer le DACPAC.

### <a name="request-support"></a>Demande d'assistance
Vous pouvez demander un support dans la [page de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Sélectionnez les champs suivants : 
- **Type de problème** : *Technique* 
- **Service** : *IoT Edge*
- **Type de problème** : *Mon problème concerne un module IoT Edge*
- **Sous-type de problème** : *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Capture d’écran montrant un exemple de ticket de support.":::

## <a name="ctp-15"></a>CTP 1.5
Moteur SQL build 15.0.2000.1331
### <a name="whats-new"></a>Nouveautés
- Fonction Date_Bucket :
    - Prise en charge du type DateTimeOffset
- Modèles PREDICT avec ONNX :
  - Prise en charge de NVARCHAR
 
## <a name="ctp-14"></a>CTP 1.4
Moteur SQL build 15.0.2000.1247
### <a name="whats-new"></a>Nouveautés
-   Modèles PREDICT avec ONNX :
    - Prise en charge de VARCHAR
    - Migration vers la version 1.0 du runtime ONNX 

- Les fonctionnalités suivantes sont activées :
  - Support CDC
  - Table d’historique avec compression
  - Facteur d’échelle supérieur pour la lecture anticipée des journaux
  - Pushdown des filtres ES en mode batch
  - Optimisations de la lecture anticipée
 
## <a name="ctp-13"></a>CTP 1.3
Moteur SQL build 15.0.2000.1147
### <a name="whats-new"></a>Nouveautés
- Déploiement du portail Azure IoT : 
    - Support du déploiement d’images AMD64 et ARM
    - Support de la création de travaux de diffusion en continu
    - Déploiement du DACPAC
- Modèles PREDICT avec ONNX :
    - Supporte des types numériques
- Les fonctionnalités suivantes sont activées :
    - Agrégat de pushdown pour l’analyse des banques de colonnes
    - Analyses de carrousel
- Travail de réduction de l’empreinte et de la consommation de mémoire
