---
title: Modèles courants et idiomes dans Xamarin. Mac
description: Ce document décrit les modèles de conception courants utilisés lors de la création d’applications Xamarin. Mac. Il aborde le modèle Model-View-Controller, les modèles de source de données et de délégué, ainsi que les protocoles.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 06/17/2016
ms.openlocfilehash: b4934fa82d862ad2e8ab53579137873ed9e4bcca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770166"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Modèles courants et idiomes dans Xamarin. Mac

Tout au long des API Apple C#exposées via, certains idiomes et modèles sont reportés de façon répétée. Si vous avez une expérience de programmation avec Xamarin. iOS, ceux-ci peuvent vous paraître familiers. La documentation fait souvent référence à ces modèles et idiomes à plusieurs reprises. ainsi, une bonne compréhension de ces modèles vous aidera à comprendre la documentation que vous trouvez.

## <a name="mvc---model-view-controller"></a>MVC-contrôleur d’affichage du modèle

Model View Controller, ou MVC pour Short, est un modèle très courant trouvé dans le cacao. Une discussion détaillée n’entre pas dans le cadre de ce document, mais en bref, il s’agit d’un moyen de structurer votre application en composants :

- Les objets de **modèle** représentent les données sous-jacentes affichées et manipulées (comme les adresses dans un carnet d’adresses)
- Les objets de **vue** gèrent le dessin d’un objet donné à l’écran et gèrent l’interaction utilisateur (un champ de texte affiche l’adresse à l’écran)
- Les objets de **contrôleur** gèrent l’interaction entre le modèle et la vue. Ils changent de modèle pour mettre à jour la vue et envoyer les modifications « vers le haut » de la vue lorsque les utilisateurs apportent des modifications à l’interface utilisateur.

Si vous êtes familiarisé avec MVVM (Model View ViewModel) à partir d’autres bibliothèques telles que WPF, le contrôleur agit comme le ViewModel, mais il est souvent plus étroitement lié aux éléments d’interface utilisateur spécifiques.

Vous trouverez plus de détails ici :

- [Formation de MVC sur le site Web d’Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Contrôleur d’affichage du modèle en Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Utilisation de Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Source de données/délégué/sous-classe

Un autre modèle très courant de cacao traite de la fourniture de données aux éléments d’interface utilisateur et de la réaction aux interactions de l’utilisateur. À `NSTableView` l’aide de comme exemple, vous devez fournir des données pour chaque ligne, un ensemble d’éléments d’interface utilisateur qui représentent cette ligne, un ensemble de comportements pour réagir aux interactions de l’utilisateur, et éventuellement un certain nombre de personnalisations. Les modèles de source de données et de délégué vous permettent de gérer la plupart des cas sans avoir `NSTableView` recours à la sous-classe.

- Une `DataSource` instance d’une sous-classe personnalisée de `NSTableViewDataSource` qui est appelée pour remplir la table avec des données (via `GetRowCount` et `GetObjectValue`) est assignée à la propriété.

- Une `Delegate` instance d’une sous-classe personnalisée de `NSTableViewDelegate` qui fournit la vue d’un objet de modèle donné (via `GetViewForItem`) et gère les interactions de l’interface utilisateur `DidClickTableColumn`( `MouseDownInHeaderOfTableColumn`via,, etc.) est assignée à la propriété.

Dans certains cas, vous souhaiterez personnaliser un contrôle d’une manière au-delà des raccordements fournis dans le délégué ou la source de données, et vous pouvez sous-définir la vue directement. Toutefois, dans de nombreux cas, si vous remplacez le comportement par défaut, vous devrez alors gérer tout ce comportement vous-même (la personnalisation du comportement de sélection peut vous obliger à implémenter tous les comportements de sélection vous-même).

Dans Xamarin. iOS, certaines API, telles que `UITableView` , ont été étendues avec une propriété qui implémente à la fois le délégué et`UITableViewSource`la source de données (). Cela permet de contourner la limitation courante qu’une seule C# classe ne peut avoir qu’une seule classe de base, et que notre surface de protocoles s’effectue via des classes de base.

Pour plus d’informations sur l’utilisation des vues de table dans une application Xamarin. Mac, consultez notre documentation sur la [vue table](~/mac/user-interface/table-view.md) .

## <a name="protocols"></a>Protocoles

Les protocoles en Objective-C peuvent être comparés C#aux interfaces dans, et dans de nombreux cas, ils sont utilisés dans des situations similaires. Par exemple `NSTableView` , l’exemple ci-dessus, le délégué et la source de données sont en fait des protocoles. Xamarin. Mac les expose en tant que classes de base avec des méthodes virtuelles que vous pouvez substituer. La principale différence entre C# les interfaces et les protocoles objective-C est que certaines méthodes d’un protocole peuvent être facultatives pour implémenter. Vous devez examiner la documentation et/ou la définition d’une API pour déterminer ce qui est facultatif.

Pour plus d’informations, consultez notre documentation sur [les délégués, les protocoles et les événements](~/ios/app-fundamentals/delegates-protocols-and-events.md) .

## <a name="related-links"></a>Liens associés

- [Vues de table](~/mac/user-interface/table-view.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [Délégués, protocoles et événements](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
