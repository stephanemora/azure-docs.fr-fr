---
title: Exemple de blueprint New Zealand ISM Restricted
description: Vue d’ensemble de l’exemple de blueprint New Zealand ISM Restricted Cet exemple de blueprint aide les clients à évaluer des contrôles spécifiques.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: c406ec453cf980c1e065c70e69151b692a878596
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125766"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Exemple de blueprint New Zealand ISM Restricted

L’exemple de blueprint New Zealand ISM Restricted fournit des garde-fous de gouvernance utilisant [Azure Policy](../../policy/overview.md) qui vous aident à évaluer des contrôles spécifiques du [New Zealand Information Security Manual](https://www.nzism.gcsb.govt.nz/). Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies de base pour toute architecture déployée sur Azure devant implémenter des contrôles New Zealand ISM Restricted.

## <a name="control-mapping"></a>Correspondance des contrôles

Le [mappage de contrôles Azure Policy](../../policy/samples/new-zealand-ism.md) fournit des détails sur les définitions de stratégie incluses dans ce blueprint et sur la façon dont ces définitions de stratégie sont mappées aux **contrôles** dans le New Zealand Information Security Manual. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Déployer

Pour déployer l’exemple de blueprint Azure Blueprints New Zealand ISM Restricted, vous devez effectuer les étapes suivantes :

> [!div class="checklist"]
> - Créer un blueprint à partir de l’exemple
> - Marquer la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

### <a name="create-blueprint-from-sample"></a>Créer un blueprint à partir de l’exemple

Commencez par implémenter l’exemple de blueprint. Pour cela, créez un blueprint dans votre environnement en vous servant de l’exemple comme point de départ.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage** à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Recherchez l’exemple de blueprint **New Zealand ISM Restricted** sous _Autres exemples_ et sélectionnez **Utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : donnez un nom à votre copie de l’exemple de blueprint New Zealand ISM Restricted.
   - **Emplacement de la définition** : utilisez les points de suspension et sélectionnez le groupe d’administration dans lequel vous souhaitez enregistrer votre copie de l’exemple.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui sont inclus dans l’exemple de blueprint. De nombreux artefacts ont des paramètres que nous définirons plus tard. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

### <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. Vous pouvez personnaliser la copie de l’exemple de blueprint en fonction de votre environnement et de vos besoins, mais ces modifications peuvent l’éloigner de son alignement avec les contrôles New Zealand ISM Restricted.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page à droite, indiquez la **Version** de votre copie de l’exemple de blueprint. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Dans **Notes de changement**, indiquez par exemple « Première version publiée à partir de l’exemple de blueprint New Zealand ISM Restricted ». Sélectionnez ensuite **Publier** en bas de la page.

### <a name="assign-the-sample-copy"></a>Affecter la copie de l’exemple

Une fois que la copie de l’exemple de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la copie de l’exemple de blueprint soit unique.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base

     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre copie de l’exemple de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli en fonction du nom du blueprint.
       Changez-le si nécessaire ou laissez-le tel quel.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée. Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition du blueprint** : Choisissez une version **publiée** de votre copie de l’exemple de blueprint.

   - Verrouiller l'affectation

     Sélectionnez le paramètre de verrouillage de blueprint pour votre environnement. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../concepts/resource-locking.md).

   - Identité managée

     Conservez l’option par défaut, _Affecté(e) par le système_, pour l’identité managée.

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour obtenir la liste complète des paramètres d’artefact et leur description, consultez le [tableau des paramètres d’artefact](#artifact-parameters-table).

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page. L’affectation du blueprint est créée et le déploiement de l’artefact démarre. Le déploiement prend environ une heure. Pour vérifier l’état du déploiement, ouvrez l’affectation du blueprint.

> [!WARNING]
> Le service Azure Blueprints et les exemples de blueprint intégrés sont **gratuits**. Les ressources Azure sont [facturées par produit](https://azure.microsoft.com/pricing/). Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de blueprint.

### <a name="artifact-parameters-table"></a>Tableau des paramètres d’artefact

Le tableau suivant fournit la liste des paramètres d’artefact de blueprint :

|Nom de l’artefact|Type d’artefact|Nom du paramètre|Description|
|-|-|-|-|
|New Zealand ISM Restricted|Affectation de rôle|Liste des utilisateurs à inclure dans le groupe Administrateurs de machine virtuelle Windows|Liste séparée par des points-virgules des utilisateurs qui doivent être inclus dans le groupe Administrateurs local. Ex. : Administrator; myUser1; myUser2|
|New Zealand ISM Restricted|Affectation de rôle|Liste des utilisateurs qui doivent être exclus du groupe Administrateurs de machines virtuelles Windows|Liste séparée par des points-virgules des utilisateurs qui doivent être exclus du groupe Administrateurs local. Ex. : Administrator; myUser1; myUser2|
|New Zealand ISM Restricted|Affectation de rôle|Liste des utilisateurs que le groupe Administrateurs de machine virtuelle Windows doit seulement inclure|Liste séparée par des points-virgules de tous les membres attendus du groupe local Administrateurs. Par ex. : Administrateur; myUser1; myUser2|
|New Zealand ISM Restricted|Affectation de rôle|ID d’espace de travail Log Analytics pour les rapports d’agent de machine virtuelle|ID (GUID) de l’espace de travail Log Analytics où les agents de machine virtuelle doivent envoyer leur rapport|
|New Zealand ISM Restricted|Affectation de rôle|Effet de stratégie : Web Application Firewall (WAF) doit être activé pour Azure Front Door Service|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les paramètres d’évaluation des vulnérabilités de SQL Server doivent contenir une adresse e-mail pour recevoir les rapports d’analyse|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les recommandations de durcissement du réseau adaptatif doivent être appliquées sur les machines virtuelles connectées à Internet|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Plusieurs propriétaires doivent être affectés à votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Le chiffrement de disque doit être appliqué sur les machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Web Application Firewall (WAF) doit utiliser le mode spécifié pour Application Gateway|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Exigence du mode WAF pour Application Gateway|Le mode de prévention ou de détection doit être activé sur le service Application Gateway|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La technologie Transparent Data Encryption doit être activée sur les bases de données SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : l’évaluation des vulnérabilités doit être activée sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Windows à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : déployer-configurer l’agent de dépendances pour qu’il soit activé sur les machines virtuelles identiques Windows|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de stratégie : Seules les connexions sécurisées à votre instance Azure Cache pour Redis doivent être activées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Auditer les machines Windows pour lesquelles des membres spécifiés dans le groupe Administrateurs sont manquants|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Windows à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : [préversion] : l’agent Log Analytics doit être activé pour les images de machine virtuelle listées|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Linux à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : [préversion] : l’agent Log Analytics doit être activé pour les images de machine virtuelle listées|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent restreindre l’accès réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Windows à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : déployer-configurer l’agent de dépendances pour qu’il soit activé sur les groupes de machines virtuelles identiques Windows|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Auditer les machines Windows qui ont des comptes supplémentaires dans le groupe Administrateurs|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La sécurisation du transfert vers des comptes de stockage doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Web Application Firewall (WAF) doit utiliser le mode spécifié pour Azure Front Door Service|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Condition du mode WAF pour Azure Front Door Service|Le mode de prévention ou de détection doit être activé sur le service Azure Front Door|
|New Zealand ISM Restricted|Affectation de rôle|Effet de stratégie : Les contrôles d’application adaptatifs pour définir les applications sécurisées doivent être activés sur vos machines|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Trois propriétaires au plus doivent être désignés pour votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : [Préversion] : L’accès public au compte de stockage doit être interdit|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de stratégie : Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de stratégie : Web Application Firewall (WAF) doit être activé pour Application Gateway|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Inclure les serveurs connectés à Arc lors de l’évaluation de la stratégie : Auditer les serveurs web Windows qui n’utilisent pas de protocoles de communication sécurisés|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Version TLS minimale pour les serveurs web Windows|Les serveurs web Windows avec des versions de TLS inférieures seront évalués comme non conformes|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Linux à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : l’agent Log Analytics doit être activé dans les groupes de machines virtuelles identiques pour les images de machine virtuelle listées|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Facultatif : liste des images de machine virtuelle personnalisées qui ont pris en charge le système d’exploitation Windows à ajouter à l’étendue supplémentaire aux images de la Galerie pour la stratégie : l’agent Log Analytics doit être activé dans les groupes de machines virtuelles identiques pour les images de machine virtuelle listées|Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Inclure les serveurs Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Auditer les machines Windows qui ont les membres spécifiés dans le groupe Administrateurs|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les comptes déconseillés doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Function App ne doit pas être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les abonnements Azure doivent avoir un profil de journal pour le journal d’activité|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Liste des types de ressources devant avoir les journaux de diagnostic activés||
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Des mises à jour système doivent être installées sur vos machines|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L’extension Microsoft IaaSAntimalware doit être déployée sur les serveurs Windows|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L'application web ne doit pas être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Azure DDoS Protection Standard doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Superviser les agents Endpoint Protection manquants dans Azure Security Center|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Le journal d’activité doit être conservé pendant au moins un an|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les ports de gestion des machines virtuelles doivent être protégés avec le contrôle d’accès réseau juste-à-temps|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L'application API doit uniquement être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Auditer les machines Windows sur lesquelles Windows Defender Exploit Guard n’est pas activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Inclure les serveurs connectés à Arc lors de l’évaluation de la stratégie : Auditer les machines Windows sur lesquelles Windows Defender Exploit Guard n’est pas activé|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|État de conformité à signaler pour les machines Windows sur lesquelles Windows Defender Exploit Guard n’est pas disponible|Windows Defender Exploit Guard est disponible uniquement à partir de Windows 10/Windows Server avec la mise à jour 1709. Si vous définissez cette valeur sur « Non conforme », les machines avec des versions antérieures sur lesquelles Windows Defender Exploit Guard n’est pas disponible (par exemple, Windows Server 2012 R2) ne sont pas conformes. Si vous définissez cette valeur sur « Conforme », ces machines sont conformes.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Auditer les machines Linux qui autorisent les connexions à distance des comptes sans mot de passe|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Auditer les machines Linux qui autorisent les connexions à distance des comptes sans mot de passe|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les machines Windows doivent répondre aux exigences de « Paramètres de sécurité - Stratégies de compte »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Conserver l’historique des mots de passe pour les comptes locaux de machine virtuelle Windows|Spécifie les limites de réutilisation du mot de passe : nombre de fois qu’un mot de passe doit être créé pour un compte d’utilisateur avant de pouvoir être répété.|
|New Zealand ISM Restricted|Affectation de rôle|Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Les machines Windows doivent répondre aux exigences de « Paramètres de sécurité - Stratégies de compte »|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Âge maximal du mot de passe pour les comptes locaux de machine virtuelle Windows|Spécifie le nombre maximal de jours avant de pouvoir changer le mot de passe d’un compte d’utilisateur. Le format de la valeur est deux entiers séparés par une virgule, indiquant une plage inclusive|
|New Zealand ISM Restricted|Affectation de rôle|Âge minimal du mot de passe pour les comptes locaux de machine virtuelle Windows|Spécifie le nombre minimal de jours devant s’écouler avant que le mot de passe d’un compte d’utilisateur ne puisse être modifié.|
|New Zealand ISM Restricted|Affectation de rôle|Longueur minimale du mot de passe pour les comptes locaux de machine virtuelle Windows|Spécifie le nombre minimal de caractères du mot de passe d’un compte d’utilisateur.|
|New Zealand ISM Restricted|Affectation de rôle|Le mot de passe doit respecter les exigences de complexité pour les comptes locaux de machine virtuelle Windows|Spécifie si le mot de passe d’un compte d’utilisateur doit être complexe. S’il est nécessaire, un mot de passe complexe ne doit pas contenir tout ou partie du nom de compte de l’utilisateur, doit avoir au moins six caractères, avoir un mélange de majuscules, de minuscules, de chiffres et de caractères non alphabétiques.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les machines virtuelles connectées à Internet doivent être protégées avec des groupes de sécurité réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Auditer les machines Linux qui ont des comptes sans mot de passe|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Ajouter des serveurs connectés à Arc pendant l’évaluation de la stratégie : Auditer les machines Linux qui ont des comptes sans mot de passe|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chaque machine connectée à Arc.|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : [Préversion] : Tout le trafic Internet doit être routé via votre Pare-feu Azure déployé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|New Zealand ISM Restricted|Affectation de rôle|Effet de la stratégie : Les vulnérabilités de vos bases de données SQL doivent être éliminées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Étapes suivantes

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).