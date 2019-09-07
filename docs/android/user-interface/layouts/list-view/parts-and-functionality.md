---
title: Composants et fonctionnement de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 4566ee5d203b5d098133aebe2c32dbaec712e17a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764212"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Fonctionnalités et composants ListView de Xamarin. Android

Un `ListView` se compose des parties suivantes :

- **Lignes** &ndash; Représentation visible des données dans la liste.

- **Adaptateur** &ndash; Classe non visuelle qui lie la source de données à la vue liste.

- **Défilement rapide** &ndash; Handle qui permet à l’utilisateur de faire défiler la liste.

- **Index de section** &ndash; Élément d’interface utilisateur qui flotte sur les lignes de défilement pour indiquer où se trouvent les lignes actuelles dans la liste.

Ces captures d’écran utilisent `ListView` un contrôle de base pour montrer comment le défilement rapide et l’index de section sont rendus :

[![Captures d’écran d’applications utilisant des lignes brutes, le défilement rapide et l’index de section](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Les éléments qui composent `ListView` un sont décrits plus en détail ci-dessous :

## <a name="rows"></a>Lignes

Chaque ligne a son propre `View`. La vue peut être l’une des vues intégrées définies dans `Android.Resources`, ou une vue personnalisée. Chaque ligne peut utiliser la même disposition de vue ou elle peut être différente. Ce document contient des exemples d’utilisation de dispositions intégrées et d’autres qui expliquent comment définir des dispositions personnalisées.

## <a name="adapter"></a>Adaptateur

Le `ListView` contrôle requiert un `Adapter` pour fournir le mis `View` en forme pour chaque ligne. Android contient des adaptateurs et des vues intégrés qui peuvent être utilisés, ou des classes personnalisées qui peuvent être créées.

## <a name="fast-scrolling"></a>Défilement rapide

Quand un `ListView` contient de nombreuses lignes de données, le défilement rapide peut être activé pour permettre à l’utilisateur d’accéder à n’importe quelle partie de la liste. La « barre de défilement » de défilement rapide peut être activée (et personnalisée au niveau de l’API 11 et versions ultérieures).

## <a name="section-index"></a>Index de section

Lorsque vous faites défiler les longues listes, l’index de section facultatif fournit à l’utilisateur des commentaires sur la partie de la liste affichée actuellement. Elle s’applique uniquement aux longues listes, en général conjointement avec le défilement rapide.

## <a name="classes-overview"></a>Vue d’ensemble des classes

Les classes principales utilisées pour afficher `ListViews` sont indiquées ici :

[![Diagramme UML illustrant les relations entre ListView et les classes associées](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

L’objectif de chaque classe est décrit ci-dessous :

- **Liste** &ndash; élément d’interface utilisateur qui affiche une collection de lignes à défilement. Sur les téléphones, elle utilise généralement la totalité de l’écran (auquel cas `ListActivity` la classe peut être utilisée) ou elle peut faire partie d’une disposition plus grande sur les téléphones ou les appareils tablettes.

- **Afficher** une vue dans Android peut être n’importe quel élément d’interface utilisateur, mais dans le `ListView` contexte d’un `View` , un doit être fourni pour chaque ligne. &ndash;

- **BaseAdapter** Classe de base pour les implémentations d’adaptateur `ListView` pour lier un à une source de données. &ndash;

- **ArrayAdapter** Classe d’adaptateur intégrée qui lie un tableau de chaînes à un `ListView` pour l’affichage. &ndash; Le générique `ArrayAdapter<T>` fait de même pour d’autres types.

- **CursorAdapter** &ndash; Utilisez oupour`SimpleCursorAdapter` afficher des données en fonction d’une requête SQLite. `CursorAdapter`

Ce document contient des exemples simples qui utilisent `ArrayAdapter` un et des exemples plus complexes qui requièrent des implémentations `BaseAdapter` personnalisées de ou `CursorAdapter`.
