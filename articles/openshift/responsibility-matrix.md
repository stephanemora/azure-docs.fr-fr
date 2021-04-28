---
title: Matrice d’attribution de responsabilité Azure Red Hat OpenShift
description: Découvrir la propriété des responsabilités pour le fonctionnement d’un cluster Azure Red Hat OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, prise en charge
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537016"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Vue d’ensemble des responsabilités pour Azure Red Hat OpenShift

Ce document présente les responsabilités de Microsoft, de Red Hat et des clients pour les clusters Azure Red Hat OpenShift. Pour plus d’informations sur Azure Red Hat OpenShift et ses composants, consultez la définition du service Azure Red Hat OpenShift.

Alors que Microsoft et Red Hat gèrent le service Azure Red Hat OpenShift, le client partage la responsabilité des fonctionnalités de son cluster. Alors que les clusters Azure Red Hat OpenShift sont hébergés sur des ressources Azure dans les abonnements Azure des clients, ils sont accessibles à distance. La sécurité des données et de la plateforme sous-jacente est détenue par Microsoft et Red Hat.

## <a name="overview"></a>Vue d’ensemble
<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Gestion des incidents et des opérations</a></strong>
   </td>
   <td><strong><a href="#change-management">Gestion des changements</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Gestion des identités et des accès</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Sécurité et conformité aux réglementations</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Données client</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Applications de client</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Services de développement</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td>Supervision de la plateforme </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Journalisation </td>
   <td>Microsoft et Red Hat </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Partagé </td>
  </tr>
  <tr>
   <td>Réseau d’applications </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Réseau de clusters </td>
   <td>Microsoft et Red Hat </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Réseau virtuel </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Partagé </td>
   <td>Partagé </td>
  </tr>
  <tr>
   <td>Nœuds de plan de contrôle </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Nœuds de travail </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Version de cluster </td>
   <td>Microsoft et Red Hat </td>
   <td>Partagé </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Gestion de la capacité </td>
   <td>Microsoft et Red Hat </td>
   <td>Partagé </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Stockage virtuel </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
  <tr>
   <td>Sécurité et infrastructure physique </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
   <td>Microsoft et Red Hat </td>
  </tr>
</table>


Tableau 1. Responsabilités par ressource


## <a name="tasks-for-shared-responsibilities-by-area"></a>Tâches correspondant aux responsabilités partagées par zone 

### <a name="incident-and-operations-management"></a>Gestion des incidents et des opérations 

