---
title: Contrôles de l’exemple de blueprint HIPAA HITRUST
description: Mappage des contrôles des exemples de blueprint HIPAA HITRUST. Chaque contrôle est mis en correspondance avec une ou plusieurs stratégies Azure qui simplifient l’évaluation.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75470744"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mappage des contrôles de l’exemple de blueprint HIPAA HITRUST

L’article suivant explique comment l’exemple de blueprint HIPAA HITRUST Azure Blueprints est mappé aux contrôles HIPAA HITRUST. Pour plus d’informations sur les contrôles, consultez [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Les mappages suivants concernent les contrôles **HIPAA HITRUST**. Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] : Auditer les contrôles HIPAA HITRUST**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../../../policy/overview.md). Ces stratégies peuvent vous aider à [évaluer la conformité](../../../policy/how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance de 1:1 ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions Azure Policy pour cet exemple de blueprint de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Contrôle contre le code malveillant

Ce blueprint permet de gérer la protection des points de terminaison, notamment la protection contre le code malveillant, en affectant des définitions [Azure Policy](../../../policy/overview.md) qui supervisent l’absence de protection des points de terminaison sur les machines virtuelles dans Azure Security Center, et appliquent la solution anti-programme malveillant de Microsoft sur les machines virtuelles Windows.

- Déployer l’extension Microsoft IaaS Antimalware par défaut pour Windows Server
- Les journaux de diagnostic doivent être activés dans les comptes Batch
- Des mises à jour système doivent être installées sur vos machines


## <a name="management-of-removable-media"></a>Gestion des médias amovibles

En fonction du niveau de classification des données, l’organisation inscrit des médias (notamment des ordinateurs portables) avant leur utilisation, met en place des restrictions acceptables sur la façon dont ces médias sont utilisés et fournit un niveau approprié de protection physique et logique (y compris de chiffrement) pour les médias contenant des informations couvertes jusqu’à leur destruction ou leur nettoyage appropriés.

- Exiger un chiffrement sur les comptes Data Lake Store
- Le protecteur TDE de l’instance gérée SQL doit être chiffré avec votre propre clé.
- Le chiffrement de disque doit être appliqué sur les machines virtuelles
- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL


## <a name="information-exchange-policies-and-procedures"></a>Stratégies et procédures d’échange d’informations

Les fournisseurs de services cloud utilisent une plateforme de virtualisation reconnue par le secteur et des formats de virtualisation standard (par exemple, Open Virtualization Format, OVF) pour garantir l’interopérabilité. Ils ont aussi documenté les modifications personnalisées apportées à tous les hyperviseurs en cours d’utilisation et tous les crochets de virtualisation propres aux solutions soumis à la révision des clients.

- Prérequis de déploiement pour auditer les machines virtuelles Windows qui n’ont pas les applications spécifiées installées

## <a name="control-of-operational-software"></a>Contrôle des logiciels opérationnels 

L’organisation identifie les logiciels non autorisés sur le système informatique, notamment les serveurs, les stations de travail et les ordinateurs portables, utilise une stratégie visant à tout autoriser et à refuser par exception pour interdire l’exécution des logiciels non autorisés connus sur le système d’information, et révise et met à jour la liste des logiciels non autorisés régulièrement, au moins une fois par an.

- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Audit »
- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Stratégies d’audit système - Gestion des comptes »

## <a name="change-control-procedures"></a>Procédures de contrôle des modifications

L’intégrité de toutes les images de machines virtuelles est assurée à tout moment par la journalisation et le déclenchement d’une alerte pour toute modification apportée aux images de machines virtuelles et par la mise à la disposition des propriétaires et/ou clients par le biais de méthodes électroniques (par exemple, portails ou alertes) des résultats d’une modification ou d’un déplacement et de la validation consécutive de l’intégrité de l’image.

- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Stratégies d’audit système - Suivi détaillé »

## <a name="inventory-of-assets"></a>Inventaire des ressources 

Un inventaire des ressources et des services est tenu à jour.

- Les journaux de diagnostic dans les services de recherche doivent être activés.
- \[Préversion\] Exigences de déploiement pour l’audit des configurations des machines virtuelles Windows dans « Options de sécurité - Serveur réseau Microsoft »
- \[Préversion\] Exigences de déploiement pour l’audit des configurations des machines virtuelles Windows dans « Modèles d’administration - Réseau »

## <a name="control-of-technical-vulnerabilities"></a>Contrôle des vulnérabilités techniques 

Il existe une norme de configuration renforcée pour tous les composants système et réseau.

- Auditer des machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée
- L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL
- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités

## <a name="segregation-in-networks"></a>Séparation dans les réseaux

Les passerelles de sécurité de l’organisation (par exemple, les pare-feu) appliquent des stratégies de sécurité et sont configurées pour filtrer le trafic entre les domaines, bloquent l’accès non autorisé et sont utilisées pour assurer la séparation entre les segments de réseaux câblés internes, sans fil internes et externes (par exemple, Internet), y compris les zones DMZ et appliquent des stratégies de contrôle d’accès pour chacun des domaines.

- Provisionnement automatique de l’agent de supervision de sécurité
- Déployer Network Watcher lors de la création de réseaux virtuels

## <a name="input-data-validation"></a>Validation des données d’entrée

Pour les applications web publiques, les pare-feu au niveau de l’application sont implémentés pour contrôler le trafic. Pour les applications publiques non-web, l’organisation a implémenté un pare-feu réseau propre au type d’application. Si le trafic vers l’application publique est chiffré, l’appareil se trouve derrière le chiffrement ou il est capable de déchiffrer le trafic avant l’analyse.

- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Propriétés du pare-feu Windows »


## <a name="network-connection-control"></a>Contrôle de connexion réseau

Le trafic réseau est contrôlé conformément à la stratégie de contrôle d’accès de l’organisation par le biais du pare-feu et d’autres restrictions réseau pour chaque point d’accès réseau ou interface managée du service de télécommunication externe.

- L’accès par le biais de points de terminaison accessibles sur Internet doit être limité.
- Le protecteur TDE de l’instance gérée SQL doit être chiffré avec votre propre clé.
- Le débogage à distance doit être désactivé pour les applications API.

## <a name="network-controls"></a>Contrôles réseau

L’organisation utilise des canaux de communication sécurisés et chiffrés lors de la migration des serveurs physiques, applications ou données vers des serveurs virtualisés.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles.
- Le protecteur TDE du serveur SQL doit être chiffré avec votre propre clé.
- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Accès réseau »
- Auditer l'accès réseau non restreint aux comptes de stockage
- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Propriétés du pare-feu Windows »
- Déployer les paramètres de diagnostic à partir des groupes de sécurité réseau
- L’accès par le biais de points de terminaison accessibles sur Internet doit être limité.

## <a name="sensitive-system-isolation"></a>Isolation de système sensible

Les ressources système partagées (par exemple, les registres, la mémoire principale, le stockage secondaire) sont remises sur le système, protégées de la divulgation à d’autres systèmes/applications/utilisateurs et les utilisateurs ne peuvent pas accéder aux données restantes intentionnellement ou non.

- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager.

## <a name="security-of-network-services"></a>Sécurité de Network Services

Les services contractuels fournis par un fournisseur/responsable de services réseau sont officiellement gérés et supervisés pour veiller à ce qu’ils soient fournis en toute sécurité.

- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager.

## <a name="network-routing-control"></a>Contrôle de routage réseau

Les contrôles de routage sont implémentés par le biais de passerelles de sécurité (par exemple, des pare-feu) utilisées entre des réseaux internes et externes (par exemple, Internet et des réseaux tiers).

- Des contrôles d’application adaptatifs doivent être activés sur les machines virtuelles.

## <a name="information-exchange-policies-and-procedures"></a>Stratégies et procédures d’échange d’informations

L’organisation limite l’utilisation de médias de stockage portables contrôlés par elle-même par des personnes autorisées sur des systèmes d’information externes.

- Auditer l'accès réseau non restreint aux comptes de stockage
- Le débogage à distance doit être désactivé pour les applications web.
- L’application API doit uniquement être accessible par HTTPS.

## <a name="electronic-messaging"></a>Messagerie électronique

Des approbations sont obtenues avant d’utiliser les services publics externes, notamment la messagerie instantanée ou le partage de fichiers.

- \[Préversion\] Afficher les résultats de l’audit des machines virtuelles Linux qui n’ont pas d’autorisations de fichier de mot de passe définies sur 0644

## <a name="on-line-transactions"></a>Transactions en ligne

L’organisation requiert l’utilisation d’un chiffrement entre, et l’utilisation de signatures électroniques par, chacune des parties impliquées dans la transaction. L’organisation garantit que le stockage des détails de la transaction se trouve en dehors des environnements accessibles publiquement (par exemple, sur une plateforme de stockage existant dans l’intranet de l’organisation) et ne sont pas conservées et exposées sur un support de stockage directement accessible à partir d’Internet. Quand une autorité approuvée est utilisée (par exemple, afin d’émettre et de gérer des signatures numériques et/ou des certificats numériques), la sécurité est intégrée et incorporée de bout en bout tout au long du processus de gestion des certificats/signatures.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles.
- \[Préversion\] Afficher les résultats de l’audit des machines virtuelles Windows qui ne contiennent pas les certificats spécifiés dans la racine de confiance

## <a name="password-management"></a>Gestion des mots de passe

Les mots de passe sont chiffrés pendant la transmission et le stockage sur tous les composants système.

- \[Préversion\] Afficher les résultats de l’audit des machines virtuelles Windows qui n’ont pas le paramètre de complexité du mot de passe activé

## <a name="user-authentication-for-external-connections"></a>Authentification utilisateur pour les connexions externes

Les méthodes d’authentification fortes comme que MFA, Radius ou Kerberos (pour l’accès privilégié) et CHAP (pour le chiffrement des informations d’identification pour les méthodes d’accès à distance) sont implémentées pour toutes les connexions externes au réseau de l’organisation.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement
- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles.

## <a name="user-identification-and-authentication"></a>Identification et authentification des utilisateurs

Les utilisateurs qui ont exécuté des fonctions privilégiées (par exemple, administration du système) utilisent des comptes distincts pour cela. Des méthodes d’authentification multifacteur sont utilisées conformément à la stratégie de l’organisation (par exemple, pour un accès réseau à distance).

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement
- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles.

## <a name="privilege-management"></a>Gestion des privilèges

L’accès aux fonctions de gestion ou aux consoles d’administration des systèmes qui hébergent les systèmes virtualisés se limite au personnel selon le principe du moindre privilège et il est pris en charge par le biais des contrôles techniques.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles.
- \[Préversion\] Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sur les services Kubernetes.

## <a name="review-of-user-access-rights"></a>Revue des droits d’accès utilisateur

L’organisation tient à jour la liste documentée des utilisateurs autorisés à accéder aux ressources.

- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Comptes »

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Diagnostic à distance et protection des ports de configuration

Les ports, les services et les applications similaires installées sur un ordinateur ou des systèmes réseau qui ne sont pas particulièrement exigés pour les fonctionnalités métier sont désactivés ou supprimés.

- Les ports de gestion doivent être fermés sur vos machines virtuelles
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées.

## <a name="audit-logging"></a>Journalisation d’audit

Les journaux des messages envoyés et reçus sont conservés, notamment la date, l’heure, l’origine et la destination des messages, mais pas leur contenu. L’audit est toujours disponible lorsque le système est actif et suit les événements clés, les accès aux données ayant réussi/échoué, les modifications apportées à la configuration de la sécurité du système, les utilisations avec privilèges ou utilitaires, les alarmes émises, l’activation et la désactivation des systèmes de protection (par exemple, A/V et IDS), l’activation et la désactivation des mécanismes d’identification et d’authentification, ainsi que la création et la suppression d’objets au niveau du système.

- Les journaux de diagnostic dans Event Hub doivent être activés.
- Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques

## <a name="monitoring-system-use"></a>Supervision de l’utilisation du système

Les systèmes automatisés déployés dans l’environnement de l’organisation sont utilisés pour superviser les événements clés et les activités anormales, ainsi que pour analyser les journaux système. Les résultats obtenus sont révisés régulièrement. La supervision inclut les opérations privilégiées, les tentatives d’accès autorisées ou non autorisées, y compris les tentatives d’accès à des comptes désactivés, ainsi que les alertes système ou les défaillances.

- Les journaux de diagnostic dans les groupes de machines virtuelles identiques doivent être activés.

## <a name="segregation-of-duties"></a>Répartition des tâches

La répartition des tâches est utilisée pour limiter le risque de modification non autorisée ou non intentionnelle des informations et des systèmes. Aucune personne ne peut accéder, modifier ou utiliser des systèmes d’information sans autorisation ou détection. L’accès des personnes responsables de l’administration et des contrôles d’accès est limité au strict minimum en fonction du rôle et des responsabilités de chaque utilisateur. Ces personnes ne peuvent pas accéder aux fonctions d’audit associées à ces contrôles.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

## <a name="administrator-and-operator-logs"></a>Journaux des administrateurs et opérateurs

L’organisation veille à ce que la journalisation appropriée soit activée afin d’auditer les activités des administrateurs. Elle examine régulièrement les journaux des administrateurs système et des opérateurs.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

## <a name="identification-of-risks-related-to-external-parties"></a>Identification des risques liés à des parties externes

Les connexions d’accès à distance entre l’organisation et des parties externes sont chiffrées.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles.

## <a name="business-continuity-and-risk-assessment"></a>Continuité de l’activité et évaluation des risques

L’organisation identifie ses processus métier critiques et intègre les exigences liées à la gestion de la sécurité des informations de la continuité de l’activité aux autres exigences de continuité relatives aux opérations, au personnel, au matériel, au transport et aux installations.

- \[Préversion\] Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Console de récupération »

> [!NOTE]
> La disponibilité des définitions Azure Policy peut varier dans Azure Government et dans d’autres clouds nationaux. 

## <a name="next-steps"></a>Étapes suivantes

Vous venez de parcourir le mappage des contrôles de l’exemple de blueprint HIPAA HITRUST. Continuez avec les articles suivants pour découvrir une vue d’ensemble de cet exemple et pour savoir comment le déployer :

> [!div class="next step action"]
> [Blueprint HIPAA HITRUST - Vue d’ensemble](./control-mapping.md)
> [Blueprint HIPAA HITRUST - Déployer les étapes](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).
