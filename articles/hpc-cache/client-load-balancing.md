---
title: Équilibrer la charge des connexions clientes vers Azure HPC Cache
description: Comment configurer un serveur DNS pour l’équilibrage de charge par tourniquet (Round Robin) pour Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: v-erkel
ms.openlocfilehash: 2a8c35db125b80223cbb4f07e8c01ca45428a3a8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004936"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>Équilibrer la charge du trafic client de HPC Cache

Cet article explique quelques méthodes de base pour équilibrer le trafic client sur tous les points de montage de votre Azure HPC Cache.

Chaque HPC Cache a au moins trois adresses IP différentes et les caches avec des valeurs de débit supérieures peuvent en avoir jusqu’à 12. Il est important d’utiliser toutes les adresses IP pour tirer pleinement parti de Azure HPC Cache.

Il existe différentes options pour équilibrer la charge de vos montages client :

* choisir manuellement une autre adresse IP de montage pour chaque client
* inclure la rotation des adresses IP dans vos scripts de montage client
* configurer un système DNS pour acheminer automatiquement les requêtes des clients entre toutes les adresses disponibles (serveur DNS à tourniquet)

Le système d’équilibrage de charge approprié dépend de la complexité de workflow, du nombre d’adresses IP dans votre cache et d’un grand nombre d’autres facteurs. Si vous avez besoin d’aide pour choisir l’approche la mieux adaptée à votre cas, consultez votre conseiller Azure.

## <a name="assign-ip-addresses-manually"></a>Attribuer des adresses IP manuellement

Les adresses IP de montage de votre cache sont affichées dans les pages **Vue d’ensemble** du cache et **Instructions de montage** du Portail Azure, ainsi que dans le message de réussite qui s’affiche lorsque vous créez un cache avec Azure CLI ou PowerShell.

Vous pouvez utiliser la page **Instructions de montage** pour générer une commande de montage personnalisée pour chaque client. Sélectionnez toutes les valeurs d'**adresse de montage du cache** lors de la création de plusieurs commandes.

Pour plus d’informations, consultez [Monter Azure HPC Cache](hpc-cache-mount.md).

## <a name="use-scripted-load-balancing"></a>Utiliser l’équilibrage de charge avec script

Il existe plusieurs façons de faire pivoter les montages de clients par programmation parmi les adresses IP disponibles. Voici deux exemples.

### <a name="mount-command-script-cksum-example"></a>Exemple de commande de montage de script cksum

Cet exemple de commande de montage utilise la fonction de synthèse ``cksum`` et le nom d’hôte client pour distribuer automatiquement les connexions clientes entre toutes les adresses IP disponibles dans votre HPC Cache. Si tous les ordinateurs clients ont des noms d’hôtes uniques, vous pouvez exécuter cette commande sur chaque client pour vous assurer que tous les points de montage disponibles sont utilisés.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

Pour utiliser cet exemple dans votre workflow, personnalisez les termes suivants :