Le client partage avec Microsoft et Red Hat la responsabilité de la supervision et de la maintenance d’un cluster Azure Red Hat OpenShift. Le client est responsable de la gestion des incidents et des opérations des [données d’application de client](#customer-data-and-applications) et de tout réseau personnalisé que le client peut avoir configuré.

<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong>Responsabilités de Microsoft et de Red Hat</strong>
   </td>
   <td><strong>Responsabilités des clients</strong>
   </td>
  </tr>
  <tr>
   <td>Réseau d’applications </td>
   <td>
<ul>

<li>Superviser le ou les équilibreurs de charge cloud et le service de routeur OpenShift natif, et répondre aux alertes.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Superviser l’intégrité des points de terminaison d’équilibreur de charge de service.

<li>Superviser l’intégrité des routes d’application et les points de terminaison se trouvant derrière elles.

<li>Signaler les pannes à Microsoft et Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau virtuel
   </td>
   <td>
<ul>

<li>Superviser les équilibreurs de charge, les sous-réseaux et les composants cloud Azure nécessaires au réseau par défaut des plateformes, et répondre aux alertes.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Superviser le trafic réseau qui est éventuellement configuré via une connexion de réseau virtuel à réseau virtuel, une connexion VPN ou une connexion de liaison privée à la recherche d’éventuels problèmes ou menaces de sécurité.
</li>
</ul>
   </td>
  </tr>
</table>


Tableau 2. Responsabilités partagées pour la gestion des incidents et des opérations


### <a name="change-management"></a>Gestion des changements

Microsoft et Red Hat sont responsables de l’activation des changements apportés à l’infrastructure et aux services de cluster que le client contrôlera, ainsi que de la gestion des versions disponibles pour les nœuds maître, les services d’infrastructure et les nœuds worker. Le client est responsable du lancement des changements d’infrastructure, de l’installation et de la maintenance des services et des configurations réseau facultatifs sur le cluster, ainsi que de tous les changements apportés aux données et applications des clients.


<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong>Responsabilités de Microsoft et de Red Hat</strong>
   </td>
   <td><strong>Responsabilités des clients</strong>
   </td>
  </tr>
  <tr>
   <td>Journalisation </td>
   <td>
<ul>

<li>Agréger et superviser de manière centralisée les journaux d’audit de plateforme.

<li>Fournir une documentation permettant au client d’activer la journalisation des applications à l’aide de Log Analytics par le biais d’Azure Monitor pour conteneurs.

<li>Fournir des journaux d’audit à la demande des clients.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Installer l’opérateur de journalisation des applications par défaut facultatif sur le cluster.

<li>Installer, configurer et gérer toutes les solutions de journalisation des applications facultatives, comme la journalisation de conteneurs sidecar ou d’applications de journalisation tierces.

<li>Ajuster la taille et la fréquence des journaux d’application produits par les applications de client si elles affectent la stabilité du cluster.

<li>Demander des journaux d’audit de plateforme par le biais d’un cas de support pour la recherche d’incidents spécifiques.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau d’applications
   </td>
   <td>
<ul>

<li>Configurer des équilibreurs de charge cloud publics

<li>Configurer le service de routeur OpenShift natif. Donner la possibilité de définir le routeur comme privé et d’ajouter au maximum une autre partition de routeur.

<li>Installer, configurer et gérer des composants SDN OpenShift pour le trafic de pods interne par défaut.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurer des autorisations de réseau de pods autres que celles par défaut pour les réseaux de projets et de pods, les entrées de pods et les sorties de pods à l’aide d’objets NetworkPolicy.

<li>Demander et configurer des équilibreurs de charge de service supplémentaires pour des services spécifiques.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau de clusters
   </td>
   <td>
<ul>

<li>Configurer des composants de gestion de cluster, comme des points de terminaison de service publics ou privés et l’intégration nécessaire à des composants réseau virtuel.

<li>Configurer les composants réseau internes nécessaires pour la communication de cluster interne entre des nœuds worker et des nœuds maître.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Fournir des plages d’adresses IP facultatives autres que celles par défaut pour le CIDR de machine, le CIDR de service et le CIDR de pod, si nécessaire, par le biais du Gestionnaire de cluster OpenShift lors du provisionnement du cluster.

<li>Demander que le point de terminaison du service d’API soit rendu public ou privé lors de la création du cluster ou après sa création par le biais d’Azure CLI.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau virtuel
   </td>
   <td>
<ul>

<li>Installer et configurer les composants réseau virtuel nécessaires pour provisionner le cluster, notamment un cloud privé virtuel, des sous-réseaux, des équilibreurs de charge, des passerelles Internet, des passerelles NAT, etc.

<li>Donner au client la possibilité de gérer la connectivité VPN avec les ressources locales, la connectivité de réseau virtuel à réseau virtuel et la connectivité de liaison privée, en fonction des besoins, par le biais du Gestionnaire de cluster OpenShift.

<li>Permettre aux clients de créer et de déployer des équilibreurs de charge cloud publics pour une utilisation avec des équilibreurs de charge de service.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurer et gérer des composants réseau cloud public facultatifs, comme une connexion de réseau virtuel à réseau virtuel, une connexion VPN ou une connexion de liaison privée.

<li>Demander et configurer des équilibreurs de charge de service supplémentaires pour des services spécifiques.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Version de cluster
   </td>
   <td>
<ul>

<li>Communiquer la planification et l’état des mises à niveau pour les versions mineures et de maintenance

<li>Publier des journaux des modifications et des notes de publication pour les mises à niveau mineures et de maintenance
</li>
</ul>
   </td>
   <td>
<ul>

<li>Lancer la mise à niveau d’un cluster

<li>Tester des applications de client sur des versions mineures et de maintenance pour garantir leur compatibilité
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Gestion de la capacité
   </td>
   <td>
<ul>

<li>Superviser l’utilisation du plan de contrôle (nœuds maître)

<li>Mettre à l’échelle et/ou redimensionner les nœuds de plan de contrôle pour maintenir la qualité de service

<li>Superviser l’utilisation des ressources de clients, notamment le réseau, le stockage et la capacité de calcul. Quand les fonctionnalités de mise à l’échelle automatique ne sont pas activées, avertir le client de tout changement à apporter aux ressources de cluster (par exemple, de nouveaux nœuds de calcul à mettre à l’échelle, du stockage supplémentaire, etc.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Utiliser les commandes du Gestionnaire de cluster OpenShift fournies pour ajouter ou supprimer des nœuds worker supplémentaires en fonction des besoins.

<li>Répondre aux notifications Microsoft et Red Hat concernant les besoins en ressources de cluster.
</li>
</ul>
   </td>
  </tr>
</table>


Tableau 3. Responsabilités partagées pour la gestion des changements


### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès 

La gestion des identités et des accès comprend toutes les responsabilités permettant de garantir que seules les personnes appropriées ont accès aux ressources de cluster, d’application et d’infrastructure. Cela comprend des tâches comme la fourniture de mécanismes de contrôle d’accès, l’authentification, l’autorisation et la gestion de l’accès aux ressources.


<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong>Responsabilités de Microsoft et de Red Hat</strong>
   </td>
   <td><strong>Responsabilités des clients</strong>
   </td>
  </tr>
  <tr>
   <td>Journalisation </td>
   <td>
<ul>

<li>Adhérer à un processus d’accès interne à plusieurs niveaux basé sur les normes du secteur pour les journaux d’audit de plateforme.

<li>Fournir des fonctionnalités OpenShift RBAC natives.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurer OpenShift RBAC pour contrôler l’accès aux projets et, par extension, aux journaux d’application d’un projet.

<li>Pour les solutions de journalisation des applications tierces ou personnalisées, le client est chargé de la gestion des accès.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau d’applications
   </td>
   <td>
<ul>

<li>Fournir des fonctionnalités OpenShift RBAC natives.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurer OpenShift RBAC pour contrôler l’accès à la configuration des routes en fonction des besoins.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau de clusters
   </td>
   <td>
<ul>

<li>Fournir des fonctionnalités OpenShift RBAC natives.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gérer l’appartenance de comptes Red Hat à l’organisation Red Hat.

<li>Gérer les administrateurs de l’organisation Red Hat afin d’accorder l’accès au Gestionnaire de cluster OpenShift.

<li>Configurer OpenShift RBAC pour contrôler l’accès à la configuration des routes en fonction des besoins.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau virtuel
   </td>
   <td>
<ul>

<li>Fournir des contrôles d’accès client par le biais du Gestionnaire de cluster OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gérer l’accès utilisateur facultatif aux composants cloud publics par le biais du Gestionnaire de cluster OpenShift.
</li>
</ul>
   </td>
  </tr>
</table>


Tableau 4. Responsabilités partagées pour la gestion des identités et des accès


### <a name="security-and-regulation-compliance"></a>Sécurité et conformité aux réglementations 

La sécurité et la conformité incluent toutes les responsabilités et tous les contrôles qui garantissent la conformité aux lois, politiques et réglementations pertinentes.


<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong>Responsabilités de Microsoft et de Red Hat</strong>
   </td>
   <td><strong>Responsabilités des clients</strong>
   </td>
  </tr>
  <tr>
   <td>Journalisation </td>
   <td>
<ul>

<li>Envoyer à un système SIEM Microsoft et Red Hat des journaux d’audit de cluster à analyser pour détecter les événements de sécurité. Conserver les journaux d’audit pendant un laps de temps défini pour prendre en charge l’analyse d’investigation.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analyser les journaux d’application pour détecter les événements de sécurité. Envoyer des journaux d’application à un point de terminaison externe par le biais de la journalisation de conteneurs sidecar ou d’applications de journalisation tierces si une conservation plus longue que celle offerte par la pile de journalisation par défaut est nécessaire.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Réseau virtuel
   </td>
   <td>
<ul>

<li>Superviser les composants réseau virtuel à la recherche d’éventuels problèmes ou menaces de sécurité.

<li>Utiliser des outils Microsoft et Red Hat Azure publics supplémentaires pour une supervision et une protection complémentaires.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Superviser les composants réseau virtuel configurés facultatifs à la recherche d’éventuels problèmes ou menaces de sécurité.

<li>Configurer toutes les règles de pare-feu ou protections de centre de données nécessaires, en fonction des besoins.
</li>
</ul>
   </td>
  </tr>
</table>


Tableau 5. Responsabilités partagées concernant la sécurité et la conformité aux réglementations


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Responsabilités du client en cas l’utilisation d’Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Applications et données client

Le client est responsable des applications, des charges de travail et des données qu’il déploie sur Azure Red Hat OpenShift. Toutefois, Microsoft et Red Hat proposent différents outils permettant au client de gérer les données et les applications sur la plateforme.


<table>
  <tr>
   <td><strong>Ressource</strong>
   </td>
   <td><strong>Aide apportée par Microsoft et Red Hat</strong>
   </td>
   <td><strong>Responsabilités des clients</strong>
   </td>
  </tr>
  <tr>
   <td>Données client </td>
   <td>
<ul>

<li>Tenir à jour les normes de niveau plateforme pour le chiffrement des données tel que défini par les normes de conformité et de sécurité du secteur. 

<li>Fournir des composants OpenShift pour faciliter la gestion des données d’application, comme les secrets.

<li>Activer l’intégration à des services de données tiers (comme Azure SQL) pour stocker et gérer des données en dehors du cluster et/ou de Microsoft et Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Tenir à jour la responsabilité de toutes les données client stockées sur la plateforme et de la façon dont les applications de client consomment et exposent ces données.

<li>Chiffrement etcd
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Applications de client
   </td>
   <td>
<ul>

<li>Provisionner des clusters avec des composants OpenShift installés afin que les clients puissent accéder aux API OpenShift et Kubernetes pour déployer et gérer des applications conteneurisé.

<li>Fournir un accès aux API OpenShift qu’un client peut utiliser pour configurer des opérateurs afin d’ajouter des services de communauté, tiers, Microsoft et Red Hat, et Red Hat au cluster. 

<li>Fournir des classes et des plug-ins de stockage pour prendre en charge des volumes persistants en vue de leur utilisation avec les applications de client.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Tenir à jour la responsabilité des applications de clients et tierces, des données et de leur cycle de vie complet.

<li>Si un client ajoute des services Red Hat, des services communauté, des services tiers, ses propres services ou d’autres services au cluster à l’aide d’opérateurs ou d’images externes, le client est responsable de ces services et de la collaboration avec le fournisseur approprié (notamment Red Hat) pour résoudre tous les problèmes.

<li>Utiliser les outils et fonctionnalités fournis pour <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">configurer et déployer</a> ; <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">rester à jour</a> ; <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">configurer des demandes et des limites de ressources</a> ; <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">dimensionner le cluster afin qu’il dispose de suffisamment de ressources pour exécuter des applications</a> ; <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">configurer des autorisations</a> ; effectuer une intégration à d’autres services ; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">gérer des flux d’images ou des modèles d’image déployés par le client</a> ; <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">traiter en externe</a> ; enregistrer, sauvegarder et restaurer des données ; et sinon gérer leurs charges de travail hautement disponibles et résilientes.

<li>Tenir à jour la responsabilité de la supervision des applications exécutées sur Azure Red Hat OpenShift, notamment l’installation et le fonctionnement des logiciels pour collecter des métriques et créer des alertes.
</li>
</ul>
   </td>
  </tr>
</table>


Tableau 7. Responsabilités du client pour les données client, les applications de client et les services
