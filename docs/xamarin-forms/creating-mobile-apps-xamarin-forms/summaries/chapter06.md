---
title: Résumé du chapitre 6. Clics de bouton
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 6. Clics de bouton'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334717"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Résumé du chapitre 6. Clics de bouton

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

La [`Button`](xref:Xamarin.Forms.Button) est la vue qui permet à l’utilisateur de lancer une commande. Une `Button` est identifiée par du texte (et éventuellement une image, comme illustré dans le [chapitre 13, bitmaps](chapter13.md)). Par conséquent, `Button` définit un grand nombre des mêmes propriétés que `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` définit également trois propriétés qui régissent l’apparence de sa bordure, mais la prise en charge de ces propriétés et de leur indépendance mutuelle est spécifique à la plateforme :

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) de type `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) de type `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) de type `Double`

`Button` hérite également de toutes les propriétés de `VisualElement` et `View`, y compris `BackgroundColor`, `HorizontalOptions`et `VerticalOptions`.

## <a name="processing-the-click"></a>Traitement de la, cliquez sur

La classe `Button` définit un événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) qui est déclenché quand l’utilisateur appuie sur le `Button`. Le gestionnaire de `Click` est de type `EventHandler`. Le premier argument est le `Button` objet qui génère l’événement ; le deuxième argument est un objet `EventArgs` qui ne fournit aucune information supplémentaire.

L’exemple [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustre la gestion des `Clicked` simple.

## <a name="sharing-button-clicks"></a>Bouton de partage clique sur

Plusieurs vues de `Button` peuvent partager le même gestionnaire de `Clicked`, mais le gestionnaire a généralement besoin de déterminer les `Button` qui sont responsables d’un événement particulier. Une approche consiste à stocker les différents objets `Button` en tant que champs et à vérifier celui qui déclenche l’événement dans le gestionnaire.

L’exemple [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) illustre cette technique. Le programme montre également comment définir la propriété [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) d’un `Button` sur `false` lorsque l’utilisation du `Button` n’est plus valide. Un `Button` désactivé ne génère pas d’événement `Clicked`.

## <a name="anonymous-event-handlers"></a>Gestionnaires d’événements anonyme

Il est possible de définir des gestionnaires de `Clicked` comme fonctions lambda anonymes, comme le montre l’exemple [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . Toutefois, gestionnaires anonymes ne peut pas être partagés sans un code de réflexion désordonnées.

## <a name="distinguishing-views-with-ids"></a>Distinction des vues avec des ID

Vous pouvez également distinguer plusieurs objets `Button` en affectant à la propriété [`StyleId`](xref:Xamarin.Forms.Element.StyleId) ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propriété la valeur d’un `string`. Cette propriété est définie par `Element`, mais elle n’est pas utilisée dans Xamarin. Forms. Elle est destinée à être utilisée uniquement par les programmes d’application.

L’exemple [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) utilise le même gestionnaire d’événements pour les 10 touches numériques sur un pavé numérique et les distingue avec la propriété `StyleId` :

[![Capture d’écran triple du pavé numérique le plus simple](images/ch06fg04-small.png "Calculatrice")](images/ch06fg04-large.png#lightbox "Calculatrice")

## <a name="saving-transient-data"></a>Enregistrer les données temporaires

De nombreuses applications ont besoin pour enregistrer les données lorsqu’un programme est arrêté et recharger ces données lorsque le programme démarre à nouveau. La classe [`Application`](xref:Xamarin.Forms.Application) définit plusieurs membres qui aident votre programme à enregistrer et à restaurer des données temporaires :

- La propriété [`Properties`](xref:Xamarin.Forms.Application.Properties) est un dictionnaire avec des clés `string` et des éléments de `object`. Le contenu du dictionnaire est automatiquement enregistré dans le stockage local des applications avant l’arrêt du programme et rechargé lorsque le programme démarre.
- La classe `Application` définit trois méthodes virtuelles protégées que la classe `App` standard du programme remplace : [`OnStart`](xref:Xamarin.Forms.Application.OnStart), [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)et [`OnResume`](xref:Xamarin.Forms.Application.OnResume). Ils font référence aux événements *du cycle de vie* de l’application.
- La méthode [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) enregistre le contenu du dictionnaire.

Il n’est pas nécessaire d’appeler `SavePropertiesAsync`. Le contenu du dictionnaire est automatiquement enregistré avant l’arrêt du programme et récupéré avant le démarrage du programme. Il est utile lors du test de programme pour enregistrer les données si le programme se bloque.

Également est utile :

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), propriété statique qui retourne l’objet `Application` actuel que vous pouvez ensuite utiliser pour obtenir le dictionnaire de `Properties`.

La première étape consiste à identifier toutes les variables dans la page que vous souhaitez rendre persistantes lorsque le programme se termine. Si vous connaissez tous les endroits où ces variables changent, vous pouvez simplement les ajouter au dictionnaire `Properties` à ce stade. Dans le constructeur de la page, vous pouvez définir les variables à partir du dictionnaire `Properties` si la clé existe.

Un programme plus vaste devrez probablement à traiter les événements de cycle de vie d’application. La plus importante est la méthode `OnSleep`. Un appel à cette méthode indique que le programme a quitté le premier plan. Peut-être que l’utilisateur a appuyé sur le bouton de **démarrage** de l’appareil ou affiché toutes les applications, ou qu’il arrête le téléphone. Un appel à `OnSleep` est la seule notification qu’un programme reçoit avant qu’il ne soit arrêté. Le programme doit prendre cette occasion pour s’assurer que le dictionnaire `Properties` est à jour.

Un appel à `OnResume` indique que le programme ne s’est pas arrêté après le dernier appel à `OnSleep`, mais qu’il s’exécute à présent au premier plan. Le programme peut utiliser cette opportunité pour actualiser les connexions internet (par exemple).

Un appel à `OnStart` se produit pendant le démarrage du programme. Il n’est pas nécessaire d’attendre que cet appel de méthode accède au dictionnaire `Properties`, car le contenu a déjà été restauré lorsque le constructeur `App` est appelé.

L’exemple [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) est très similaire à **SimplestKeypad** , sauf que le programme utilise le `OnSleep` remplacement pour enregistrer l’entrée du pavé numérique en cours et le constructeur de la page pour restaurer ces données.

> [!NOTE]
> Une autre approche de l’enregistrement des paramètres de programme est fournie par la classe de [Préférences](~/essentials/preferences.md) Xamarin. Essentials.

## <a name="related-links"></a>Liens connexes

- [Chapitre 6 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemples du chapitre 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Exemples du F# chapitre 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Bouton Xamarin. Forms](~/xamarin-forms/user-interface/button.md)
