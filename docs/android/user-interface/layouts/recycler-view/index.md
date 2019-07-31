---
title: RecyclerView
description: RecyclerView est un groupe d’affichage pour l’affichage des collections; Il est conçu pour être un remplacement plus souple pour les anciens groupes d’affichage tels que ListView et GridView.  Ce guide explique comment utiliser et personnaliser RecyclerView dans les applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 95d71beff2bd5219712494deb43f1f9fb4b082ec
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646307"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView est un groupe d’affichage pour l’affichage des collections; Il est conçu pour être un remplacement plus souple pour les anciens groupes d’affichage tels que ListView et GridView.  Ce guide explique comment utiliser et personnaliser RecyclerView dans les applications Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

De nombreuses applications ont besoin d’afficher des regroupements du même type (tels que des messages, des contacts, des images ou des chansons). souvent, cette collection est trop grande pour tenir sur l’écran, de sorte que la collection est présentée dans une petite fenêtre qui peut faire défiler facilement tous les éléments de la collection.
`RecyclerView`est un widget Android qui affiche une collection d’éléments dans une liste ou une grille, ce qui permet à l’utilisateur de faire défiler la collection. Voici une capture d’écran d’un exemple d’application qui `RecyclerView` utilise pour afficher le contenu de la boîte de réception de messagerie dans une liste de défilement verticale:

[![Exemple d’application utilisant RecyclerView pour répertorier les messages de la boîte de réception](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView`offre deux fonctionnalités attrayantes:

-  Il dispose d’une architecture flexible qui vous permet de modifier son comportement en branchant vos composants préférés.

-  Elle est efficace avec les collections volumineuses, car elle réutilise les vues d’élément et requiert l’utilisation de *conteneurs de vue* pour mettre en cache des références de vue.

Ce guide explique comment utiliser `RecyclerView` dans les applications Xamarin. Android. il explique comment ajouter le package à votre projet Xamarin. Android et décrit comment `RecyclerView` fonctionne les `RecyclerView` fonctions dans une application classique. Des exemples de code réels sont fournis pour vous montrer comment `RecyclerView` intégrer à votre application, comment implémenter le mode d’affichage des éléments et comment `RecyclerView` actualiser le moment où ses données sous-jacentes sont modifiées. Ce guide part du principe que vous êtes familiarisé avec le développement Xamarin. Android.


### <a name="requirements"></a>Configuration requise

Bien `RecyclerView` que soit souvent associé à Android 5,0 Lollipop, il est proposé sous la forme &ndash; d’une bibliothèque `RecyclerView` de prise en charge qui fonctionne avec les applications qui ciblent le niveau d’API 7 (Android 2,1) et versions ultérieures. Les éléments suivants doivent être utilisés `RecyclerView` dans les applications basées sur Xamarin:

-  **Xamarin. Android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac.

-  Votre projet d’application doit inclure le package **Xamarin. Android. support. v7. RecyclerView** . Pour plus d’informations sur l’installation des packages [NuGet, consultez Procédure pas à pas: Y compris un NuGet dans votre](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)projet.


### <a name="overview"></a>Présentation

`RecyclerView`peut être considérée comme un remplacement des `ListView` widgets et `GridView` dans Android. Comme ses prédécesseurs, `RecyclerView` est conçu pour afficher un jeu de données volumineux dans une petite fenêtre, `RecyclerView` mais il offre davantage d’options de disposition et est mieux optimisé pour l’affichage des collections volumineuses. Si vous êtes familiarisé avec `ListView`, il existe plusieurs différences importantes entre `ListView` et `RecyclerView`:

-   `RecyclerView`est légèrement plus complexe à utiliser: vous devez écrire du code supplémentaire à utiliser `RecyclerView` par rapport `ListView`à.

-   `RecyclerView`ne fournit pas d’adaptateur prédéfini; vous devez implémenter le code d’adaptateur qui accède à votre source de données. Toutefois, Android intègre plusieurs adaptateurs prédéfinis qui fonctionnent avec `ListView` et `GridView`.

-   `RecyclerView`n’offre pas d’événement de clic d’élément lorsqu’un utilisateur appuie sur un élément; au lieu de cela, les événements de clic d’élément sont gérés par les classes d’assistance. En revanche, `ListView` propose un événement de clic d’élément.

-   `RecyclerView`améliore les performances en recyclant les vues et en appliquant le modèle de support d’affichage, ce qui élimine les recherches de ressources de disposition inutiles. L’utilisation du modèle de conteneur d’affichage est facultative `ListView`dans.

-   `RecyclerView`est basé sur une conception modulaire qui facilite la personnalisation. Par exemple, vous pouvez intégrer une stratégie de mise en page différente sans modification significative du code de votre application.
    En revanche, `ListView` est relativement monolithique dans la structure.

-   `RecyclerView`comprend des animations intégrées pour l’ajout et la suppression d’éléments. `ListView`les animations requièrent des efforts supplémentaires sur la partie du développeur de l’application.


### <a name="sections"></a>Sections

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Composants et fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Cette rubrique explique `Adapter`comment, `LayoutManager`et `ViewHolder` fonctionnent ensemble en tant que classes d’assistance pour prendre `RecyclerView`en charge.
Il fournit une vue d’ensemble détaillée de chacune de ces classes d’assistance et explique comment les utiliser dans votre application.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Exemple de RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Cette rubrique s’appuie sur les informations fournies dans les [composants et fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) en fournissant des exemples de code réels `RecyclerView` sur la façon dont les différents éléments sont implémentés pour créer une application de navigation de photos réelle.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Cette rubrique ajoute du code supplémentaire à l’exemple d’application présenté dans [un exemple RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) pour montrer comment gérer des événements de clic `RecyclerView` d’élément et mettre à jour lorsque la source de données sous-jacente est modifiée.


### <a name="summary"></a>Récapitulatif

Ce guide a présenté le `RecyclerView` widget Android. il a expliqué comment ajouter `RecyclerView` la bibliothèque de prise en charge aux projets Xamarin `RecyclerView` . Android, comment recycle les vues, comment elle applique le modèle de détenteur d’affichage pour l’efficacité et comment les différentes classes d’assistance qui composent la collaboration pour afficher des `RecyclerView` Collections. Il a fourni un exemple de code `RecyclerView` pour illustrer la façon dont est intégré à une application `RecyclerView`, mais il a expliqué comment adapter la stratégie de mise en page en connectant différents gestionnaires de disposition et décrit comment gérer les événements de clic d’élément et notifier `RecyclerView`des modifications de la source de données.

Pour plus d’informations `RecyclerView`sur, consultez la référence de la [classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Liens associés

- [RecyclerViewer (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Présentation du symbole Lollipop](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