* Dans l'expression ```X=```, utilisez une liste séparée par des espaces de toutes les adresses de montage du cache, dans l’ordre de tri.

  L’expression ``(X=(10.0.0.{7..9})`` définit la variable X comme cet ensemble d’adresses de montage : {10.0.0.7, 10.0.0.8, 10.0.0.9}. Utilisez l’adresse IP de base du cache et les adresses exactes indiquées dans la page Vue d’ensemble du cache. Si les adresses ne sont pas consécutives, répertoriez-les dans l’ordre numérique.

* Dans le terme ```%3```, utilisez le nombre réel d’adresses IP de montage dont dispose votre cache (en général 3, 6, 9 ou 12).

  Par exemple, utilisez ``%9`` si votre cache expose neuf adresses IP de montage client.

* Pour l’expression ``${NAMESPACE}``, utilisez le chemin d’accès de l’espace de noms cible de stockage auquel le client accède.

  Vous pouvez utiliser une variable que vous avez définie (*ESPACE DE NOMS* dans l’exemple) ou passer la valeur littérale à la place.
  
  L’exemple de commande à la fin de cette section utilise une valeur littérale pour le chemin d’accès de l’espace de noms, ``/blob-target-1``.

* Si vous souhaitez utiliser un chemin d’accès local personnalisé sur vos ordinateurs clients, remplacez la valeur ``/mnt`` par le chemin d’accès de votre choix.

Voici un exemple de commande de montage client remplie :

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

### <a name="round-robin-function-example"></a>Exemple de fonction de tourniquet (round robin)

Cet exemple de code utilise des adresses IP clientes comme élément aléatoire pour distribuer des clients à toutes les adresses IP disponibles de HPC Cache.

```bash
function mount_round_robin() {

  # to ensure the clients are spread out somewhat evenly the default
  # mount point is based on this client's IP octet4 % number of HPC cache mount IPs.

  declare -a MOUNT_IPS="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
  HASH=$(hostname | cksum | cut -f 1 -d ' ')
  DEFAULT_MOUNT_INDEX=$((${HASH} % ${#MOUNT_IPS[@]}))
  ROUND_ROBIN_IP=${MOUNT_IPS[${DEFAULT_MOUNT_INDEX}]}

  DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

  # no need to write again if it is already there
  if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
      echo "${ROUND_ROBIN_IP}:${NFS_PATH} ${DEFAULT_MOUNT_POINT} nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
      mkdir -p "${DEFAULT_MOUNT_POINT}"
      chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
  fi
  if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
      retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
  fi
}
```

## <a name="use-dns-load-balancing"></a>Utiliser un équilibrage de charge du DNS

Cette section explique les principes fondamentaux de la configuration d’un système DNS pour distribuer des connexions clientes à tous les points de montage de votre Azure HPC Cache. Cette méthode ne prend pas en compte la quantité de trafic généré par chaque client, mais elle garantit que les clients sont répartis uniformément sur toutes les interfaces du cache au lieu d’en utiliser simplement une ou deux.

Ce document n’inclut pas d’instructions pour la configuration et la gestion d’un serveur DNS dans l’environnement Azure.

DNS n’est pas requis pour monter des clients à l’aide du protocole NFS et des adresses IP. Le DNS *est* nécessaire si vous souhaitez utiliser des noms de domaine au lieu d’adresses IP pour atteindre des systèmes NAS matériels ou si votre workflow comprend certains paramètres de protocole avancés.

Le système DNS que vous utilisez pour distribuer des adresses aux clients n’a pas besoin d’être accessible par le HPC Cache. Dans certains cas, vous souhaiterez peut-être utiliser un système DNS personnalisé pour le cache lui-même, mais la configuration de ce système est bien plus complexe que la configuration d’un système de tourniquet (Round Robin) client. Vous devez consulter le support Azure si vous envisagez de [remplacer le *serveur DNS* de votre cache HPC](configuration.md#set-a-custom-dns-configuration) par un système personnalisé.

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>Configurer la distribution par tourniquet pour les points de montage du cache

Un système DNS à tourniquet (Round Robin) (RRDNS) achemine automatiquement les requêtes des clients entre plusieurs adresses.

Pour configurer ce système, vous devez personnaliser le fichier config du serveur DNS de façon à ce qu’il attribut le trafic parmi tous les points de montage du système HPC Cache lorsqu’il reçoit des requêtes de montage vers l’adresse de domaine principale HPC Cache. Les clients montent le HPC Cache en utilisant son nom de domaine comme argument de serveur et sont acheminés automatiquement vers l’adresse IP de montage suivante.

Il existe deux étapes principales pour configurer RRDNS :

1. Modifier le fichier ``named.conf`` de votre serveur DNS pour définir l’ordre cyclique des requêtes envoyées à votre HPC Cache. Cette option amène le serveur à parcourir toutes les valeurs d’adresse IP disponibles. Ajoutez une instruction similaire à celle-ci :

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. Configurez des enregistrements A et des enregistrements de pointeur (PTR) pour chaque adresse IP disponible, comme dans l’exemple suivant.

   Ces commandes ``nsupdate`` fournissent un exemple de configuration correcte du DNS pour un HPC Cache avec le nom de domaine hpccache.contoso.com et trois adresses de montage (10.0.0.10, 10.0.0.11 et 10.0.0.12) :

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Ces commandes créent un enregistrement A pour chacune des adresses de montage de HPC Cache et configurent également des enregistrements de pointeur pour prendre en charge les vérifications DNS inversées de manière appropriée.

   Le diagramme ci-dessous illustre la structure de base de cette configuration.

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="Diagramme montrant la configuration DNS du point de montage client.":::
   <Le diagramme montre les connexions entre trois catégories d’éléments : le nom de domaine unique HPC Cache (à gauche), trois adresses IP (colonne du milieu) et trois interfaces clientes inversées d’usage interne (colonne de droite). Un seul ovale à gauche nommé « hpccache.contoso.com » est connecté par des flèches pointant vers trois ovales étiquetés avec des adresses IP : 10.0.0.10, 10.0.0.11 et 10.0.0.12. Les flèches entre l’ovale hpccache.contoso.com et les trois ovales IP sont étiquetées « A ». Chacun des ovales de l’adresse IP est connecté par deux flèches à un ovale étiqueté comme une interface client : l’ovale avec l’adresse IP 10.0.0.10 est connecté à « client-IP-10.contoso.com », l’ovale avec l’adresse IP 10.0.0.11 est connecté à « client-IP-11.contoso.com » et l’ovale avec l’adresse IP 10.0.0.12 est connecté à « client-IP-11.contoso.com ». Les connexions entre les ovales de l’adresse IP et les ovales de l’interface client sont deux flèches : une flèche intitulée « PTR » qui pointe de l’ovale de l’adresse IP vers l’ovale de l’interface client et une flèche intitulée « A » qui pointe de l’ovale de l’interface client vers l’ovale de l’adresse IP.> :::image-end:::

Une fois le système RRDNS configuré, demandez à vos ordinateurs clients de l’utiliser pour résoudre l’adresse HPC Cache dans leurs commandes de montage.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir de l’aide sur l’équilibrage de la charge client, [contactez le support](hpc-cache-support-ticket.md).
* Pour déplacer des données vers les cibles de stockage du cache, consultez [Remplir de données un nouveau stockage Blob Azure](hpc-cache-ingest.md).
