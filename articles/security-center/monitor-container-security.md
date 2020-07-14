---
title: Superviser la sécurité de vos conteneurs dans Azure Security Center
description: Découvrez comment vérifier l’état de la sécurité de vos conteneurs à partir d’Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 76bf38c9d15e977b39922fdfbc7d30f533707cda
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801374"
---
# <a name="monitor-the-security-of-your-containers"></a>Superviser la sécurité de vos conteneurs

Cette page explique comment utiliser les fonctionnalités de sécurité des conteneurs qui sont décrites dans l’[article Sécurité des conteneurs](container-security.md) de la section Concepts.


## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Analyser vos registres de conteneurs basés sur ARM pour identifier les vulnérabilités 

1. Pour activer les analyses de vulnérabilités dans vos images Azure Container Registry :

    1. Veillez à utiliser Azure Security Center avec le niveau tarifaire Standard.

    1. Dans la page **Tarification et paramètres**, activez le bundle facultatif Registres de conteneurs pour votre abonnement : ![Activation du bundle Registres de conteneurs](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center est maintenant prêt à analyser les images envoyées (push) au registre. 

        >[!NOTE]
        >Cette fonctionnalité est facturée par image.


1. Pour déclencher l’analyse d’une image, envoyez l’image à votre registre. 

    Une fois l’analyse terminée (généralement au bout d’une dizaine de minutes), les résultats sont disponibles dans les recommandations de Security Center.
    

1. Pour voir les résultats, accédez à la page **Recommandations**. Si des problèmes ont été détectés, vous voyez cette recommandation :

    ![Recommandation de correction des problèmes ](media/monitor-container-security/acr-finding.png)


1. Sélectionnez la recommandation. 
    La page de détails de la recommandation s’ouvre et présente des informations supplémentaires. Vous y trouverez notamment la liste des registres contenant des images vulnérables (« Ressources affectées ») et les étapes de correction. 

1. Sélectionnez un registre pour voir les référentiels vulnérables qu’il contient.

    ![Sélectionner un registre](media/monitor-container-security/acr-finding-select-registry.png)

    La page de détails du registre s’ouvre et présente la liste des référentiels affectés.

1. Sélectionnez un référentiel pour afficher les référentiels qui contiennent des images vulnérables.

    ![Sélectionner un référentiel](media/monitor-container-security/acr-finding-select-repository.png)

    La page de détails du référentiel s’ouvre. Elle liste les images vulnérables, accompagnées d’une évaluation de la gravité des vulnérabilités détectées.

1. Sélectionnez une image pour voir les vulnérabilités.

    ![Sélectionner une image](media/monitor-container-security/acr-finding-select-image.png)

    La liste des résultats pour l’image sélectionnée s’affiche.

    ![Liste des résultats](media/monitor-container-security/acr-findings.png)

1. Pour en savoir plus sur un résultat, sélectionnez-le. 

    Le volet des résultats détaillés s’ouvre.

    [![Volet des résultats détaillés](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Ce volet inclut une description détaillée du problème ainsi que des liens vers des ressources externes pour vous aider à atténuer les menaces.

1. Effectuez les étapes de la section Correction de ce volet.

1. Une fois que vous avez suivi les étapes requises pour corriger le problème de sécurité, remplacez l’image dans votre registre :

    1. Envoyez l’image mise à jour. Cette opération push déclenche une analyse. 
    
    1. Accédez à la page des recommandations pour voir si la recommandation « Les vulnérabilités dans les images Azure Container Registry doivent être corrigées » y figure. 
    
        Si vous voyez encore cette recommandation et que l’image corrigée figure toujours dans la liste des images vulnérables, recommencez les étapes de correction.

    1. Quand vous êtes sûr que l’image mise à jour a été envoyée et analysée, et qu’elle n’apparaît plus dans la recommandation, supprimez l’« ancienne » image vulnérable de votre registre.


## <a name="hardening-your-containers-docker-hosts"></a>Durcissement de la sécurité des hôtes Docker de vos conteneurs

Security Center supervise en continu la configuration de vos hôtes Docker et génère des recommandations de sécurité qui reflètent les normes du secteur.

Pour voir les recommandations de sécurité d’Azure Security Center pour les hôtes Docker de vos conteneurs :

1. Dans la barre de navigation de Security Center, ouvrez **Calcul et applications** et sélectionnez l’onglet **Conteneurs**.

1. Si vous le souhaitez, filtrez la liste de vos ressources de conteneur sur les machines hôtes de conteneur.

    ![Filtre des ressources de conteneur](media/monitor-container-security/container-resources-filter.png)

1. Dans la liste de vos machines hôtes de conteneur, sélectionnez-en une pour approfondir vos recherches.

    ![Recommandations pour l’hôte de conteneur](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    La **page Informations sur l’hôte de conteneur** s’ouvre et présente les détails de l’hôte ainsi qu’une liste de recommandations.

1. Dans la liste des recommandations, sélectionnez une recommandation à examiner plus en détail.

    ![Liste des recommandations pour l’hôte de conteneur](media/monitor-container-security/container-host-rec.png)

1. Vous pouvez également lire la description, et prendre connaissance des informations, des menaces et des étapes de correction. 

1. Sélectionnez **Entreprendre une action** en bas de la page.

    [![Bouton Entreprendre une action](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics s’ouvre sur une opération personnalisée prête à être exécutée. La requête personnalisée par défaut comprend une liste de toutes les règles ayant échoué qui ont été évaluées, ainsi que des instructions pour vous aider à résoudre les problèmes.

    [![Action Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Ajustez les paramètres de requête si nécessaire.

1. Lorsque vous êtes sûr que la commande est appropriée et prête pour votre hôte, sélectionnez **Exécuter**.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment utiliser les fonctionnalités de sécurité des conteneurs dans Security Center. 

Pour d’autres informations connexes, consultez les pages suivantes : 

- [Recommandations de Security Center pour les conteneurs](recommendations-reference.md#recs-containers)
- [Alertes pour le niveau du cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertes pour niveau de l’hôte du conteneur](alerts-reference.md#alerts-containerhost)
