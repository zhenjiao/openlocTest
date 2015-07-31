<properties
	pageTitle="Improve Business Continuity with Azure Regional Pairs"
	description="Use Regional pairs to keep applications resilient during data center failures."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Améliorer la disponibilité à l'aide de paires régional d'Azur

## Azur régions appariées a expliqué

Azur opère dans plusieurs zones géographiques du monde entier. Une géographie d'Azur est une zone définie du monde contenant au moins une région d'Azure. Une région d'Azur est une zone dans une géographie contenant un ou plusieurs centres de données.

Chaque région d'Azur est jumelée avec une autre région au sein de la même géographie (à l'exception du Brésil du Sud qui est associé à une région en dehors de sa géographie), ensemble faire une paire régionale.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figure 1 – schéma d'azur régional paire



| Géographie |  Jumelé régions |                  |
| :-------------| :-------------   | :-------------   |
| Amérique du Nord | North Central US | Centre Sud des États-Unis |
| Amérique du Nord | Oriental US | Ouest américain |
| Amérique du Nord | Est américaine 2 | US Central |
| L'Europe | Europe du Nord | Europe de l'ouest |
| L'Asie | L'Asie du sud-est | Asie de l'est |
| La Chine | Chine orientale | Chine du Nord |
| Japon | Japon est | Japon de l'ouest |
| Brésil | Brésil Sud (1) | Centre Sud des États-Unis |
| L'Australie | Australie orientale | Southeast| Australie
| Gouvernement américain | US Gov Iowa | Virginie Gov, États-Unis |

Tableau 1 - cartographie des paires régionales Azur

> (1) le Brésil sud est unique car il est couplé avec une région à l'extérieur de sa propre géographie. Notez que région secondaire du Brésil sud est sud Central US mais de South Central US région secondaire n'est pas Sud Brésil.

Nous recommandons que vous répliquez des charges de travail sur des paires régionaux de bénéficier de politiques d'isolement et de la disponibilité de d'Azure. Par exemple, mises à jour système Azur prévues sont déployées dans l'ordre (pas en même temps) dans toutes les régions appariées. Cela signifie que même dans l'éventualité rare d'une mise à jour défectueuse, les deux régions ne seront pas affectées simultanément. En outre, dans l'éventualité d'une panne générale, recouvrement d'au moins une région hors de chaque paire est priorisée.

## Exemple de paire régional
Figure 2 ci-dessous illustre une application hypothétique qui utilise la paire régionale pour la reprise après sinistre. Les numéros verts mettent en évidence les activités de la Croix-région des trois services Azure (Azure Compute, de stockage et de base de données) et comment ils sont configurés pour se répliquer dans toutes les régions. Les avantages uniques du déploiement dans toutes les régions appariées sont mises en évidence par les numéros orange.


![Aperçu des avantages de la région appariés](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figure 2 – hypothétique paire régional Azur

## Activités de la Croix-région
Conformément à la figure 2.

![1Green](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – Il doit aménager des ressources de calcul supplémentaire à l'avance pour s'assurer que les ressources sont disponibles dans une autre région en cas de catastrophe. Pour plus d'informations, consultez [Azur Business continuité des orientations techniques](https://msdn.microsoft.com/library/azure/hh873027.aspx)

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Stockage d'Azur** -Stockage géo-redondantes (GRS) est configuré par défaut lorsqu'un compte de stockage Azure est créé. Avec GRS, vos données sont répliquées automatiquement trois fois dans la région principale et trois fois dans la région de paire. Pour plus d'informations, consultez [Options de redondance de stockage Azur](../storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png) **Bases de données SQL Azure** – Avec Azure SQL Standard géo-réplication, vous pouvez configurer la réplication asynchrone des opérations à une région appariée. Avec Premium Geo-réplication, vous pouvez configurer réplication vers n'importe quelle région du monde ; Cependant, nous vous recommandons de que déployer ces ressources dans une région appariée pour la plupart des scénarios de récupération après sinistre. Pour plus d'informations, consultez  [Geo-réplication dans la base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Green](./media/best-practices-availability-paired-regions/4Green.png) **Gestionnaire de ressources Azur (ARM)** -BRAS fournit intrinsèquement isolation logique de composants de gestion de service dans toutes les régions. Cela signifie des défaillances logiques dans une région sont moins susceptibles d'influencer l'autre.

## Avantages d'une région apparié
Conformément à la figure 2.  

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolement physique** – Lorsque cela est possible, Azure préfère au moins 300 milles de séparation entre les centres de données dans une paire régionale, même si ce n'est pas pratique ou possible dans toutes les zones géographiques. Séparation de centre de données physique réduit les risques de catastrophes naturelles, troubles civils, des pannes d'électricité ou les coupures de réseau physique affectant les deux régions à la fois. L'isolement est soumis à des contraintes au sein de la géographie (taille de géographie, disponibilité de l'infrastructure réseau/alimentation, règlements, etc.).  

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)
**Réplication fournis par plateforme** -Certains services tels que le stockage redondant Geo fournissent une réplication automatique dans la région de paire.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png)
**Ordre de recouvrement de région** – Dans le cas d'une panne large, récupération d'une région prioritaire de chaque paire. Applications qui sont déployées dans toutes les régions appariées sont la garantie d'avoir une des régions récupéré en priorité. Si une application est déployée dans toutes les régions qui ne sont pas appariées, récupération peut être différée – dans le pire des cas que les régions choisies peuvent être les deux derniers à recouvrer.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png)
**Mises à jour séquentielles** – Mises à jour système Azur prévues sont déroulés aux régions appariées séquentiellement (pas en même temps) pour minimiser les temps d'arrêt, l'effet des bogues et des défaillances logiques dans l'éventualité rare d'une mauvaise mise à jour.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png)
**Résidence de données** – Une région réside dans la géographie même comme sa paire (à l'exception du sud du Brésil) afin de répondre aux conditions de résidence des données pour fins de compétence d'exécution fiscal et du droit.
