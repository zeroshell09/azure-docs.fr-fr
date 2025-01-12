---
title: Créer un serveur Ubuntu NFS (Network File System) pour une utilisation avec des pods dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer manuellement un volume de serveur Ubuntu Linux NFS pour une utilisation avec des pods dans Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468498"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Créer manuellement et utiliser un volume de serveur Linux NFS (Network File System) avec Azure Kubernetes Service (AKS)
Le partage de données entre conteneurs représente souvent un élément nécessaire des applications et services basés sur des conteneurs. Généralement, différents pods ont besoin d’accéder aux mêmes informations sur un volume persistant externe.    
Si les fichiers Azure représentent une option, la création d’un serveur NFS sur une machine virtuelle Azure offre une autre forme de stockage partagé persistant. 

Cet article vous explique comment créer un serveur NFS sur une machine virtuelle Ubuntu. Il explique également comment donner aux conteneurs AKS accès à ce système de fichiers partagé.

## <a name="before-you-begin"></a>Avant de commencer
Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le Portail Azure][aks-quickstart-portal].

Votre cluster AKS devra exister dans le même réseau virtuel que le serveur NFS ou un réseau virtuel appairé. Le cluster doit être créé dans un réseau virtuel existant, qui peut être le même que celui de votre machine virtuelle.

Les étapes de configuration avec un réseau virtuel existant sont décrites dans les documentations : [Créer un cluster AKS dans le réseau virtuel][aks-virtual-network] et [Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant le Portail Azure][peer-virtual-networks].

Cet article suppose également que vous avez créé une machine virtuelle Ubuntu Linux (par exemple, 18.04 LTS). Vous pouvez choisir les paramètres et la taille, qui peuvent être déployés via Azure. Pour obtenir un guide de démarrage rapide Linux, consultez [Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI][linux-create].

Si vous déployez d’abord votre cluster AKS, Azure renseigne automatiquement le champ de réseau virtuel lors du déploiement de votre ordinateur Ubuntu, pour le faire exister dans le même réseau virtuel. Cependant, si vous souhaitez plutôt utiliser des réseaux appairés, consultez la documentation ci-dessus.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Déploiement du serveur NFS sur une machine virtuelle
Voici le script permettant de configurer un serveur NFS au sein de votre machine de virtuelle Ubuntu :
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Le serveur redémarre (en raison du script), et vous pouvez monter le serveur NFS sur AKS.

>[!IMPORTANT]  
>Veillez à remplacer **AKS_SUBNET** par le sous-réseau correct de votre cluster. Sinon, « * » ouvre votre serveur NFS à tous les ports et connexions.

Une fois que vous avez créé la machine virtuelle, copiez le script ci-dessus dans un fichier. Ensuite, vous pouvez le déplacer à partir de votre ordinateur local ou de tout autre emplacement sur lequel figure le script vers la machine virtuelle à l’aide de la commande : 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Une fois le script dans la machine virtuelle, vous pouvez établir une connexion SSH avec la machine virtuelle et l’exécuter à l’aide de la commande :
```console
sudo ./nfs-server-setup.sh
```
Si l’exécution échoue en raison d’une autorisation refusée, définissez l’autorisation d’exécution par le biais de la commande :
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Connexion du cluster AKS au serveur NFS
Nous pouvons connecter le serveur NFS au cluster en approvisionnant un volume persistant et une revendication de volume persistant qui spécifie la manière d’accéder au volume.  
Il est nécessaire de connecter les deux services dans le même réseau virtuel ou des réseaux virtuels appairés. Pour obtenir des instructions relatives à la configuration du cluster dans le même réseau virtuel, consultez : [Créer le cluster AKS dans le réseau virtuel][aks-virtual-network].

Une fois qu’ils se trouvent dans le même réseau virtuel (ou des réseaux virtuels appairés), vous devez approvisionner un volume persistant et une revendication de volume persistant dans votre cluster AKS. Les conteneurs peuvent ensuite monter le lecteur NFS sur leur répertoire local.

Voici un exemple de définition de kubernetes pour le volume persistant (cette définition suppose que votre cluster et votre machine virtuelle se trouvent dans le même réseau virtuel) :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Remplacez **NFS_INTERNAL_IP**, **NFS_NAME** et **NFS_EXPORT_FILE_PATH** par les informations du serveur NFS.

Vous aurez également besoin d’un fichier de revendication de volume persistant. Voici un exemple de ce qui doit être inclus :

>[!IMPORTANT]  
>La chaîne **« storageClassName »** doit rester vide. Sinon, la revendication ne fonctionnera pas.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous ne pouvez pas vous connecter au serveur à partir d’un cluster, il se peut que le répertoire exporté ou son parent ne dispose pas des autorisations suffisantes pour accéder au serveur.

Vérifiez que votre répertoire d’exportation et que son répertoire parent disposent des autorisations 777.

Vous pouvez vérifier les autorisations en exécutant la commande ci-dessous. Les répertoires doivent disposer des autorisations *« drwxrwxrwx »* :
```console
ls -l
```

## <a name="more-information"></a>Plus d’informations
Pour obtenir une procédure pas à pas complète ou une assistance pour déboguer la configuration du serveur NFS, utilisez ce didacticiel approfondi :
  - [Didacticiel sur NFS][nfs-tutorial]

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives au stockage et aux sauvegardes dans Azure Kubernetes Service (AKS)][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
