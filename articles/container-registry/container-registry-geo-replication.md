---
title: Géoréplication d’un registre de conteneurs Azure
description: Prise en main de la création et de la gestion de registres de conteneurs Azure géorépliqués.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: a83cf6b37a28ec38165778faa7a9ecc266cce7bd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858254"
---
# <a name="geo-replication-in-azure-container-registry"></a>Géoréplication dans Azure Container Registry

Les entreprises qui souhaitent une présence locale ou une sauvegarde à chaud choisissent d’exécuter des services à partir de plusieurs régions Azure. La meilleure pratique recommandée consiste à placer un registre de conteneurs dans chaque région où les images sont exécutées afin de permettre des opérations à proximité du réseau pour des transferts de calque d’image rapides et fiables. La géoréplication permet à un registre de conteneurs Azure de fonctionner comme un registre unique desservant plusieurs régions à l’aide de registres régionaux à multiples maîtres.

Un registre géorépliqué offre les avantages suivants :

* Noms de registre/d’image/de balise uniques utilisables dans plusieurs régions
* Accès au registre à proximité du réseau à partir des déploiements régionaux
* Aucuns frais de sortie supplémentaires, les images étant extraites à partir d’un registre local répliqué dans la même région que l’hôte de votre conteneur
* Gestion unique d’un registre dans plusieurs régions

> [!NOTE]
> Si vous devez conserver les copies des images de conteneur dans plusieurs registres de conteneurs Azure, Azure Container Registry prend également en charge l'[importation d'images](container-registry-import-images.md). Par exemple, dans un flux de travail DevOps, vous pouvez importer une image entre un registre de développement et un registre de production, sans devoir utiliser les commandes Docker.
>

## <a name="example-use-case"></a>Exemple de cas d’usage
Contoso dispose d’un site web de présence publique situé aux États-Unis, au Canada et en Europe. Pour alimenter ces marchés avec du contenu local et à proximité du réseau, Contoso exécute des clusters [Azure Kubernetes Service](/azure/aks/) (AKS) dans les régions USA Ouest, USA Est, Canada Centre et Europe Ouest. Déployée en tant qu’image Docker, l’application de site web utilise le même code et la même image dans toutes les régions. Le contenu, local pour cette région, est récupéré à partir d’une base de données qui est configurée de façon unique dans chaque région. Chaque déploiement régional possède sa propre configuration unique pour les ressources, telles que la base de données locale.

L’équipe de développement se trouve à Seattle, dans l’État de Washington, et utilise le centre de données dans la région USA Ouest.

![Transmission vers plusieurs registres](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Transmission vers plusieurs registres*

Avant d’utiliser les fonctionnalités de géoréplication, Contoso disposait d’un registre basé dans la région USA Ouest et d’un autre registre dans Europe Ouest. Pour gérer ces différentes régions, l’équipe de développement devait envoyer (push) des images à deux registres différents.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Extraction à partir de plusieurs registres](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Extraction à partir de plusieurs registres*

Voici les principaux défis liés à l’utilisation de plusieurs registres :

* Tous les clusters (USA Est, USA Ouest et Canada Centre) extraient leurs données à partir du registre de la région USA Ouest, ce qui génère des frais de sortie puisque chacun de ces hôtes de conteneur à distance extrait des images à partir des centres de données situés dans la région USA Ouest.
* L’équipe de développement doit transmettre les images aux registres des région USA Ouest et Europe Ouest.
* L’équipe de développement doit configurer et tenir à jour chaque déploiement régional avec les noms d’images référençant le registre local.
* L’accès au registre doit être configuré pour chaque région.

## <a name="benefits-of-geo-replication"></a>Avantages de la géoréplication

![Extraction à partir d’un registre géorépliqué](media/container-registry-geo-replication/after-geo-replicate-pull.png)

La fonctionnalité de géoréplication d’Azure Container Registry permet de bénéficier des avantages suivants :

* Gérer un registre unique dans toutes les régions : `contoso.azurecr.io`
* Gérer une configuration unique pour le déploiement des images, car toutes les régions utilisent la même URL d’image : `contoso.azurecr.io/public/products/web:1.2`
* Transmettre les données vers un registre unique, tandis qu’ACR gère la géoréplication, y compris des webhooks régionaux pour les notifications locales.

## <a name="configure-geo-replication"></a>Configuration de la géo-réplication
La configuration de la géoréplication est aussi simple que de cliquer sur des régions sur une carte.

La géoréplication est une fonctionnalité disponible uniquement pour les [registres Premium](container-registry-skus.md). Si votre registre n’est pas encore Premium, vous pouvez passer de la formule De base ou Standard à Premium dans le [portail Azure](https://portal.azure.com) :

![Modification des références (SKU) dans le portail Azure](media/container-registry-skus/update-registry-sku.png)

Pour configurer la géoréplication pour votre registre Premium, connectez-vous au portail Azure à l’adresse http://portal.azure.com.

Accédez à votre registre de conteneurs Azure, puis sélectionnez **Réplications** :

![Option Réplications du registre de conteneurs dans le portail Azure](media/container-registry-geo-replication/registry-services.png)

Une carte s’affiche et indique toutes les régions Azure actuelles :

 ![Carte des régions dans le portail Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Les hexagones bleus représentent les réplicas actuels.
* Les hexagones verts représentent des régions où le réplica est possible.
* Les hexagones gris représentent des régions Azure qui ne sont pas encore disponibles pour les réplicas.

Pour configurer un réplica, sélectionnez un hexagone vert, puis cliquez sur **Créer** :

 ![Boîte de dialogue Créer une réplication dans le portail Azure](media/container-registry-geo-replication/create-replication.png)

Pour configurer des réplicas supplémentaires, sélectionnez les hexagones verts pour les autres régions, puis cliquez sur **Créer**.

ACR commence la synchronisation des images entre les réplicas configurés. Une fois l’opération terminée, le portail affiche la mention *Prêt*. L’état du réplica dans le portail n’est pas mis à jour automatiquement. Utilisez le bouton Actualiser pour le mettre à jour.

## <a name="geo-replication-pricing"></a>Tarification de la géoréplication

La géoréplication est une fonctionnalité de la [Référence SKU Premium](container-registry-skus.md) d’Azure Container Registry. Lorsque vous répliquez un registre dans les régions de votre choix, cela entraîne des frais de registre Premium pour chaque région.

Dans l’exemple précédent, Contoso a fusionné deux registres en un seul, en ajoutant des réplicas dans les régions USA Est, Canada Centre et Europe Ouest. Contoso payerait le tarif Premium quatre fois par mois, sans configuration ni gestion supplémentaire. Chaque région extraie désormais ses images localement, ce qui améliore les performances et la fiabilité sans frais de sortie de réseau de la région USA Ouest au Canada, en passant par USA Est.

## <a name="summary"></a>Résumé

Avec la géoréplication, vous pouvez gérer vos centres de données régionaux comme un cloud global. Comme les images sont utilisées dans de nombreux services Azure, vous pouvez bénéficier d’un plan de gestion unique tout en conservant des extractions d’images locales à proximité du réseau, rapides et fiables.

## <a name="next-steps"></a>Étapes suivantes

Découvrez la série de didacticiels en trois parties relative à la [géoréplication dans Azure Container Registry](container-registry-tutorial-prepare-registry.md). Découvrez les étapes permettant de créer un registre géorépliqué, de générer un conteneur, puis de le déployer avec une seule commande `docker push` vers plusieurs instances régionales de Web App pour conteneurs.

> [!div class="nextstepaction"]
> [Géoréplication dans Azure Container Registry](container-registry-tutorial-prepare-registry.md)
