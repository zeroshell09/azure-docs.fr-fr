---
title: Liste de contrôle pour la sécurité Azure Service Fabric | Microsoft Docs
description: Cet article fournit un ensemble de listes de contrôle pour la sécurité Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927723"
---
# <a name="azure-service-fabric-security-checklist"></a>Liste de contrôle pour la sécurité Azure Service Fabric
Cet article fournit une liste de contrôle facile à utiliser qui vous aide à sécuriser votre environnement Azure Service Fabric.

## <a name="introduction"></a>Introduction
Azure Service Fabric est une plateforme de systèmes distribués qui permet d’empaqueter, de déployer et de gérer facilement des microservices scalables et fiables. Service Fabric résout également les problèmes non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer.

## <a name="checklist"></a>Liste de contrôle
Utilisez la liste de contrôle suivante pour vous assurer que vous n’avez pas négligé de points importants relatifs à la gestion et à la configuration d’une solution Azure Service Fabric sécurisée.


|Catégorie de la liste de contrôle| Description |
| ------------ | -------- |
|[Contrôle d’accès en fonction du rôle (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster.</li><li>Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). </li><li> Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.</li></ul>|
|[Certificats X.509 et Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Les [certificats](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) utilisés dans les clusters qui exécutent des charges de travail de production doivent être créés en utilisant un service de certificats Windows Server correctement configuré ou obtenus auprès d’une [autorité de certification (AC)](https://en.wikipedia.org/wiki/Certificate_authority) approuvée.</li><li>En production, n’utilisez jamais de [certificats temporaires ou de test](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) créés avec des outils comme [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Vous pouvez utiliser un [certificat auto-signé](../../service-fabric/service-fabric-windows-cluster-x509-security.md), mais seulement pour les clusters de test et non en production.</li></ul>|
|[Sécurité des clusters](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Les scénarios relatifs à la sécurité des clusters incluent la sécurité nœud à nœud, la sécurité client à nœud et le [contrôle d’accès en fonction du rôle (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Authentification du cluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Authentifie la [communication nœud à nœud](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) pour la fédération du cluster. </li></ul>|
|[Authentification du serveur](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Authentifie les [points de terminaison de gestion de cluster](../../service-fabric/service-fabric-cluster-creation-via-portal.md) auprès d’un client de gestion.</li></ul>|
|[Sécurité des applications](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Le chiffrement et déchiffrement de valeurs de configuration d’application.</li><li>   Le chiffrement des données sur les nœuds lors de la réplication.</li></ul>|
|[Certificat de cluster](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Ce certificat est requis pour sécuriser les communications entre les nœuds sur un cluster.</li><li>    Définissez l’empreinte du certificat principal dans la section Thumbprint, et celui du certificat secondaire dans les variables ThumbprintSecondary.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Vous pouvez utiliser deux certificats de serveur différents : un certificat principal et un certificat secondaire pour la mise à niveau.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Il s’agit du jeu de certificats que vous souhaitez installer sur les clients authentifiés. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Définissez le nom commun du premier certificat client pour CertificateCommonName. L’élément CertificateIssuerThumbprint est l’empreinte numérique relative à l’émetteur de ce certificat. </li></ul>|
|ReverseProxyCertificate| <ul><li>Il s’agit d’un certificat facultatif à spécifier si vous souhaitez sécuriser votre [Proxy inverse](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Key Vault| <ul><li>Permet de gérer des certificats pour des clusters Service Fabric dans Azure.  </li></ul>|


## <a name="next-steps"></a>Étapes suivantes

- [Bonnes pratiques pour la sécurité Service Fabric](service-fabric-best-practices.md)
- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](../../service-fabric/service-fabric-health-introduction.md).
