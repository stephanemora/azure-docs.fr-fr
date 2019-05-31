---
title: Comparer les lignes de base avec le Monitoring d’intégrité de fichier dans Azure Security Center | Microsoft Docs
description: Découvrez comment comparer les lignes de base avec le Monitoring d’intégrité de fichier dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358437"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparer les lignes de base à l’aide de la surveillance de l’intégrité du fichier (FIM)

Surveillance de l’intégrité du fichier (FIM) vous informe lorsque les modifications se produisent dans les zones sensibles dans vos ressources, afin de pouvoir examiner et résoudre toute activité non autorisée. FIM surveille les fichiers de Windows, les registres de Windows et les fichiers Linux.

Cette rubrique explique comment activer FIM sur les fichiers et les registres. Pour plus d’informations sur FIM, consultez [Monitoring d’intégrité de fichier dans Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Pourquoi utiliser FIM ?

Système d’exploitation, les applications et les configurations associées contrôlent l’état de sécurité et le comportement de vos ressources. Par conséquent, les attaquants ciblent les fichiers qui contrôlent vos ressources, afin de rattraper le système d’exploitation de la ressource et/ou d’exécuter des activités sans être détecté.

En fait, nombreuses normes de conformité aux réglementations telles que PCI-DSS et ISO 17799 nécessitent l’implémentation de contrôles FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Activer les vérifications de Registre intégré récursive

Les valeurs par défaut de ruche de Registre FIM fournissent un moyen pratique pour surveiller les modifications récursive dans les zones de sécurité communes.  Par exemple, un adversaire peut configurer un script à exécuter dans le contexte LOCAL_SYSTEM en configurant une exécution au démarrage ou arrêt.  Pour surveiller les modifications de ce type, activer la vérification intégrée.  

![Registre](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Vérifications de récursive s’appliquent uniquement aux ruches de sécurité recommandées et pas aux chemins d’accès de Registre personnalisée.  

## <a name="adding-a-custom-registry-check"></a>Ajout d’une vérification du Registre personnalisée

Lignes de base de FIM démarrer en identifiant les caractéristiques d’un état correct connu pour le système d’exploitation prenant en charge d’application.  Pour cet exemple, nous allons nous concentrer sur les configurations de stratégie de mot de passe pour Windows Server 2008 et versions ultérieures.


|Nom de la stratégie                 | Paramètre de Registre|
|---------------------------------------|-------------|
|Contrôleur de domaine : Refuser les modifications de mot de passe de compte d’ordinateur| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Membre de domaine : Chiffrer ou signer les données des canaux sécurisés (toujours) numériquement|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Membre de domaine : Chiffrer numériquement les données des canaux sécurisés (lorsque cela est possible)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Membre de domaine : Signer numériquement les données de canal (si possible) sécurisés|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Membre de domaine : Désactiver les modifications de mot de passe du compte ordinateur|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Membre de domaine : Ancienneté de mot de passe du compte ordinateur maximale|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Membre de domaine : Nécessite une clé de session forte (Windows 2000 ou version ultérieure)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Sécurité réseau : Restreindre NTLM :  Authentification NTLM dans ce domaine|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Sécurité réseau : Restreindre NTLM : Ajouter des exceptions de serveur dans ce domaine|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Sécurité réseau : Restreindre NTLM : Auditer l’authentification NTLM dans ce domaine|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Pour en savoir plus sur les paramètres de Registre pris en charge par les différentes versions de système d’exploitation, reportez-vous à la [feuille de calcul de référence des paramètres de stratégie de groupe](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Pour configurer FIM pour surveiller les lignes de base de Registre :*

1. Dans le **ajouter le Registre Windows pour le suivi des modifications** fenêtre, dans le **clé de Registre Windows** texte, entrez la clé de Registre.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Activer FIM sur un Registre](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Suivi des modifications apportées aux fichiers de Windows

1. Dans le **ajouter le fichier Windows pour le suivi des modifications** fenêtre, dans le **chemin d’accès de l’entrée** texte, entrez le dossier qui contient les fichiers que vous souhaitez suivre. Dans l’exemple dans la figure suivante, **Contoso Web application** réside dans le D:\ lecteur dans le **ContosWebApp** structure de dossiers.  
1. Créer une entrée de fichier Windows personnalisée en fournissant un nom de la classe de paramètre, l’activation de la récursivité et en spécifiant le dossier supérieur avec un suffixe de caractère générique (*).

    ![Activer FIM sur un fichier](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Récupération des données modifiées

Fichier de données se trouvent dans l’Analytique de journal Azure Monitoring d’intégrité / ConfigurationChange table définie.  

 1. Définir un intervalle de temps pour récupérer un résumé des modifications par ressource.
Dans l’exemple suivant, nous sommes en train de toutes les modifications apportées au cours des 14 derniers jours dans les catégories de Registre et de fichiers :

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Pour afficher les détails de la modification du Registre :

    1. Supprimer **fichiers** à partir de la **où** clause, 
    1. Supprimez la ligne de synthèse et remplacez-le par une clause de classement :

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Les rapports peuvent être exportées au format CSV pour archivage et/ou canalise à un rapport Power BI.  

![Données FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)